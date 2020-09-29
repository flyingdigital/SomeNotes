## nginx高可用准备

所谓高可用就是为了防止nginx服务器突然无法正常提供服务，导致应用无法访问。这里我们通过keepalived来监控，如果主机nginx出现问题，那么我们就访问从机。(访问的是一个虚拟ip)

需要准备如下

- 两台虚拟机
- 两台虚拟机安装nginx服务     # 没有特殊要求默认即可  有再配置
- 两台虚拟机安装keepalived    # 用来监控ngin服务器是否出问题。。  yum -y install keepalived



### keepalived的配置

keelalived的配置文件路径/etc/keepalived/keepalived.conf

- keepalived主机的配置

  ```
  ! Configuration File for keepalived
  
  global_defs {
   #  notification_email {
   #    acassen@firewall.loc
   #    failover@firewall.loc
   #    sysadmin@firewall.loc
   #  }
   #  notification_email_from Alexandre.Cassen@firewall.loc
   #  smtp_server 192.168.200.1
   #  smtp_connect_timeout 30
     router_id LVS_DEVEL
   #  vrrp_skip_check_adv_addr
   #  vrrp_strict
   #  vrrp_garp_interval 0
   #  vrrp_gna_interval 0
  }
  vrrp_script chk_nginx {
          script "/usr/local/src/nginx_check.sh"    #监控脚本的位置
          interval 2
          weight -20
  }
  vrrp_instance VI_1 {
      state MASTER # 标识为主服务MASTER
      interface ens33 #绑定虚拟机的IP
      virtual_router_id 51 # 虚拟路由id，和从机保持一致
      #mcast_src_ip 192.168.43.245  #本机ip
      priority 100 #权重，需要高于从机
      advert_int 1
      authentication {
          auth_type PASS
          auth_pass 1111
      }
     track_script {
                  chk_nginx ## 执行 Nginx 监控的服务
          }
      virtual_ipaddress {
          192.168.43.50 #/32 brd 255.255.255.0 dev ens33 label ens33:vip #虚拟IP地址
  #        192.168.200.17
  #        192.168.200.18
      }
  }
  ```

- keepalived从机的配置

  ```
  ! Configuration File for keepalived
  
  global_defs {
   #  notification_email {
   #    acassen@firewall.loc
   #    failover@firewall.loc
   #    sysadmin@firewall.loc
   #  }
   #  notification_email_from Alexandre.Cassen@firewall.loc
   #  smtp_server 192.168.200.1
   #  smtp_connect_timeout 30
     router_id deram1
   #  vrrp_skip_check_adv_addr
   #  vrrp_strict
   #  vrrp_garp_interval 0
   #  vrrp_gna_interval 0
  }
  vrrp_script chk_nginx {
          script "usr/local/src/nginx_check.sh"
          interval 2
          weight -20
  }
  vrrp_instance VI_1 {
      state BACKUP # 标识为从服务 BACKUP
      interface ens33 #绑定虚拟机的IP
      virtual_router_id 51 # 虚拟路由id，和从机保持一致
      #mcast_src_ip 192.168.126.2  #本机ip
      priority 90 #权重，需要高于从机
      advert_int 1
      authentication {
          auth_type PASS
          auth_pass 1111
      }
     track_script {
                  chk_nginx ## 执行 Nginx 监控的服务
          }
      virtual_ipaddress {
          192.168.43.34 #/32 brd 255.255.255.0 dev ens33 label ens33:vip #虚拟IP地址
  #        192.168.200.17
  #        192.168.200.18
      }
  }
  ```



如上需要关注的几个地方为

- router_id	路由id

- script          脚本路径
- state           主机从机服务标识
- interface     使用的是那一块网卡  ifconfig查看 
- virtual_router    虚拟路由ip  主从机要一致
- priority             权重，主机大于从机
- virtual_script   虚拟ip地址



### 测试

1. 主从机启动服务

   ```
   # 分别启动nginx服务与keepalived服务
   cd /usr/local/nginx/sbin
   ./nginx
   
   service keepalived start
   ```

2. 由浏览器地址栏访问虚拟ip（192.168.43.50），如果出现nginx默认欢迎页面即表示主机配置成功

3. 关掉主机nginx与keepalived服务，再次访问虚拟ip，若为nginx默认欢迎页面，则表示从机设置成功

   ```
   # 关闭主机nginx与keepalived服务
   
   cd /usr/local/nginx/sbin
   ./nginx -s stop
   
   service keepalived stop
   ```

   

最后， 主机从机使用ifconfig命令可以看到虚拟ip已经绑定到网卡上了。


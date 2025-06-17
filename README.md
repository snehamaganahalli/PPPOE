**Point to Point Protocol over Ethernrt (PPPoE)**


client1 ===== DUT[eth4(br-lan)=====eth5====pppoe] ====  client2

      DUT-1 (PPPoE Server)
      
       fw stop
       /etc/ppp/pppoe-stop
       uci set network.loopback=interface
       uci set network.loopback.ifname=lo
       uci set network.loopback.proto=static
       uci set network.loopback.ipaddr=127.0.0.1
       uci set network.loopback.netmask=255.0.0.0
       uci set network.lan=interface
       uci set network.lan.ifname='eth4'
       uci set network.lan.type=bridge
       uci set network.lan.proto=static
       uci set network.lan.ipaddr=192.168.2.1
       uci set network.lan.netmask=255.255.255.0
       uci set network.lan.ip6addr=4aaa::1/64
       uci set network.wan=interface
       uci set network.wan.ifname=eth5
       uci set network.wan.proto=dhcp
       /etc/init.d/firewall stop
       uci commit
       /etc/init.d/network restart
       rm /etc/ppp/pppoe-server-options
       #echo "plugin /usr/lib/pppd/2.4.7/rp-pppoe.so" >> /etc/ppp/pppoe-server-options
       echo 'plugin /usr/lib/pppd/2.4.9/pppoe.so' >> /etc/ppp/pppoe-server-options
       echo "require-chap" >> /etc/ppp/pppoe-server-options
       echo "login" >> /etc/ppp/pppoe-server-options
       echo "lcp-echo-interval 10" >> /etc/ppp/pppoe-server-options
       echo "lcp-echo-failure 1000" >> /etc/ppp/pppoe-server-options
       echo "defaultroute" >> /etc/ppp/pppoe-server-options
       echo "noipdefault" >> /etc/ppp/pppoe-server-options
       echo "usepeerdns" >> /etc/ppp/pppoe-server-options
       echo "debug" >> /etc/ppp/pppoe-server-options
       rm /etc/ppp/options
       echo "logfile /dev/null" >> /etc/ppp/options
       echo "noipdefault" >> /etc/ppp/options
       echo "noaccomp" >> /etc/ppp/options
       echo "nopcomp" >> /etc/ppp/options
       echo "nocrtscts" >> /etc/ppp/options
       echo "lock" >> /etc/ppp/options
       echo "maxfail 0" >> /etc/ppp/options
       echo "lcp-echo-failure 1000" >> /etc/ppp/options
       echo "lcp-echo-interval 1" >> /etc/ppp/options
       rm /etc/ppp/chap-secrets
       echo " builder * builder * " >>/etc/ppp/chap-secrets
       rm /etc/ppp/pppoe-start
       echo "pppoe-server -k -C isp -L 172.31.7.1 -p /etc/ppp/ipaddress_pool -I eth5 -m 1412" >> /etc/ppp/pppoe-start
       rm /etc/ppp/ipaddress_pool
       echo "172.31.7.2" >> /etc/ppp/ipaddress_pool
       rm /etc/ppp/pppoe-stop
       echo "killall pppoe-server" >> /etc/ppp/pppoe-stop
       echo "killall pppd" >> /etc/ppp/pppoe-stop         
       chmod 777 /etc/ppp/pppoe-start
       chmod 777 /etc/ppp/pppoe-stop
       chmod 777 /etc/ppp/ipaddress_pool
       echo 1 > /proc/sys/net/ipv4/ip_forward
       /etc/ppp/pppoe-start


       DUT-2 (PPPoE Client)

       uci set network.loopback=interface
       uci set network.loopback.ifname=lo
       uci set network.loopback.proto=static
       uci set network.loopback.ipaddr=127.0.0.1
       uci set network.loopback.netmask=255.0.0.0
       uci set network.lan=interface
       uci set network.lan.ifname='eth4'
       uci set network.lan.type=bridge
       uci set network.lan.proto=static
       uci set network.lan.ipaddr=192.168.1.1
       uci set network.lan.netmask=255.255.255.0
       uci set network.lan.ip6addr=2aaa::1/64
       uci set network.wan=interface
       uci set network.wan.ifname=eth5
       uci set network.wan.proto=pppoe
       uci set network.wan.ipaddr=192.168.10.10
       uci set network.wan.netmask=255.255.255.0
       uci set network.wan.username='builder'
       uci set network.wan.password='builder'
       uci set firewall.@defaults[0]=defaults
       uci set firewall.@defaults[0].syn_flood=1
       uci set firewall.@defaults[0].input=ACCEPT
       uci set firewall.@defaults[0].output=ACCEPT
       uci set firewall.@defaults[0].forward=ACCEPT
       uci set firewall.@zone[0]=zone
       uci set firewall.@zone[0].name=lan
       uci set firewall.@zone[0].network=lan
       uci set firewall.@zone[0].input=ACCEPT
       uci set firewall.@zone[0].output=ACCEPT
       uci set firewall.@zone[0].forward=ACCEPT
       uci set firewall.@zone[1]=zone
       uci set firewall.@zone[1].name=wan
       uci set firewall.@zone[1].network=wan
       uci set firewall.@zone[1].input=ACCEPT
       uci set firewall.@zone[1].output=ACCEPT
       uci set firewall.@zone[1].forward=ACCEPT
       uci set firewall.@zone[1].masq=1
       uci set firewall.@zone[1].mtu_fix=1
       uci add firewall redirect
       uci set firewall.@redirect[0]=redirect
       uci set firewall.@redirect[0].target=DNAT
       uci set firewall.@redirect[0].src=wan
       uci set firewall.@redirect[0].dest=lan
       uci set firewall.@redirect[0].proto='tcp udp'
       uci set firewall.@redirect[0].name=DNAT
       uci set firewall.@redirect[0].src_ip=192.168.2.2
       uci set firewall.@redirect[0].src_dip=172.31.7.2
       uci set firewall.@redirect[0].dest_ip=192.168.1.2
       uci commit
       /etc/init.d/firewall restart
       /etc/init.d/network restart
       echo '#debug' > /etc/ppp/options
       echo 'logfile /dev/null' >>/etc/ppp/options 
       echo 'noipdefault'>>/etc/ppp/options
       echo 'noaccomp'>>/etc/ppp/options
       echo 'nopcomp'>>/etc/ppp/options
       echo 'nocrtscts'>>/etc/ppp/options
       echo 'lock'>>/etc/ppp/options
       echo 'maxfail 0'>>/etc/ppp/options
       echo 'lcp-echo-failure 10000'>>/etc/ppp/options
       echo 'lcp-echo-interval 1'>>/etc/ppp/options


       /etc/ppp/pppoe-start

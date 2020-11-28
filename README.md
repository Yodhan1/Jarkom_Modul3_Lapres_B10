# Jarkom_Modul3_Lapres_B10
Kelompok B10 Kelas Jarkom Tanpa Revisi
topologi.sh
    # Switch
    uml_switch -unix switch1 > /dev/null < /dev/null &
    uml_switch -unix switch2 > /dev/null < /dev/null &
    uml_switch -unix switch3 > /dev/null < /dev/null &

    # Router
    xterm -T SURABAYA -e linux ubd0=SURABAYA,jarkom umid=SURABAYA eth0=tuntap,,,10.151.74.45 eth1=daemon,,,switch1 eth2=daemon,,,switch3 eth3=daemon,,,switch2 mem=256M &

    # Server switch 2
    xterm -T MALANG -e linux ubd0=MALANG,jarkom umid=MALANG eth0=daemon,,,switch2 mem=160M &
    xterm -T MOJOKERTO -e linux ubd0=MOJOKERTO,jarkom umid=MOJOKERTO eth0=daemon,,,switch2 mem=128M &
    xterm -T TUBAN -e linux ubd0=TUBAN,jarkom umid=TUBAN eth0=daemon,,,switch2 mem=128M &

    # Klien switch 1
    xterm -T SIDOARJO -e linux ubd0=SIDOARJO,jarkom umid=SIDOARJO eth0=daemon,,,switch1 mem=64M &
    xterm -T GRESIK -e linux ubd0=GRESIK,jarkom umid=GRESIK eth0=daemon,,,switch1 mem=64M &

    # Klien switch 3
    xterm -T MADIUN -e linux ubd0=MADIUN,jarkom umid=MADIUN eth0=daemon,,,switch3 mem=64M &
    xterm -T BANYUWANGI -e linux ubd0=BANYUWANGI,jarkom umid=BANYUWANGI eth0=daemon,,,switch3 mem=64M &

bye.sh 
    uml_mconsole SURABAYA halt
    uml_mconsole MALANG halt
    uml_mconsole MOJOKERTO halt
    uml_mconsole TUBAN halt
    uml_mconsole SIDOARJO halt
    uml_mconsole GRESIK halt
    uml_mconsole MADIUN halt
uml_mconsole BANYUWANGI halt

langkah langkah pemulaan
    SURABAYA{

        nano /etc/sysctl.conf
        net.ipv4.ip_forward=1
        sysctl -p
    }

    nano /etc/network/interfaces
        SURABAYA
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet static
            address 10.151.74.46
            netmask 255.255.255.252
            gateway 10.151.74.45

            auto eth1
            iface eth1 inet static
            address 192.168.0.1
            netmask 255.255.255.0

            auto eth2
            iface eth2 inet static
            address 192.168.1.1
            netmask 255.255.255.0

            auto eth3
            iface eth3 inet static
            address 10.151.83.89
            netmask 255.255.255.248
        MALANG
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet static
            address 10.151.83.90
            netmask 255.255.255.248
            gateway 10.151.83.89
        MOJOKERTO
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet static
            address 10.151.83.91
            netmask 255.255.255.248
            gateway 10.151.83.89
        TUBAN
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet static
            address 10.151.83.92
            netmask 255.255.255.248
            gateway 10.151.83.89
        SIDOARJO
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet dhcp
        GRESIK
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet dhcp
        MADIUN
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet dhcp
        BANYUWANGI
            auto lo
            iface lo inet loopback

            auto eth0
            iface eth0 inet dhcp


    service networking restart

    apt-get update


jawaban no 2
SURABAYA
    nano /etc/sysctl.conf
    net.ipv4.conf.all.accept_source_route = 1
    sysctl -p
    apt-get install isc-dhcp-relay -y
    nano /etc/default/isc-dhcp-relay
    server 10.151.83.92 or eth3
    interface <blank> or eth1 eth2 eth3
    

TUBAN
    apt-get install isc-dhcp-server -y
    nano /etc/default/isc-dhcp-server
    INTERFACES="eth0"
    nano /etc/dhcp/dhcpd.conf
        subnet 10.151.83.0 netmask 255.255.255.0 {  
            option routers 10.151.83.92; 
        }

jawaban no 3 4 5 6

nano /etc/dhcp/dhcpd.conf
    subnet 192.168.0.0 netmask 255.255.255.0 {
        range 192.168.0.10 192.168.0.100;
        range 192.168.0.110 192.168.0.200;
        option routers 10.151.83.89;
        option broadcast-address 192.168.0.255;
        option domain-name-servers 10.151.83.90, 202.46.129.2;
        default-lease-time 300;
        max-lease-time 600;
    }

    subnet 192.168.1.0 netmask 255.255.255.0 {
        range 192.168.1.50 192.168.1.70;
        option routers 10.151.83.89;
        option broadcast-address 192.168.1.255;
        option domain-name-servers 10.151.83.90, 202.46.129.2;
        default-lease-time 600;
        max-lease-time 1200;
    }
service isc-dhcp-server stop
service isc-dhcp-server start
service isc-dhcp-relay stop
service isc-dhcp-relay start

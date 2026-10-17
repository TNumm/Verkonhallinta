1. Johdanto
	Ympäristön tarkoituksena on oppia käyttämään verkonhallintaa WSL:n Dockerin ja Containerlabin avulla

1.1 Topologian kartoitus
	Laite			Tarkoitus
	R1			Yhdistää käyttäjäverkko (client, attacker) muihin verkkoihin
	R2			Yhdistää R1, R3, palvelinverkko sekä hallintaverkko
	R3			Yhdistää branch-client
	client1			Käyttäjän tietokone
	attacker		Tietoturvatestaus
	web1			Verkkopalvelin
	db1			Tietokantapalvelin
	branch-client		Haarakakäyttäjä
	ansible			Verkonhallinnan automaatiotyökalo
	prometheus		Valvontajärjestelmä
	grafana			Mittaustietojen visualisointityökalo
	zabbix			Verkkovalvontajärjestelmä
	

2. Verkkokaavio
Kuva liitettynä images kansioon topology.png nimellä

3. Laiteluettelo ja IP suunnitelma

	Verkko		Tarkoitus		Yhdyskäytävä	Mitä tarkoitusta verkko palvelee					Mitä laitteita verkkoon kuuluu
	10.10.10.0/24	Käyttäjäverkko		10.10.10.1	Käyttäjäverkossa sijaitsee tavalliset client laitteet			client1, attacker
	10.10.20.0/24	Palvelinverkko		10.10.20.1	Verkko ja tietokantapalvelimet						web1, db1
	10.10.30.0/24	Etäkäyttö		10.10.30.1	Etäkäytönverkko mahdollistaa etäyhteyden käytön?			branch-client
	10.10.99.0/24	Hallintaverkko				Mahdollistaa laitteiden hallinnan, automatisoinnin ja sen valvonnan	grafana, syslog, ansible, cadvisor, promethus, zabbix
	10.255.12.0/30	R1-R2 yhteys				R1-R2 yhteys								R1,R2	
	10.255.23.0/30	R2-R3 yhteys				R2-R3 yhteys								R2, R3
	
	Mikä reititin toimii yhdyskäytävänä?
	R3 reititin, sillä se välittää liikenteen muihin verkkoihin


4. Reitityksen analyysi

	Löytyykö yhteys kaikkiin verkkoihin 
	Kyllä löytyy.
 
		Pingit ping -c 4 10.10.20.101 ja ping -c 4 10.10.30.101 toimivat
		root@client1:/# ping -c 4 10.10.20.101
		PING 10.10.20.101 (10.10.20.101) 56(84) bytes of data.
		64 bytes from 10.10.20.101: icmp_seq=1 ttl=62 time=0.076 ms
		64 bytes from 10.10.20.101: icmp_seq=2 ttl=62 time=0.063 ms
		64 bytes from 10.10.20.101: icmp_seq=3 ttl=62 time=0.067 ms
		64 bytes from 10.10.20.101: icmp_seq=4 ttl=62 time=0.113 ms

		--- 10.10.20.101 ping statistics ---
		4 packets transmitted, 4 received, 0% packet loss, time 3051ms
		rtt min/avg/max/mdev = 0.063/0.079/0.113/0.019 ms

		root@client1:/# ping -c 4 10.10.30.101
		PING 10.10.30.101 (10.10.30.101) 56(84) bytes of data.
		64 bytes from 10.10.30.101: icmp_seq=1 ttl=61 time=0.078 ms
		64 bytes from 10.10.30.101: icmp_seq=2 ttl=61 time=0.065 ms
		64 bytes from 10.10.30.101: icmp_seq=3 ttl=61 time=0.055 ms
		64 bytes from 10.10.30.101: icmp_seq=4 ttl=61 time=0.055 ms

		--- 10.10.30.101 ping statistics ---
		4 packets transmitted, 4 received, 0% packet loss, time 3077ms
		rtt min/avg/max/mdev = 0.055/0.063/0.078/0.009 ms


	 Mitä reittiä liikenne kulkee 
	
		traceroute to 10.10.30.101 (10.10.30.101), 30 hops max, 60 byte packets
 		1  10.10.10.1 (10.10.10.1)  0.333 ms  0.260 ms  0.250 ms
 		2  10.255.12.2 (10.255.12.2)  0.243 ms  0.219 ms  0.209 ms
 		3  10.255.23.2 (10.255.23.2)  0.199 ms  0.177 ms  0.165 ms
 		4  10.10.30.101 (10.10.30.101)  0.153 ms  0.131 ms  0.116 ms

 	Mitä reitittimiä reitille kuuluu
	R1,R2 ja R3

	ip addr
	
	root@client1:/# ip addr
	1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    		link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    		inet 127.0.0.1/8 scope host lo
       		valid_lft forever preferred_lft forever
    		inet6 ::1/128 scope host
       		valid_lft forever preferred_lft forever
	2: eth0@if33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    		link/ether 12:45:e5:0d:4f:32 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    		inet 172.20.20.9/24 brd 172.20.20.255 scope global eth0
       		valid_lft forever preferred_lft forever
	21: eth1@if22: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 9500 qdisc noqueue state UP group default
    		link/ether aa:c1:ab:0b:9a:90 brd ff:ff:ff:ff:ff:ff link-netnsid 1
    		altname clab-o-05031180f95d8850
    		inet 10.10.10.101/24 scope global eth1
      		valid_lft forever preferred_lft forever
    		inet6 fe80::a8c1:abff:fe0b:9a90/64 scope link
       		valid_lft forever preferred_lft forever

	ip route

	root@client1:/# ip route
	default via 10.10.10.1 dev eth1
	10.10.10.0/24 dev eth1 proto kernel scope link src 10.10.10.101
	172.20.20.0/24 dev eth0 proto kernel scope link src 172.20.20.9



5. Yhteenveto


	Mitkä asiat kuluttivat aikaa dokumentaation muodostamisessa?
		
		Yleisesti tehtävä kokonaisuutena vei hieman aikaa alkun ongelmien takia. Jouduin käyttämään tekoälyä selvittääkseni miksi 1.4 kohdassa pingit eivät toimineet.
		Kävi ilmi, että järjestelmän käyttöönoton asennusten aikana oli tapahtunut joitakin virheitä ja päädyin aloittamaan kokonaan puhtaalla asennuksella.	
	

	Miten dokumentaatio auttaa it asiantuntijaa työssään?
	
		Hyvin luotu dokumentaatio selventää ja helpottaa it vastaavaa työssään käyttöönottovaiheessa ja etenkin virhe/ongelma tilanteiden selvittämisessä.	

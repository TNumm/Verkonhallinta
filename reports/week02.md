
    1. Johdanto
        SNMP eli Simple Network Management Protocol, jonka avulla voidaan tutkia laitetietoja järjestelmään kytketyistä laitteista


    2. Miten SNMP asennettiin?

        apt update
        apt install snmp -y

        Yhteyden testaus
        snmpwalk -v2c -c public web1 system

        nano /etc/snmp/snmpd.conf
            agentaddress udp:161   <-- Tässä käytin tekoälyä apuna. Oli ongelmia saada yhteydet toimimaan. Palaan tähän vielä myöhemmässä vaiheessa jos vastaan tulee ohejeita kuinka olisi pitänyt homma saada toimimaan.

            rocommunity public

        snmp mibs lataus & asennus
        
        nano /etc/snmp/snmp.conf
            # mibs :



    3. Kerätyt tiedot
        
    Usean laitteen valvonta / Laitetiedot

    Laite 	        Nimi 	                            Käyttöjärjestelmä 	                                        Uptime
    web1 			STRING: web1                        Linux web1 6.18.33.2-microsoft-standard-WSL2 #1             sysUpTimeInstance = Timeticks: (133443) 0:22:14.43
    db1 			STRING: db1 STRING:                 Linux db1 6.18.33.2-microsoft-standard-WSL2 #1              sysUpTimeInstance = Timeticks: (26710) 0:04:27.10
    branch-client 	STRING: branch-client			    Linux branch-client 6.18.33.2-microsoft-standard-WSL2 #1    sysUpTimeInstance = Timeticks: (7633) 0:01:16.33

    4 Verkkorajapinnat

    Selvitä:

    montako verkkorajapintaa löytyi
        IF-MIB::ifDescr.1 = STRING: lo
        IF-MIB::ifDescr.2 = STRING: eth0
        IF-MIB::ifDescr.29 = STRING: eth1


    mikä rajapinta yhdistää laitteen verkkoon
        IF-MIB::ifDescr.29 = STRING: eth1


    5. OID-analyysi

    OID 	        Tarkoitus
    sysName.0 	    Laitteen hostname
    sysDescr.0 	    Käyttöjärjestelmän kuvaus
    sysUpTime.0 	Kuinka kauan SNMP ollut käynnissä
    ifDescr 	    Näyttää rajapinnat
    ifOperStatus    Rajapinnan toiminta status (Up, down)



    6. Pohdinta

    Mitä hyötyä SNMP:stä on verkonhallinnassa?
        SNMP avulla voidaan hallita ja tutkia järjestelmässä olevia laitteita ilman että jokaiselle laittelle tarvitsee kirjautua erikseen

    Mitä tietoa SNMP:n avulla voidaan kerätä?
        Nopeana esimerkkinä tehtävästä:
        sysName.0, sysDescr.0 	sysUpTime.0 	ifDescr 	ifOperStatus
        Eli järjestelmän nimi, käyttöjärjestelmä, käyttöaika, rajapinnat ja niiden toimintatila
    
    Mitä ongelmia yhteisöpohjaisessa SNMPv2:ssa on?
        Se on suhteellisen salaamaton ja sitä voidaan seurata ulkopuolisten toimesta

    Missä tilanteissa käyttäisit mieluummin SNMPv3:a?
        Yritys / tuotantokäytössä, sillä se on paljon tietoturvallisempi versio
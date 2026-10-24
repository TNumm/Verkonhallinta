Johdanto
    Mikä on Infrastructure as Code?
        Infrastruktuurin hallintaa koodin avulla. Helpotetaan toimintaa jos ympäristö on laaja (esim. +500 laitetta, palvelimia, yms).
        Tämä nopeuttaa muutosten tekoa ja tekee työnteosta tehokkaampaa.

Inventory
    Ympäristön rakenne.

    mitä laitteita inventory sisältää
        # ROUTERS
        # ============================================================
        [routers]
        r1 ansible_host=clab-hamk-verkonhallinta-golden-r1
        r2 ansible_host=clab-hamk-verkonhallinta-golden-r2
        r3 ansible_host=clab-hamk-verkonhallinta-golden-r3

        # CLIENT WORKSTATIONS
        # ============================================================
        [clients]
        client1 ansible_host=clab-hamk-verkonhallinta-golden-client1 data_ip=10.10.10.101
        attacker ansible_host=clab-hamk-verkonhallinta-golden-attacker data_ip=10.10.10.200
        branch-client ansible_host=clab-hamk-verkonhallinta-golden-branch-client data_ip=10.10.30.101

        # SERVERS
        # ============================================================
        [servers]
        web1 ansible_host=clab-hamk-verkonhallinta-golden-web1 data_ip=10.10.20.101 role=webserver
        db1 ansible_host=clab-hamk-verkonhallinta-golden-db1 data_ip=10.10.20.102 role=database

        # MONITORING INFRASTRUCTURE
        # ============================================================
        [monitoring]
        prometheus ansible_host=clab-hamk-verkonhallinta-golden-prometheus service=prometheus port=9090
        grafana ansible_host=clab-hamk-verkonhallinta-golden-grafana service=grafana port=3000
        zabbix ansible_host=clab-hamk-verkonhallinta-golden-zabbix service=zabbix port=8080
        cadvisor ansible_host=clab-hamk-verkonhallinta-golden-cadvisor service=cadvisor port=8080

        # MANAGEMENT TOOLS
        # ============================================================
        [management]
        ansible ansible_host=clab-hamk-verkonhallinta-golden-ansible


    miten ryhmät on muodostettu
        Laite kohtaisesti tehtävän tai käytön perusteella. Reitittimet ovat oma ryhmänsä, clientit/käyttäjät oma ryhmänsä, palvelimet omana etc etc..
        Tällä voidaan rajata komentojen ajo turvallisesti
        
        
    mitä hyötyä ryhmistä on
        Ryhmien avulla voidaan hallita useita laitteita samanaikaisesti, jotta ei tarvitse erikseen jokaista laitetta määrittää erikseen.
        ansible routers -i /ansible/inventory.ini -m ping   <-- Pyysin tekoälyltä yksinkertaisen komennon, jolla pingata kaikki reitittimet kerralla.



SNMP Playbook
Koodi ja tulokset.

Node Exporter Playbook
Koodi ja tulokset.

Vertailu
Käsin vs. automaatio.

Automaatio:Rajatut kohteet kuten inventory, ryhmät, host patternit
Syntax check, check mode ja limit
Recap facts ja palvelun verifiointi
Toistettavuus, skaala ja versionhallinta

Yhteenveto

Opitut asiat.
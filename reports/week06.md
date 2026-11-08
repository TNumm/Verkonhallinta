1. Johdanto
Mikä on Zabbix ja mihin sitä käytetään?
    Zabbix on valvontajärjestelmä jonka avulla voidaan seurata palvelimella tapahtuvaa toimintaa.
    Mitä tapahtuu - Mittarien ja lokien seuranta
    Onko se normaalia - Kynnysarvot ja ehtologiikka joilla määritetään normaalitilanteet
    Mitä siitä seuraa - Ongelma, sen vakavuus ja tapahtumat
    Kenen pitää toimia - Hälytysilmoitukset ja automatisoidut toiminnot

Tehtävä 6.1 – Tutustu Zabbixiin

    Tutki käyttöliittymän seuraavat osat:

        Hosts - Valvottavat laitteet sekä palvelimet.
        Templates - Valmiita templateja valvontamalleista
        Monitoring - Valvottavien laitteiden seuranta
        Dashboards - Voidann lisätä mittareita ja hälytyksiä
        Alerts - Hälytysten hallinta
        Reports - Voidaan tarkastella hälytyksia sekä valvonnan tuloksia pidemmältä aikaväliltä


2. Hostien lisääminen
        web1, db1 ja branch-client hostit lisätty

        images/Hostit

3. Dashboard
images/GoldenTopology.png

4. Mittarit
    Valitse vähintään viisi mittaria.

        mittarin nimi       arvo        mitä mittari kertoo                          miksi sitä kannattaa seurata

        CPU utilizaton 	    0.7631%    Kertoo prossun käyttö %                       Nähdään jos prosessori on ylikuormittunut                
        Memory utlilization 29.813%    Muistin käyttö %                              Nähdään muistin käyttö                                                
        Load Average 1m avg 0.2002     Järjestelmän keski kuormitusta 1min aikana    Nähdään jos on ollut jotain hetkellisiä rasituspiikkejä        
        SystemUptime        01:51:50   Nähdään järjestelmän käyttöaika               Nähdään koska järjestelmä on restartannut                      
        Disk utilization    0%         Levyn kuormitus                               Nähdään levyn käytön kuormitus                          
        Available memory    10.9GB      Muistin määrä                                Nähdään muistin määrä, jos se on käymässä vähiin                                        




5. Triggerit
Images/Triggers

    Cpu Over 80% {web1:system.cpu.util.last()}>80
    Disc space < 20% {web1:vfs.fs.size[/configs,pused].last()}>80

    
6. Hälytys- ja häiriötestit
Suoritetut testit ja tulokset.

7. Vertailu
    Käytin tässä hieman tekoälyä apuna muistin virkistämiseksi

    Ominaisuus 	                SNMP 	                                Prometheus  	                                Zabbix
    Tiedonkeruu 			    SNMP Kyselyt                            HTTP rajapinta                                 Agentit
    Dashboardit 			    -                                       Grafana                                        Oma sisäänrakennettu dashboard
    Hälytykset 		            Tukee trap                              Hälytyssäännöt	ja alert management             Sisäänrakennettu hälytysjärjestelmä
    Käyttöönotto 			    Helppo, vaatii valvonta ohjelman        Vaatii palvelimen ja mittarien määrittämisen   Vaatii palvelimen ja valvottavien laitteden määrittämisen
    Skaalautuvuus 			    Heikko                                  Hyvä, soveltuu suuriin ympäristöihin           Hyvä, soveltuu suuriin ympäristöihin
    Yrityskäyttö 			    Yleinen verkkolaitteiden valvonnassa    Soveltuu hyvin pilvi ja konttiympäristöihin     Soveltuu keskitettyyn palvelin ja verkkovalvontaan

8. Yhteenveto
Kurssin tärkeimmät opit.
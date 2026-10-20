1. Johdanto
Prometheus on verkon, sekä palvelimien valvontajärjestelmä, jolla voidaan kerätä haluttua dataa laitteista ja palveluista

2. Node Exporterin käyttöönotto
Kirjaudu web1- koneelle
docker exec -it clab-hamk-verkonhallinta-golden-web1 bash

Asenna tarvittavat työkalut
apt update
apt install wget tar -y

Selvitä uusin saatavilla oleva versio
curl -s https://api.github.com/repos/prometheus/node_exporter/releases/latest | grep tag_name  <-- Tekoälyltä lainattu tieto

Lataa se
wget https://github.com/prometheus/node_exporter/releases/latest/download/node_exporter-<1.12.1>.linux-amd64.tar.gz

Pura paketti
tar xvf node_exporter-*.linux-amd64.tar.gz

Siirry hakemistoon
cd node_exporter-*/

Käynnistä Node Exporter
./node_exporter

Testaus
Avasin uuden web1 terminaalin ja ajoin:
curl http://localhost:9100/metrics

images/ExporterWorking.png


3. Prometheus

Yhteydet toimivat
images/PrometheusAPIconnectionOk.png

4. Dashboard

Kuva dashboard kokonaisuudesta:
images/Dashboard.png

5. Kuormitustesti

Miten CPU-käyrä muuttui?
Pomppasi 0.5% käytöltä noin 4.9% käytölle
images/CPUusage.png

Miten levytilan käyttö muuttui?
6.165% --> ~6.213%
images/DiskUsage.png

Näkyikö verkkoliikenteessä muutoksia?
Kyllä siellä näkyi selvää muutosta. Näkyi myös kuinka liikenne loppui. Ilmeisesti tulee muutaman minuutin viiveellä, saattaa olla metrics arvo 5min johtuva?

images/NetworkReceiveChanges.png
images/NetworkTransmitChanges.png  <-- Liikenne lakkaa

6. SNMP vs Prometheus
Tässä käytin hieman tekoälyä tiedonlähteenä


Ominaisuus                           SNMP                                                Prometheus
Tiedonkeruu                         Kyselyt (polling) ja trap-ilmoitukset               Pull-menetelmä, hakee mittaustiedot HTTP:n kautta
Käyttöönotto                        Melko helppo, jos laite tukee SNMP:tä               Vaatii Prometheuksen ja usein Exporterin asentamisen
Mittarienmäärä                      Riippuu laitteen tukemista MIB-tiedoista            Erittäin laaja, riippuu Exporterista ja sovelluksesta
Visualisointi                       Vaatii yleensä erillisen ohjelmiston                Prometheuksen oma käyttöliittymä, Grafana
Hälytysmahdollisuudet               Trap-ilmoitukset ja erilliset valvontajärjestelmät  Hälytyssäännöt ja Alertmanager
Soveltuvuus pilviympäristöihin      Soveltuu, mutta ei erityisesti suunniteltu niihin   Erittäin hyvä, tukee dynaamisia ympäristöjä ja kontteja


7. Yhteenveto

Mitä hyötyä Prometheuksesta on verrattuna SNMP:hen?
Prometheus on käyttöasteeltaan ja muokattavuudeltaan paljon laajempi kuin SNMP

Millaisia mittareita ylläpitäjän kannattaa seurata jatkuvasti?
CPU usage
Disk usage
Network data (usage, errors)
Verkon vasteajat


Mitä tietoa dashboardisi tarjoaa ylläpitäjälle?
Lähinnä mitä tahansa tietoä käyttäjä on asettanut seurattavaksi. Todella käytännöllinen tapa visualisoida kerättävää dataa

Mitä uusia mittareita lisäisit dashboardiin?
Vasteajat tulee ensimmäisenä mieleen.

Miten monitorointitiedosta voisi olla hyötyä vianetsinnässä?
Voidaan päätellä syytä mistä johtuu jokin vika / useampi vika. Ilman minkäänlaista tietoa on vaikea lähteä selvittämään syytä / syiden toistuvuutta


Mitä opit harjoituksesta?
Opin asentamaa Node Exporterin, sekä keräämään mittaustietoja Prometheuksella sekä visualisoimaan niitä Grafanalla.
Grafanan dashboardin luonti, sekä omien mittareiden asentaminen.

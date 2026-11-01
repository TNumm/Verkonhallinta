Tässä oli vaihteeksi sekavat ohjeistukset. Vertaisohjeistuksen avulla tein nyt Moodlen puolen tehtävänannon mukaisesti.


Osa 1: Kaappaa liikennettä Wireshark ohjelmalla paikalliselta työasemalta. Surfaa työaseman selaimella https://www.yle.fi verkkosivulle. Suodata liikennettä sekä capture filtterillä tai display filttereillä. Pyri selvittämään palvelun tuottavan webbi palvelimen GeoIP sijainti.  Ylen sivut sijaitsevat pilvipalvelussa, joten jokainen kokeilemasi kerta, voi saada erilaisen vastauksen. Kokeile kumpaakin tekniikkaa ja kirjaa käyttämäsi komennot palautettavaan kirjalliseen tuotokseen. Tämä tehtävä liittyy ylläolevan videosarjan viimeiseen videoon. 

/images/YleGeoIP.png
Palvelin: Amazon.com
Sijainti: USA
[Source or Destination GeoIP Latitude: 37.751]
[Source or Destination GeoIP Longitude: -97.822]


Osa 2: Kaappaa liikennettä ja mene osoitteeseen learn.hamk.fi. Lopeta kaappaus onnistuneen kirjautumisen jälkeen.

Tässä tehtävässä käytin tekoälyä apuna, oli ongelmia saada oikeita tietoja kalastettua, ja en vieläkään ole ihan varma kalastinko oikeita tietoja.
Suodattimena käytin: dns.flags.response == 1 && dns.qry.name == "learn.hamk.fi"

1) Mitkä olivat learn.hamk.fi DNS-kyselyn vastauksen IP osoite ja authoratiiviset DNS palvelimet? Paljon on DNS kyselyn TTL aika? Termistöön liittyvä artikkeli

/images/HamkData.png
DNS-kyselyn vastauksen IP osoite: NS learn.hamk.fi
authoratiiviset DNS palvelimet: ns1.hamk.fi
Paljon on DNS kyselyn TTL aika?: 3600 sekuntia


2) Salattu yhteys muodostetaan TLS kättelyllä. Mitä TLS protokollaa käytetään learn.hamk.fi yhteydessä? Mitä salausalgoritmeja selain ehdottaa käytettäväksi (Client Hello) ja minkä palvelin valitsee käytettäväksi salausalgoritmiksi (Server Hello)? Vihje: Wireshark DisplayFilter: ssl.handshake.ciphersuite

display filter: ssl.handshake.ciphersuite

Mitä TLS protokollaa käytetään learn.hamk.fi yhteydessä?: Extension: supported_versions (len=2) TLS 1.3
Mitä salausalgoritmeja selain ehdottaa käytettäväksi (Client Hello)
Nämä on tekoälyn poimimat tiedot:
TLS_AES_128_GCM_SHA256
TLS_CHACHA20_POLY1305_SHA256
TLS_AES_256_GCM_SHA384
TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
TLS_ECDHE_ECDSA_WITH_CHACHA20_POLY1305_SHA256
TLS_ECDHE_RSA_WITH_CHACHA20_POLY1305_SHA256
TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA
TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA
TLS_RSA_WITH_AES_128_GCM_SHA256
TLS_RSA_WITH_AES_256_GCM_SHA384
TLS_RSA_WITH_AES_128_CBC_SHA
TLS_RSA_WITH_AES_256_CBC_SHA


minkä palvelin valitsee käytettäväksi salausalgoritmiksi (Server Hello)?
Cipher Suite: TLS_AES_128_GCM_SHA256 (0x1301)


Osa 3: Aja seuraava image uudeksi kontiksi

Käytin porttia 81, en saanut 80 toimimaan...

filteri: http.authorization
Hypertext Tranasfer Protocol, Authentizator, Credentials: kayttaja:salasana




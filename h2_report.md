Tämä dokumentti on kirjoitettu vastauksena [Tero Karvisen Verkkoon tunkeutuminen ja tiedustelu -kurssin](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/) kotitehtävään _h2 Nuuh_.

### x) [Karvinen 2025: Wireshark - Getting Started](https://terokarvinen.com/wireshark-getting-started/) / [Karvinen 2025: Network Interface Names on Linux](https://terokarvinen.com/network-interface-linux/)   

- Wireshark on verkkoliikenteen sieppaamiseen ja analysointiin käytettävä työkalu
- liikennettä kannattaa tutkia hallittavia määriä kerrallaan
- tilastotiedoista näkee yhteenvetoja mm. kestosta, protokollista ja ip-osoitteista
- monipuolisesti suodatusmahdollisuuksia
- verkkorajapinnat voivat olla fyysisiä tai virtuaalisia
- etuliite määrittää tyypin:
   - en = wired ethernet
   - wl = wlan
   - lo = loopback adapter
- rajapinnat voi tarkastaa komennoilla `ip a` tai `ip route`

### a) Käytetty ympäristö

Suoritettujen harjoitusten alustana toimi Oraclen VirtualBoxiin asennettu Kali-käyttöjärjestelmä. Virtuaalikoneen laitteistoresursseiksi oli asetettuna kaksi prosessoriydintä ja 4 Gt keskusmuistia.

### b) Virtuaalikoneen Internet-yhteyden katkaisu

Lähtötilanteessa virtuaalikone oli yhdistettynä verkkoon VirtualBoxin eth0-rajapinnan kautta osoitteenmuunnoksen eli NAT:n välityksellä. Yhteyden toimivuus varmistettiin pingaamalla Cloudfaren DNS-palvelinta komennolla `ping -c 4 1.1.1.1`.

Verkkorajapinta otettiin pois käytöstä komennolla `sudo ip link set eth0 down`, minkä jälkeen ping-komentoa uudelleen kokeilemalla voitiin varmistua yhteyden katkenneen. Yhteys palautettiin kytkemällä rajapinta takaisin päälle komennolla `sudo ip link set eth0 up`, minkä jälkeen verkkoyhteys toimi jälleen normaalisti.

<img width="968" height="668" alt="Screenshot 2026-11-01 202139" src="https://github.com/user-attachments/assets/e2c2a041-f889-459f-85f9-b6c3c2319bb8" />

### c) Wireshark 

Käytössä olleessa Kali-jakelussa verkkoliikenteen sieppaamiseen käytettävä Wireshark-työkalu oli valmiiksi asennettuna. 

Ohjelman käynnistyksen yhteydessä valittiin siepattavaksi verkkokortin rajapinta `eth0`. Liikenteen generoimiseksi selaimella avattiin osoite wikipedia.org, jotta Wiresharkin näkymään saatiin suurempi määrä verkkopaketteja tutkittavaksi. 

### d) TCP/IP-pino ja paketit 

Wiresharkin käyttöliittymän alaosassa näkyy valittuna olevan paketin tarkempi rakenne kerroksittain. Tässä yhteydessä valittiin tarkasteluun DNS-protokollaa hyödyntänyt vastauspaketti, jonka tarkemmissa tiedoissa voitiin havaita TCP/IP-mallin kaikki neljä kerrosta. Oheisessa kuvassa on kuvattu TCP/IP-kerrosten suhde Wiresharkissa näkyviin kerroksiin.  

<img width="2081" height="922" alt="tcpip_d" src="https://github.com/user-attachments/assets/1b101272-7ce3-4c4f-8b2c-6b04c9c41014" />  

Kuvan TCP/IP-pinon lähde: Wikimedia Commons (Public Domain).  

Ylimpänä Wiresharkin näkymässä näkyy _Ethernet II_ eli linkkikerros, joka sisältää MAC-osoitteet. Seuraavana on verkkokerros, joka vastaa IP-osoitteista. Tämän jälkeen kuljetuskerroksen kohdalla on mainittu käytetty tiedonsiirtoprotokolla UDP (User Datagram Protocol) ja alimpana paketin tiedoissa on sovelluskerros ja sillä toimiva nimenselvennysprotokolla eli DNS (Domain Name System).

### e) Tallennetun verkkoliikenteen kuvaileminen

Seuraavaksi tarkasteltiin tehtävänannossa annettua tiedostoa `surfing-secure.pcap`, mikä sisälsi aiemmin tallennettua verkkoliikennettä. Wiresharkin tilastoista (yläpalkista `Statistics -> Capture File Properties`) oli nähtävillä, että paketteja oli tarkasteltavana yhteensä 283 noin 7,5 sekunnin ajalta ja dataa oli liikkunut 122 445 tavun verran. 

<img width="898" height="268" alt="Screenshot 2026-11-02 032122" src="https://github.com/user-attachments/assets/b12e0f57-a06a-4c69-a799-6ac8d02b9962" />
  
Kyseisen kaappauksen alussa havaittiin lähdekoneelta (`192.168.122.7`) lähteviä nimenselvennyksiä eli DNS-pyyntöjä, joiden perusteella koneella vierailtiin aluksi hakukone Googlen sivulla ja myöhemmin sivulla `terokarvinen.com`. Joukossa oli myös muun muassa ARP-kysely, missä `192.168.122.1` haki osoitteen `192.168.122.7` omistajaa, johon laite vastasi omalla MAC-osoitteellaan. 

Pääosa liikenteestä oli TLS-protokollan salaamaa liikennettä, joka hyödynsi TCP-protokollaa. 

### g) Verkkokortin selvittäminen

Wiresharkissa otsakkeen _Ethernet II_ alta on mahdollista nähdä paketin osalta lähde- ja kohde-MAC-osoite. Tässä tapauksessa lähtevän kortin MAC-osoite oli `52:54:00:2f:e1:e5`, josta ensimmäiset kolme oktettia (`52:54:00`) muodostavat valmistajan _Organizational Unique Identifier_ eli OUI-tunnuksen (GeeksforGeeks 2024). Kokeilin hakea kyseisen tunnuksen perusteella valmistajaa, mutta en syystä tai toisesta saanut omassa ympäristössäni suoraan samanlaista vastausta kuin mitä [tehtäväpaketin vihjeissä linkitetyn toisen opiskelijan raportissa](https://github.com/gianglex/Courses/blob/6e74794c2e27752698f9bff0455a927843d9f934/Verkkoon-Tunkeutuminen-ja-Tiedustelu/h1-sniff.md#g-mink%C3%A4-merkkinen-verkkokortti-k%C3%A4ytt%C3%A4j%C3%A4ll%C3%A4-on), jossa verkkokortiksi nimettiin `RealTekU`, vaikka harjoituksessa tutkittava tiedosto vaikutti molemmissa tapauksissa samalta. Oletettavasti kuitenkin tunnus osoittaa virtuaalikoneen omaan verkkokorttiin. 

<img width="1244" height="346" alt="Screenshot 2026-11-02 043552" src="https://github.com/user-attachments/assets/222ab2d6-83b3-4331-b446-b98e8bdfebc7" />

### h) Selatut web-palvelimet

TLS-salauksella suojatun yhteyden tiedoista ei voi suoraan päätellä käytettävää verkkopalvelinta, mutta suodattamalla näkymään vain suojaamattomana liikkuvat DNS-nimikyselypaketit oli vierailtuja palvelimia kuitenkin todennettavissa. Hakuparametrillä `dns.flags.response == 0` saatiin Wireshark tuomaan esille pelkät lähtevät nimikyselyt (LabEx s.a.). Istunnon aikana oli otettu yhteys aiemmin mainituille `google.com` ja `terokarvinen.com` -sivustoille, mutta tuloksissa näkyy lisäksi analytiikkasivustoon `goatcounter.com` viittaavia tuloksia (EuroToolKit s.a.).

<img width="1338" height="408" alt="Screenshot 2026-11-02 035337" src="https://github.com/user-attachments/assets/f2c939db-48d5-40e5-8531-24e8a8e1d205" />

### i) Verkkoliikenteen analysoiminen

Viimeiseksi tehtiin nimikysely Googlen DNS-palvelimelle komennolla `nslookup lentoposti.fi 8.8.8.8`.  

<img width="1512" height="1180" alt="Screenshot 2026-11-02 040420" src="https://github.com/user-attachments/assets/c259cb28-c8c5-4f11-a335-24893ddaf258" />  

Lähemmässä tarkastelussa on yhteensä neljä pakettia, joista ensimmäiset kaksi liittyvät IPv4-osoitteen selvittämiseen ja kaksi seuraavaa IPv6-osoitteen selvittämiseen. Parien ensimmäiset paketit ovat lähteviä kyselyitä ja jälkimmäiset nimipalvelimen lähettämiä vastauksia. Wiresharkin avulla selviää mm. seuraavat tiedot:

Virtuaalikoneen IP-osoite `10.0.2.15`    
Googlen nimipalvelimen IP-osoite `8.8.8.8`    
Tiedonsiirtoprotokolla `UDP` ja käytetyt portit (`53` nimipalvelimella ja `42784/44815` virtuaalikoneella)

lentoposti.fi-sivuston osoitteet:

IPv4-osoite `185.220.77.3` (A-tietue)  
IPv6-osoite `2a0b:f240:0:1::3` (AAAA-tietue)  

MAC-osoitteet:

`08:00:27:5a:87:bc` (virtuaalikoneen verkkokortin osoite)  
`52:55:0a:00:02:02` (VirtualBoxin yhdyskäytävän MAC-osoite)  
`Time to live: 3600` (tietuetta pidetään DNS-välimuistissa tunnin ajan)  

### Lähteet

EuroToolKit s.a. GoatCounter. Luettavissa: https://www.eurotoolkit.eu/goatcounter. Luettu: 1.11.2026. 

GeeksForGeeks 2024. What is MAC Address? Luettavissa: https://www.geeksforgeeks.org/computer-networks/mac-address-in-computer-network/. Luettu: 30.10.2026.

Giang 2025. H1 Sniff. Luettavissa: https://github.com/gianglex/Courses/blob/6e74794c2e27752698f9bff0455a927843d9f934/Verkkoon-Tunkeutuminen-ja-Tiedustelu/h1-sniff.md. Luettu: 1.11.2026.

Karvinen, Tero 2026. Network Interface Names on Linux. Luettavissa: https://terokarvinen.com/network-interface-linux/. Luettu: 29.10.2026. 

Karvinen, Tero 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/. Luettu: 29.10.2026. 

Karvinen, Tero 2026. Wireshark - Getting Started. Luettavissa: https://terokarvinen.com/wireshark-getting-started/. Luettu: 29.10.2026. 

LabEx Technology s.a. Troubleshoot DNS Queries in Tshark. Luettavissa: https://labex.io/tutorials/wireshark-troubleshoot-dns-queries-in-tshark-548938. Luettu: 1.11.2026. 

Wikimedia Commons 2009. InternetProtocolStack.png. Luettavissa: https://commons.wikimedia.org/wiki/File:InternetProtocolStack.png. Luettu: 1.11.2026.

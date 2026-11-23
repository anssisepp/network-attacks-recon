#### Palautus on keskeneräinen ja valmistuu b) ja c) kohtien osalta myöhässä. 

Tämä dokumentti on kirjoitettu vastauksena Lari Iso-Anttilan ja Tero Karvisen järjestämän [Verkkoon tunkeutuminen ja tiedustelu -kurssin](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/) kotitehtävään _h5 Labratakki_. Suoritettujen harjoitusten alustana toimi _VMware Workstation Pro_ -virtualisointiohjelmistoon asennettu Kali-virtuaalikone ja tehtävänannossa annettu Mininet-verkkoemulaattori. 

### a) ARP-hyökkäys Mininet-ympäristössä

Ensimmäiseksi tuli selvittää Mininet-koneen IP-osoite, sillä harjoituksia tultaisiin suorittamaan SSH-yhteydellä Kalista Mininet-ympäristöön. Molemmat virtuaalikoneet oli asetettu VMwaren verkkoadapteriasetusten osalta host-only -tilaan eli suljetuiksi internetistä ja kotiverkosta. Lähtötilanteessa Mininet-koneen `eth1`-verkkoliitäntä ei ollut päällä, joten alkumäärityksinä se käynnistettiin ja sille haettiin IP-osoite:

`sudo ip link set eth1 up`  
`sudo dhclient eth1` 

Tämän jälkeen tarkistettiin `ip a`-komennolla IP-osoite ja muodostettiin SSH-yhteys Kali-virtuaalikoneelta Mininet-koneelle komennolla `ssh -X mininet@192.168.79.128`. Tässä määre `-X` tarkoittaa X11-edelleenlähetystä (_X11 forwarding_), mikä mahdollistaa graafisten sovellusten, kuten Wiresharkin tai xterm-pääteikkunoiden, näyttämisen Kalin ruudulla etäyhteyden yli (Hakatemia s.a.).

Ensimmäisessä päätteessä käynnistettiin tehtävänannon ohjeiden mukaisesti verkko-ohjain komennolla `ryu-manager ryu.app.simple_switch_13`, ja toisessa päätteessä käynnistettiin kolmen isäntäkoneen (`h1`, `h2`, `h3`) ja yhden kytkimen Mininet-ympäristö komennolla `sudo mn --topo single,3 --mac --switch ovsk --controller remote`. Viimeisen määreen myötä hyödynnetään aiemmalla komennolla käynnistettyä ulkoista verkko-ohjainta oletusohjaimen sijaan. Yhteyksien toimivuus varmistettiin Mininet-kehotteessa komennolla `pingall`.

Xterm-ohjelman saamisessa toimintaan oli haasteita (_X11 connection rejected because of wrong authentication_), sillä tehtävänannossa annettu vinkki ei sellaisenaan auttanut. Asia ratkesi lopulla korvaamalla `mininet-vm/unix:10` -parametri tekstillä `localhost:11` koko komennon ollessa `sudo -s xauth add localhost:11 MIT-MAGIC-COOKIE-1 [avain]`. 

Ennen hyökkäyksen toteuttamista avattiin pääteikkuna komennolla `xterm h1` ja tarkistettiin oikeat IP- ja MAC-osoitteet ARP-taulusta komennolla `arp -n`. ARP-taulua tarkastellaan hyökkäyksen yhteydessä uudelleen. 

<img width="668" height="104" alt="Screenshot 2026-11-23 053256" src="https://github.com/user-attachments/assets/c6a3de9a-e93e-4ae6-893d-a13b96261a32" />

Harjoitusympäristössä hyökkääjä `h3` pyrki `h1` ja `h2`-koneiden SSH-liikenteen väliin ARP-myrkytykseen perustuvalla MitM-hyökkäyksellä. ARP eli _Address Resolution Protocol_ yhdistää lähiverkossa laitteiden IP-osoitteet ja fyysiset MAC-osoitteet toisiinsa. Koneet lähettävät ARP-pyyntöjä selvittääkseen kohdekoneen MAC-osoitteen ja tallentavat saamansa vastauksen ARP-välimuistiinsa käyttäen sitä liikenteen ohjaamiseen. ARP-myrkytyksessä hyökkääjä lähettää verkossa väärennettyjä viestejä saadakseen kohteen tallentamaan hyökkääjän MAC-osoitteen toisen laitteen IP-osoitteelle, jolloin liikenne kulkee hyökkääjän kautta (Crowdstrike 2022). 

`h3`-koneella suoritettiin hyökkäys ja liikenteen kaappaus seuraavilla komennoilla:
* `sysctl -w net.ipv4.ip_forward=1`
  * ottaa IPv4-edelleenlähetyksen käyttöön, jotta liikennettä voidaan välittää pudottamatta paketteja 
* `iptables -t nat -A PREROUTING -p tcp --dport 22 -j REDIRECT --to-port 22`
  * ohjaa portin 22 TCP-liikenteen `sshesame`-ansaan   
* `tcpdump -i h3-eth0 -w arp-p.pcap &`
  * pakettiliikenteen tallentaminen tiedostoon  
* `wireshark &`
  * liikenteen tarkastelu graafisessa käyttöliittymässä
* `sshesame -port 22 -listen_address 0.0.0.0`
  * SSH-ansapalvelimen käynnistäminen, kuuntelee kaikkia IP-osoitteita 
* Viimeisenä erillisessä `h3`-ikkunassa käynnistettiin ARP-myrkytyskripti `python3 ./arp_poison.py`.

Komentojen parametrien avaamisen tukena hyödynnettiin Google Gemini -kielimallia. Versio ei todennettavissa (_Auto_). 

ARP-myrkytyksen ollessa käynnissä `h1`-koneen `arp -n` -tulosteesta voitiin havaita, että IP-osoite `10.0.0.2` osoitti nyt poikkeuksellisesti MAC-osoitteeseen `00:00:00:00:00:03` eli hyökkääjä `h3`:n MAC-osoitteeseen. `h1`:n ARP-välimuisti oli siis vääristynyt ja alun perin kohteeseen `h2` osoitettu liikenne ohjautui `h3`:n kautta.

<img width="662" height="106" alt="Screenshot 2026-11-23 062044" src="https://github.com/user-attachments/assets/86b3fd1f-d59e-42d0-9e76-a71a6489edc4" />

Kun `h1`-koneelta kokeiltiin SSH-yhteyttä komennolla `ssh h2`, tuli ruudulle huomautus muuttuneesta isäntäavaimesta (_Host key verification failed_), sillä yhteys ohjautui nyt `sshesame`-ansapalvelimeen. Vanha avainmerkintä poistettiin komennolla `ssh-keygen -R h2`, jonka jälkeen `ssh h2` ajettiin uudelleen ja yhteys muodostettiin hyväksymällä avain ja syöttämällä salasana `salasana`. Tämän jälkeen `sshesame`-lokissa näkyi yhteys asiakkaalta `h1` (`10.0.0.1`), hyväksytty salasanatodennus (`user=root`, `password=salasana`) sekä muut session muodostamiseen liittyneet pyynnöt ja tilat.  

<img width="1080" height="412" alt="Screenshot 2026-11-23 072044" src="https://github.com/user-attachments/assets/ff1f717a-7292-4b18-a39d-c823b9256088" />

Wiresharkin näkymästä voitiin helposti havaita, kuinka skripti alkuun selvitti IP-osoitteiden `10.0.0.1` ja `10.0.0.2` MAC-osoitteet, minkä jälkeen se alkoi lähettää verkkoon jatkuvasti väärennettyjä ARP-vastauksia, minkä myötä kohdekoneiden ARP-taulut vääristyivät ja liikenne saatiin ohjattua `h3`:n kautta.

<img width="1222" height="346" alt="Screenshot 2026-11-23 074009" src="https://github.com/user-attachments/assets/bc64e22c-e42e-4aac-ae58-0a0885be85dd" />


### b) ICMP Spoofing ja TCP Session Hijacking

Ensimmäiseksi käynnistettiin Mininetin `/home/mininet/lab/01-Network-Security-Lab/scripts` hakemistossa valmiina ollut verkkotopologia `python hub_topo.py` ja avattiin `h1`-koneelle ikkuna, missä ajettiin verkon ICMP-viestintää kuunteleva `sniff_icmp.py`-skripti. Kuvitteellisen pahantahtoisen `h3`:n ikkunassa suoritettiin väärennettyjä viestejä lähettävä `spoof_icmp.py`-skripti. Kun `h2`-koneelta lähetettiin ping-viestejä `h1`-koneen IP-osoitteeseen (`10.0.0.1`), terminaali-ikkunoista oli nähtävissä miten jokaista `h1`:n lähettämää aitoa vastausta kohti `h3` lähetti yhden väärennetyn vastausviestin, jossa kone esitti omistavansa `h1`-koneen IP-osoitteen. Harjoituksen pohjana toimivan topologian nimestä voi päätellä, että se pohjautuu hubiin. Verkossa hubit lähettävät dataa eteenpäin kaikille muille porteille paitsi sieltä, mistä se saapui, minkä vuoksi myös `h3` näkee liikenteen ja pääsee väliin (SecureW2 2026).

<img width="2380" height="644" alt="Screenshot 2026-11-23 102815" src="https://github.com/user-attachments/assets/bfdb2032-25d4-4011-b44e-51bc96ccb2be" />



### c) TCP SYN-Flood-hyökkäys


#### Lähteet

Crowdstrike 2022. Address Resolution Protocol (ARP) Spoofing: What It Is and How to Prevent an ARP Attack. Luettavissa: https://www.crowdstrike.com/en-us/cybersecurity-101/social-engineering/arp-spoofing/

Hakatemia s.a. X11 Forwarding and SSH tunneling (port forwarding). Luettavissa: https://www.hakatemia.fi/en/courses/linux-management-and-hardening/x11-forwarding-and-ssh-tunneling-port-forwarding

Iso-Anttila, Lari & Karvinen, Tero. 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Mininet Project Contributors s.a. Mininet Walkthrough. Luettavissa: https://mininet.org/walkthrough/

SecureW2 2026. What Is a Network Hub? Uses, Risks, and Better Solutions. Luettavissa: https://securew2.com/blog/what-is-a-network-hub-uses-risks-and-better-solutions







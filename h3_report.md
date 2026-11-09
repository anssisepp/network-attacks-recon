Tämä dokumentti on kirjoitettu vastauksena [Tero Karvisen Verkkoon tunkeutuminen ja tiedustelu -kurssin](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#tehtavanannot) kotitehtävään _h3 Aaltoja harjaamassa_. Suoritettujen harjoitusten alustana toimi Oracle VirtualBoxiin asennettu Kali-käyttöjärjestelmä. Virtuaalikoneen laitteistoresursseiksi oli asetettuna kaksi prosessoriydintä ja 4 Gt keskusmuistia.

### x)

#### [Hubacek 2019: Universal Radio Hacker SDR Tutorial on 433 MHz radio plugs](https://www.youtube.com/watch?v=sbqMqb6FVMY&t=199s)
- URH-sovelluksen käytöstä näihin tehtäviin relevantein osin kohdassa 3:19-7:40
- radioliikenteen sieppaaminen vaatii omat laitteensa
- modulaation valinta tulee huomioida 
- virhesiedon saaminen oikeaan arvoon
- testaamalla selviää eteenpäin

#### [Cornelius 2022: Decode 433.92 MHz weather station data](https://www.onetransistor.eu/2022/01/decode-433mhz-ask-signal.html)
- käytännön esimerkki rtl_433 käytöstä sääaseman kanssa
- aina harrastajatkaan eivät tiedä miksi asiat onnistuvat tai epäonnistuvat
- ASK-modulaatio yleinen ja laajasti yhteensopiva standardi

### b) rtl_433 asentaminen

Ensimmäisessä tehtävässä asennettiin `rtl_433`-ohjelmisto, jota ennen päivitettiin pakettilistat:

```
sudo apt-get update
sudo apt-get install rtl-433
```
Asennuksen läpimeno onnistuneesti todennettiin avaamalla tehtäväsivulta ladattu ja seuraavassa tehtävässä tutkittava harjoitustiedosto: `rtl_433 -r Converted_433.92M_2000k.cs8`

- `-r` -parametriä käytetään kun käsittelyyn otetaan tiedosto reaaliaikaisen laitevastaanottimen sijaan

<img width="1416" height="828" alt="Screenshot 2026-11-09 082013" src="https://github.com/user-attachments/assets/abfea7af-f70b-4e1c-8435-0f29baf1ed86" />
  
Ennen harjoitustiedostojen lataamista mitä ilmeisimmin pakettilistojen päivityksen yhteydessä suoritettu järjestelmäpäivitys aiheutti tilapäisen häiriön nimenselvennyksessä mikä johti toimimattomaan verkkoon. Vaikka Googlen DNS-palvelimen pingaus IP-osoitteella 8.8.8.8 onnistui ongelmitta, nimipohjainen komento `ping google.com` tuotti vikailmoituksen _"Temporary failure in name resolution"_. Tämä ongelmatilanne saatiin ratkaistua käynnistämällä virtuaalikone uudelleen.

### c) Näytteen Converted_433.92M_2000k.cs8 analyysi

Tiedostonimen perusteella voitiin todeta, että tarkasteltavaksi avautui 433 MHz taajuudella lähetettyä radioliikennettä. Tutkiessani asiaa `rtl_433`-ohjelman avulla kävi ilmi, että radioliikennettä toistui tiedostossa neljällä eri aikaleimalla. Oletettavasti siis samat komennot olivat lähetetty peräkkäin useamman kerran perillemenon varmistamiseksi. Kaikissa toistoissa esiintyi yhtenäinen tunniste eli `id` tai `House Code 8785315`. Radiosignaalin lähettäneiksi laitemalleiksi tunnistettiin kolme eri vaihtoehtoa eli `KlikaAnkKlikUit-Switch`, `Proove-Security` sekä `Nexa-Security`. Lokitiedoista kävi selväksi signaalin sisältämä komento, sillä jokaisella toistokerralla tilaksi oli määritetty `Off`. Kanava- ja yksikkötiedot (`channel`, `unit`) todennäköisesti määrittivät tarkemmin kohdelaitteen. Laitteelle ei myöskään annettu himmennyskomentoja (`Dim: No`, `Dim Value: 0`), minkä perusteella kyseessä oli todennäköisesti pelkkä laitteeseen kohdistettu sammutuskäsky.    

### d) Recorded-HackRF-20250411_183354-433_92MHz-2MSps-2MHz.complex16s

Toinen annettu tiedosto avautui `rtl_433`:n avulla sellaisenaan. Tehtävän muotoilun perusteella olettamus oli, että näin ei tapahtuisi. Lähdin sen vuoksi kokeilemaan tiedostonimen käytännön vaikutusta muuttamalla nimen muotoon `Recorded_test.cs8`. Tämän jälkeen `rtl_433` ei enää kyennyt avaamaan tiedostoa. Tiedostonimi muutettiin lopulta [Teron vinkeissä](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/#tehtavanannot) mainitun syntaksin mukaiseen lyhyempään taajuuden ja näytteenottotaajuuden sisältävään muotoon `Recorded-HackRF_433.92M_2000k.cs8`, minkä jälkeen tiedoston avaaminen jälleen onnistui.

Komentorivillä avautunutta sisältöä hetki tarkkaillessa kävi ilmi, että kyseessä vaikutti olevan täysin samaa dataa kuin aiemmassa tehtävässä olleessa tiedostossa. 

<img width="1404" height="628" alt="Screenshot 2026-11-09 032900" src="https://github.com/user-attachments/assets/e1f2a1b3-6922-48a3-8b83-16cc79509ecb" />

### e) Ultimate Radio Hacker

Seuraavaksi asennettiin URH-ohjelma (Ultimate Radio Hacker) tulevia tutkimuksia varten tehtäväsivun vinkeissä annettujen komentojen kautta:

```
sudo apt-get update
sudo apt-get -y install pipx
pipx install urh
pipx ensurepath 
```

Tätä viimeistä ei varsinaisesti komentotulkin ilmoituksen mukaan olisi omassa ympäristössä ollut tarpeellista tehdä, sillä haluttu hakemisto (```/home/kali/.local/bin```) oli jo valmiiksi järjestelmän hakupolussa.

Tämän jälkeen ohjelman graafinen käyttöliittymä avattiin komennolla `urh`. 

### f) Pistorasian kaukosäätimen tallennetiedosto 1-on-on-on-HackRF-20250412_113805-433_912MHz-2MSps-2MHz.complex16s

URH-ohjelmassa signaaleja kuvaavalla aikajanalla näkyi selvästi kolme erillistä ja samanlaista signaalipakettia, jotka vastasivat tehtävänannossa kuvattuja kaukosäätimen ON-napin kolmea peräkkäistä painallusta. Tiedoston nauhoitusajankohta 12. huhtikuuta 2025 klo 11:38:05 oli pääteltävissä tiedostonimen perusteella, samoin keskeisenä taajuus 433,912 MHz.

<img width="1884" height="1034" alt="Screenshot 2026-11-09 063922" src="https://github.com/user-attachments/assets/a1ebd757-3396-499d-b7ae-46414f8b22b8" />

Muutamia yleisiä tietoja tiedostosta, kuten näytteenottotaajuus oli nähtävillä ohjelman ikkunan vasemmasta laidasta löytyneen `Signal details` -näppäimen kautta. Tiedostonimen perusteella näytteenottotaajuus oli 2MSps, mutta URH:n tiedoissa näytteenottotaajuutena oli ilmoitettu 1.0M. Tämän myötä ohjelma näytti nauhoituksen pituuden olleen 5,49 sekuntia, sillä kesto lasketaan jakamalla kokonaisnäytemäärä näytteenottotaajuudella. Kun näytteenottotaajuudeksi asetettiin manuaalisesti tiedostonimen mukainen 2M, nauhoituksen ilmoitettu pituus laski 2,75 sekuntiin. 

<img width="2101" height="294" alt="image" src="https://github.com/user-attachments/assets/66b9505f-c2fb-4e26-89be-bd5bb863a3b0" />

### g) Demodulointi

Signaali demoduloitiin valitsemalla URH:n antamista vaihtoehdoista ASK-modulaatio. Oikea modulaatio todettiin sen perusteella, että ohjelmassa ASK tulkitsi signaalin selkeinä jaksoina, kun taas FSK- tai PSK-modulaation valitseminen johti hyvin epäsäännölliseen aaltomuotoon. Yhden raakabitin pituus ajassa selviää suoraan URH-ohjelman tulkintanäkymästä bittejä maalaamalla. Tarkemmassa ajoitustarkastelussa havaittiin, että bittien pituudet ajassa vaihtelevat arvojen mukaan niin, että bitti 0 oli kestoltaan noin 250 mikrosekuntia ja bitti 1 kestoltaan noin 260,5 µs. Vertailun vuoksi ihmisen keskimääräinen silmänräpäys kestää noin 150-400 millisekuntia, eli näiden radiosignaalien kohdalla puhutaan noin 600-1600 kertaa nopeammista asioista (Fogelton & Benesova, 2018).

<img width="1274" height="702" alt="Screenshot 2026-11-09 070744" src="https://github.com/user-attachments/assets/496f42f1-2ac7-4d2b-9463-f712c8139f8f" />

### Lähteet

Christian W. Zuckschwerdt 2026. rtl_433 repositorio. Luettavissa: https://github.com/merbanan/rtl_433

Fogelton, A. & Benesova, W. 2018. Eye blink completeness detection. Computer Vision and Image Understanding, 176–177, s. 78–85. https://doi.org/10.1016/j.cviu.2018.09.006

Karvinen, Tero 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

Nurminen, Kasper 2025. h3 Aaltoja harjaamassa. Luettavissa: https://github.com/nurminenkasper/Verkkoon-tunkeutuminen-ja-tiedustelu/blob/main/h3/h3-Aaltoja-harjaamassa.md


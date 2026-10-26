Tämä dokumentti on kirjoitettu vastauksena Tero Karvisen kurssin [Verkkoon tunkeutuminen ja tiedustelu](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/) kotitehtävään _h1 Korva netissä_.

### x) Sähkömagneettinen spektri

<img width="2360" height="1093" alt="electromagnetic_spectrum" src="https://github.com/user-attachments/assets/d39b2ad5-846a-4b83-a102-7b2850da0e4e" />

_Kuva 1: Esitetty aaltoviiva on vain havainnollistamistarkoitusta varten eikä vastaa todellisia mittasuhteita (mukaillen National Aeronautics and Space Administration s.a. ja Encyclopædia Britannica 2026)._

## a) WebSDR-ohjelmaradio

Sivustolla [websdr.org](http://websdr.org/) (Web Software-Defined Radio) on mahdollista kuunnella radioamatöörien ja harrastajien internetissä ylläpitämiä etävastaanottimia. Valitsin sivuston listasta lähempään tarkasteluun Belgian Heppenissä sijaitsevan vastaanottimen, joka toimii 0-30 MHz taajuusalueella. Amplitudimodulaatiota (AM) hyödyntävät yleisradiolähetykset toimivat keskiaaltoalueella, joten hallintanäkymästä valittiin _Band_-otsakkeen alta MW eli _Medium Waves_ (Radio Broadcast Authority s.a.).

Vastaanottimen vesiputousnäkymän avulla taajuudelta 909,00 kHz löytyi suhteellisen selkeästi kuulunut BBC Radio 5, jonka lähetyksessä käsiteltiin kuunteluhetkellä ajankohtaisia poliittisia aiheita. Vesiputousnäkymässä näkyvät vaaleammat ja kirkkaat kohdat auttavat paikantamaan aktiiviset radiolähetykset spektriltä.

Aallonpituus on mahdollista laskea kaavalla `λ = C/f`, missä `λ` on aallonpituus metreinä, `C` valonnopeus tyhjiössä ja `f` taajuus. Tämän perusteella voitiin laskea aallonpituudeksi kyseisellä taajuudella noin 330 metriä.

<img width="2808" height="1440" alt="heppen_sdr" src="https://github.com/user-attachments/assets/9caaed06-9a50-4c10-b3b9-526f8c08958e" />

_Kuva 2: Käytetyn belgialaisen WebSDR:n hallintanäkymä._

## b) Moduwhat?

Seuraavaksi siirryttiin tutkimaan AM-modulaation sijaan muiden modulaatiotapojen käyttöä 80 metrin radioamatöörialueella, jonka valitsemisen yhteydessä vastaanotin asetti automaattisesti asetukseksi LSB-modulaation. Saman vastaanottimen kohdalla noin taajuudelta 3,74 MHz kuului selkeää, joviaalia saksankielistä, todennäköisesti radioharrastelijoiden välistä keskustelua. Tässä tapauksessa radioliikenteen onnistunut seuranta vaati tarkempaa hienosäätöä ja kapeampaa suodatinta, jotta puheesta sai selvää.

## c) ADS-B

ADS-B eli _Automatic Dependent Surveillance–Broadcast_ (suom. automatiikkaan perustuvat valvontalähetykset) on ilmailussa käytetty radioaaltoihin perustuva seurantateknologia, jonka avulla ilma-alukset jakavat sijainti- ja lentotietojaan avoimesti. Aluksissa olevat radiolähettimet lähettävät jatkuvasti automaattisesti 1090 MHz taajuudella viestejä, joita kuka tahansa sopivan radiovastaanottimen omaava taho voi vastaanottaa. Lentokoneiden lähettämiin tietoihin kuuluvat muun muassa paikannussatelliittien perusteella laskettu sijainti, lentokorkeus ja maanopeus. (Federal Aviation Administration 2023.)

[ADS-B Exchange verkkopalvelusta](https://globe.adsbexchange.com/) on mahdollista tarkkailla erinäisiä ADS-B-viestejä lähettäviä aluksia. Valitsin yhdeksi seurattavaksi kohteeksi Finnairin sunnuntaina 25.10. ensimmäistä kertaa lentämän lennon AY145 Helsinki-Vantaalta Bangkokin kautta Australian Melbourneen. Kyseessä oli tavanomainen matkustajaliikenteen reittilento, mitä lennettiin Airbus A350-900 -lentokoneella. Havaintohetkellä kone lensi 41 000 jalan korkeudessa 570 solmun nopeudella ja oli saapumaisillaan Victorian osavaltion ilmatilaan.

<img width="1664" height="1366" alt="AY145" src="https://github.com/user-attachments/assets/0458be26-40d2-4616-a2b8-ffeaa8153056" />

_Kuva 3: Seuratun koneen tietoja ADS-B Exchange -palvelussa._


### Lähteet

ADS-B Exchange s.a. Global Flight Tracking. Luettavissa: https://www.adsbexchange.com/. Luettu: 25.10.2026.

Encyclopædia Britannica 2026. Electromagnetic spectrum. Luettavissa: https://www.britannica.com/science/electromagnetic-spectrum. Luettu: 24.10.2026.

Federal Aviation Administration 2023. Equip ADS-B. Luettavissa: https://www.faa.gov/air_traffic/technology/equipadsb/capabilities/ins_outs. Luettu: 25.10.2026. 

Karvinen, Tero 2026. Verkkoon tunkeutuminen ja tiedustelu -kurssi. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/. Luettu: 24.10.2026.

National Aeronautics and Space Administration s.a. Introduction to the Electromagnetic Spectrum. Luettavissa: https://science.nasa.gov/ems/01_intro/. Luettu: 24.10.2026.

Radio Broadcast Authority s.a. AM Radio Broadcasting Explained. Luettavissa: https://radiobroadcastauthority.com/am-radio-broadcasting-explained/. Luettu: 24.10.2026. 


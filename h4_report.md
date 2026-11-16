Tämä dokumentti on kirjoitettu vastauksena Lari Iso-Anttilan ja Tero Karvisen järjestämän [Verkkoon tunkeutuminen ja tiedustelu -kurssin](https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/) kotitehtävään _h4 - NFC ja RFID_.

## a) RFID-tuotteet ja urkinta

Muiden ihmisten tavoin hallussani ja päivittäisessä käytössäni on useita RFID- ja NFC-teknologiaa hyödyntäviä tuotteita kuten pankkikortteja, matkakortteja ja joitain jäsenyyskortteja. Moni fyysinen kortti, kuten joukkoliikenteen matkakortti, on kuitenkin viime vuosina korvautunut joko mobiilisovelluksilla tai erinäisiin tunnistetietoihin, kuten puhelinnumeroon tai sähköpostiosoitteeseen perustuvilla tilitiedoilla.

Tärkeimmät maksukorttini ovat suojassa alumiinisen korttikotelon sisässä, mikä estää sähkömagneettiset kentät ja vaimentaa radiotaajuudet, jolloin korttien siruihin ei ole mahdollista saada yhteyttä ilman, että kortteja otetaan fyysisesti pois kotelosta. Mediassa ja joidenkin tuotteiden mainosmateriaaleissa saattaa usein törmätä esitettyihin uhkakuviin langattomasta korttitietojen urkinnasta tai suoritetuista veloituksista julkisissa paikoissa, mutta todellisuudessa riski on nykyaikaisilla sirullisilla maksukorteilla hyvin pieni myös ilman minkäänlaisia RFID-signaalinestäjiä tai koteloita. Suurempi uhkakuva on niin kutsuttu _skimming_ eli korttitietojen kopiointi fyysisesti muunnelluilla pankkiautomaateilla tai maksupäätteillä. (Forbes 2026.)

Vähemmän kriittiset kortit ovat kotelon ulkopuolella, mutta nämä kortit eivät lähtökohtaisesti sisällä mitään arkaluonteisia tai väärinkäytettävissä olevia henkilö- tai maksutietoja, joten niiden mahdolliseen lukemiseen ulkopuolisen toimesta ei liity kovin merkittävää riskiä.

## b) APDU-komennot ja niiden rakenne

APDU (_Application Protocol Data Unit_) on älykortin ja lukijalaitteen välinen standardoitu tiedonsiirtoyksikkö. Sanoma koostuu joko komennosta tai vastauksesta, joita lähetetään lukijalaitteen ja kortin välillä. Viestintä muodostaa komento–vastaus-parin, jossa lukija lähettää komennon ja kortti käsittelee sen ja lähettää vastauksen takaisin. Tässä kohdassa ilmoitetut tiedot perustuvat älykorttiteknologiaan erikoistuneen [Cardlogix-yhtiön verkkosivujen](https://www.cardlogix.com/glossary/apdu-application-protocol-data-unit-smart-card/) materiaaliin. Alla olevassa tiivistyksessä hyödynnettiin apuna Googlen Gemini 3.5 Flash-Lite -tekoälymallia komentojen sisältämien lyhenteiden avaamiseen ja niiden suomennoksissa.

### APDU-sanoman pääosat

**Lukijalta kortille:**
#### Command APDU (C-APDU)
- Otsikko (pakollinen), sisältää:
  - CLA (Class, komentoluokka)
  - INS (Instruction, itse käsky/toiminto)
  - P1 ja P2 (parameter 1 ja 2, lisäparametrit)
- Runko (valinnainen), sisältää:
  - tarvittaessa lähetettävän datan pituuden (Lc, Length of command data)
  - itse datan sekä odotetun vastauksen pituuden (Le, Length of expected response)

**Kortilta lukijalle:**
#### Response APDU (R-APDU) 
- Runko (valinnainen), sisältää:
  - kortin palauttaman vastausdatan (maks. 255 tavua)
- Pääte / Tila- ja virhekoodit (pakolliset), sisältää:
  - kentät SW1 ja SW2 (Status Word 1 ja 2) kertovat komennon suorituksen tilan (esimerkiksi tilakoodi 0x9000 tarkoittaa komennon onnistunutta suoritusta)

## c) VISA-tietoturvahaavoittuvuus "Zombie Card"
[The Hacker News -sivustolla](https://thehackernews.com/2026/08/zombie-card-attack-can-revive-expired.html) elokuussa 2026 julkaistun uutisen mukaan Massachusettsin yliopiston tutkijat ovat demonstroineet uutta niin kutsuttua _Zombie Card_ -haavoittuvuutta. Tutkimuksessa osoitettiin, että vanhentuneilla ja jo umpeutuneilla Visa-lähimaksukorteilla pystyttiin edelleen tekemään onnistuneita kauppaostoksia muuttamalla kortin vanhenemispäivää lennosta NFC-lukijan ja kortin välillä.

Hyökkäyksessä pahantahtoinen toimija käyttää välikätenä toimivaa laitetta, kuten kahta älypuhelinta, kortin ja maksupäätteen välissä. Koska kortin tietoliikenteessä vanhenemispäivää ei ole sidottu tiukasti kryptografiseen allekirjoitukseen Visan hyödyntämässä protokollassa, väliin pääsevä laitteisto kykenee vaihtamaan päivämäärän tulevaisuuteen. Tämän myötä maksupääte luulee korttia edelleen voimassa olevaksi, ja mikäli kortin taustalla oleva tili on edelleen aktiivinen, pankki hyväksyy transaktion.

#### Lähteet

CardLogix s.a. Application Protocol Data Unit (APDU). Luettavissa: https://www.cardlogix.com/glossary/apdu-application-protocol-data-unit-smart-card/

Forbes 11.4.2026. Worried About Credit Card Safety When You Travel? Try These Expert Tips. Luettavissa: https://www.forbes.com/sites/christopherelliott/2026/04/11/worried-about-credit-card-safety-when-you-travel-try-these-expert-tips/

Iso-Anttila, Lari & Karvinen, Tero. 2026. Verkkoon tunkeutuminen ja tiedustelu. Luettavissa: https://terokarvinen.com/verkkoon-tunkeutuminen-ja-tiedustelu/

The Hacker News 20.8.2026. Zombie Card Attack Can Revive Expired Visa Cards for Contactless Payments. Luettavissa: https://thehackernews.com/2026/08/zombie-card-attack-can-revive-expired.html








# portswager academy - Tietoturvahyökkäysten raportti

## 


## 1. SQL-injektio (SQL Injection)

Mistä riskistä on kyse:
Kyseessä on OWASP Top 10 -listan (A03:2021 – Injection) mukainen injektiohaavoittuvuus, joka on yksi vakavimmista ja yleisimmistä verkkosovellusten uhista. Riskissä hyökkääjä pääsee manipuloimaan sovelluksen tietokannassa olevia kyselyitä. Tämä voi johtaa luottamuksellisten tietojen (kuten salasanojen tai asiakastietojen) vuotamiseen, tietojen muokkaamiseen tai jopa koko tietokannan hallinnan kaappaamiseen.

Omin sanoin, kuinka hyökkäys tapahtuu:
Hyökkäys tapahtuu syöttämällä sovelluksen tekstikenttään (esim. hakukenttä tai kirjautumislomake) normaalin tekstin sijaan SQL-komentoja ja erikoismerkkejä (kuten heittomerkki '). Tällä tavoin alkuperäinen tietokantakysely katkaistiin ja siihen liitetään oma, loogisesti aina tosi oleva ehto (esim. OR 1=1). Kun sovellus suoritti tämän muokatun kyselyn, se palautti tuloksena tietoa tai antoi sellaiset oikeudet, joihin muuten ei normaalisti pitäisi olla pääsyä.

Mikä sovelluksen puute tai virhe tekee kyseisestä hyökkäyksen mahdolliseksi:
Hyökkäys on mahdollinen, koska sovellus ei validioi tai puhdista käyttäjän syöttämää hakua, vaan yhdistää sen suoraan osaksi SQL-kyselymerkkijonoa. Sovellus ei pysty erottamaan, mikä osa kyselystä on ohjelmoijan tarkoittamaa komentoa ja mikä osa käyttäjän syöttämää väärää dataa, jolloin syötetty data suoritetaan komentoina.

Mitä sovelluksen toteutuksessa pitäisi ottaa huomioon, jotta hyökkäys voitaisiin estää:
Tärkein osa hyökkäyksen estämiseksi on erottaa suoritettava koodi (SQL-komento) ja käyttäjän syöttämä data toisistaan. Tämä tehdään käyttämällä tietokantakyselyissä ns. valmisteltuja lausekkeita. Tällöin tietokanta käsittelee käyttäjän syötteen aina puhtaasti tekstinä tai arvona, eikä koskaan suoritettavana komentona, vaikka se sisältäisi SQL-komennon.

En saanut tehtävää itse tehtyä, joten katsoin tehtävän ratkaisun joka oli: 

Use Burp Suite to intercept and modify the request that sets the product category filter.
Modify the category parameter, giving it the value '+OR+1=1--
Submit the request, and verify that the response now contains one or more unreleased products. 

## 2. Välimuistin harhautus (Web Cache Deception)

Mistä riskistä on kyse:
Kyseessä on haavoittuvuus, jossa hyökkääjä hyödyntää verkkosovelluksen ja sen edestä löytyvän välimuistipalvelimen (esim. nimipalvelin) välistä epäjohdonmukaisuutta. Riskinä on, että hyökkääjä saa välimuistipalvelimen tallentamaan uhrin yksityisiä, luottamuksellisia tietoja (kuten tilitiedot tai käyttäjätiedot) julkiseen välimuistiin, josta hyökkääjä voi ne myöhemmin ottaa.

Omin sanoin, kuinka hyökkäys tapahtuu:
Hyökkäyksessä luodaan muokattu URL-osoite, joka osoittaa uhrin dynaamiselle profiilisivulle jonka perään on lisätty staattisen tiedosto (esim. /profiili/testi.css). Tämä linkki toimitettaan uhrille. Kun uhri klikkaa linkkiä, sovelluspalvelin jättää .css -päätteen huomiotta ja palauttaa uhrin henkilökohtaisen profiilisivun. Välimuistipalvelin kuitenkin katsoo URL:n .css -päätettä, olettaa kyseessä olevan julkinen tyylitiedosto ja tallenstaa vastauksen (uhrin profiilitiedoston) välimuistiin. Tämän kuuluisi päästä katsomaan uhrin tiedot, mutta en itse päässyt sillä sivu ei lataa.

Mikä sovelluksen puute tai virhe tekee ko. hyökkäyksen mahdolliseksi:
Perimmäinen syy on reitityksen ja välimuistisääntöjen ristiriitaisuus. Välimuistipalvelin on konfiguroitu välimuistittamaan tiedostoja pelkän URL-osoitteen tiedostopäätteen (esim. .js, .css, .jpg. .png) perusteella. Samaan aikaan taustalla oleva verkkosovellus on ohjelmoitu liian sallivaksi reitityksessään: se palauttaa dynaamisena ja arkaluontoisena sisällön, vaikka URL-osoitteen perässä olisi tuntematon staattisen tiedostopolku.

Mitä sovelluksen toteutuksessa pitäisi ottaa huomioon, jotta hyökkäys voitaisiin estää:
Välimuistipalvelimen ei pitäisi luottaa pelkkään URL-osoitteen loppuun päätellessään, voidaanko sisältöä tallentaa. Sovelluksen tulisi lähettää jokaisen arkaluontaisen ja dynaamisen vastauksen mukana selkeät HTTP-otsakkeet, jotka kieltävät välimuistin käytön. Lisäksi sovelluksen reititys tulisi määrittää tiukemmaksi siten, että se palauttaa virheen (kuten 404), jos dynaamiseen reittiin yritetään yhdistää olemattomia staattisia tiedostoja.

Portswiger ei lataa tarvitsemaani osiota itselläni ja en pääse tekemään tehtäviä

## 3. Web LLM -hyökkäykset (Large Language Model Attacks)

Mistä riskistä on kyse:
Verkkosovelluksiin integroitujen kielimallien (LLM) myötä on syntynyt uusia riskejä (esim. OWASP Top 10 for LLMs). Näissä hyökkäyksissä hyödynnetään tyypillisesti "kehotteen injektiota" (Prompt Injection) tai liiallisia oikeuksia. Riskinä on, että hyökkääjä voi manipuloida kielimallia ohittamaan sille asetetut turvasäännöt, saaden sen vuotamaan järjestelmän sisäiset ohjeet, paljastamaan muiden käyttäjien tietoja tai jopa suorittamaan luvattomia toimintoja, jos mallille on annettu liian laajat oikeudet.

Omin sanoin, kuinka hyökkäys tapahtui:
Hyökkäys suoritettaisiin antamalla sovelluksen chat- tai tekstikenttään tietyllä tavalla muotoiltu syöte, joka huijaa kielimallia. Antaisin kehotteen, joka käskisi mallia unohtamaan kaikki sen aiemmat kehittäjän antamat ohjeet ja sen sijaan suorittamaan uuden komennon jonka tarkoitus on ohittaa suojarajat. Testissä kielimallin kuuluisi palauttaa vastaus, jota sen ei pitäisi pystyä antamaan.

Mikä sovelluksen puute tai virhe tekee ko. hyökkäyksen mahdolliseksi:
Ongelma johtuu siitä, että kielimallit käsittelevät luonnollista kieltä, jolloin järjestelmän antamien ohjeiden ja käyttäjän syöttämän tiedon välillä ei ole selkeää eroa. Sovellus luottaa kielimallin liikaa ja saattaa antaa pääsyn johonkin, mihin sen ei kuuluisi antaa pääsyä.

Mitä sovelluksen toteutuksessa pitäisi ottaa huomioon, jotta hyökkäys voitaisiin estää:
Kielimallin tuottamaan sisältöön ja sen ehdottamiin toimintoihin tulisi aina suhtautua epäluotettavana. Sovelluksen toteutuksessa kielimallille tulee antaa vain minimimäärä oikeuksia rajapintoihin, jotta se ei pysty ajamaan sovelluksia tai käyttämään rajapintoja joihin sillä ei kuulu olla pääsyä. Jos malli voi tehdä mittavia muutoksia tai päästä käsiksi johonkin tärkeen, pitäisi olla toinen validaatio joka varmistaa, että asioita ei tapahdu ilman lupaa, esim. lopussa ihminen tekee päätöksen siitä, saako kielimalli tehdä asian.

# Raportointi ja Linuxin asennus
Hyvän raportin tekeminen mahdollistaa, että tehtävä voidaan suorittaa uudelleen täysin samalla tavalla. Tehtyjen toimenpiteiden lisäksi myös pienet yksityiskohdat, kuten työvaiheen suoritusaika tai omat virheet ja huomiot, tulisi merkata tarkasti raporttiin. Raportista saa helppolukuisen mm. lisäämällä väliotsikoita, ja lähteisiin pitää aina viitata, kun ei ole kyse omasta tiedosta. Asioiden sepittämisessä ei ole raportoinnissa mitään järkeä, eli ei kannata pyytää LLM:ää kirjoittamaan raporttia omasta puolesta. (Karvinen 2006.)
Lähde: Karvinen, T. 2006. Raportin kirjoittaminen. Viitattu 18.1.2026. https://terokarvinen.com/2006/raportin-kirjoittaminen-4/

## Linuxin asentaminen
Latasin www.debian.org/distrib/ -sivustolta Debian live Xfce ISO-levykuvan, jota hyödynnän uuden VirtualBox-virtuaalikoneen luomisessa. 

(12:45) Uuden virtuaalikoneen luomisessa käyttämäni asetukset:

![Virtuaalikoneen asetukset](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_870eccdd.png)
![Virtuaalikoneen asetukset](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_c370580b.png)
![Virtuaalikoneen asetukset](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_a5268cdb.png)

(12:55) Lisään ISO-levykuvan asennukseen vasta myöhemmässä vaiheessa, sillä en halua, että VirtualBox alkaa tekemään asennusta puolestani (Unattended Install).

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_50608cfd.png)

(12:57) Notification centeriin tuli seuraavanlainen ilmoitus. Tarkistan tietokoneen UEFI/BIOS -asetuksista, onko virtualisointi päällä, vai onko kyse jostain muusta. 

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_f05c4200.png)

(13:06) Enabloin SVM-mode asetuksen BIOSista, sen pitäisi ottaa käyttöön CPU virtualisaatio.

(13:08) Uusi errorviesti

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_6b8bfd7c.png)

(13:14) KVM virtualisaatio vaikuttaa olevan Linuxissa päällä oletusarvoisesti, ja käyttää ilmeisesti samaa hardware ominaisuutta kuin VirtualBox https://www.virtualbox.org/ticket/22248

(13:18) Ratkaisu näyttää olevan KVM-kernelmoduulin poistaminen tilapäisesti.

(13:21) rmmod-komennolla saa disabloitua KVM-kernelmoduulin tilapäisesti. https://itsfoss.community/t/issue-with-virtual-box-7-0-on-ubuntu-22-04/12472/3 

(13:25) 

    lsmod | grep kvm 

Tällä komennolla näen mitä kvm moduuleja kernelissä on käytössä

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_2f197f3a.png)

(13:28)

    sudo rmmod kvm_amd 
    sudo rmmod kvm

(13:32) Yllä olevien komentojen jälkeen sain VirtualBoxin käynnistymään. Ilmeisesti jokin päivitys Linuxin kerneliin tai Ubuntuun on tullut viimeisen vuoden aikana, sillä tälläista ongelmaa ei ole ennen tullut vastaan VirtualBoxia käytettässä.

VirtualBox toiminnassa: 

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_2d29cd0.png)

(15:54) Valitsin ensin Live system (amd64) ja sitten Xfce:n käyttöliittymästä Install Debian
- American English
- Region Europe: Helsinki
- Keyboard layout: Finnish
- Erase disk, jätetään Encrypt system checkkaamatta
- Bootloader: Master Boot Record
- Name: Joonas Ristola, username: joonas, Computer Name: ahma, Password: *******
- Do not log in automatically
Yhteenveto asetuksista näyttökuvassa alla:

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_8538c3f5.png)

(16:06) Aloitin asennuksen painamalla Install
(16:18) Pääsin kirjautumisruutuun, mutta ilman että VM käynnistyi uudelleen. Live-ympäristö ilmeisesti lukittautui, pääsin kirjautumaan siihen takaisin tunnuksilla “user”, “live”
(16:22) Lukituksen avattuani, sain ilmoituksen onnistuneesta asennuksesta. Laitoin raksin ruutuun Restart Now ja painoin Done
(16:26) Virtuaalikone käynnistyi uudelleen ja GRUBista valitsin Debian, jonka jälkeen pääsin kirjautumisruutuun. Kirjautuminen onnistui aiemmin luomillani käyttäjätunnuksilla.

### Ohjelman käyttö Linuxilla
Kävin läpi esiasennettuja sovelluksia, ja LibreOffice oli yksi niistä. Libreofficen Writer toimii hyvin samalla tavalla kuin Microsoftin Word, joitain toiminnallisuuksia vain puuttuu tai se toimii eri tavalla. 
Kirjoitin Hello World -dokumentin ja tallensin sen kotikansiooni. LibreOffice tallentaa tiedostot .odt-muotoon, ja LibreOfficella luotuja dokumentteja voi avata Wordilla, sekä päinvastoin.

![](./Photos/Raportin%20kirjoittaminen%20ja%20Debianin%20asennus_html_bbfd9c4e.gif)

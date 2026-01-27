# Komentaja pingviini :)

Tehtävänanto sivustolla https://terokarvinen.com/linux-palvelimet/ kohdassa h2

- Komentorivi on tehokas työkalu, vaikkakin sen opetteluun voi mennä paljonkin aikaa.
- Hakemistossa liikkuminen tapahtuu cd -komennolla. Pwd kertoo, missä hakemistossa on tällä hetkellä, ls listaa nykyisen hakemiston tiedostot.
- Uutena tietona itselleni tuli se, että järjestelmälogit tallentuu kansioon /var/log ja se, että scp-komennon kanssa voi kopioida tiedostoja etäyhteyden yli.
- Testaillessani huomasin, että apt-get tai apt-cache komennoissa tuota toista osaa apt:n jälkeen ei tarvita. Esim. apt search tai apt install toimivat itsenäisesti.

Karvinen, T. 2020. Command Line Basics Revisited. https://terokarvinen.com/2020/command-line-basics-revisited/?fromSearch=command%20line%20basics%20revisited. Viitattu 21.1.2026.

22.1.2026

Välihuomautuksena, VS-coden asentaminen nopeutti omaa workflowta huomattavasti. Helpottaa toimintaa Gitin kanssa ja varsinkin kuvakaappausten kanssa. 

### Virtuaalikoneen asetuksia

![Linux Settings](/Photos/h2-image.png)

## Text editorin asennus

Micro text editorin asentaminen onnistui seuraavilla komennoilla

    sudo apt update
    sudo apt install micro

Micro text editorin asennuksen mukana asentui xclip. 

Avasin micro-editorin seuraavalla komennolla:

    micro uusitekstitiedosto.md

![Micro avattu](/Photos/h2-image-1.png)

Ctrl + S tallensi tiedoston. Ctrl + Q poistui editorista. 

## Sovellusten asennus

APT applikaatioita: git python3 ssh

Kävin läpi asennettavia sovelluksia alla olevalla komennolla

    apt search cli

Asensin sovellukset seuraavalla komennolla:

    sudo apt install git python3 ssh

![alt text](/Photos/h2-image-2.png)

Python3 oli jo asennettu, mutta git ja ssh asentuivat tarvittavien lisäosien kanssa.

Kolmanneksi sovellukseksi asensin ufw:n

    sudo apt install ufw

## Käyttöjärjestelmän kansioita

/ eli Root kansio on käyttöjärjestelmän juuri. Täältä pääsee navigoimaan käyttöjärjestelmän kaikkiin kansioihin. 

![Root ls](/Photos/h2-image-3.png)

Esimerkiksi /bin kansio sisältää mm.käyttöjärjestelmässä käytettiä komentoja. Bin on lyhenne binääristä, eli toisin sanoen tämä kansio sisältää järjestelmälle tärkeimmät binääritiedostot. (https://www.scaler.com/topics/bin-in-linux/.)

/home Home-kansiosta löytyy kaikkien käyttäjien kotikansiot. Virtuaalikoneellani näkyy siis vain oman käyttäjäni kansio, 'joonas'

/home/joonas Käyttäjän kotikansioon on luotu oletuksena muutama kansio, kuten Documents, Downloads, Desktop, etc. Tähän kansioon on  oletuksena oikeus vain kansion omistajalla. 

![my home folder](/Photos/h2-image-4.png)

/etc Etc-kansiosta löytyvät järjestelmän asetustiedot. Tarkastellaan esim passwd-tiedostoa. 

    cat passwd

Tässä tiedostossa löytyvät tiedot kaikkien käyttäjien tiedot. Esim. Käyttäjänimi, käyttäjän ID, ensisijaisen ryhmän ID, kotihakemisto. (https://www.geeksforgeeks.org/linux-unix/understanding-the-etc-passwd-file/.)

/media Media-kansioon ilmestyvät siirrettävät mediat. Tavallisesti hakemistossa on vain tyhjä '/joonas' -niminen kansio, mutta jos lisään Guest Additions CD:n ja mountaan sen, kansioon ilmestyy CD:llä olevat tiedot. 

/var/log Tässä kansiossa löytyy järjestelmän logit. Esim. VirtualBoxin Guest Additions -lisäosaa asennettaessa sen logit kirjoitettiin "vboxadd-install.log" -tiedostoon. 

![var/log kansio ja vboxadd-install.log](/Photos/h2-image-5.png)

## Grep komento ja putket

Mielestäni grep-komento on hyödyllisin, kun sitä käytetään toisen komennon outputista putkitettuna. Sen avulla voi hakea myös tiedostosta suoraan jotain tiettyä tekstiä tai patternia.  

    grep [pattern] [file/input]

(man grep, synopsis.)

Jotain esimerkkejä grepin käytöstä: 

    grep joonas /etc/passwd

![grep 1](/Photos/h2-image-6.png)

    grep -E "H?llo w*" uusitekstitiedosto.md

(man grep, matching control.)

Tämä ei aluksi toiminut, koska käytin pientä e-kirjainta ison E-kirjaimen sijasta. E-lipun kanssa voi käyttää haettavassa patternissa Regex:iä. 

![grep 2](/Photos/h2-image-7.png)

    apt list | grep chrome

Näyttää kaikki asennettavat sovellukset, joiden nimessä esintyy sana chrome.

Asensin tässä välissä wget-sovelluksen koneelle, jotta voin tehdä vielä jonkun esimerkin putkesta. 

    sudo apt install wget
    wget https://www.lipsum.com/feed/http
    cat http | grep -i "lorem ipsum"

i -lippua käytettäessä isoilla tai pienillä kirjaimella ei ole väliä. Kokeilin seuraavaksi putkittaa grepin tuloksen touch-komennolle, mutta touch loi vain uuden tyhjän tiedoston. 

    cat http | grep -i "lorem ipsum" >  loremipsum.md
    cat loremipsum.md

Tämä loi uuden tiedoston, jossa grepin tulos siirtyy tiedostoon "loremipsum.md". 

![grep ja putki esimerkki](/Photos/h2-image-8.png)

(man grep, matching control.)

https://man7.org/linux/man-pages/man1/grep.1.html

## Rauta

Ohjeessa mainittu komento:

    sudo lshw -short -sanitize

Mikä kyseinen komento on ja mitä se tekee? 

Asennetaan se ensin koneelle...

    sudo apt install lshw

... ja sitten katsotaan manuaali

    man lshw

    lshw - list hardware
    lshw is a small tool to extract detailed information on the hardware configuration of the machine

short-lippu outputtaa laitepuun ja 
sanitize-lippu poistaa arkaluonteiset tiedot, kuten ip-osoitteet.

(man lshw.)

https://linux.die.net/man/1/lshw

![lshw](/Photos/h2-image-9.png)

Tuloksessa näkyivät kaikki virtuaalikoneen virtualisoidut laitteet ja myös ne komponentit joita virtuaalikone käyttää suoraan host-koneelta. Esim. AMD Ryzen -prosessori.

## EDIT 27.1.2026

Changed file names of the screenshots

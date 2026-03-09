# h7 - Maalisuora

Tämä raportti on osa Tero Karvisen Linux palvelimet -kurssia. Tehtävänanto löytyy sivustolta https://terokarvinen.com/linux-palvelimet/

## Tehtävä a - kirjoita ja aja "Hei maailma" kolmella kielellä

### bash

Hello World bash-skriptauskielenä:

![alt text](Photos/h7a-image.png)

Oikeudet kaikille ajaa ohjelma.   

![alt text](Photos/h7a-image-1.png)

Toimii.

### python

Tarkistin että python3 on asennettu

![alt text](Photos/h7a-image-2.png)

Jos ei olisi asennettuna ajaisin komennot

    sudo apt update
    sudo apt install python3

Hello world pythonilla

![alt text](Photos/h7a-image-4.png)

Oikeudet ajaa ohjelma

    chmod ugo+x helloworld.py

![alt text](Photos/h7a-image-3.png)

### C

Tarkistin että gcc compiler ohjelma on asennettu.

![alt text](Photos/h7a-image-5.png)

Kirjoitin Hello World:

![alt text](Photos/h7a-image-6.png)

Compile käyttämällä gcc. Loi uuden a.out tiedoston, jonka ajamalla saa ohjelman ajettua.

![alt text](Photos/h7a-image-7.png)

Lähde: https://www.geeksforgeeks.org/cpp/how-to-compile-and-run-a-c-c-code-in-linux/

## Tehtävä c - uusi komento

Loin komennon, joka tarkistaa ja asentaa saatavilla olevat päivitykset:

![alt text](Photos/h7a-image-8.png)

Kaikille oikeudet ajaa komentoa:

    chmod ugo+x paketit

Siirretään komento kansioon, josta kaikki voivat ajaa komennon.

    mv paketit /usr/local/bin/

Testataan että toimii

    sudo paketit

![alt text](Photos/h7a-image-9.png)

Lähde: https://askubuntu.com/questions/308045/differences-between-bin-sbin-usr-bin-usr-sbin-usr-local-bin-usr-local

## Tehtävä d - Vanha labraharjoitus (Kesken)

9.3.2026 17:00

Päätin tehdä laboratorioharjoituksen vuodelta 2024.

Linkki laboratorioharjoitukseen:

https://terokarvinen.com/2024/arvioitava-laboratorioharjoitus-2024-linux-palvelimet/

Tehtävä kuuluisi tehdä tyhjällä virtuaalikoneella, mutta virtuaalikoneen uudelleen asentamisessa menee sen verran aikaa, että on järkevämpää asentaa esim. apache vain uudelleen. 

Tarkoitus tehdä harjoituksessa tehtävät d:stä h:n alkuosaan

### d - 'howdy'

Loin uuden komennon micro-editorilla:

![alt text](image.png)

Annoin komennolle execute oikeudet ja siirsin sen sudo mv komennolla /usr/local/bin kansioon.

![alt text](image-1.png)

Lähdeaineisto, kun halusin käyttää komennon tulosta muuttujassa: https://www.cyberciti.biz/faq/unix-linux-bsd-appleosx-bash-assign-variable-command-output/



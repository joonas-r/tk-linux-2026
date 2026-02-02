# Hello Web Server - Apache2

## 27.1.2026

Asensin apachen komennolla 

    sudo apt install apache2

Sivustolle http://localhost ilmestyi default testisivu.

![Apache testisivu](/Photos/h3-image.png)

Tällä komennolla laitoin apache2:n käynnistymään koneen käynnistyessä

    sudo systemctl enable apache2

Tein kotihakemistoon kansion, jonne sivusto linkataan

    mkdir sites
    cd sites
    mkdir joonas

Siirryin /etc/apache2/ hakemistoon, ja siellä sites-available hakemistoon. Sitten loin uuden tiedoston sudoedit komennolla

    cd /etc/apache2
    cd /sites-available
    sudoedit joonas.conf

sudoedit ei tallentanut tiedostoa oikeaan kansioon. Joten loin tiedoston komennolla 

    sudo micro joonas.conf

Tarvittavat linet .conf -tiedostossa

![alt text](/Photos/h3-image-1.png)

Testataan syntaksi

    sudo apachectl configtest

![alt text](/Photos/h3-image-2.png)

Luodaan index.html -tiedosto /sites/joonas/ kansioon

    cd /home/joonas/sites/joonas
    micro index.html

Disabloidaan default sivusto, enabloidaan oma sivusto ja käynnistetään Apache uudelleen

    sudo a2dissite 000-default.conf
    sudo a2ensite joonas.conf
    sudo systemctl restart apache2

Error viesti, kun yritin käynnistää Apachea uudelleen

![alt text](/Photos/h3-image-3.png)

    systemctl status apache2
    Syntax error on line 9 of /etc/apache2/sites-enabled/joonas.conf

Huomasin, että < -merkki puuttui ensimmäiseltä riviltä. Korjauksen jälkeen apache käynnistyi.

Localhost sivu antoi vielä errorin Forbidden. Katsoin error logeista mikä virheilmoitus siellä oli.

    sudo tail /var/log/apache2/error.log

    [Tue Jan 27 15:41:26.919733 2026] [core:error] [pid 2107:tid 2129] (13)Permission denied: [client 127.0.0.1:56600] AH00035: access to /favicon.ico denied (filesystem path '/home/joonas/sites') because search permissions are missing on a component of the path, referer: http://localhost/

Tarkistin vielä file permissionit:

    ls -l /home/joonas

    drwxrwxr-x 3 joonas joonas 4096 27. 1. 15:18 sites

    ls -l /home

    drwx------ 16 joonas joonas 4096 27. 1. 15:17 joonas

Eli tarvitsin execute oikeudet /home/joonas/ hakemistoon kaikille. u=user g=group o=other, ja +x lisää execute oikat.

    cd home
    chmod ugo+x joonas/
    ls -l

    drwx--x--x 16 joonas joonas 4096 27. 1. 15:17 joonas

Restarttasin apachen ja testasin sitten uudelleen. Tällä kertaa toimii.

![alt text](/Photos/h3-image-4.png)

## 1.2.

### Tehtävä X

Tehtävänanto sivustolla https://terokarvinen.com/linux-palvelimet/

- Name-based virtual hosting mahdollistaa monen verkkosivuston pyörittämisen samalla palvelimella yhdessä IP-osoitteessa. 
- VirtualHost -blokilla voi määrittää verkkosivuston porttinumeron, nimen, vaihtoehtoisen nimen ja missä sivusto sijaitsee palvelimella

https://httpd.apache.org/docs/2.4/vhosts/name-based.html

https://terokarvinen.com/2018/04/10/name-based-virtual-hosts-on-apache-multiple-websites-to-single-ip-address/

## 2.2. 

### Tehtävä b, tarkistetaan lokit

Sain lokit näkymään tail -f komennolla. Tällä komennolla lokit päivittyvät reaaliajassa.  

![alt text](/Photos/h3-image-5.png)

![alt text](/Photos/h3-image-6.png)

    [Mon Feb 02 09:56:30.728974 2026] [mpm_event:notice] [pid 1006:tid 1006] AH00489: Apache/2.4.66 (Debian) configured -- resuming normal operations
    [Mon Feb 02 09:56:30.728991 2026] [core:notice] [pid 1006:tid 1006] AH00094: Command line: '/usr/sbin/apache2'

Aika, lokin tyyppi, Process ID:Thread ID, error/viestin ID: Viesti

    ==> apache2/other_vhosts_access.log <==
    joonas.example.com:80 127.0.0.1 - - [02/Feb/2026:10:07:52 +0200] "GET / HTTP/1.1" 304 248 "-" "Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0"

Avattu sivusto, käyttäjän IP-osoite - - Aika, tehty pyyntö, statuskoodi, pyynnön koko, tiedot käyttäjän selaimesta ja käyttöjärjestelmästä

https://httpd.apache.org/docs/2.4/logs.html

### Tehtävä c, hattu.example.com

Sivua voidaan jo muokata peruskäyttäjänä, ja oletussivusto on muutettu. Http://localhost -osoitteesta pääsee jo lukemaan sivua. Vaihdan sivuston nimen.

Ensimmäisenä suljin sivuston ja uudelleennimesin sen

    sudo a2dissite joonas.conf
    sudo systemctl reload apache2
    sudo mv joonas.conf hattu.conf

Seuraavaksi tein uuden kansion /home/sites hakemistoon ja päivitin hattu.conf tiedoston sisällön.

    cd ~/sites
    mkdir hattu
    cd hattu
    echo "This is hattu.example.com" >> index.html

![alt text](/Photos/h3-image-7.png)

Sitten laitoin sivuston käyntiin ja tarkastin sen toimivuuden.

    sudo a2ensite hattu.conf
    sudo systemctl restart apache2

![alt text](/Photos/h3-image-8.png)

### Tehtävä e, HTML5 sivu

![alt text](/Photos/h3-image-9.png)

![alt text](/Photos/h3-image-10.png)

https://www.w3schools.com/hTml/html5_syntax.asp

### Tehtävä f, curl 

Curl-komento noutaa tietoa internetistä eri verkkoprotokollien avulla. 

Esim. 

    curl http://localhost:80 

Hakee juuri luodun verkkosivun sisällön html muodossa. 

    curl -I https://terokarvinen.com

Hakee vastauksen palvelimelta ja näyttää sivuston metatietoja. 

![alt text](/Photos/h3-image-11.png)

HTTP statuskoodi, Server: käytetty palvelinohjelmisto. 

ETag, eli entitytag on tunniste verkkosivuston versiosta, ja muuttuu aina kun verkkosivustoa muutetaan. Mahdollistaa verkkosivustojen tallentamisen asiakkaan välimuistiin, sillä se kertoo, että sivusto ei ole muuttunut. 

https://www.geeksforgeeks.org/linux-unix/curl-command-in-linux-with-examples/

https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/ETag
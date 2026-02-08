# Pilvipalvelimen vuokraus, peruskonfiguraatio ja Apachen asennus

## Tehtävä a) Vuokraa virtuaalipalvelin

Käytän AWS:ää pilvipalvelimen vuokraukseen. AWS tarjoaa uusille tileille 100 $:n edestä puoleksi vuodeksi ilmaista käyttöä.

Laitoin budjettivaroitukset päälle. Tämä piti tehdä root accountilla. Tein itselleni myös toisen käyttäjän (console-admin), jotta root accountilla ei tarvitse tehdä kaikkia toimenpiteitä. Loin ryhmiä, joihin liitin käyttöoikeuksia, kuten IAM, EC2 ja S3, ja lisäsin console-admin -käyttäjän näihin ryhmiin.

![alt text](/Photos/h4-image.png)

Valitaan Debian Amazon Machine Image

![alt text](/Photos/h4-image-1.png)

Virtuaalikoneen tyypiksi t3-micro

![alt text](/Photos/h4-image-2.png)

Luodaan key pair. Amazon luo tässä tilanteessa sen itse, ja avain ladataan koneelle.

![alt text](/Photos/h4-image-3.png)

En tässä tilanteessa tee uutta Virtual Private Cloud -verkkoa, vaan käytän oletusasetuksia. Luon uuden security groupin, ja avaan sinne SSH yhteydet. En vielä kuitenkaan avaa http ja https yhteyksiä. 

![alt text](/Photos/h4-image-4.png) 

Oletuksena 8 GiB gp3 muistia (general purpose SSD gen 3)

Sitten vaan Launch Instance

![alt text](/Photos/h4-image-5.png)

Siirsin ssh-avaimen virtuaalikoneen .ssh hakemistoon, ja yhdistin EC2-instanssiin komennolla 
        
        ssh admin@{ip-osoite} -i /.ssh/avaimen-nimi.pem

## h4 Maailma kuulee

Tehtävänanto sivustolla https://terokarvinen.com/linux-palvelimet/ , kohta h4.

## Tehtävä x) Lue ja tiivistä

- Palveluntarjoajaa ja palvelinta valittaessa tulee miettiä, ketkä tulevat palvelinta käyttämään sekä pohtia lainsäädäntöjen, kuten GDPR:n vaikutusta. Palvelin tulee siis fyysisesti sijaitsemaan paikassa, jonka sille määrittää.

- Uuden virtuaalipalvelimen vuokraamisen jälkeen sille tarvitsee tehdä simppeleitä koventamistoimenpiteitä

- Linuxin palomuuri ufw kytketään päälle, mutta ennen sitä siihen tehdään sääntö avata ssh-yhteydet porttiin 22, ettei itseä lukitse pois palvelimelta.

- Root tai oletustili pitää sulkea. Ennen sitä palvelimelle tehdään uusi käyttäjä, jolle annetaan sudo-oikeudet.

- Softapäivitykset ennen käyttöä

Karvinen, T. 2017. First Steps on a New Virtual Private Server – an Example on DigitalOcean and Ubuntu 16.04 LTS. https://terokarvinen.com/2017/first-steps-on-a-new-virtual-private-server-an-example-on-digitalocean/

Lehto, S. 2022. Teoriasta käytäntöön pilvipalvelimen avulla. https://susannalehto.fi/2022/teoriasta-kaytantoon-pilvipalvelimen-avulla-h4/

## Tehtävä b

Yhdistin palvelimelle SSH:lla. Ensimmäiseksi haluan laittaa palomuurin päälle. Ufw ei ollut asennettuna, joten päivitin paketit ja asensin ufw:n. SSH liikenne on sallittu sisäänpäin, joten ei pitäisi lukittautua ulkopuolelle. 

        sudo apt update
        sudo apt upgrade
        sudo apt install ufw
        sudo ufw allow 22/tcp
        sudo ufw enable

![alt text](/Photos/h4-image-6.png)

Luodaan uusi käyttäjä

        sudo adduser joonas

Perl valittaa locale asetuksista, ignorataan viesti toistaiseksi

![alt text](/Photos/h4-image-7.png)

Loin salasanamanagerilla randomin 20 merkkisen salasanan ja laitoin sen käyttäjäni salasanaksi. Lisätään käyttäjä seuraavaksi sudo ja admin ryhmiin

        sudo adduser joonas sudo
        sudo adduser joonas adm
        sudo adduser joonas admin

Vaihdoin käyttäjää ja testasin sudoa.

![alt text](/Photos/h4-image-8.png)

Testasin, onnistuuko SSH uudelle käyttäjälle. Vaatii authorized keys -tiedoston kopioinnin uuden käyttäjän hakemistoon.

![alt text](/Photos/h4-image-9.png)

        su joonas
        mkdir /home/joonas/.ssh
        sudo cp /home/admin/.ssh/authorized_keys /home/joonas/.ssh/
        sudo chown joonas authorized_keys

Sen jälkeen pääsin kirjautumaan luodulla käyttäjällä

![alt text](/Photos/h4-image-10.png)

Tarkistetaan onko root-tili lukittu

        sudo passwd -S root

        root L 2025-10-06 0 99999 7 -1

L tarkoittaa että tili on lukittu.

https://askubuntu.com/questions/584847/how-do-i-check-if-my-root-password-login-is-disabled

Lukitsin myös oletuksena luodun tilin

        sudo usermod --lock admin

Ja disabloin root loginin ssh:lla. 

![sshd_config](/Photos/h4-image-11.png)

![sudoedit asetuksen muokkaus](/Photos/h4-image-12.png)

Näyttökaappauksissa näkyy asetuksenmuutos.

Korjataan vielä tuo locale error, 

        sudo dpkg-reconfigure locales

https://www.cyberciti.biz/faq/perl-warning-setting-locale-failed-in-debian-ubuntu/

## Tehtävä c - Asennetaan web palvelin

Päivitin paketit ja asensin Apache2:n

        sudo apt update
        sudo apt install apache2

Loin kansion, mistä Apache hakee name based virtual hostin

        cd /home/joonas
        mkdir sites
        cd sites
        mkdir frontpage
        cd frontpage
        echo "Hello world" >> index.html

Sitten päivitin asetustiedostot

        cd /etc/apache2/sites-available/
        sudo nano frontpage.conf

![alt text](/Photos/h4-image-13.png)

Otin default sivun pois käytöstä ja enabloin uuden sivun
        
        sudo a2dissite 000-default.conf
        sudo a2ensite frontpage.conf
        sudo systemctl restart apache2

![alt text](/Photos/h4-image-14.png)

Kokeilin curlin kanssa sivustoa, vielä antaa erroria. Error.logissa löytyi "permission denied" -viesti. 

Web-palvelin tarvitsee vielä oikeuksia käyttäjäni kotihakemistoon. Annetaan Read oikeudet komennolla: 

        sudo chmod go+r /home/joonas

Read oikeudet eivät riittäneet. Kokeilin antaa execute-oikeudet, ja poistin read-oikeudet, least privilege -periaatteen mukaisesti. 

        sudo chmod go-r /home/joonas
        sudo chmod go+x /home/joonas

![alt text](/Photos/h4-image-15.png)

Sitten vaadittiin vielä porttien avaukset virtuaalikoneen palomuuriin ja AWS:n security groupiin. Tarkistin syntaksin wikistä: https://www.linux.fi/wiki/Ufw

        sudo ufw allow 80

![alt text](/Photos/h4-image-16.png)

![alt text](/Photos/h4-image-17.png)

Allow all HTTP traffic... Tallennetaan muutokset

![alt text](/Photos/h4-image-18.png)

Nyt voin tervehtiä maailmaa millä tahansa koneella. Näyttökuva otettu Windows-koneeltani, vaikka kaikki muutokset serveriin on tehty Debian-virtuaalikoneella SSH-yhteyden yli.

![alt text](/Photos/h4-image-19.png)

Suljen EC2 instanssin, ettei se kuluta turhaan resursseja...

EC2 > Instances > Instance state > Stop instance


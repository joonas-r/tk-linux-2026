# h6 - TLS salaus

Tämä raportti on osa Tero Karvisen Linux-palvelimet -kurssia. Tehtävänanto sivustolla https://terokarvinen.com/linux-palvelimet/

## Tehtävä x: Lue ja tiivistä

Domainin hallinnan todistaminen tapahtuu siten, että Let's Encrypt CA (Certificate Authority) lähettää jonkin haasteen (challenge), jonka avulla palvelin voi todistaa hallitsevansa verkkosivustoa. Sen voi tehdä DNS-tietueen tai HTTP sivun avulla sen domainin alle, jota halutaan sertifioida. Jos haaste menee läpi, client saa oikeuden hallita sertifikaatteja tälle domain-nimelle. 
Saadakseen sertifikaatin, client lähettää CSR-pyynnön, jonka allekirjoitukset Let's Encrypt CA varmistaa. Jos allekirjoitukset ovat kunnossa, CA lähettää sertifikaatin takaisin clientille. Pyynnöt ovat tietenkin salattu julkisen avaimen kryptografialla. 
Varmeenteen kumoaminen (revocation) tapahtuu samalla tavalla. Client lähettää kumoamispyynnön CA:lle. Jos pyyntö on oikeutettu, sertifikaatti lisätään CA:n CRL-listaan (Certificate Revocation List), joka ilmoittaa muille, että se ei ole voimassa.

Lähteet:

Let's Encrypt. 2024: How It Works. Luettavissa: https://letsencrypt.org/how-it-works/

The Apache Software Foundation. 2025. Apache HTTP Server Version 2.4 Official Documentation: SSL/TLS Strong Encryption: How-To: Basic Configuration Example. Luettavissa: https://httpd.apache.org/docs/2.4/ssl/ssl_howto.html#configexample

#### 25.2.2026

## Tehtävä a: Let's encrypt

Kirjautuisin webbipalvelimelle SSH:lla ja asensin certbotin komennolla. 

    sudo apt install certbot
    sudo apt install python3-certbot-apache

Avasin ufw-palomuurista portin 443

    sudo ufw allow 443

Portti pitää avata myös AWS:n security groupista. 

![alt text](/Photos/h6-image.png)

Kun avasin portit, latasin netcatin jolla voin testata, onko portti auki. 

    sudo apt install netcat-traditional
    nc jons.fi 443

![alt text](/Photos/h6-image-1.png)

Connection refused. Voisin kokeilla käynnistää uudelleen palvelimen, ennen kuin alan tekemään mitään certbotilla. 

![alt text](/Photos/h6-image-2.png)

Huomasin, että osoite oli väärä. Www.jons.fi osoittaa oikeaan paikkaan. Sielläkin tulee Connection refused.

Kokeilin lisätä certbotilla SSL sertifikaattia. Tarkastin mitä certbotin komentoja voi käyttää man-komennolla, ja ajoin sitten certbotin. 

    man certbot
    sudo certbot --apache -d jons.fi,www.jons.fi

Lähde: man certbot, https://linuxcommandlibrary.com/man/certbot

![alt text](/Photos/h6-image-3.png)

Kokeilin sitten verkkosivustolta https:ää. Huomasin, että https://www.jons.fi toimii, mutta https://jons.fi ei toimi. Sama homma curl-komennon kanssa. Johtuu varmaankin aikaisemmin tehdyistä DNS-asetuksista. Http:lla yhteys toimii edelleen.  

![alt text](/Photos/h6-image-4.png)

#### 1.3.2026

Päivitin DNS tietueet kuntoon.

Lähtötilanne:

![alt text](/Photos/h6-image-5.png)

Muutosten jälkeen: 

![alt text](/Photos/h6-image-6.png)

Muutoksen jälkeen nettisivu toimi kaikista osoitteista: www.jons.fi, jons.fi, kotisivu.jons.fi

## Tehtävä b: A-rating

Siirryin sivustolle https://www.ssllabs.com/ssltest/ ja testasin jons.fi domainin SSL/TLS configuraatiota. Overall rating: A.

![alt text](/Photos/h6-image-7.png)


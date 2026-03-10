# Nimipalvelun käyttäminen

Tehtävänanto sivustolla https://terokarvinen.com/linux-palvelimet/ kohdassa h5 (Karvinen 2026, Luku h6).

Olen jo aiemmin vuokrannut vuodeksi jons.fi -domainnimen. Todennäköisesti vuokraan jonkin toisenkin nimen jossain vaiheessa, mutta käytän tässä harjoituksessa kyseistä domainnimeä. Olen vuokrannut nimen joker.com -sivustolta. 

## AWS ja nimipalvelu

Amazon tarjoaa omaa Amazon Route 53 -palvelua DNS:n hallintaan ja verkkoliikenteen ohjaamiseen. 

Tämä palvelu ei ole "Free Tier" -tilien käytettävissä, joten en voi hyödyntää sitä tässä tehtävässä. 

Löysin kuitenkin dokumentaation siitä, miten käyttää Amazon Route 53:a ulkoisen rekisteröijän domainin kanssa. https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/migrate-dns-domain-in-use.html (AWS Docs s.a.)

IP-osoite vaihtuu automaattisesti, kun EC2-instanssin sammuttaa ja sulkee. IP-osoitteen voi kuitenkin säilyttää, jos käyttää AWS:n elastista IP-osoitetta (tai mahdollisesti IPv6 osoitetta). Nämä pitää määrittää virtuaalipalvelimen luonnin vaiheessa, kun määrittää mihin VPC:hen (Virtual Private Cloud) ja aliverkkoon se asetetaan. Jos palvelinta ei sammuteta, IP-osoite ei luonnollisesti vaihdu, ja reboot ei vaihda IP-osoitetta.

## Tehtävä A - "Laita julkinen nimi osoittamaan omaan koneeseesi"

Kirjauduin joker.com:iin ja kävin muuttamassa jons.fi domainin DNS-kirjauksia. 

![alt text](/Photos/h5-image.png)

Nimeksi www.jons.fi ja osoite on virtuaalikoneen IPv4 osoite.

![alt text](/Photos/h5-image-1.png)

Nyt virtuaalipalvelimeen pääsee osoitteella www.jons.fi

## Tehtävä B - "Tee kaksi uutta alidomainia, jotka osoittavat omaan koneeseesi"

Tein URL redirectin, joka osoittaa jons.fi osoitteen www.jons.fi osoitteeseen. 

![alt text](/Photos/h5-image-2.png)  

Tein vielä toisen alidomainin kotisivu.jons.fi, tällä kertaa CNAME tietueena, testimielessä. Huomasin, että tällä tavalla sivuston nimi ei muuttunut www-osoitteeseen, vaan osoitepalkissa on edelleen kotisivu.jons.fi.

![alt text](/Photos/h5-image-3.png)

Muutan tämänkin redirectiksi. Mielestäni on parempi, että osoitepalkilla on aina sama nimi, kun sivustolla käy.

Päivitin sivustoa, jotta se sanoo jotain muutakin, kuin "Hello world"

## Tehtävä C - "Tutki jonkin nimen DNS-tietoja 'host' ja 'dig' -komennoilla"

### "Oma domain-nimesi. Vertaa tuloksia nimen vuokraajan (namecheap.com, name.com...) weppiliittymässä näkyviin asetuksiin."

Asensin dnsutils-sovelluksen jonka mukana host ja dig tulevat

    sudo apt install dnsutils

![alt text](/Photos/h5-image-4.png)

Kuvassa näkyy luomani A-tietue sekä tiedot käyttämistäni sähköpostiservereistä.

### "Jonkin pikkuyrityksen, kerhon tai yksittäisen henkilön weppisivut."

Kokeilin erään pienyrityksen kotisivuja.

    dig bellearti.fi
    host bellearti.fi

![alt text](/Photos/h5-image-5.png)

### "Jonkin suuren ja kaikkien tunteman palvelun tiedot."

Yle.fi dig ja host

![alt text](/Photos/h5-image-6.png)

Dig-komennosta huomaa yle.fi domainilla olevan 4 eri IPv4 osoitetta. Host-komennolla näemme myös IPv6 osoitteet, joita on 8 kappaletta. Sähköposti ohjataan aspmx.l.google.com kautta, ja tälle palvelulle on 5 eri vaihtoehtoista osoitetta. Vaihdoehtoiset osoitteet ovat redundanssia eli käyttövarmuutta ja kuormantasausta varten. Numero ennen osoitetta merkkaa priorityn. (Zomaya 2020.)

![alt text](/Photos/h5-image-7.png)

Dig ANY komennolla näemme myös TXT-tietueita. Näillä tekstitietueilla voidaan lisätä ylimääräistä tietoa liittyen verkkotunnukseen. Sillä voidaan esim. varmentaa verkkotunnuksen omistajuus ja tunnistautua ulkopuoliseen palveluun. (Zomaya 2020.)

    dig yle.fi ANY

## Tehtävä D - SPF DMARC

Haaga-Helian SPF TXT-tietue 

"v=spf1 " "ip4:193.166.0.0/24 ip4:193.166.13.6 ip4:193.166.9.48/30 ip4:193.166.11.88 ip4:94.237.113.214 ip4:80.69.172.127 include:spf1.lnmailer.net include:spf2.lnmailer.net include:_spf.haaga-helia.fi include:spf.protection.outlook.com " "include:spf.mailjet.com " "include:mail.webropolsurveys.com  include:sendgrid.net include:hosted-at.csc.fi -all"

SPF-tietue kertoo IPv4-osoitteet, jotka saavat lähettää viestejä domainin puolesta. Include:lla osoitetaan, mitkä muut domainit saavat lähettää viestejä tämän domainin puolesta. All-lippu kertoo, että mitkään muut, kuin SPF-tietueessa mainitut osoitteet ja domainit eivät saa lähettää viestejä tämän domainin puolesta. (CloudFlare s.a.)   

SPF mahdollistaa ulkoisen sähköpostipalvelun käytön omalla domain-nimellä ilman, että kaikki viestit menisivät automaattisesti roskapostiin. (CloudFlare s.a.)

Haaga-Helian DMARC TXT-tietue.

![alt text](/Photos/h5-image-8.png)

DMARCia käytetään lähettäjän varmistamiseen ja viestin luotettavuuden varmistamiseen (Dmarc s.a). 

    v=DMARC1; p=quarantine; rua=mailto:mhxubk5a@ag.eu.dmarcadvisor.com

v = Protokollan versio

p = Käytäntö "huonoille" viesteille. Tässä tapauksessa nämä menevät karanteeniin. 

rua = Osoite, johon raportoidaan "huonoista viesteistä"

(Dmarc s.a.)

Lähdeluettelo:

AWS Docs s.a. Making Route 53 the DNS service for a domain that's in use. https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/migrate-dns-domain-in-use.html

Zomaya, D. 2020. DNS Records Explained (with Examples). https://www.cbtnuggets.com/blog/technology/system-admin/dns-records-explained-with-examples

CloudFlare s.a. What is a DNS SPF record. https://www.cloudflare.com/learning/dns/dns-records/dns-spf-record/

DMarc s.a. Overview. https://dmarc.org/overview/
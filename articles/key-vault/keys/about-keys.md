---
title: Over Azure Key Vault sleutels-Azure Key Vault
description: Overzicht van Azure Key Vault REST interface en Details voor ontwikkel aars voor sleutels.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 3d89275e1418035fed8aad3ffddd8def2c1d59ce
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81686062"
---
# <a name="about-azure-key-vault-keys"></a>Over Azure Key Vault sleutels

Azure Key Vault ondersteunt meerdere sleutel typen en-algoritmen en maakt het gebruik van HSM (Hardware Security modules) mogelijk voor sleutels met een hoge waarde.

Cryptografische sleutels in Key Vault worden weer gegeven als JSON Web Key [JWK]-objecten. De specificaties voor het ondertekenen en versleutelen van het object voor de JavaScript Object Notation (JSON) en Java script-objecten zijn:

-   [JSON-websleutel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key)  
-   [JSON-webencryptie (JWE)](http://tools.ietf.org/html/draft-ietf-jose-json-web-encryption)  
-   [JSON-webalgoritmen (JWA)](http://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms)  
-   [JSON Web Signature (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature) 

De basis-JWK/JWA-specificaties worden ook uitgebreid om sleutel typen die uniek zijn voor de Key Vault-implementatie in te scha kelen. Voor beelden van het importeren van sleutels met een HSM die specifiek is voor een leverancier, maakt het beveiligen van sleutels mogelijk die alleen kunnen worden gebruikt in Key Vault Hsm's. 

Azure Key Vault ondersteunt zowel zachte als harde sleutels:

- **"Zachte" sleutels**: een sleutel die in software wordt verwerkt door Key Vault, maar is versleuteld met een systeem sleutel die zich in een HSM bevindt. Clients kunnen een bestaande RSA-of EC-sleutel (elliptische curve) importeren of een aanvraag indienen om er een te Key Vault genereren.
- **"Hard" sleutels**: een sleutel die is verwerkt in een HSM (Hardware Security module). Deze sleutels zijn beveiligd in een van de Key Vault HSM-beveiligings werelden (er is één beveiligings wereld per Geografie om isolatie te behouden). Clients kunnen een RSA-of EC-sleutel importeren in een zacht formulier of door vanaf een compatibel HSM-apparaat te exporteren. Clients kunnen ook Key Vault aanvragen om een sleutel te genereren. Met dit sleutel type voegt u het key_hsm kenmerk toe aan de JWK om het HSM-sleutel materiaal te kunnen dragen.

Zie het [vertrouwens centrum van Microsoft Azure](https://azure.microsoft.com/support/trust-center/privacy/) voor meer informatie over geografische grenzen  

## <a name="cryptographic-protection"></a>Cryptografische beveiliging

Key Vault ondersteunt alleen de sleutels RSA en elliptische curve. 

-   **EC**: "zacht" elliptische curve sleutel.
-   **EC-HSM**: "hard" elliptische curve sleutel.
-   **RSA**: ' soft ' RSA-sleutel.
-   **RSA-HSM**: "hard" RSA-sleutel.

Key Vault ondersteunt RSA-sleutels met een grootte van 2048, 3072 en 4096. Key Vault ondersteunt de sleutel typen elliptische curve P-256, P-384, P-521 en P-256 kB (SECP256K1).

De cryptografische modules die Key Vault gebruikt, of HSM of software, FIPS (Federal Information Processing Standards) zijn gevalideerd. U hoeft niets te doen om uit te voeren in de FIPS-modus. Sleutels **die zijn gemaakt** of **GEÏMPORTEERD** als met HSM beveiligd, worden verwerkt in een HSM, gevalideerd op FIPS 140-2 level 2. Sleutels **die zijn gemaakt** of **geïmporteerd** als software-beveiligd, worden verwerkt in cryptografische modules die zijn gevalideerd voor FIPS 140-2 level 1.

###  <a name="ec-algorithms"></a>EC-algoritmen
 De volgende algoritme-id's worden ondersteund met EC-en EC-HSM-sleutels in Key Vault. 

#### <a name="curve-types"></a>Curve typen

-   **P-256** -de NIST-curve p-256, gedefinieerd op [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256 KB** : de seconde curve SECP256K1, gedefinieerd op [SEC 2: aanbevolen elliptische domein parameters](https://www.secg.org/sec2-v2.pdf).
-   **P-384** -de NIST-curve p-384, gedefinieerd op [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** -de NIST-curve p-521, gedefinieerd op [DSS FIPS pub 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>ONDERTEKENEN/CONTROLEREN

-   **ES256** -ECDSA voor SHA-256-samen vattingen en sleutels die zijn gemaakt met curve P-256. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** -ECDSA voor SHA-256-samen vattingen en sleutels gemaakt met bocht P-256 KB. Dit algoritme is in afwachting van de standaardisatie.
-   **ES384** -ECDSA voor SHA-384-samen vattingen en sleutels die zijn gemaakt met curve P-384. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** -ECDSA voor SHA-512-samen vattingen en sleutels die zijn gemaakt met curve P-521. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).

###  <a name="rsa-algorithms"></a>RSA-algoritmen  
 De volgende algoritme-id's worden ondersteund met RSA-en RSA-HSM-sleutels in Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/SLEUTEL UITPAKKEN, VERSLEUTELEN/ONTSLEUTELEN

-   **RSA1_5** -RSAES-PKCS1-V1_5 [RFC3447] sleutel versleuteling  
-   **RSA-OAEP** -RSAES met behulp van optimale geocoderings opvulling (OAEP) [RFC3447], waarbij de standaard parameters worden opgegeven door RFC 3447 in sectie A. 2.1. Deze standaard parameters maken gebruik van een hash-functie van SHA-1 en een masker functie voor het genereren van MGF1 met SHA-1.  

#### <a name="signverify"></a>ONDERTEKENEN/CONTROLEREN

-   **PS256** -ONDERTEKENINGSMETHODE RSASSA-PSS met sha-256 en MGF1 met sha-256, zoals beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** -ONDERTEKENINGSMETHODE RSASSA-PSS met sha-384 en MGF1 met sha-384, zoals beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** -ONDERTEKENINGSMETHODE RSASSA-PSS met sha-512 en MGF1 met sha-512, zoals beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** -ONDERTEKENINGSMETHODE RSASSA-PKCS-V1_5 met SHA-256. De waarde van de door de toepassing geleverde Digest moet worden berekend met SHA-256 en moet 32 bytes lang zijn.  
-   **RS384** -ONDERTEKENINGSMETHODE RSASSA-PKCS-V1_5 met SHA-384. De waarde van de door de toepassing geleverde Digest moet worden berekend met SHA-384 en moet 48 bytes lang zijn.  
-   **RS512** -ONDERTEKENINGSMETHODE RSASSA-PKCS-V1_5 met SHA-512. De waarde van de door de toepassing geleverde Digest moet worden berekend met SHA-512 en moet 64 bytes lang zijn.  
-   **RSNULL** -Zie [RFC2437], een gespecialiseerde use-case voor het inschakelen van bepaalde TLS-scenario's.  

##  <a name="key-operations"></a>Belang rijke bewerkingen

Key Vault ondersteunt de volgende bewerkingen op belang rijke objecten:  

-   **Maken**: Hiermee kan een client een sleutel maken in Key Vault. De waarde van de sleutel wordt gegenereerd door Key Vault en opgeslagen en wordt niet vrijgegeven aan de client. Er kunnen asymmetrische sleutels worden gemaakt in Key Vault.  
-   **Importeren**: een client kan een bestaande sleutel importeren in Key Vault. Asymmetrische sleutels kunnen worden geïmporteerd in Key Vault met behulp van een aantal verschillende pakket methoden in een JWK-constructie. 
-   **Update**: Hiermee staat u een client met voldoende machtigingen toe om de meta gegevens (sleutel kenmerken) te wijzigen die zijn gekoppeld aan een sleutel die eerder is opgeslagen in Key Vault.  
-   **Verwijderen**: Hiermee staat u een client met voldoende machtigingen toe om een sleutel uit Key Vault te verwijderen.  
-   **Lijst**: een client kan alle sleutels in een bepaalde Key Vault weer geven.  
-   **Lijst versies**: een client kan alle versies van een bepaalde sleutel in een bepaalde Key Vault weer geven.  
-   **Get**: Hiermee kan een client de open bare delen van een bepaalde sleutel in een Key Vault ophalen.  
-   **Backup**: exporteert een sleutel in een beveiligd formulier.  
-   **Herstellen**: Hiermee wordt een eerder gemaakte back-upsleutel geïmporteerd.  

Zie voor meer informatie [belang rijke bewerkingen in de referentie Key Vault rest API](/rest/api/keyvault).  

Zodra een sleutel is gemaakt in Key Vault, kunnen de volgende cryptografische bewerkingen worden uitgevoerd met behulp van de sleutel:  

-   **Ondertekenen en controleren**: alleen deze bewerking is ' Sign hash ' of ' verify hash ' omdat Key Vault geen ondersteuning biedt voor hashing van inhoud als onderdeel van het maken van een hand tekening. Toepassingen moeten de gegevens die lokaal worden ondertekend hashen en vervolgens aanvragen dat Key Vault de hash ondertekenen. Verificatie van ondertekende hashes wordt ondersteund als een gebruiks vriendelijke bewerking voor toepassingen die mogelijk geen toegang hebben tot het sleutel materiaal van [Public]. Controleer voor de beste toepassings prestaties of de bewerkingen lokaal worden uitgevoerd.  
-   **Sleutel versleuteling/-terugloop**: een sleutel die is opgeslagen in Key Vault kan worden gebruikt voor het beveiligen van een andere sleutel, meestal een symmetrische coderings sleutel voor inhoud (CEK). Wanneer de sleutel in Key Vault asymmetrisch is, wordt sleutel versleuteling gebruikt. RSA-OAEP en de WRAPKEY/sleutel uitpakken-bewerkingen zijn bijvoorbeeld gelijk aan versleutelen/ontsleutelen. Wanneer de sleutel in Key Vault symmetrisch is, wordt sleutel terugloop gebruikt. Bijvoorbeeld: AES-KW. De WRAPKEY-bewerking wordt ondersteund als gemak voor toepassingen die mogelijk geen toegang hebben tot het sleutel materiaal van [Public]. Voor de beste prestaties van toepassingen moeten WRAPKEY-bewerkingen lokaal worden uitgevoerd.  
-   **Versleutelen en ontsleutelen**: een sleutel die is opgeslagen in Key Vault kan worden gebruikt voor het versleutelen of ontsleutelen van één gegevens blok. De grootte van het blok wordt bepaald door het sleutel type en het geselecteerde versleutelings algoritme. De versleutelings bewerking is beschikbaar voor het gemak, voor toepassingen die mogelijk geen toegang tot het sleutel materiaal van [Public] hebben. Voor de beste prestaties van toepassingen moeten versleutelings bewerkingen lokaal worden uitgevoerd.  

Hoewel WRAPKEY/sleutel uitpakken met behulp van asymmetrische sleutels wellicht overbodig lijkt (omdat de bewerking gelijk is aan versleutelen/ontsleutelen), is het gebruik van afzonderlijke bewerkingen belang rijk. Het onderscheid biedt semantische en autorisatie schei ding van deze bewerkingen en consistentie wanneer andere sleutel typen door de service worden ondersteund.  

Key Vault biedt geen ondersteuning voor EXPORT bewerkingen. Zodra een sleutel is ingericht in het systeem, kan deze niet worden geëxtraheerd of het sleutel materiaal is gewijzigd. Gebruikers van Key Vault kunnen echter hun sleutel voor andere gebruiks voorbeelden vereisen, bijvoorbeeld nadat deze is verwijderd. In dit geval kunnen ze gebruikmaken van de back-up-en herstel bewerkingen voor het exporteren/importeren van de sleutel in een beveiligd formulier. Sleutels die zijn gemaakt met de back-upbewerking, kunnen niet buiten Key Vault worden gebruikt. De IMPORT bewerking kan ook worden gebruikt voor meerdere Key Vault exemplaren.  

Gebruikers kunnen de cryptografische bewerkingen die Key Vault ondersteunt per sleutel beperken met behulp van de eigenschap key_ops van het object JWK.  

Zie [JSON Web Key (JWK) (Engelstalig)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)voor meer informatie over JWK-objecten.  

## <a name="key-attributes"></a>Sleutel kenmerken

Naast de instellingen voor sleutels kunnen de volgende kenmerken worden opgegeven. In een JSON-aanvraag zijn de kenmerken tref woord en accolades, {}} vereist, zelfs als er geen kenmerken zijn opgegeven.  

- *ingeschakeld*: Boole, optioneel, standaard waarde is **True**. Hiermee geeft u op of de sleutel is ingeschakeld en bruikbaar is voor cryptografische bewerkingen. Het kenmerk *enabled* wordt gebruikt in combi natie met *NBF* en *exp*. Wanneer een bewerking plaatsvindt tussen *NBF* en *exp*, wordt deze alleen toegestaan als *ingeschakeld* is ingesteld op **waar**. Bewerkingen buiten het venster *NBF* / *exp* worden automatisch niet toegestaan, met uitzonde ring van bepaalde bewerkings typen onder [bepaalde voor waarden](#date-time-controlled-operations).
- *NBF*: IntDate, optioneel, standaard is nu. Het kenmerk *NBF* (niet voor) geeft aan hoe lang de sleutel niet moet worden gebruikt voor cryptografische bewerkingen, met uitzonde ring van bepaalde typen bewerkingen onder [bepaalde voor waarden](#date-time-controlled-operations). Voor de verwerking van het *NBF* -kenmerk moet de huidige datum/tijd na of gelijk zijn aan de niet-voor-datum/-tijd die wordt vermeld in het *NBF* -kenmerk. Key Vault kan worden geboden voor sommige kleine Leeway, normaal gesp roken niet meer dan een paar minuten, om rekening te trekken met Clock scheefheid. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *exp*: IntDate, optioneel, standaard is "permanent". Met het kenmerk *exp* (verval tijd) wordt de verval tijd van of waarna de sleutel mag niet worden gebruikt voor een cryptografische bewerking, behalve voor bepaalde typen bewerkingen onder [bepaalde voor waarden](#date-time-controlled-operations). Voor de verwerking van het kenmerk *exp* moet de huidige datum/tijd vóór de verval datum/-tijd van het kenmerk *exp* zijn. Key Vault kan worden geboden voor sommige kleine Leeway, meestal niet meer dan een paar minuten, om rekening te trekken met Clock scheefheid. De waarde moet een getal zijn dat een IntDate-waarde bevat.  

Er zijn aanvullende alleen-lezen kenmerken die zijn opgenomen in een antwoord dat sleutel kenmerken bevat:  

- *gemaakt*: IntDate, optioneel. Het kenmerk *gemaakt* geeft aan wanneer deze versie van de sleutel is gemaakt. De waarde is null voor sleutels die zijn gemaakt vóór het toevoegen van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *bijgewerkt*: IntDate, optioneel. Het kenmerk *bijgewerkt* geeft aan wanneer deze versie van de sleutel is bijgewerkt. De waarde is null voor de sleutels die voor het laatst zijn bijgewerkt vóór het toevoegen van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.  

Zie voor meer informatie over IntDate en andere gegevens typen [info over sleutels, geheimen en certificaten: [gegevens typen](../general/about-keys-secrets-certificates.md#data-types).

### <a name="date-time-controlled-operations"></a>Datum-en tijd beheer bewerkingen

Nog geen geldige en verlopen sleutels, buiten het venster *NBF* / *exp* , werken voor **ontsleutelen**, **uitpakken**en **controleren** (niet 403, verboden). De reden voor het gebruik van de niet-geldige status is het toestaan dat een sleutel wordt getest voordat het productie gebruik wordt uitgevoerd. De motivering voor het gebruik van de verlopen status is het toestaan van herstel bewerkingen voor gegevens die zijn gemaakt toen de sleutel geldig was. U kunt ook de toegang tot een sleutel met behulp van Key Vault-beleid uitschakelen of door het *ingeschakelde* sleutel kenmerk op **Onwaar**bij te werken.

Zie [gegevens typen](../general/about-keys-secrets-certificates.md#data-types)voor meer informatie over gegevens typen.

Zie de [JSON Web Key (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)voor meer informatie over andere mogelijke kenmerken.

## <a name="key-tags"></a>Sleutel Tags

U kunt aanvullende toepassingsspecifieke meta gegevens opgeven in de vorm van tags. Key Vault ondersteunt Maxi maal 15 Tags, die elk een 256-teken naam en een 256-teken waarde kunnen bevatten.  

>[!Note]
>Labels kunnen worden gelezen door een beller als ze de *lijst* of *machtiging* voor dat object type (sleutels, geheimen of certificaten) hebben.

##  <a name="key-access-control"></a>Toegangsbeheer voor sleutels

Toegangs beheer voor sleutels die door Key Vault worden beheerd, wordt op het niveau van een Key Vault aangegeven dat fungeert als container met sleutels. Het toegangscontrole beleid voor sleutels verschilt van het toegangs beheer beleid voor geheimen in hetzelfde Key Vault. Gebruikers kunnen een of meer kluizen maken voor het bewaren van sleutels en zijn vereist voor het bijhouden van de juiste segmentatie en het beheer van sleutels. Toegangs beheer voor sleutels is onafhankelijk van toegangs beheer voor geheimen.  

De volgende machtigingen kunnen worden toegekend, op basis van gebruikers-en service-principals, in de toegangscontrole vermelding voor sleutels op een kluis. Deze machtigingen spie gelen de bewerkingen die zijn toegestaan voor een sleutel object.  Het verlenen van toegang aan een Service-Principal in de sleutel kluis is een eenmalige-bewerking. deze blijft hetzelfde voor alle Azure-abonnementen. U kunt deze gebruiken om zoveel certificaten te implementeren als u wilt. 

- Machtigingen voor sleutel beheer bewerkingen
  - *ophalen*: het open bare gedeelte van een sleutel en de bijbehorende kenmerken lezen
  - *lijst*: een lijst met de sleutels of versies van een sleutel die is opgeslagen in een sleutel kluis
  - *Update*: de kenmerken voor een sleutel bijwerken
  - *maken*: nieuwe sleutels maken
  - *importeren*: een sleutel importeren in een sleutel kluis
  - *verwijderen*: het sleutel object verwijderen
  - *herstellen*: een verwijderde sleutel herstellen
  - *back-up*: back-up maken van een sleutel in een sleutel kluis
  - *herstellen*: een back-up van een sleutel herstellen naar een sleutel kluis

- Machtigingen voor cryptografische bewerkingen
  - *ontsleutelen*: de sleutel voor het opheffen van de beveiliging van een reeks bytes gebruiken
  - *versleutelen*: de sleutel gebruiken voor het beveiligen van een wille keurige reeks bytes
  - *sleutel uitpakken*: gebruik de-sleutel voor het opheffen van de beveiliging van verpakte symmetrische sleutels
  - *wrapKey*: de sleutel gebruiken voor het beveiligen van een symmetrische sleutel
  - *controleren*: de sleutel gebruiken om samen vattingen te controleren  
  - *ondertekenen*: de sleutel gebruiken om samen vattingen te ondertekenen
    
- Machtigingen voor bevoegde bewerkingen
  - *leegmaken*: een verwijderde sleutel leegmaken (permanent verwijderen)

Voor meer informatie over het werken met sleutels raadpleegt u [belang rijke bewerkingen in de naslag informatie over Key Vault rest API](/rest/api/keyvault). Zie voor meer informatie over het instellen van machtigingen [-kluizen-maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [kluizen-toegangs beleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="next-steps"></a>Volgende stappen

- [Over Key Vault](../general/overview.md)
- [Over sleutels, geheimen en certificaten](../general/about-keys-secrets-certificates.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

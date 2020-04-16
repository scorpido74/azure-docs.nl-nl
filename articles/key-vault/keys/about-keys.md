---
title: Informatie over Azure Key Vault-sleutels - Azure Key Vault
description: Overzicht van de Azure Key Vault REST-interface en ontwikkelaarsgegevens voor sleutels.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: keys
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 1c12135ec6e5a0f4de1fdd46134a056447d3c331
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424235"
---
# <a name="about-azure-key-vault-keys"></a>Informatie over Azure Key Vault-sleutels

Azure Key Vault stelt Microsoft Azure-toepassingen en gebruikers in staat om sleutels op te slaan en te gebruiken. Het ondersteunt meerdere sleuteltypen en algoritmen en maakt het gebruik van Hardware Security Modules (HSM) mogelijk voor sleutels met een hoge waarde. 

Zie Wat is Azure Key Vault voor meer algemene informatie over Key [Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

De volgende secties bieden algemene informatie die van toepassing is op de implementatie van de Key Vault-service.

### <a name="supporting-standards"></a>Ondersteunende normen

De SPECIFICATIES JavaScript Object Notation (JSON) en JavaScript Object Signing and Encryption (JOSE) zijn belangrijke achtergrondinformatie.  

-   [JSON-websleutel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON-webversleuteling (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON Web Algoritmen (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON-webhandtekening (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

### <a name="data-types"></a>Gegevenstypen

Raadpleeg de JOSE-specificaties voor relevante gegevenstypen voor sleutels, versleuteling en ondertekening.  

-   **algoritme** - een ondersteund algoritme voor een sleutelbewerking, bijvoorbeeld RSA1_5  
-   **ciphertext-waarde** - cipher tekst octetten, gecodeerd met Base64URL  
-   **digest-value** - de uitvoer van een hash-algoritme, gecodeerd met Base64URL  
-   **type sleutel** - een van de ondersteunde sleuteltypen, bijvoorbeeld RSA (Rivest-Shamir-Adleman).  
-   **plaintext-waarde** - plaintext octetten, gecodeerd met Base64URL  
-   **handtekeningwaarde** - uitvoer van een handtekeningalgoritme, gecodeerd met Base64URL  
-   **base64URL** - een Base64URL [RFC4648] gecodeerde binaire waarde  
-   **boolean** - waar of onwaar  
-   **Identiteit** - een identiteit van Azure Active Directory (Azure AD).  
-   **IntDate** - een JSON decimale waarde die het aantal seconden van 1970-01-01T0:0:0Z UTC vertegenwoordigt tot de opgegeven UTC-datum/-tijd. Zie RFC3339 voor meer informatie over datum/tijden, in het algemeen en UTC in het bijzonder.  

### <a name="objects-identifiers-and-versioning"></a>Objecten, id's en versiebeheer

Objecten die zijn opgeslagen in Key Vault worden geversioneerd wanneer een nieuwe instantie van een object wordt gemaakt. Aan elke versie is een unieke id en URL toegewezen. Wanneer een object voor het eerst wordt gemaakt, krijgt het een unieke versie-id en wordt het gemarkeerd als de huidige versie van het object. Het maken van een nieuwe instantie met dezelfde objectnaam geeft het nieuwe object een unieke versie-id, waardoor het de huidige versie wordt.  

Objecten in Key Vault kunnen worden aangepakt met de huidige id of een versiespecifieke id. Als u bijvoorbeeld een sleutel `MasterKey`met de naam krijgt, zorgt het uitvoeren van bewerkingen met de huidige id ervoor dat het systeem de laatst beschikbare versie gebruikt. Door bewerkingen uit te voeren met de versiespecifieke id, wordt het systeem die specifieke versie van het object gebruikt.  

Objecten worden uniek geïdentificeerd in Key Vault met behulp van een URL. Geen twee objecten in het systeem hebben dezelfde URL, ongeacht de geolocatie. De volledige URL van een object wordt object-id genoemd. De URL bestaat uit een voorvoegsel dat het sleutelkluis, objecttype, de door de gebruiker opgegeven objectnaam en een objectversie identificeert. De objectnaam is hoofdletterongevoelig en onveranderlijk. Id's die de objectversie niet bevatten, worden base-id's genoemd.  

Zie [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md) voor meer informatie

Een object-id heeft de volgende algemene indeling:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Waar:  

|||  
|-|-|  
|`keyvault-name`|De naam voor een sleutelkluis in de Microsoft Azure Key Vault-service.<br /><br /> Key Vault-namen worden door de gebruiker geselecteerd en zijn wereldwijd uniek.<br /><br /> De naam Key Vault moet een tekenreeks met 3-24 tekens zijn, die alleen 0-9, a-z, A-Z en -bevat.|  
|`object-type`|Het type object, ofwel "sleutels" of "geheimen".|  
|`object-name`|An `object-name` is een door de gebruiker opgegeven naam en moet uniek zijn in een Key Vault. De naam moet een tekenreeks van 1-127 tekens zijn, die slechts 0-9, a-z, A-Z en -bevat.|  
|`object-version`|An `object-version` is een systeemgegenereerde, 32-tekentekenreeks-id die optioneel wordt gebruikt om een unieke versie van een object aan te pakken.|  

## <a name="key-vault-keys"></a>Key Vault-toetsen

### <a name="keys-and-key-types"></a>Sleutels en sleuteltypen

Cryptografische sleutels in Key Vault worden weergegeven als JSON Web Key [JWK]-objecten. De basis JWK/JWA specificaties zijn ook uitgebreid om belangrijke types die uniek zijn voor de Key Vault implementatie mogelijk te maken. Als u bijvoorbeeld sleutels importeert met een specifieke verpakking van HSM-leveranciers, u sleutels beveiligen die alleen in Key Vault HSM's kunnen worden gebruikt.  

- **"Zachte" sleutels**: Een sleutel verwerkt in software door Key Vault, maar wordt in rust versleuteld met behulp van een systeemsleutel die zich in een HSM bevindt. Clients kunnen een bestaande RSA- of EC-toets (Elliptic Curve) importeren of vragen dat Key Vault er een genereert.
- **"Harde" toetsen**: Een sleutel verwerkt in een HSM (Hardware Security Module). Deze sleutels zijn beschermd in een van de Key Vault HSM Security Worlds (er is één Security World per geografie om isolatie te behouden). Clients kunnen een RSA- of EC-sleutel importeren, in zachte vorm of door te exporteren vanaf een compatibel HSM-apparaat. Klanten kunnen Key Vault ook vragen om een sleutel te genereren. Dit sleuteltype voegt het key_hsm attribuut toe aan het JWK verkrijgen om het HSM-sleutelmateriaal te dragen.

     Zie Microsoft Azure Trust [Center](https://azure.microsoft.com/support/trust-center/privacy/) voor meer informatie over geografische grenzen  

Key Vault ondersteunt alleen RSA- en Elliptic Curve-toetsen. 

-   **EC**: "Soft" Elliptic Curve key.
-   **EC-HSM**: "Harde" elliptische curve sleutel.
-   **RSA**: "Soft" RSA toets.
-   **RSA-HSM**: "Harde" RSA-toets.

Key Vault ondersteunt RSA-toetsen in de maten 2048, 3072 en 4096. Key Vault ondersteunt Elliptic Curve-sleuteltypen P-256, P-384, P-521 en P-256K (SECP256K1).

### <a name="cryptographic-protection"></a>Cryptografische bescherming

De cryptografische modules die Key Vault gebruikt, of het nu HSM of software is, zijn FIPS (Federal Information Processing Standards) gevalideerd. U hoeft niets speciaals te doen om in de FIPS-modus te worden uitgevoerd. Sleutels die zijn **gemaakt** of **geïmporteerd** als HSM-beveiligd, worden verwerkt in een HSM, gevalideerd naar FIPS 140-2 Level 2. Sleutels **die zijn gemaakt** of **geïmporteerd** als softwarebeveiligd, worden verwerkt in cryptografische modules die zijn gevalideerd naar FIPS 140-2 Niveau 1. Zie [Sleutels en sleuteltypen](#keys-and-key-types)voor meer informatie.

###  <a name="ec-algorithms"></a>EG-algoritmen
 De volgende algoritme-id's worden ondersteund met EC- en EC-HSM-sleutels in Key Vault. 

#### <a name="curve-types"></a>Curvetypen

-   **P-256** - De NIST-curve P-256, gedefinieerd bij [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-256K** - De SEC-curve SECP256K1, gedefinieerd bij [SEC 2: Aanbevolen elliptische curvedomeinparameters](https://www.secg.org/sec2-v2.pdf).
-   **P-384** - De NIST-curve P-384, gedefinieerd bij [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).
-   **P-521** - De NIST-curve P-521, gedefinieerd bij [DSS FIPS PUB 186-4](https://nvlpubs.nist.gov/nistpubs/FIPS/NIST.FIPS.186-4.pdf).

#### <a name="signverify"></a>ONDERTEKENEN/VERIFIËREN

-   **ES256** - ECDSA voor SHA-256 digests en toetsen gemaakt met curve P-256. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES256K** - ECDSA voor SHA-256 digests en toetsen gemaakt met curve P-256K. Dit algoritme is in afwachting van standaardisatie.
-   **ES384** - ECDSA voor SHA-384 digests en toetsen gemaakt met curve P-384. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **ES512** - ECDSA voor SHA-512-samenvattingen en toetsen gemaakt met curve P-521. Dit algoritme wordt beschreven op [RFC7518](https://tools.ietf.org/html/rfc7518).


###  <a name="rsa-algorithms"></a>RSA-algoritmen  
 De volgende algoritme-id's worden ondersteund met RSA- en RSA-HSM-sleutels in Key Vault.  

#### <a name="wrapkeyunwrapkey-encryptdecrypt"></a>WRAPKEY/UNWRAPKEY, VERSLEUTELEN/DECODEREN

-   **RSA1_5** - RSAES-PKCS1-V1_5 [RFC3447] sleutelversleuteling  
-   **RSA-OAEP** - RSAES met behulp van Optimal Asymmetric Encryption Padding (OAEP) [RFC3447], met de standaardparameters die zijn opgegeven door RFC 3447 in sectie A.2.1. Deze standaardparameters gebruiken een hash-functie van SHA-1 en een maskergeneratiefunctie van MGF1 met SHA-1.  

#### <a name="signverify"></a>ONDERTEKENEN/VERIFIËREN

-   **PS256** - RSASSA-PSS met SHA-256 en MGF1 met SHA-256, zoals beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS384** - RSASSA-PSS met SHA-384 en MGF1 met SHA-384, zoals beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **PS512** - RSASSA-PSS met SHA-512 en MGF1 met SHA-512, zoals beschreven in [RFC7518](https://tools.ietf.org/html/rfc7518).
-   **RS256** - RSASSA-PKCS-v1_5 met SHA-256. De meegeleverde digest-waarde van de toepassing moet worden berekend met SHA-256 en moet 32 bytes lang zijn.  
-   **RS384** - RSASSA-PKCS-v1_5 met SHA-384. De meegeleverde digest-waarde van de toepassing moet worden berekend met SHA-384 en moet 48 bytes lang zijn.  
-   **RS512** - RSASSA-PKCS-v1_5 met SHA-512. De meegeleverde digest-waarde van de toepassing moet worden berekend met SHA-512 en moet 64 bytes lang zijn.  
-   **RSNULL** - Zie [RFC2437], een gespecialiseerde use-case om bepaalde TLS-scenario's in te schakelen.  

###  <a name="key-operations"></a>Belangrijkste bewerkingen

Key Vault ondersteunt de volgende bewerkingen op belangrijke objecten:  

-   **Maken:** Hiermee kan een client een sleutel maken in Key Vault. De waarde van de sleutel wordt gegenereerd door Key Vault en opgeslagen en wordt niet vrijgegeven aan de client. Asymmetrische sleutels kunnen worden gemaakt in Key Vault.  
-   **Importeren:** hiermee kan een client een bestaande sleutel importeren in Key Vault. Asymmetrische sleutels kunnen worden geïmporteerd naar Key Vault met behulp van een aantal verschillende verpakkingsmethoden binnen een JWK-constructie. 
-   **Update**: Hiermee kan een client met voldoende machtigingen de metagegevens (sleutelkenmerken) wijzigen die zijn gekoppeld aan een sleutel die eerder is opgeslagen in Key Vault.  
-   **Verwijderen:** hiermee kan een client met voldoende machtigingen een sleutel uit Key Vault verwijderen.  
-   **Lijst:** Hiermee kan een client alle sleutels in een bepaalde Key Vault vermelden.  
-   **Lijstversies:** Hiermee kan een client alle versies van een bepaalde sleutel in een bepaalde Key Vault vermelden.  
-   **Get:** Hiermee kan een client de openbare delen van een bepaalde sleutel ophalen in een Key Vault.  
-   **Back-up:** exporteert een sleutel in een beveiligde vorm.  
-   **Herstellen:** importeert een eerder back-upsleutel.  

Zie [Belangrijke bewerkingen in de api-verwijzing Key Vault REST](/rest/api/keyvault)voor meer informatie .  

Zodra een sleutel is gemaakt in Key Vault, kunnen de volgende cryptografische bewerkingen worden uitgevoerd met de sleutel:  

-   **Ondertekenen en verifiëren:** deze bewerking is strikt genomen "sign hash" of "verify hash", omdat Key Vault geen ondersteuning biedt voor hashing van inhoud als onderdeel van het maken van handtekeningen. Toepassingen moeten de gegevens hashen die lokaal moeten worden ondertekend en vervolgens vragen dat Key Vault de hash ondertekent. Verificatie van ondertekende hashes wordt ondersteund als een gemaksbewerking voor toepassingen die mogelijk geen toegang hebben tot [openbaar] sleutelmateriaal. Controleer voor de beste toepassingsprestaties of bewerkingen lokaal worden uitgevoerd.  
-   **Sleutelversleuteling / Wrapping**: Een sleutel die is opgeslagen in Key Vault kan worden gebruikt om een andere sleutel te beschermen, meestal een symmetrische inhoudsversleutelingssleutel (CEK). Wanneer de sleutel in Key Vault asymmetrisch is, wordt sleutelversleuteling gebruikt. RSA-OAEP en de WRAPKEY/UNWRAPKEY-bewerkingen zijn bijvoorbeeld gelijk aan ENCRYPT/DECRYPT. Wanneer de sleutel in Key Vault symmetrisch is, wordt sleutelverpakking gebruikt. Bijvoorbeeld AES-KW. De WRAPKEY-bewerking wordt ondersteund als een gemak voor toepassingen die mogelijk geen toegang hebben tot [openbaar] sleutelmateriaal. Voor de beste toepassingsprestaties moeten WRAPKEY-bewerkingen lokaal worden uitgevoerd.  
-   **Versleutelen en decoderen:** een sleutel die is opgeslagen in Key Vault kan worden gebruikt om een enkel blok gegevens te versleutelen of te decoderen. De grootte van het blok wordt bepaald door het sleuteltype en het geselecteerde versleutelingsalgoritme. De versleutelingsbewerking is voor het gemak beschikbaar voor toepassingen die mogelijk geen toegang hebben tot [openbaar] sleutelmateriaal. Voor de beste toepassingsprestaties moeten versleutelingsbewerkingen lokaal worden uitgevoerd.  

Hoewel WRAPKEY/UNWRAPKEY met behulp van asymmetrische sleutels overbodig lijkt (omdat de bewerking gelijk is aan ENCRYPT/DECRYPT), is het gebruik van afzonderlijke bewerkingen belangrijk. Het onderscheid biedt semantische en autorisatiescheiding van deze bewerkingen en consistentie wanneer andere sleuteltypen door de service worden ondersteund.  

Key Vault ondersteunt geen EXPORT-bewerkingen. Zodra een sleutel in het systeem is ingericht, kan deze niet meer worden geëxtraheerd of het belangrijkste materiaal ervan worden gewijzigd. Gebruikers van Key Vault kunnen echter hun sleutel nodig hebben voor andere use cases, zoals nadat deze is verwijderd. In dit geval kunnen ze de back-up- en herstelbewerkingen gebruiken om de sleutel in een beveiligde vorm te exporteren/importeren. Sleutels die zijn gemaakt door de back-upbewerking, zijn niet bruikbaar buiten Key Vault. Als alternatief kan de IMPORT-bewerking worden gebruikt voor meerdere Key Vault-exemplaren.  

Gebruikers kunnen een van de cryptografische bewerkingen die Key Vault per sleutel ondersteunt, beperken met behulp van de key_ops eigenschap van het JWK-object.  

Zie [JSON Web Key (JWK) voor](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)meer informatie over JWK-objecten.  

###  <a name="key-attributes"></a>Belangrijke kenmerken

Naast de instellingen voor sleutels kunnen de volgende kenmerken worden opgegeven. In een JSON-aanvraag zijn het trefwoord en de accolades '{' '}' vereist, zelfs als er geen kenmerken zijn opgegeven.  

- *ingeschakeld*: booleaan, optioneel, standaard is **waar**. Hiermee geeft u op of de sleutel is ingeschakeld en bruikbaar is voor cryptografische bewerkingen. Het *ingeschakelde* kenmerk wordt gebruikt in combinatie met *nbf* en *exp*. Wanneer een bewerking plaatsvindt tussen *nbf* en *exp,* is deze alleen toegestaan als *ingeschakeld* is ingesteld op **true**. Bewerkingen buiten het *nbf* / *exp-venster* zijn automatisch verboden, met uitzondering van bepaalde bewerkingstypen onder [bepaalde omstandigheden](#date-time-controlled-operations).
- *nbf*: IntDate, optioneel, standaard is nu. Het *kenmerk nbf* (niet eerder) identificeert de tijd waarvoor de sleutel NIET mag worden gebruikt voor cryptografische bewerkingen, met uitzondering van bepaalde bewerkingstypen onder [bepaalde omstandigheden](#date-time-controlled-operations). De verwerking van het *nbf-kenmerk* vereist dat de huidige datum/tijd moet worden na of gelijk zijn aan de niet-voordatum/tijd die in het *nbf-kenmerk* wordt vermeld. Key Vault kan voorzien in een aantal kleine speelruimte, normaal gesproken niet meer dan een paar minuten, om rekening te houden met klok scheeftrekken. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *exp*: IntDate, optioneel, standaard is "forever". Het *kenmerk exp* (vervaldatum) identificeert de vervaldatum op of waarna de sleutel NIET mag worden gebruikt voor cryptografische bewerking, behalve voor bepaalde bewerkingstypen onder bepaalde [omstandigheden](#date-time-controlled-operations). De verwerking van het *kenmerk exp* vereist dat de huidige datum/tijd moet worden vóór de vervaldatum / tijd vermeld in het *kenmerk exp.* Key Vault kan voorzien in een aantal kleine speelruimte, meestal niet meer dan een paar minuten, om rekening te houden met klok scheeftrekken. De waarde moet een getal zijn dat een IntDate-waarde bevat.  

Er zijn extra alleen-lezen kenmerken die zijn opgenomen in een reactie die belangrijke kenmerken bevat:  

- *gemaakt*: IntDate, optioneel. Het *gemaakte* kenmerk geeft aan wanneer deze versie van de sleutel is gemaakt. De waarde is null voor sleutels die zijn gemaakt vóór de toevoeging van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *bijgewerkt*: IntDate, optioneel. Het *bijgewerkte* kenmerk geeft aan wanneer deze versie van de sleutel is bijgewerkt. De waarde is null voor sleutels die voor het laatst zijn bijgewerkt voordat dit kenmerk werd toegelaten. De waarde moet een getal zijn dat een IntDate-waarde bevat.  

Zie [Gegevenstypen voor](#data-types) meer informatie over IntDate en andere gegevenstypen  

#### <a name="date-time-controlled-operations"></a>Datumgecontroleerde bewerkingen

Niet-nog-geldig en verlopen sleutels, buiten de *nbf* / *exp* venster, zal werken voor **decoderen,** **uitpakken**, en **controleren** operaties (zal niet terugkeren 403, Verboden). De reden voor het gebruik van de nog niet geldige status is om een sleutel te laten testen voordat het gebruik van de productie. De reden voor het gebruik van de verlopen status is om herstelbewerkingen toe te staan op gegevens die zijn gemaakt toen de sleutel geldig was. U ook de toegang tot een sleutel uitschakelen met het Key Vault-beleid of door het *ingeschakelde* sleutelkenmerk bij te werken naar **false.**

Zie [Gegevenstypen](#data-types)voor meer informatie over gegevenstypen .

Zie de [JSON Web Key (JWK) voor](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)meer informatie over andere mogelijke kenmerken.

### <a name="key-tags"></a>Hoofdtags

U aanvullende toepassingsspecifieke metagegevens opgeven in de vorm van tags. Key Vault ondersteunt maximaal 15 tags, die elk een naam van 256 tekens en een tekenwaarde van 256 kunnen hebben.  

>[!Note]
>Tags zijn leesbaar voor een beller als deze de *lijst* heeft of toestemming *krijgt* voor dat objecttype (sleutels, geheimen of certificaten).

###  <a name="key-access-control"></a>Toegangsbeheer voor sleutels

Toegangscontrole voor sleutels die door Key Vault worden beheerd, wordt geleverd op het niveau van een Key Vault die fungeert als de container met sleutels. Het toegangscontrolebeleid voor sleutels onderscheidt zich van het toegangscontrolebeleid voor geheimen in dezelfde Key Vault. Gebruikers kunnen een of meer kluizen maken om sleutels vast te houden en moeten scenario's behouden die geschikt zijn voor segmentatie en beheer van sleutels. Toegangscontrole voor sleutels is onafhankelijk van toegangscontrole voor geheimen.  

De volgende machtigingen kunnen worden verleend, op een per gebruiker / service principal basis, in de sleutels toegangscontrole vermelding op een kluis. Deze machtigingen weerspiegelen de toegestane bewerkingen op een belangrijk object nauwkeurig.  Het verlenen van toegang tot een serviceprincipal in key vault is een eenmalige bewerking en blijft hetzelfde voor alle Azure-abonnementen. U het gebruiken om zoveel certificaten te implementeren als u wilt. 

- Machtigingen voor belangrijke beheerbewerkingen
  - *get*: Lees het openbare deel van een sleutel, plus de attributen
  - *lijst:* De sleutels of versies van een sleutel weergeven die is opgeslagen in een sleutelkluis
  - *update*: De kenmerken van een sleutel bijwerken
  - *maken:* Nieuwe toetsen maken
  - *importeren:* Een sleutel importeren in een sleutelkluis
  - *verwijderen:* het hoofdobject verwijderen
  - *herstellen*: Een verwijderde sleutel herstellen
  - *back-up:* maak een back-up van een sleutel in een sleutelkluis
  - *herstellen:* Een back-upsleutel herstellen naar een sleutelkluis

- Machtigingen voor cryptografische bewerkingen
  - *decoderen:* gebruik de sleutel om een reeks bytes ongedaan te maken
  - *versleutelen:* Gebruik de sleutel om een willekeurige reeks bytes te beschermen
  - *uitpakkenSleutel:* gebruik de sleutel om verpakte symmetrische toetsen te ontbeschermen
  - *wrapKey*: Gebruik de sleutel om een symmetrische toets te beschermen
  - *controleren:* Gebruik de sleutel om digests te verifiëren  
  - *teken*: Gebruik de sleutel om digesten te ondertekenen
    
- Machtigingen voor bevoorrechte bewerkingen
  - *zuivering*: Een verwijderde sleutel verwijderen (permanent verwijderen)

Zie [Sleutelbewerkingen in de Key Vault REST API-referentie](/rest/api/keyvault)voor meer informatie over het werken met sleutels. Zie [Kluizen - Gewaarmaak of Update](/rest/api/keyvault/vaults/createorupdate) en [Kluizen - Toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy)voor informatie over het instellen van machtigingen. 

## <a name="see-also"></a>Zie ook

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

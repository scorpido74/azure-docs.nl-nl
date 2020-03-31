---
title: Over Azure Key Vault-sleutels, -geheimen en -certificaten - Azure Key Vault
description: Overzicht van de Azure Key Vault REST-interface en ontwikkelaarsgegevens voor sleutels, geheimen en certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: dd8be482009e067bf9016cc8e351fc42a2db39c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271731"
---
# <a name="about-keys-secrets-and-certificates"></a>Informatie over sleutels, geheimen en certificaten

Azure Key Vault stelt Microsoft Azure-toepassingen en -gebruikers in staat om verschillende soorten geheime/sleutelgegevens op te slaan en te gebruiken:

- Cryptografische sleutels: ondersteunt meerdere sleuteltypen en algoritmen en maakt het gebruik van Hardware Security Modules (HSM) mogelijk voor sleutels met een hoge waarde. 
- Geheimen: biedt veilige opslag van geheimen, zoals wachtwoorden en databaseverbindingstekenreeksen.
- Certificaten: ondersteunt certificaten, die zijn gebouwd op de top van sleutels en geheimen en voeg een geautomatiseerde vernieuwing functie.
- Azure Storage: kan sleutels van een Azure Storage-account voor u beheren. Intern kan Key Vault sleutels (synchroniseren) weergeven met een Azure Storage-account en de sleutels periodiek regenereren (roteren). 

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
-   **Identiteit** - een identiteit van Azure Active Directory (AAD).  
-   **IntDate** - een JSON decimale waarde die het aantal seconden van 1970-01-01T0:0:0Z UTC vertegenwoordigt tot de opgegeven UTC-datum/-tijd. Zie RFC3339 voor meer informatie over datum/tijden, in het algemeen en UTC in het bijzonder.  

### <a name="objects-identifiers-and-versioning"></a>Objecten, id's en versiebeheer

Objecten die zijn opgeslagen in Key Vault worden geversioneerd wanneer een nieuwe instantie van een object wordt gemaakt. Aan elke versie is een unieke id en URL toegewezen. Wanneer een object voor het eerst wordt gemaakt, krijgt het een unieke versie-id en wordt het gemarkeerd als de huidige versie van het object. Het maken van een nieuwe instantie met dezelfde objectnaam geeft het nieuwe object een unieke versie-id, waardoor het de huidige versie wordt.  

Objecten in Key Vault kunnen worden aangepakt met de huidige id of een versiespecifieke id. Als u bijvoorbeeld een sleutel `MasterKey`met de naam krijgt, zorgt het uitvoeren van bewerkingen met de huidige id ervoor dat het systeem de laatst beschikbare versie gebruikt. Door bewerkingen uit te voeren met de versiespecifieke id, wordt het systeem die specifieke versie van het object gebruikt.  

Objecten worden uniek geïdentificeerd in Key Vault met behulp van een URL. Geen twee objecten in het systeem hebben dezelfde URL, ongeacht de geolocatie. De volledige URL van een object wordt object-id genoemd. De URL bestaat uit een voorvoegsel dat het sleutelkluis, objecttype, de door de gebruiker opgegeven objectnaam en een objectversie identificeert. De objectnaam is hoofdletterongevoelig en onveranderlijk. Id's die de objectversie niet bevatten, worden base-id's genoemd.  

Zie [Verificatie, aanvragen en antwoorden](authentication-requests-and-responses.md) voor meer informatie

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

## <a name="key-vault-secrets"></a>Key Vault geheimen 

### <a name="working-with-secrets"></a>Werken met geheimen

Vanuit het perspectief van een ontwikkelaar accepteren en retourneren Key Vault API's geheime waarden als tekenreeksen. Intern slaat Key Vault geheimen op en beheert deze als sequenties van octetten (8-bits bytes), met een maximale grootte van 25k bytes per stuk. De Key Vault-service biedt geen semantiek voor geheimen. Het accepteert alleen de gegevens, versleutelt deze, slaat deze op en retourneert een geheime id ('id'). De id kan worden gebruikt om het geheim op een later tijdstip op te halen.  

In het geval van zeer gevoelige gegevens is het raadzaam dat klanten extra beveiligingslagen overwegen. Dit kan bijvoorbeeld door het versleutelen van gegevens met behulp van een afzonderlijke beveiligingssleutel vóór de opslag in Key Vault.  

Key Vault ondersteunt ook een contentType-veld voor geheimen. Clients kunnen het inhoudstype van een geheim opgeven om te helpen bij het interpreteren van de geheime gegevens wanneer deze worden opgehaald. De maximale lengte van dit veld is 255 tekens. Er zijn geen vooraf gedefinieerde waarden. Het voorgestelde gebruik is als een hint voor het interpreteren van de geheime gegevens. Een implementatie kan bijvoorbeeld zowel wachtwoorden als certificaten opslaan als geheimen en dit veld vervolgens gebruiken om te differentiëren. Er zijn geen vooraf gedefinieerde waarden.  

### <a name="secret-attributes"></a>Geheime kenmerken

Naast de geheime gegevens kunnen de volgende kenmerken worden opgegeven:  

- *exp*: IntDate, optioneel, standaard is **voor altijd**. Het *kenmerk exp* (vervaldatum) identificeert de vervaldatum op of waarna de geheime gegevens NIET mogen worden opgehaald, behalve in bepaalde [situaties](#date-time-controlled-operations). Dit veld is alleen voor **informatieve** doeleinden omdat het gebruikers van de sleutelkluisservice informeert dat een bepaald geheim niet mag worden gebruikt. De waarde moet een getal zijn dat een IntDate-waarde bevat.   
- *nbf*: IntDate, optioneel, standaard is **nu**. Het *nbf* (niet eerder) attribuut identificeert de tijd vóór die de geheime gegevens niet mogen worden opgehaald, behalve in [bepaalde situaties](#date-time-controlled-operations). Dit veld is alleen voor **informatieve** doeleinden. De waarde moet een getal zijn dat een IntDate-waarde bevat. 
- *ingeschakeld*: booleaan, optioneel, standaard is **waar**. Dit kenmerk geeft aan of de geheime gegevens kunnen worden opgehaald. Het ingeschakelde attribuut wordt gebruikt in combinatie met *nbf* en *exp* wanneer een bewerking plaatsvindt tussen *nbf* en *exp,* het zal alleen worden toegestaan als ingeschakeld is ingesteld op **true**. Bewerkingen buiten het *nbf-* en *exp-venster* worden automatisch geweigerd, behalve in [bepaalde situaties](#date-time-controlled-operations).  

Er zijn extra alleen-lezen kenmerken die zijn opgenomen in een reactie die geheime kenmerken bevat:  

- *gemaakt*: IntDate, optioneel. Het gemaakte kenmerk geeft aan wanneer deze versie van het geheim is gemaakt. Deze waarde is null voor geheimen die zijn gemaakt vóór de toevoeging van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *bijgewerkt*: IntDate, optioneel. Het bijgewerkte kenmerk geeft aan wanneer deze versie van het geheim is bijgewerkt. Deze waarde is null voor geheimen die voor het laatst zijn bijgewerkt voordat dit kenmerk werd toegegeven. De waarde moet een getal zijn dat een IntDate-waarde bevat.

#### <a name="date-time-controlled-operations"></a>Datumgecontroleerde bewerkingen

Een geheim **te krijgen** operatie zal werken voor niet-nog-geldig en verlopen geheimen, buiten de *nbf* / *exp* venster. Het aanroepen van een **geheim's krijgen** operatie, voor een nog niet-geldig geheim, kan worden gebruikt voor testdoeleinden. Ophalen **(get**ting) een verlopen geheim, kan worden gebruikt voor herstelbewerkingen.

Zie [Gegevenstypen](#data-types)voor meer informatie over gegevenstypen .  

### <a name="secret-access-control"></a>Toegangsbeheer voor geheimen

Toegangscontrole voor geheimen die worden beheerd in Key Vault, wordt geleverd op het niveau van de Key Vault dat deze geheimen bevat. Het toegangscontrolebeleid voor geheimen onderscheidt zich van het toegangscontrolebeleid voor sleutels in dezelfde Key Vault. Gebruikers kunnen een of meer kluizen maken om geheimen te bewaren en zijn verplicht om scenario's te onderhouden die geschikt zijn voor segmentatie en beheer van geheimen.   

De volgende machtigingen kunnen per hoofd worden gebruikt in de toegang tot de toegangscontrole op een kluis en de toegestane bewerkingen op een geheim object nauwkeurig weerspiegelen:  

- Machtigingen voor geheime beheerbewerkingen
  - *get*: Lees een geheim  
  - *lijst:* Vermeld de geheimen of versies van een geheim dat is opgeslagen in een Key Vault  
  - *set:* Een geheim maken  
  - *verwijderen:* Een geheim verwijderen  
  - *herstellen*: Een verwijderd geheim herstellen
  - *back-up:* Maak een back-up van een geheim in een sleutelkluis
  - *herstellen:* Een back-up geheim herstellen naar een sleutelkluis

- Machtigingen voor bevoorrechte bewerkingen
  - *zuivering*: Purge (permanent verwijderen) een verwijderd geheim

Zie [Geheime bewerkingen in de Key Vault REST API-referentie](/rest/api/keyvault)voor meer informatie over het werken met geheimen. Zie [Kluizen - Gewaarmaak of Update](/rest/api/keyvault/vaults/createorupdate) en [Kluizen - Toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy)voor informatie over het instellen van machtigingen. 

### <a name="secret-tags"></a>Geheime tags  
U aanvullende toepassingsspecifieke metagegevens opgeven in de vorm van tags. Key Vault ondersteunt maximaal 15 tags, die elk een naam van 256 tekens en een tekenwaarde van 256 kunnen hebben.  

>[!Note]
>Tags zijn leesbaar voor een beller als deze de *lijst* heeft of toestemming *krijgt* voor dat objecttype (sleutels, geheimen of certificaten).

## <a name="key-vault-certificates"></a>Key Vault-certificaten

Key Vault-certificatenondersteuning biedt het beheer van uw x509-certificaten en de volgende gedragingen:  

-   Hiermee kan een certificaateigenaar een certificaat maken via een sleutelkluiscreatieproces of via de import van een bestaand certificaat. Bevat zowel zelfondertekende als door de Certificaatautoriteit gegenereerde certificaten.
-   Hiermee kan een key vault-certificaateigenaar veilige opslag en beheer van X509-certificaten implementeren zonder interactie met privésleutelmateriaal.  
-   Hiermee kan een certificaateigenaar een beleid maken dat Key Vault aanstuurt om de levenscyclus van een certificaat te beheren.  
-   Hiermee kunnen certificaateigenaren contactgegevens verstrekken voor meldingen over levenscyclusgebeurtenissen van vervaldatum en verlenging van het certificaat.  
-   Ondersteunt automatische verlenging met geselecteerde emittenten - Key Vault-partner X509-certificaatproviders / certificaatautoriteiten.

>[!Note]
>Niet-partneraanbieders/autoriteiten zijn ook toegestaan, maar ondersteunen de functie voor automatische vernieuwing niet.

### <a name="composition-of-a-certificate"></a>Samenstelling van een certificaat

Wanneer een Key Vault-certificaat wordt gemaakt, worden ook een adresseerbare sleutel en een geheim met dezelfde naam gemaakt. De Key Vault-sleutel maakt belangrijke bewerkingen mogelijk en het Sleutelkluisgeheim maakt het mogelijk om de certificaatwaarde als geheim op te halen. Een Key Vault-certificaat bevat ook openbare x509-certificaatmetagegevens.  

De id en versie van certificaten is vergelijkbaar met die van sleutels en geheimen. Een specifieke versie van een adresseerbare sleutel en geheim die is gemaakt met de Key Vault-certificaatversie is beschikbaar in het antwoord van het Key Vault-certificaat.
 
![Certificaten zijn complexe objecten](media/azure-key-vault.png)

### <a name="exportable-or-non-exportable-key"></a>Exporteerbare of niet-exporteerbare sleutel

Wanneer een Key Vault-certificaat wordt gemaakt, kan het worden opgehaald uit het adresseerbare geheim met de privésleutel in PFX- of PEM-formaat. Het beleid dat wordt gebruikt om het certificaat te maken, moet aangeven dat de sleutel exporteerbaar is. Als het beleid niet-exporteerbaar aangeeft, maakt de privésleutel geen deel uit van de waarde wanneer deze als geheim wordt opgehaald.  

De adresseerbare sleutel wordt relevanter met niet-exporteerbare KV-certificaten. De bewerkingen van de adresseerbare KV-sleutel worden toegewezen vanuit het *veld Keyusage* van het KV-certificaatbeleid dat wordt gebruikt om het KV-certificaat te maken.  

Twee soorten sleutels worden ondersteund : *RSA* of *RSA HSM* met certificaten. Exporteerbaar is alleen toegestaan met RSA, niet ondersteund door RSA HSM.  

### <a name="certificate-attributes-and-tags"></a>Certificaatkenmerken en -tags

Naast certificaatmetadata, een adresseerbare sleutel en adresseerbaar geheim, bevat een Key Vault-certificaat ook kenmerken en tags.  

#### <a name="attributes"></a>Kenmerken

De certificaatkenmerken worden gespiegeld aan kenmerken van de adresseerbare sleutel en het geheim dat is gemaakt wanneer het KV-certificaat wordt gemaakt.  

Een Key Vault-certificaat heeft de volgende kenmerken:  

-   *ingeschakeld*: booleaan, optioneel, standaard is **waar**. Kan worden opgegeven om aan te geven of de certificaatgegevens als geheim of bruikbaar als sleutel kunnen worden opgehaald. Ook gebruikt in combinatie met *nbf* en *exp* wanneer een operatie plaatsvindt tussen *nbf* en *exp*, en zal alleen worden toegestaan als ingeschakeld is ingesteld op true. Bewerkingen buiten het *nbf-* en *exp-venster* zijn automatisch niet toegestaan.  

Er zijn extra alleen-lezen kenmerken die zijn opgenomen in de reactie:

-   *gemaakt*: IntDate: geeft aan wanneer deze versie van het certificaat is gemaakt.  
-   *bijgewerkt*: IntDate: geeft aan wanneer deze versie van het certificaat is bijgewerkt.  
-   *exp*: IntDate: bevat de waarde van de vervaldatum van het x509-certificaat.  
-   *nbf*: IntDate: bevat de waarde van de datum van het x509-certificaat.  

> [!Note] 
> Als een Key Vault-certificaat verloopt, is het adresseerbare sleutel en wordt het geheim onbruikbaar.  

#### <a name="tags"></a>Tags

 Client opgegeven woordenboek van sleutelwaardeparen, vergelijkbaar met tags in sleutels en geheimen.  

 > [!Note]
> Tags zijn leesbaar voor een beller als deze de *lijst* heeft of toestemming *krijgt* voor dat objecttype (sleutels, geheimen of certificaten).

### <a name="certificate-policy"></a>Certificaatbeleid

Een certificaatbeleid bevat informatie over het maken en beheren van de levenscyclus van een Key Vault-certificaat. Wanneer een certificaat met privésleutel wordt geïmporteerd in de sleutelkluis, wordt een standaardbeleid gemaakt door het x509-certificaat te lezen.  

Wanneer een Key Vault-certificaat helemaal opnieuw wordt gemaakt, moet een beleid worden opgegeven. Het beleid bepaalt hoe u deze Key Vault-certificaatversie of de volgende Key Vault-certificaatversie maakt. Zodra een beleid is vastgesteld, is het niet vereist met opeenvolgende bewerkingen maken voor toekomstige versies. Er is slechts één exemplaar van een beleid voor alle versies van een Key Vault-certificaat.  

Op hoog niveau bevat een certificaatbeleid de volgende informatie:  

-   X509-certificaateigenschappen: bevat onderwerpnaam, alternatieve namen voor onderwerp en andere eigenschappen die worden gebruikt om een x509-certificaataanvraag te maken.  
-   Belangrijke eigenschappen: bevat sleuteltype, sleutellengte, exporteerbare en hergebruik belangrijkevelden. Deze velden instrueren key vault over het genereren van een sleutel.  
-   Geheime eigenschappen: bevat geheime eigenschappen zoals inhoudstype adresseerbaar geheim om de geheime waarde te genereren, voor het ophalen van certificaat als geheim.  
-   Levenslange acties: bevat levenslange acties voor het KV-certificaat. Elke levenslange actie bevat:  

     - Trigger: opgegeven via dagen voor het verstrijken of levensduur percentage  

     - Actie: actietype opgeven – *e-mailContacten* of *automatisch vernieuwen*  

-   Uitgever: Parameters over de certificaatuitgever die moet worden gebruikt om x509-certificaten uit te geven.  
-   Beleidskenmerken: bevat kenmerken die zijn gekoppeld aan het beleid  

#### <a name="x509-to-key-vault-usage-mapping"></a>X509 naar Key Vault-gebruikstoewijzing

De volgende tabel geeft de toewijzing aan het gebruiksbeleid van x509-sleutel aan effectieve sleutelbewerkingen van een sleutel die is gemaakt als onderdeel van het maken van een Key Vault-certificaat.

|**X509-vlaggen voor sleutelgebruik**|**Key Vault-sleutelops**|**Standaardgedrag**|
|----------|--------|--------|
|DataEncipherment|versleutelen, decoderen| N.v.t. |
|Alleen ontcijferen|Decoderen| N.v.t.  |
|Digitaal handtekening|ondertekenen, verifiëren| Key Vault standaard zonder gebruiksspecificatie op tijd voor het maken van certificaten | 
|Alleen ontcijferen|encrypt| N.v.t. |
|KeyCertSign|ondertekenen, verifiëren|N.v.t.|
|Sleutelencipherment|wrapKey, uitpakkenSleutel| Key Vault standaard zonder gebruiksspecificatie op tijd voor het maken van certificaten | 
|Niet-repudiation|ondertekenen, verifiëren| N.v.t. |
|crlsign crlsign|ondertekenen, verifiëren| N.v.t. |

### <a name="certificate-issuer"></a>Certificaatuitgever

Een key vault-certificaatobject bevat een configuratie die wordt gebruikt om te communiceren met een geselecteerde leverancier van certificaatuitgevers om x509-certificaten te bestellen.  

-   Key Vault-partners met volgende certificaatverstrekkerproviders voor TLS/SSL-certificaten

|**Naam van provider**|**Locaties**|
|----------|--------|
|DigiCert|Ondersteund in alle belangrijke vault service locaties in public cloud en Azure Government|
|GlobalSign|Ondersteund in alle belangrijke vault service locaties in public cloud en Azure Government|

Voordat een certificaatuitgever kan worden gemaakt in een Sleutelkluis, moeten de volgende vereiste stappen 1 en 2 met succes worden uitgevoerd.  

1. Leveranciers van certificaatautoriteit (CA)  

    -   Een organisatiebeheerder moet aan boord van hun bedrijf (bijvoorbeeld. Contoso) met ten minste één CA-provider.  

2. Beheerder maakt credentialer-referenties voor Key Vault om TLS/SSL-certificaten in te schrijven (en te vernieuwen)  

    -   Biedt de configuratie die moet worden gebruikt om een emittentobject van de provider in de sleutelkluis te maken  

Zie de [blog Key Vault Certificates](https://aka.ms/kvcertsblog) voor meer informatie over het maken van emittentobjecten uit de portal Certificaten  

Key Vault maakt het mogelijk om meerdere emittentobjecten te maken met verschillende configuratie van de emittentprovider. Zodra een object van een uitgever is gemaakt, kan de naam ervan worden vermeld in een of meerdere certificaatbeleidsregels. Als u verwijst naar het object van de uitgever, geeft Key Vault opdracht om configuratie te gebruiken zoals gespecificeerd in het object van de uitgever wanneer u het x509-certificaat aanvraagt bij de CA-provider tijdens het maken en vernieuwen van het certificaat.  

Uitgevende objecten worden gemaakt in de kluis en kunnen alleen worden gebruikt met KV-certificaten in dezelfde kluis.  

### <a name="certificate-contacts"></a>Certificaatcontactpersonen

Certificaatcontactpersonen bevatten contactgegevens om meldingen te verzenden die worden geactiveerd door gebeurtenis van de certificaatlevensduur. De contactgegevens worden gedeeld door alle certificaten in de sleutelkluis. Er wordt een melding verzonden naar alle opgegeven contactpersonen voor een gebeurtenis voor een certificaat in de sleutelkluis.  

Als het beleid van een certificaat is ingesteld op automatische verlenging, wordt er een melding verzonden over de volgende gebeurtenissen.  

- Vóór certificaatverlenging
- Na verlenging van het certificaat, met vermelding of het certificaat is verlengd of als er een fout is opgetreden, waarvoor handmatige verlenging van het certificaat vereist is.  

  Wanneer een certificaatbeleid dat handmatig moet worden verlengd (alleen e-mail), wordt een melding verzonden wanneer het tijd is om het certificaat te vernieuwen.  

### <a name="certificate-access-control"></a>Certificaattoegangsbeheer

 Toegangsbeheer voor certificaten wordt beheerd door Key Vault en wordt geleverd door de Key Vault die deze certificaten bevat. Het toegangscontrolebeleid voor certificaten onderscheidt zich van het toegangscontrolebeleid voor sleutels en geheimen in dezelfde Key Vault. Gebruikers kunnen een of meer kluizen maken om certificaten te bezitten, om scenario's te behouden die geschikt zijn voor segmentatie en beheer van certificaten.  

 De volgende machtigingen kunnen per hoofd worden gebruikt in de toegang tot de toegang tot de geheimen op een sleutelkluis en de toegestane bewerkingen op een geheim object nauw weergeven:  

- Machtigingen voor certificaatbeheerbewerkingen
  - *get:* Download de huidige certificaatversie of een versie van een certificaat 
  - *lijst:* De huidige certificaten of versies van een certificaat weergeven  
  - *update*: Een certificaat bijwerken
  - *maken:* Een Key Vault-certificaat maken
  - *importeren*: Materiaal voor importcertificaten in een Key Vault-certificaat
  - *verwijderen:* Een certificaat, het beleid en alle versies verwijderen  
  - *herstellen*: Een verwijderd certificaat herstellen
  - *back-up:* een back-up maken van een certificaat in een sleutelkluis
  - *herstellen:* Een back-upcertificaat herstellen naar een sleutelkluis
  - *contactpersonen beheren:* Key Vault-certificaatcontactpersonen beheren  
  - *beheert emittenten*: Key Vault-certificaatautoriteiten/emittenten beheren
  - *veremittenten*: Ontvang de autoriteiten/emittenten van een certificaat
  - *lijstverstrekkers*: Vermeld de autoriteiten/emittenten van een certificaat  
  - *setissuers*: De autoriteiten/emittenten van een Key Vault-certificaat maken of bijwerken  
  - *verwijderingen :* De autoriteiten/emittenten van een Key Vault-certificaat verwijderen  
 
- Machtigingen voor bevoorrechte bewerkingen
  - *zuivering*: Een verwijderd certificaat verwijderen (permanent verwijderen)

Zie de [certificaatbewerkingen in de API-referentie Key Vault REST](/rest/api/keyvault)voor meer informatie . Zie [Kluizen - Gewaarmaak of Update](/rest/api/keyvault/vaults/createorupdate) en [Kluizen - Toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy)voor informatie over het instellen van machtigingen.

## <a name="azure-storage-account-key-management"></a>Beheer van azure Storage-accountsleutels

Key Vault kan Azure-opslagaccountsleutels beheren:

- Intern kan Key Vault sleutels (synchronisatie) weergeven met een Azure-opslagaccount. 
- Key Vault regenereert (roteert) de toetsen periodiek.
- Belangrijke waarden worden nooit geretourneerd in reactie op de beller.
- Key Vault beheert sleutels van zowel opslagaccounts als klassieke opslagaccounts.

Zie [Azure Key Vault Storage Account Keys](key-vault-ovw-storage-keys.md) voor meer informatie

### <a name="storage-account-access-control"></a>Toegangsbeheer voor opslagaccount

De volgende machtigingen kunnen worden gebruikt wanneer een gebruiker of toepassingsprincipal toestemming wordt gegeven om bewerkingen uit te voeren op een beheerde opslagaccount:  

- Machtigingen voor beheerde opslagaccount en SaS-definitiebewerkingen
  - *get:* Krijgt informatie over een opslagaccount 
  - *lijst:* Opslagaccounts aanbieden die worden beheerd door een sleutelkluis
  - *update*: Een opslagaccount bijwerken
  - *verwijderen:* een opslagaccount verwijderen  
  - *herstellen*: Een verwijderd opslagaccount herstellen
  - *back-up:* een back-up maken van een opslagaccount
  - *herstellen:* een back-upopslagaccount herstellen naar een Key Vault
  - *instellen:* een opslagaccount maken of bijwerken
  - *regenererende sleutel*: Een opgegeven sleutelwaarde voor een opslagaccount regenereren
  - *getsas*: Informatie over een SAS-definitie voor een opslagaccount
  - *listsas*: Lijst opslag SAS definities voor een opslagaccount
  - *deletesas*: Een SAS-definitie verwijderen uit een opslagaccount
  - *sets:* een nieuwe SAS-definitie/-kenmerken maken of bijwerken voor een opslagaccount

- Machtigingen voor bevoorrechte bewerkingen
  - *zuivering*: Een beheerd opslagaccount verwijderen (permanent verwijderen)

Zie de [bewerkingen van het opslagaccount in de referentie key vault rest API](/rest/api/keyvault)voor meer informatie. Zie [Kluizen - Gewaarmaak of Update](/rest/api/keyvault/vaults/createorupdate) en [Kluizen - Toegangsbeleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy)voor informatie over het instellen van machtigingen.

## <a name="see-also"></a>Zie ook

- [Verificatie, aanvragen en antwoorden](authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](/azure/key-vault/key-vault-developers-guide)

---
title: Over Azure Key Vault-certificaten - Azure Key Vault
description: Overzicht van azure key vault rest-interface en certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: e9fbe624563ad03880ff1a75efdc2df41b151846
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424277"
---
# <a name="about-azure-key-vault-certificates"></a>Informatie over Azure Key Vault-certificaten

Azure Key Vault stelt Microsoft Azure-toepassingen en -gebruikers in staat om certificaten op te slaan en te gebruiken, die bovenop sleutels en geheimen zijn gebouwd en een geautomatiseerde vernieuwingsfunctie kunnen toevoegen.

Zie Wat is Azure Key Vault voor meer algemene informatie over Key [Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

De volgende secties bieden algemene informatie die van toepassing is op de implementatie van de Key Vault-service.

### <a name="supporting-standards"></a>Ondersteunende normen

De SPECIFICATIES JavaScript Object Notation (JSON) en JavaScript Object Signing and Encryption (JOSE) zijn belangrijke achtergrondinformatie.  

-   [JSON-websleutel (JWK)](https://tools.ietf.org/html/draft-ietf-jose-json-web-key-41)  
-   [JSON-webversleuteling (JWE)](https://tools.ietf.org/html/draft-ietf-jose-json-web-encryption-40)  
-   [JSON Web Algoritmen (JWA)](https://tools.ietf.org/html/draft-ietf-jose-json-web-algorithms-40)  
-   [JSON-webhandtekening (JWS)](https://tools.ietf.org/html/draft-ietf-jose-json-web-signature-41)  

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
 
![Certificaten zijn complexe objecten](../media/azure-key-vault.png)

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

## <a name="see-also"></a>Zie ook

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

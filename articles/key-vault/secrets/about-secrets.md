---
title: Over Azure Key Vault-geheimen - Azure Key Vault
description: Overzicht van azure key vault rest-interface en details van ontwikkelaars voor geheimen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 2578f48ce218a0feaa5fb515ebc5d0e7154802ac
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81424263"
---
# <a name="about-azure-key-vault-secrets"></a>Over Azure Key Vault-geheimen

Azure Key Vault stelt Microsoft Azure-toepassingen en -gebruikers in staat om verschillende soorten geheime gegevens op te slaan en te gebruiken:

- Geheimen: biedt veilige opslag van geheimen, zoals wachtwoorden en databaseverbindingstekenreeksen.

- Azure Storage: kan sleutels van een Azure Storage-account voor u beheren. Intern kan Key Vault sleutels (synchroniseren) weergeven met een Azure Storage-account en de sleutels periodiek regenereren (roteren). 

Zie Wat is Azure Key Vault voor meer algemene informatie over Key [Vault?](/azure/key-vault/key-vault-overview)

## <a name="azure-key-vault"></a>Azure Key Vault

De volgende secties bieden algemene informatie die van toepassing is op de implementatie van de Key Vault-service. 

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
>Tags zijn leesbaar door een beller als ze de *lijst* hebben of toestemming *krijgen.*

## <a name="azure-storage-account-key-management"></a>Beheer van azure Storage-accountsleutels

Key Vault kan Azure-opslagaccountsleutels beheren:

- Intern kan Key Vault sleutels (synchronisatie) weergeven met een Azure-opslagaccount. 
- Key Vault regenereert (roteert) de toetsen periodiek.
- Belangrijke waarden worden nooit geretourneerd in reactie op de beller.
- Key Vault beheert sleutels van zowel opslagaccounts als klassieke opslagaccounts.

Zie [Azure Key Vault Storage Account Keys](../secrets/overview-storage-keys.md)voor meer informatie)

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

- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

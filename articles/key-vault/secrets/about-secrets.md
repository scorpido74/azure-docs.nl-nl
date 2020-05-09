---
title: Over Azure Key Vault geheimen-Azure Key Vault
description: Overzicht van Azure Key Vault REST interface en Details voor ontwikkel aars voor geheimen.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: secrets
ms.topic: overview
ms.date: 09/04/2019
ms.author: mbaldwin
ms.openlocfilehash: 7aa2feba5a2b2fa47bbb0c055a2f556b8997ab34
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82930468"
---
# <a name="about-azure-key-vault-secrets"></a>Over Azure Key Vault geheimen

Key Vault biedt beveiligde opslag van geheimen, zoals wacht woorden en database verbindings reeksen.

Vanuit het oogpunt van een ontwikkelaar Key Vault Api's accepteren en geheime waarden retour neren als teken reeksen. Intern worden geheimen door Key Vault opgeslagen en beheerd als reeksen van octetten (8-bits bytes), met een maximale grootte van 25k bytes. De Key Vault-service biedt geen semantiek voor geheimen. Alleen de gegevens worden geaccepteerd, versleuteld, opgeslagen en een geheime id (' id ') wordt geretourneerd. De id kan worden gebruikt om het geheim op een later tijdstip op te halen.  

In het geval van zeer gevoelige gegevens is het raadzaam dat klanten extra beveiligingslagen overwegen. Dit kan bijvoorbeeld door het versleutelen van gegevens met behulp van een afzonderlijke beveiligingssleutel vóór de opslag in Key Vault.  

Key Vault biedt ook ondersteuning voor een veld Content type voor geheimen. Clients kunnen het inhouds type van een geheim opgeven om te helpen bij het interpreteren van de geheime gegevens wanneer deze worden opgehaald. De maximale lengte van dit veld is 255 tekens. Er zijn geen vooraf gedefinieerde waarden. Het voorgestelde gebruik is als hint voor het interpreteren van de geheime gegevens. Een implementatie kan bijvoorbeeld beide wacht woorden en certificaten opslaan als geheimen en vervolgens dit veld gebruiken om onderscheid te maken. Er zijn geen vooraf gedefinieerde waarden.  

## <a name="encryption"></a>Versleuteling

Alle geheimen in uw Key Vault worden versleuteld opgeslagen. Deze versleuteling is transparant en vereist geen actie van de gebruiker. De Azure Key Vault-service versleutelt uw geheimen wanneer u deze toevoegt en ontsleutelt deze automatisch wanneer u ze leest. De versleutelings sleutel is uniek voor elke sleutel kluis.

## <a name="secret-attributes"></a>Geheime kenmerken

Naast de geheime gegevens kunnen de volgende kenmerken worden opgegeven:  

- *exp*: IntDate, optioneel, standaard waarde is **permanent**. Met het kenmerk *exp* (verval tijd) geeft u de verval tijd op of waarna de geheime gegevens niet moeten worden opgehaald, behalve in [bepaalde situaties](#date-time-controlled-operations). Dit veld is alleen ter **informatie** bedoeld omdat gebruikers van de sleutel kluis service informeert dat een bepaald geheim niet mag worden gebruikt. De waarde moet een getal zijn dat een IntDate-waarde bevat.   
- *NBF*: IntDate, optioneel, standaard is **nu**. Het kenmerk *NBF* (niet voor voor) geeft aan hoe lang de geheime gegevens niet moeten worden opgehaald, behalve in het [geval van bepaalde situaties](#date-time-controlled-operations). Dit veld is alleen ter **informatie** bedoeld. De waarde moet een getal zijn dat een IntDate-waarde bevat. 
- *ingeschakeld*: Boole, optioneel, standaard waarde is **True**. Dit kenmerk geeft aan of de geheime gegevens kunnen worden opgehaald. Het kenmerk enabled wordt gebruikt in combi natie met *NBF* en *exp* wanneer een bewerking plaatsvindt tussen *NBF* en *exp*, maar is alleen toegestaan als ingeschakeld is ingesteld op **waar**. Bewerkingen buiten het venster *NBF* en *exp* worden automatisch niet toegestaan, behalve in [bepaalde situaties](#date-time-controlled-operations).  

Er zijn aanvullende alleen-lezen kenmerken die zijn opgenomen in een antwoord dat geheime kenmerken bevat:  

- *gemaakt*: IntDate, optioneel. Het kenmerk gemaakt geeft aan wanneer deze versie van het geheim is gemaakt. Deze waarde is null voor geheimen die zijn gemaakt vóór het toevoegen van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.  
- *bijgewerkt*: IntDate, optioneel. Het kenmerk bijgewerkt geeft aan wanneer deze versie van het geheim is bijgewerkt. Deze waarde is null voor geheimen die voor het laatst zijn bijgewerkt vóór het toevoegen van dit kenmerk. De waarde moet een getal zijn dat een IntDate-waarde bevat.

### <a name="date-time-controlled-operations"></a>Datum-en tijd beheer bewerkingen

De **Get** -bewerking van een geheim werkt voor niet-geldige en verlopen geheimen, buiten het venster *NBF* / *exp* . Het aanroepen van de **Get** -bewerking van een geheim voor een niet-geldig geheim, kan worden gebruikt voor test doeleinden. Het**ophalen (** afmaken) van een verlopen geheim dat kan worden gebruikt voor herstel bewerkingen.

## <a name="secret-access-control"></a>Toegangsbeheer voor geheimen

Access Control voor geheimen die worden beheerd in Key Vault, wordt op het niveau van de Key Vault die deze geheimen bevat, vermeld. Het toegangs beheer beleid voor geheimen verschilt van het toegangs beheer beleid voor sleutels in dezelfde Key Vault. Gebruikers kunnen een of meer kluizen maken om geheimen te bewaren en zijn vereist voor het bijhouden van de juiste segmentatie en het beheer van geheimen.   

De volgende machtigingen kunnen per principal worden gebruikt, in de toegangscontrole vermelding geheimen in een kluis en de bewerkingen die zijn toegestaan voor een geheim object, nauw keurig spie gelen:  

- Machtigingen voor geheime beheer bewerkingen
  - *ophalen*: een geheim lezen  
  - *lijst*: vermeld de geheimen of versies van een geheim dat is opgeslagen in een Key Vault  
  - *instellen*: een geheim maken  
  - *verwijderen*: een geheim verwijderen  
  - *herstellen*: een verwijderd geheim herstellen
  - *Backup*: een back-up maken van een geheim in een sleutel kluis
  - *herstellen*: een back-up van een geheim naar een sleutel kluis herstellen

- Machtigingen voor bevoegde bewerkingen
  - *opschonen*: leegmaken (permanent verwijderen) een verwijderd geheim

Zie voor meer informatie over het werken met geheimen [geheime bewerkingen in de naslag informatie over Key Vault rest API](/rest/api/keyvault). Zie voor meer informatie over het instellen van machtigingen [-kluizen-maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [kluizen-toegangs beleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy). 

## <a name="secret-tags"></a>Geheime Tags  
U kunt aanvullende toepassingsspecifieke meta gegevens opgeven in de vorm van tags. Key Vault ondersteunt Maxi maal 15 Tags, die elk een 256-teken naam en een 256-teken waarde kunnen bevatten.  

>[!Note]
>Labels kunnen worden gelezen door een beller als ze de machtiging *lijst* of *ophalen* hebben.

## <a name="azure-storage-account-key-management"></a>Sleutel beheer van Azure Storage account

Key Vault kunt sleutels van Azure Storage-account beheren:

- Intern kunnen Key Vault een lijst met sleutels (Sync) met een Azure-opslag account. 
- Key Vault de sleutels regel matig opnieuw genereren (roteert).
- Sleutel waarden worden nooit geretourneerd als antwoord op de aanroeper.
- Key Vault beheert sleutels van zowel opslag accounts als klassieke opslag accounts.

Zie Azure Key Vault-Storage- [account sleutels](../secrets/overview-storage-keys.md)) voor meer informatie.

## <a name="storage-account-access-control"></a>Toegangs beheer voor opslag accounts

De volgende machtigingen kunnen worden gebruikt voor het autoriseren van een gebruiker of toepassings-principal voor het uitvoeren van bewerkingen op een beheerd opslag account:  

- Machtigingen voor beheerde opslag accounts en SaS-definitie bewerkingen
  - *ophalen*: Hiermee wordt informatie over een opslag account opgehaald 
  - *lijst*: opslag accounts weer geven die worden beheerd door een Key Vault
  - *bijwerken*: een opslag account bijwerken
  - *verwijderen*: een opslag account verwijderen  
  - *herstellen*: een verwijderd opslag account herstellen
  - *back-up*: back-up maken van een opslag account
  - *herstellen*: een back-up van een opslag account terugzetten naar een Key Vault
  - *instellen*: een opslag account maken of bijwerken
  - *regeneratekey*: een opgegeven sleutel waarde voor een opslag account opnieuw genereren
  - *getsas*: informatie over een SAS-definitie voor een opslag account ophalen
  - *listsas*: SAS-definities voor opslag accounts weer geven
  - *deletesas*: een SAS-definitie uit een opslag account verwijderen
  - *setsas*: een nieuwe SAS-definitie/kenmerken voor een opslag account maken of bijwerken

- Machtigingen voor bevoegde bewerkingen
  - *opschonen*: een beheerd opslag Account leegmaken (definitief verwijderen)

Zie voor meer informatie de [bewerkingen voor opslag accounts in de naslag informatie over Key Vault rest API](/rest/api/keyvault). Zie voor meer informatie over het instellen van machtigingen [-kluizen-maken of bijwerken](/rest/api/keyvault/vaults/createorupdate) en [kluizen-toegangs beleid bijwerken](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Volgende stappen

- [Over Key Vault](../general/overview.md)
- [Over sleutels, geheimen en certificaten](../general/about-keys-secrets-certificates.md)
- [Over sleutels](../keys/about-keys.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

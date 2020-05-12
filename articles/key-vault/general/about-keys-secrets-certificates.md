---
title: Over Azure Key Vault sleutels, geheimen en certificaten-Azure Key Vault
description: Overzicht van Azure Key Vault REST interface en Details voor ontwikkel aars voor sleutels, geheimen en certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 627dfee81cb10e4e442b3cefb10d786d87d5c81d
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005875"
---
# <a name="about-keys-secrets-and-certificates"></a>Over sleutels, geheimen en certificaten

Met Azure Key Vault kunnen Microsoft Azure toepassingen en gebruikers verschillende soorten geheime/sleutel gegevens opslaan en gebruiken:

- Cryptografische sleutels: ondersteunt meerdere sleutel typen en-algoritmen, en maakt het gebruik van HSM (Hardware Security modules) mogelijk voor sleutels met een hoge waarde. Zie [about Keys](../keys/about-keys.md)(Engelstalig) voor meer informatie.
- Geheimen: biedt beveiligde opslag van geheimen, zoals wacht woorden en database verbindings reeksen. Zie [over geheimen](../secrets/about-secrets.md)voor meer informatie.
- Certificaten: ondersteunt certificaten, die zijn gebaseerd op sleutels en geheimen, en een functie voor automatische verlenging toevoegen. Zie [over certificaten](../certificates/about-certificates.md)voor meer informatie.
- Azure Storage: kan sleutels van een Azure Storage account voor u beheren. Intern kunnen Key Vault met een Azure Storage-account (Sync) sleutels weer geven en de sleutels periodiek opnieuw genereren (draaien). Zie voor meer informatie [beheren van opslag account sleutels met Key Vault](../secrets/overview-storage-keys.md).

Zie [over Azure Key Vault](overview.md)voor meer algemene informatie over Key Vault.

## <a name="data-types"></a>Gegevenstypen

Raadpleeg de JOSE-specificaties voor relevante gegevens typen voor sleutels, versleuteling en ondertekening.  

-   **algoritme** : een ondersteunde algoritme voor een sleutel bewerking, bijvoorbeeld RSA1_5  
-   **gecodeerde** tekst-gecodeerde octetten, gecodeerd met Base64URL  
-   **Digest-value** : de uitvoer van een hash-algoritme, gecodeerd met Base64URL  
-   **sleutel-type** : een van de ondersteunde sleutel typen, bijvoorbeeld RSA (Rivest-Shamir-Adleman).  
-   **tekst zonder opmaak** : octetten met lees bare tekst, gecodeerd met Base64URL  
-   **hand tekening-waarde** -uitvoer van een handtekening algoritme, gecodeerd met Base64URL  
-   **base64URL** : een BASE64URL [RFC4648] gecodeerde binaire waarde  
-   **Booleaans** : waar of onwaar  
-   **Identiteit** : een identiteit van Azure Active Directory (Aad).  
-   **IntDate** : een JSON-decimale waarde waarmee het aantal seconden van 1970-01-01T0:0: 0Z UTC wordt weer gegeven tot de opgegeven UTC-datum/-tijd. Zie RFC3339 voor meer informatie over datum/tijd, in het algemeen en UTC in het bijzonder.  

## <a name="objects-identifiers-and-versioning"></a>Objecten, id's en versie beheer

Objecten die zijn opgeslagen in Key Vault, worden geversiond wanneer er een nieuw exemplaar van een object wordt gemaakt. Aan elke versie wordt een unieke id en URL toegewezen. Wanneer een object voor het eerst wordt gemaakt, wordt er een unieke versie-id gegeven en als de huidige versie van het object gemarkeerd. Het maken van een nieuw exemplaar met dezelfde object naam geeft het nieuwe object een unieke versie-id, waardoor het de huidige versie wordt.  

Objecten in Key Vault kunnen worden geadresseerd door specifing van een versie of door de versie voor bewerkingen op de huidige versie van het object uit te laten gaan. Als er bijvoorbeeld een sleutel met de naam `MasterKey`wordt uitgevoerd en de uitvoering van bewerkingen zonder specifing-versie is, zorgt het systeem ervoor dat de meest recente beschik bare versie wordt gebruikt. Door bewerkingen uit te voeren met de versie-specifieke id wordt het systeem gebruikt die specifieke versie van het object.  

Objecten worden uniek geïdentificeerd binnen Key Vault met behulp van een URL. Er zijn geen twee objecten in het systeem die dezelfde URL hebben, ongeacht de geografische locatie. De volledige URL naar een object wordt de object-id genoemd. De URL bestaat uit een voor voegsel waarmee de Key Vault, het object type, de door de gebruiker ingevoerde object naam en een object versie worden geïdentificeerd. De object naam is hoofdletter gevoelig en onveranderbaar. Id's die de object versie niet bevatten, worden aangeduid als basis-Id's.  

Zie [verificatie, aanvragen en antwoorden](authentication-requests-and-responses.md) voor meer informatie.

Een object-id heeft de volgende algemene notatie:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Waar:  

|||  
|-|-|  
|`keyvault-name`|De naam voor een sleutel kluis in de Microsoft Azure Key Vault-service.<br /><br /> Key Vault namen worden geselecteerd door de gebruiker en zijn wereld wijd uniek.<br /><br /> Key Vault naam moet een teken reeks van 3-24 zijn die alleen 0-9, a-z, A-Z en-bevat.|  
|`object-type`|Het type van het object, "sleutels", "geheimen" of "certificaten".|  
|`object-name`|Een `object-name` is een door de gebruiker ingevoerde naam voor en moet uniek zijn binnen een Key Vault. De naam moet een teken reeks van 1-127 zijn die begint met een letter en alleen 0-9, a-z, A-Z en-bevat.|  
|`object-version`|Een `object-version` door het systeem gegenereerde 32 teken reeks-id die optioneel wordt gebruikt om een unieke versie van een object aan te pakken.|  

## <a name="next-steps"></a>Volgende stappen

- [Over sleutels](../keys/about-keys.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

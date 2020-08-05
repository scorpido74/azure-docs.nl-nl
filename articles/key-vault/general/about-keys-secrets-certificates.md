---
title: 'Over Azure Key Vault-sleutels, geheimen en certificaten: Azure Key Vault'
description: Overzicht van REST-interface van Azure Key Vault en detailinformatie voor ontwikkelaars over sleutels, geheimen en certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: cb8a29c5d2eff46eecb2cf977bfb492f28731e68
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87043633"
---
# <a name="about-keys-secrets-and-certificates"></a>Over sleutels, geheimen en certificaten

Met Azure Key Vault kunnen Microsoft Azure-toepassingen en -gebruikers verschillende typen geheim-/sleutelgegevens opslaan en gebruiken:

- Cryptografische sleutels: Biedt ondersteuning voor meerdere sleutels en algoritmes, en maakt het gebruik van HSM (Hardware Security Modules) mogelijk voor sleutels met een hoge waarde. Zie [Over sleutels](../keys/about-keys.md) voor meer informatie.
- Geheimen: Biedt beveiligde opslag van geheimen, zoals wachtwoorden en databaseverbindingsreeksen. Zie [Over geheimen](../secrets/about-secrets.md) voor meer informatie.
- Certificaten: Ondersteunt certificaten, die worden gebouwd op basis van sleutels en geheimen en een functie voor automatisch verlengen toevoegen. Zie [Over certificaten](../certificates/about-certificates.md) voor meer informatie.
- Azure Storage: Kan sleutels van een Azure Storage-account voor u beheren. Intern kan Key Vault sleutels synchroniseren met een Azure Storage-account en de sleutels periodiek opnieuw genereren (rouleren). Zie [Opslagaccountsleutels beheren met Key Vault](../secrets/overview-storage-keys.md) voor meer informatie.

Zie [Over Azure Key Vault](overview.md) voor meer algemene informatie over Azure Key Vault.

## <a name="data-types"></a>Gegevenstypen

Raadpleeg de JOSE-specificaties voor relevante gegevenstypen voor sleutels, versleuteling en ondertekening.  

-   **algorithm**: een ondersteund algoritme voor een sleutelbewerking, bijvoorbeeld RSA1_5  
-   **ciphertext-value**: ciphertekstoctetten, gecodeerd met Base64URL  
-   **digest-value**: de uitvoer van een hash-algoritme, gecodeerd met Base64URL  
-   **key-type**: een van de ondersteunde sleuteltypen, bijvoorbeeld RSA (Rivest-Shamir-Adleman).  
-   **plaintext-value** - niet-versleutelde-tekstoctetten, gecodeerd met Base64URL  
-   **signature-value**: uitvoer van een handtekeningalgoritme, gecodeerd met Base64URL  
-   **base64URL**: een met Base64URL [RFC4648] gecodeerde binaire waarde  
-   **boolean**: waar of onwaar  
-   **Identity**: een identiteit van AAD (Azure Active Directory).  
-   **IntDate**: een decimale JSON-waarde die het aantal seconden van 1970-01-01T0:0: 0Z UTC tot de opgegeven UTC-datum/-tijd vertegenwoordigt. Zie RFC3339 voor meer informatie over datums/tijden in het algemeen en UTC in het bijzonder.  

## <a name="objects-identifiers-and-versioning"></a>Objecten, id's en versiebeheer

In Key Vault opgeslagen objecten worden geversioneerd telkens wanneer er een nieuw exemplaar van een object wordt gemaakt. Aan elke versie wordt een unieke id en URL toegewezen. Wanneer een object voor het eerst wordt gemaakt, krijgt het een unieke versie-id en wordt het gemarkeerd als de huidige versie van het object. Het maken van een nieuw exemplaar met dezelfde objectnaam geeft het nieuwe object een unieke versie-id, waardoor het de huidige versie wordt.  

Objecten in Key Vault kunnen worden geadresseerd door een versie op te geven of door de versie weg te laten voor bewerkingen op de huidige versie van het object. Als er bijvoorbeeld een sleutel met de naam `MasterKey` is, wordt de nieuwste beschikbare versie gebruikt als er bewerkingen worden uitgevoerd zonder een versie op te geven. Door bewerkingen uit te voeren met de versie-specifieke id wordt die specifieke versie van het object gebruikt.  

Objecten worden binnen Key Vault uniek geïdentificeerd met behulp van een URL. Geen twee objecten in het systeem hebben dezelfde URL, ongeacht hun geografische locatie. De volledige URL van een object wordt de object-id genoemd. De URL bestaat uit een voorvoegsel waarmee de sleutelkluis wordt geïdentificeerd, een objecttype, een door de gebruiker opgegeven objectnaam en een objectversie. De objectnaam is hoofdlettergevoelig en onveranderbaar. Id's die niet de objectversie bevatten, worden aangeduid als basis-id's.  

Zie [Verificatie, aanvragen en antwoorden](authentication-requests-and-responses.md) voor meer informatie

Een object-id heeft de volgende algemene notatie:  

`https://{keyvault-name}.vault.azure.net/{object-type}/{object-name}/{object-version}`  

Waar:  

| Element | Beschrijving |  
|-|-|  
|`keyvault-name`|De naam voor een sleutelkluis in de Microsoft Azure Key Vault-service.<br /><br /> Sleutelkluisnamen worden geselecteerd door de gebruiker en zijn wereldwijd uniek.<br /><br /> De naam van een sleutelkluis moet een tekenreeks van 3 tot 24 tekens lang zijn en mag alleen de tekens 0-9, a-z, A-Z en - bevatten.|  
|`object-type`|Het type van het object, "sleutels", "geheimen" of "certificaten".|  
|`object-name`|Een `object-name` is een door de gebruiker ingevoerde naam en moet uniek zijn binnen een sleutelkluis. De naam moet een reeks van 1-127 tekens zijn die begint met een letter en alleen 0-9, a-z, A-Z en - bevat.|  
|`object-version`|Een `object-version` is een door het systeem gegenereerde id van 32 tekens die optioneel wordt gebruikt om een unieke versie van een object te adresseren.|  

## <a name="next-steps"></a>Volgende stappen

- [Over sleutels](../keys/about-keys.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)

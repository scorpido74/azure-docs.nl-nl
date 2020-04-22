---
title: Over Azure Key Vault-sleutels, -geheimen en -certificaten - Azure Key Vault
description: Overzicht van de Azure Key Vault REST-interface en ontwikkelaarsgegevens voor sleutels, geheimen en certificaten.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: overview
ms.date: 04/17/2020
ms.author: mbaldwin
ms.openlocfilehash: 241efab246dc903981da570a4191f93cc744bca7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726443"
---
# <a name="about-keys-secrets-and-certificates"></a>Over sleutels, geheimen en certificaten

Azure Key Vault stelt Microsoft Azure-toepassingen en -gebruikers in staat om verschillende soorten geheime/sleutelgegevens op te slaan en te gebruiken:

- Cryptografische sleutels: ondersteunt meerdere sleuteltypen en algoritmen en maakt het gebruik van Hardware Security Modules (HSM) mogelijk voor sleutels met een hoge waarde. Zie [Over toetsen voor](../keys/about-keys.md)meer informatie.
- Geheimen: biedt veilige opslag van geheimen, zoals wachtwoorden en databaseverbindingstekenreeksen. Zie [Over geheimen](../secrets/about-secrets.md)voor meer informatie.
- Certificaten: ondersteunt certificaten, die zijn gebouwd op de top van sleutels en geheimen en voeg een geautomatiseerde vernieuwing functie. Zie [Over certificaten voor](../certificates/about-certificates.md)meer informatie .
- Azure Storage: kan sleutels van een Azure Storage-account voor u beheren. Intern kan Key Vault sleutels (synchroniseren) weergeven met een Azure Storage-account en de sleutels periodiek regenereren (roteren). Zie [Opslagaccountsleutels beheren met Key Vault](../secrets/overview-storage-keys.md)voor meer informatie.

Zie Over Azure Key Vault voor meer algemene informatie over Key [Vault.](overview.md)

## <a name="data-types"></a>Gegevenstypen

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

## <a name="objects-identifiers-and-versioning"></a>Objecten, id's en versiebeheer

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
|`object-type`|Het type object, 'sleutels', 'geheimen' of 'certificaten'.|  
|`object-name`|An `object-name` is een door de gebruiker opgegeven naam en moet uniek zijn in een Key Vault. De naam moet een tekenreeks van 1-127 tekens zijn, die slechts 0-9, a-z, A-Z en -bevat.|  
|`object-version`|An `object-version` is een systeemgegenereerde, 32-tekentekenreeks-id die optioneel wordt gebruikt om een unieke versie van een object aan te pakken.|  

## <a name="next-steps"></a>Volgende stappen

- [Over sleutels](../keys/about-keys.md)
- [Over geheimen](../secrets/about-secrets.md)
- [Over certificaten](../certificates/about-certificates.md)
- [Verificatie, aanvragen en antwoorden](../general/authentication-requests-and-responses.md)
- [Gids voor Key Vault-ontwikkelaars](../general/developers-guide.md)
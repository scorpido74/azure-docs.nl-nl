---
title: Algemene parameters en headers
description: De para meters en kopteksten die gemeen schappelijk zijn voor alle bewerkingen die u kunt uitvoeren in verband met Key Vault resources.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: d1d93bcd84fd9460e658b221089a4b24d46b0429
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/10/2020
ms.locfileid: "83005821"
---
# <a name="common-parameters-and-headers"></a>Algemene parameters en headers

De volgende informatie is gebruikelijk voor alle bewerkingen die u kunt uitvoeren in verband met Key Vault resources:

- De HTTP `Host` -header moet altijd aanwezig zijn en moet de kluis hostnaam opgeven. Bijvoorbeeld: `Host: contoso.vault.azure.net`. Houd er rekening mee dat de meeste `Host` client technologieën de header van de URI vullen. Bijvoorbeeld: `GET https://contoso.vault.azure.net/secrets/mysecret{...}` de `Host` as wordt ingesteld als `contoso.vault.azure.net`. Dit betekent dat als u Key Vault gebruikt voor onbewerkte IP `GET https://10.0.0.23/secrets/mysecret{...}`-adressen, de automatische `Host` waarde van de koptekst onjuist is en u hand matig zeker weet dat de `Host` header de kluis-hostnaam bevat.
- Vervang `{api-version}` door de API-versie in de URI.
- Vervang `{subscription-id}` door de id van uw abonnement in de URI
- Vervang `{resource-group-name}` door de resource groep. Zie Resource groepen gebruiken om Azure-resources te beheren voor meer informatie.
- Vervang `{vault-name}` door de naam van uw sleutel kluis in de URI.
- Stel de content-type-header in op Application/JSON.
- Stel de autorisatie-header in op een JSON Web Token die u van Azure Active Directory (AAD) hebt verkregen. Zie voor meer informatie [verificatie van Azure Resource Manager](authentication-requests-and-responses.md) aanvragen.

## <a name="common-error-response"></a>Veelvoorkomende fout melding
De service gebruikt HTTP-status codes om aan te geven of de fout is geslaagd. Daarnaast bevatten fouten een antwoord met de volgende indeling:

```
   {  
     "error": {  
     "code": "BadRequest",  
     "message": "The key vault sku is invalid."  
     }  
   }  
```

|Elementnaam | Type | Beschrijving |
|---|---|---|
| code | tekenreeks | Het type fout dat is opgetreden.|
| message | tekenreeks | Een beschrijving van de oorzaak van de fout. |



## <a name="see-also"></a>Zie ook
 [Naslag informatie over Azure Key Vault REST API](/rest/api/keyvault/)

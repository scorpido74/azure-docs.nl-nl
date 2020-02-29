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
ms.openlocfilehash: d0ada9c1e6b45b1be17b15b67f67fc64fc266203
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78197586"
---
# <a name="common-parameters-and-headers"></a>Algemene parameters en headers

De volgende informatie is gebruikelijk voor alle bewerkingen die u kunt uitvoeren in verband met Key Vault resources:

- Vervang `{api-version}` door de API-versie in de URI.
- `{subscription-id}` vervangen door de id van uw abonnement in de URI
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

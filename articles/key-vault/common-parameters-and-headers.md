---
title: Algemene parameters en headers
description: De para meters en kopteksten die gemeen schappelijk zijn voor alle bewerkingen die u kunt uitvoeren in verband met Key Vault resources.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: mbaldwin
ms.openlocfilehash: 7ec8e1df71e6513b13e9c37174a3363471be01d9
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70879278"
---
# <a name="common-parameters-and-headers"></a>Algemene parameters en headers

De volgende informatie is gebruikelijk voor alle bewerkingen die u kunt uitvoeren in verband met Key Vault resources:

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

|De naam van element | type | Description |
|---|---|---|
| code | string | Het type fout dat is opgetreden.|
| message | string | Een beschrijving van de oorzaak van de fout. |



## <a name="see-also"></a>Zie ook
 [Naslag informatie over Azure Key Vault REST API](/rest/api/keyvault/)

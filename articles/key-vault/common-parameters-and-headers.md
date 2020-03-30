---
title: Algemene parameters en headers
description: De parameters en kopteksten die u mogelijk gebruikt met betrekking tot Key Vault-resources.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197586"
---
# <a name="common-parameters-and-headers"></a>Algemene parameters en headers

De volgende informatie is gemeenschappelijk voor alle bewerkingen die u mogelijk uitvoert met betrekking tot Key Vault-bronnen:

- Vervang `{api-version}` door de api-versie in de URI.
- Vervangen `{subscription-id}` door uw abonnements-id in de URI
- Vervang `{resource-group-name}` door de resourcegroep. Zie Resourcegroepen gebruiken om uw Azure-bronnen te beheren voor meer informatie.
- Vervang `{vault-name}` door de naam van uw sleutelkluis in de URI.
- Stel de header Content-Type in op toepassing/json.
- Stel de koptekst Autorisatie in op een JSON-webtoken dat u hebt verkregen uit Azure Active Directory (AAD). Zie [Azure Resource Manager-aanvragen verifiëren](authentication-requests-and-responses.md) voor meer informatie.

## <a name="common-error-response"></a>Veelvoorkomende foutreactie
De service gebruikt HTTP-statuscodes om succes of fout aan te geven. Bovendien bevatten fouten een antwoord in de volgende indeling:

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
 [Naslaginformatie over Azure Key Vault REST API](/rest/api/keyvault/)

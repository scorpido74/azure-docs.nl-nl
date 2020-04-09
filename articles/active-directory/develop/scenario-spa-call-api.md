---
title: Een app voor één pagina maken die een web-API aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een toepassing van één pagina die een web-API aanroept
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: 5b70b109f43e80fc3ec68f52aef2dba6823033bb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882128"
---
# <a name="single-page-application-call-a-web-api"></a>Toepassing met één pagina: een web-API aanroepen

We raden u `acquireTokenSilent` aan de methode aan te roepen om een toegangstoken te verkrijgen of te vernieuwen voordat u een web-API aanroept. Nadat u een token hebt, u een beveiligde web-API aanroepen.

## <a name="call-a-web-api"></a>Een web-API aanroepen

# <a name="javascript"></a>[Javascript](#tab/javascript)

Gebruik het verkregen toegangstoken als drager in een HTTP-verzoek om een web-API aan te roepen, zoals Microsoft Graph API. Bijvoorbeeld:

```javascript
    var headers = new Headers();
    var bearer = "Bearer " + access_token;
    headers.append("Authorization", bearer);
    var options = {
         method: "GET",
         headers: headers
    };
    var graphEndpoint = "https://graph.microsoft.com/v1.0/me";

    fetch(graphEndpoint, options)
        .then(function (response) {
             //do something with response
        }
```

# <a name="angular"></a>[Angular](#tab/angular)

De MSAL Angular wrapper maakt gebruik van de HTTP interceptor om automatisch toegangstokens in stilte te verkrijgen en deze aan de HTTP-verzoeken aan API's te koppelen. Zie [Een token aanschaffen om een API aan te roepen](scenario-spa-acquire-token.md)voor meer informatie.

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Verplaatsen naar productie](scenario-spa-production.md)

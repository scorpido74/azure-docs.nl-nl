---
title: Toepassing met één pagina (een web-API aanroepen)-micro soft Identity-platform
description: Meer informatie over het bouwen van een toepassing met één pagina (een web-API aanroepen)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/06/2019
ms.author: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4170a6642d35802581b5d1ff28eb802a6eb3482b
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74766154"
---
# <a name="single-page-application-call-a-web-api"></a>Toepassing met één pagina: een web-API aanroepen

U wordt aangeraden de `acquireTokenSilent`-methode aan te roepen om een toegangs token te verkrijgen of te vernieuwen voordat u een web-API aanroept. Nadat u een token hebt, kunt u een beveiligde web-API aanroepen.

## <a name="call-a-web-api"></a>Een web-API aanroepen

### <a name="javascript"></a>Javascript

Gebruik het verkregen toegangs token als een Bearer in een HTTP-aanvraag om een web-API, zoals Microsoft Graph-API, aan te roepen. Bijvoorbeeld:

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

### <a name="angular"></a>Angular

De MSAL-hoek wrapper maakt gebruik van de HTTP-Interceptor om automatisch toegangs tokens op de achtergrond te verkrijgen en deze te koppelen aan de HTTP-aanvragen aan Api's. Zie [een token verkrijgen voor het aanroepen van een API](scenario-spa-acquire-token.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Naar productie verplaatsen](scenario-spa-production.md)

---
title: Een app met één pagina configureren-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een toepassing met één pagina (de code configuratie van de app)
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: b42d720a425b92ec9002f7c2b9797a91f70dafe2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89003021"
---
# <a name="single-page-application-code-configuration"></a>Toepassing met één pagina: code configuratie

Meer informatie over het configureren van de code voor een toepassing met één pagina (SPA).

## <a name="msal-libraries-for-spas-and-supported-authentication-flows"></a>MSAL-bibliotheken voor SPAs en ondersteunde verificatie stromen

Het micro soft Identity-platform biedt de volgende micro soft-verificatie bibliotheek voor Java script (MSAL.js) ter ondersteuning van een impliciete stroom-en autorisatie code stroom met PKCE met behulp van door de branche aanbevolen beveiligings procedures:

| MSAL-bibliotheek | Stroom | Beschrijving |
|--------------|------|-------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (2. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) | Autorisatie code stroom (PKCE) | Een gewone Java script-bibliotheek voor gebruik in een webtoepassing aan de client zijde die is gebouwd via Java script-of SPA-frameworks, zoals hoek, Vue.js en React.js. |
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js (1. x)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-core) | Impliciete stroom | Een gewone Java script-bibliotheek voor gebruik in een webtoepassing aan de client zijde die is gebouwd via Java script-of SPA-frameworks, zoals hoek, Vue.js en React.js. |
| ![MSAL hoek](media/sample-v2-code/logo_angular.png) <br/> [MSAL hoek](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Impliciete stroom | Wrapper van de core MSAL.js-bibliotheek om gebruik te vereenvoudigen in apps met één pagina die zijn gebouwd op basis van het hoek kader. |

## <a name="application-code-configuration"></a>Configuratie van de toepassings code

In een MSAL-bibliotheek worden de registratie gegevens van de toepassing door gegeven als configuratie tijdens de initialisatie van de bibliotheek.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_client_id'
    }
};

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Zie voor meer informatie over de Configureer bare opties [toepassing initialiseren met MSAL.js](msal-js-initializing-client-applications.md).

# <a name="angular"></a>[Angular](#tab/angular)

```javascript
// App.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
    imports: [
        MsalModule.forRoot({
            auth: {
                clientId: 'your_app_id'
            }
        })
    ]
})

export class AppModule { }
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](scenario-spa-sign-in.md)

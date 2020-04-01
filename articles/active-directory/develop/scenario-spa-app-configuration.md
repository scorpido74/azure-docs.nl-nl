---
title: App voor één pagina configureren - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een toepassing met één pagina (de codeconfiguratie van de app)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/11/2020
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: c1799b25ec1adf44342d2305d3b2a29039c39cd4
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419708"
---
# <a name="single-page-application-code-configuration"></a>Toepassing met één pagina: codeconfiguratie

Meer informatie over het configureren van de code voor uw toepassing op één pagina (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>MSAL-bibliotheken die impliciete stroom ondersteunen

Het Microsoft-identiteitsplatform biedt de volgende MSAL-bibliotheken (Microsoft Authentication Library) om impliciete stroom te ondersteunen door gebruik te maken van door de industrie aanbevolen beveiligingspraktijken:

| MSAL-bibliotheek | Beschrijving |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Eenvoudige JavaScript-bibliotheek voor gebruik in elke web-app aan de clientzijde die is gebouwd via JavaScript- of SPA-frameworks zoals Angular, Vue.js en React.js. |
| ![MSAL Hoekig](media/sample-v2-code/logo_angular.png) <br/> [MSAL Hoekig](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper van de kern MSAL.js bibliotheek om het gebruik te vereenvoudigen in single-page apps die zijn gebouwd via de Hoekige kader. |

## <a name="application-code-configuration"></a>Configuratie van toepassingscode

In een MSAL-bibliotheek wordt de registratiegegevens van de toepassing doorgegeven als configuratie tijdens de initialisatie van de bibliotheek.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
// Configuration object constructed.
const config = {
    auth: {
        clientId: 'your_app_id',
        redirectUri: "your_app_redirect_uri" //defaults to application start page
    }
}

// create UserAgentApplication instance
const userAgentApplication = new UserAgentApplication(config);
```

Zie [Initialiseren toepassing met MSAL.js](msal-js-initializing-client-applications.md)voor meer informatie over de configureerbare opties.

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

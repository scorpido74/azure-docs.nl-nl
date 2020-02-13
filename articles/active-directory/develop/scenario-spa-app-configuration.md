---
title: Een app met één pagina configureren-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een toepassing met één pagina (de code configuratie van de app)
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
ms.openlocfilehash: f1e0bf44515aab18019b19b4f0a6f84183e5aac3
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/12/2020
ms.locfileid: "77160080"
---
# <a name="single-page-application-code-configuration"></a>Toepassing met één pagina: code configuratie

Meer informatie over het configureren van de code voor een toepassing met één pagina (SPA).

## <a name="msal-libraries-that-support-implicit-flow"></a>MSAL-bibliotheken die ondersteuning bieden voor impliciete stroom

Het micro soft Identity-platform biedt de volgende MSAL-bibliotheken (micro soft Authentication Library) ter ondersteuning van impliciete stroom door gebruik te maken van door de branche aanbevolen beveiligings procedures:  

| MSAL-bibliotheek | Beschrijving |
|--------------|--------------|
| ![MSAL. js](media/sample-v2-code/logo_js.png) <br/> [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Een gewone Java script-bibliotheek voor gebruik in een webtoepassing aan de client zijde die is gebouwd via Java script-of SPA-frameworks, zoals hoek, vue. js en reageren. js. |
| ![hoek van MSAL](media/sample-v2-code/logo_angular.png) <br/> [MSAL hoek](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper van de core MSAL. JS-bibliotheek om gebruik te vereenvoudigen in apps met één pagina die zijn gebouwd met behulp van het hoek kader. Deze bibliotheek is beschikbaar als preview-versie en heeft [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) met bepaalde hoek versies en browsers. |

## <a name="application-code-configuration"></a>Configuratie van de toepassings code

In een MSAL-bibliotheek worden de registratie gegevens van de toepassing door gegeven als configuratie tijdens de initialisatie van de bibliotheek.

# <a name="javascripttabjavascript"></a>[JavaScript](#tab/javascript)

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

Zie voor meer informatie over de Configureer bare opties [toepassing initialiseren met MSAL. js](msal-js-initializing-client-applications.md).

# <a name="angulartabangular"></a>[Angular](#tab/angular)

```javascript
//In app.module.ts
import { MsalModule } from '@azure/msal-angular';

@NgModule({
  imports: [ MsalModule.forRoot({
                clientID: 'your_app_id'
            })]
         })

  export class AppModule { }
```

---

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](scenario-spa-sign-in.md)

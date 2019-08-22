---
title: Toepassing met één pagina (code configuratie van de app)-micro soft Identity-platform
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
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7b4fba03f9edf8a3f4e42b23c6a1b5e06518863
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891536"
---
# <a name="single-page-application---code-configuration"></a>Configuratie van een toepassing met één pagina

Meer informatie over het configureren van de code voor een toepassing met één pagina (SPA).

## <a name="msal-libraries-supporting-implicit-flow"></a>MSAL-bibliotheken die impliciete stroom ondersteunen

Micro soft Identity platform biedt MSAL. JS-bibliotheek ter ondersteuning van de impliciete stroom met behulp van de door de branche aanbevolen veilige prak tijken.  

De bibliotheken die impliciete stromen ondersteunen, zijn:

| MSAL-bibliotheek | Description |
|--------------|--------------|
| ![MSAL.js](media/sample-v2-code/logo_js.png) <br/> [MSAL.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)  | Een gewone Java script-bibliotheek voor gebruik in een web-app aan de client zijde die is gebouwd met behulp van Java script-of SPA-frameworks, zoals hoek, vue. js, reageren. js, enzovoort. |
| ![MSAL hoek](media/sample-v2-code/logo_angular.png) <br/> [MSAL hoek](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | Wrapper van de core MSAL. JS-bibliotheek om gebruik te vereenvoudigen in apps met één pagina die zijn gebouwd met het hoek kader. Deze bibliotheek is beschikbaar als preview-versie en heeft [bekende problemen](https://github.com/AzureAD/microsoft-authentication-library-for-js/issues?q=is%3Aopen+is%3Aissue+label%3Aangular) met bepaalde hoek versies en browsers. |

## <a name="application-code-configuration"></a>Configuratie van de toepassings code

In de MSAL-bibliotheek worden de registratie gegevens van de toepassing door gegeven als configuratie tijdens de initialisatie van de bibliotheek.

### <a name="javascript"></a>JavaScript

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
Zie voor meer informatie over de Configureer bare opties die beschikbaar zijn, initialiseren van de [toepassing met MSAL. js](msal-js-initializing-client-applications.md).

### <a name="angular"></a>Angular

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

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aanmelden en afmelden](scenario-spa-sign-in.md)

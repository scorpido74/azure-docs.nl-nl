---
title: Meer informatie over hoe toepassingen kunnen werken met Azure AD B2C met behulp van micro soft-verificatie bibliotheek
description: Met micro soft Authentication Library (MSAL) kunnen toepassingen gelijktijdig gebruikmaken van Azure AD B2C en tokens verkrijgen om beveiligde web-Api's aan te roepen. Deze web-Api's kunnen worden Microsoft Graph, andere Api's van micro soft, Web-Api's van anderen of uw eigen web-API.
services: active-directory
documentationcenter: dev-center-name
author: negoe
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0eea0fd03b1df49e912a867b0c667ff0fd28c08a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71097625"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Micro soft-verificatie bibliotheek gebruiken om te werken met Azure Active Directory B2C

Met micro soft Authentication Library (MSAL) kunnen ontwikkel aars van toepassingen gebruikers met sociale en lokale identiteiten verifiëren met behulp van [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Azure AD B2C is een service voor identiteits beheer. Door het te gebruiken, kunt u aanpassen en bepalen hoe klanten zich registreren, aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken.

Met Azure AD B2C kunt u ook de gebruikers interface van uw toepassingen branden en aanpassen om uw klanten een naadloze ervaring te bieden.

In deze zelf studie wordt gedemonstreerd hoe u MSAL kunt gebruiken om te werken met Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C Tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)hebt gemaakt, maakt u er nu een. U kunt ook een bestaande Azure AD B2C Tenant gebruiken.

## <a name="javascript"></a>JavaScript

De volgende stappen laten zien hoe een toepassing met één pagina kan Azure AD B2C gebruiken om zich aan te melden, zich aan te melden en een beveiligde web-API aan te roepen.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u verificatie wilt implementeren, moet u de toepassing eerst registreren. Zie [uw toepassing registreren](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: De voorbeeldtoepassing downloaden

Down load het voor beeld als een zip-bestand of kloon het uit GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: Verificatie configureren

1. Open het bestand **index. html** in het voor beeld.

1. Configureer het voor beeld met de client-ID en-sleutel die u eerder hebt vastgelegd tijdens het registreren van uw toepassing. Wijzig de volgende regels met code door de waarden te vervangen door de namen van uw map en API's:

   ```javascript
   // The current application coordinates were pre-registered in a B2C tenant.

    var appConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"],
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };

    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902" //This is your client/application ID
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/b2c_1_susi", //This is your tenant info
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage",
            storeAuthStateInCookie: true
        }
    };
    // create UserAgentApplication instance
    const myMSALObj = new Msal.UserAgentApplication(msalConfig);

   ```

De naam van de [gebruikers stroom](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) in deze zelf studie is **B2C_1_signupsignin1**. Als u een andere gebruikers stroom naam gebruikt, stelt u de waarde van de **instantie** in op die naam.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Stap 4: Uw toepassing configureren voor gebruik`b2clogin.com`

U kunt in `b2clogin.com` plaats van `login.microsoftonline.com` gebruiken als omleidings-URL. U doet dit in uw Azure AD B2C-toepassing bij het instellen van een id-provider voor registratie en aanmelding.

Gebruik van `b2clogin.com` in de context van `https://your-tenant-name.b2clogin.com/your-tenant-guid` heeft de volgende effecten:

- Micro soft-Services verbruiken minder ruimte in de cookie-header.
- Uw Url's bevatten niet langer een verwijzing naar micro soft. Zo verwijst uw Azure AD B2C-toepassing waarschijnlijk naar `login.microsoftonline.com`.

 Als u `b2clogin.com`wilt gebruiken, moet u de configuratie van uw toepassing bijwerken.  

- Stel de eigenschap **validateAuthority** in `false`op, zodat er omleidingen `b2clogin.com` kunnen worden gebruikt.

In het volgende voor beeld ziet u hoe u de eigenschap kunt instellen:

```javascript
// The current application coordinates were pre-registered in a B2C directory.

const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://contoso.b2clogin.com/tfp/contoso.onmicrosoft.com/B2C_1_signupsignin1",
        b2cScopes: ["https://contoso.onmicrosoft.com/demoapi/demo.read"],
        webApi: 'https://contosohello.azurewebsites.net/hello',
        validateAuthority: false;

};
// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

> [!NOTE]
> Uw Azure AD B2C-toepassing verwijst waarschijnlijk `login.microsoftonline.com` naar verschillende locaties, zoals de verwijzingen naar uw gebruikers stroom en Token-eind punten. Zorg ervoor dat uw autorisatie-eind punt, het eind punt van het token en de uitgever `your-tenant-name.b2clogin.com`zijn bijgewerkt voor gebruik.

Volg [deze MSAL java script](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) -voor beeld over het gebruik van de MSAL Preview voor Java script (MSAL. js). Het voor beeld haalt een toegangs token op en roept een API aan die is beveiligd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Aanpassing van de gebruikers interface](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)
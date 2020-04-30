---
title: MSAL gebruiken met Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Met de micro soft-verificatie bibliotheek voor Java script (MSAL. js) kunnen toepassingen met Azure AD B2C werken en tokens verkrijgen voor het aanroepen van beveiligde web-Api's. Deze web-Api's kunnen worden Microsoft Graph, andere Api's van micro soft, Web-Api's van anderen of uw eigen web-API.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/16/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 8e076dfd6670265d458eb35d8e1b3e4500009a12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534479"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Micro soft-verificatie bibliotheek voor Java script gebruiken om te werken met Azure Active Directory B2C

Met de [micro soft-verificatie bibliotheek voor Java script (MSAL. js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) kunnen java script-ontwikkel aars gebruikers verifiëren met sociale en lokale identiteiten met behulp van [Azure Active Directory B2C (Azure AD B2C)](https://docs.microsoft.com/azure/active-directory-b2c/). Door Azure AD B2C als een service voor identiteits beheer te gebruiken, kunt u aanpassen en bepalen hoe klanten zich registreren, aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken.

Met Azure AD B2C kunt u ook de gebruikers interface van uw toepassingen tijdens het verificatie proces branden en aanpassen om uw klanten een naadloze ervaring te bieden.

In dit artikel wordt gedemonstreerd hoe u MSAL. js kunt gebruiken om te werken met Azure AD B2C en hoe u belang rijke punten samenvat waarvan u rekening moet houden. Raadpleeg de [documentatie van Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/overview)voor een volledige bespreking en zelf studie.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C Tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)hebt gemaakt, begint u met het maken van een nu (u kunt ook een bestaande Azure AD B2C Tenant gebruiken als u er al een hebt).

Deze demonstratie bevat twee onderdelen:

- een web-API beveiligen.
- een toepassing met één pagina registreren om *die* Web-API te verifiëren en aan te roepen.

## <a name="nodejs-web-api"></a>Node.js-web-API

> [!NOTE]
> Op dit moment is MSAL. js voor het knoop punt nog in ontwikkeling (Zie het [schema](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)). In de tussen tijd maken we gebruik van [Pass Port-Azure-AD](https://github.com/AzureAD/passport-azure-ad), een verificatie bibliotheek voor node. js ontwikkeld en ondersteund door micro soft.

De volgende stappen laten zien hoe een **Web-API** Azure AD B2C kan gebruiken om zichzelf te beschermen en geselecteerde bereiken beschikbaar te maken voor een client toepassing.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u uw web-API met Azure AD B2C wilt beveiligen, moet u deze eerst registreren. Zie [uw toepassing registreren](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: de voorbeeld toepassing downloaden

Down load het voor beeld als een zip-bestand of kloon het uit GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: verificatie configureren

1. Open het bestand `config.js` in het voorbeeld.

2. Configureer het voor beeld met de referenties van de toepassing die u eerder hebt verkregen tijdens het registreren van uw toepassing. Wijzig de volgende regels code door de waarden te vervangen door de namen van uw clientID, host, tenantId en beleids naam.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Bekijk voor meer informatie dit [node. js B2C Web-API](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)-voor beeld.

---

## <a name="javascript-spa"></a>Java script SPA

De volgende stappen laten zien hoe een **toepassing met één pagina** kan Azure AD B2C gebruiken om zich aan te melden, zich aan te melden en een beveiligde web-API aan te roepen.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u verificatie wilt implementeren, moet u de toepassing eerst registreren. Zie [uw toepassing registreren](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: de voorbeeld toepassing downloaden

Down load het voor beeld als een zip-bestand of kloon het uit GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: verificatie configureren

Er zijn twee belang rijke punten bij het configureren van uw toepassing:

- API-eind punt en weer gegeven bereiken configureren
- Verificatie parameters en Token scopes configureren

1. Open het bestand `apiConfig.js` in het voorbeeld.

2. Configureer het voor beeld met de para meters die u eerder hebt verkregen tijdens het registreren van uw web-API. Wijzig de volgende regels code door de waarden te vervangen door het adres van uw web-API en weer gegeven bereiken.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Open het bestand `authConfig.js` in het voorbeeld.

4. Configureer het voor beeld met de para meters die u eerder hebt verkregen tijdens het registreren van uw toepassing met één pagina. Wijzig de volgende regels code door de waarden te vervangen door uw ClientId-, Authority-meta gegevens en Token aanvraag scopes.

   ```javascript
    // Config object to be passed to Msal on creation.
    const msalConfig = {
        auth: {
            clientId: "e760cab2-b9a1-4c0d-86fb-ff7084abd902",
            authority: "https://fabrikamb2c.b2clogin.com/fabrikamb2c.onmicrosoft.com/B2C_1_signupsignin1",
            validateAuthority: false
        },
        cache: {
            cacheLocation: "localStorage", // This configures where your cache will be stored
            storeAuthStateInCookie: false // Set this to "true" to save cache in cookies
        }
    };

    // Add here scopes for id token to be used at the MS Identity Platform endpoint
    const loginRequest = {
        scopes: ["openid", "profile"],
    };
   ```

Lees voor meer informatie dit [Java script B2C-voorbeeld toepassing voor één pagina](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:
- [Gebruikers stromen](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX-aanpassing](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)

---
title: MSAL gebruiken met Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Met Microsoft Authentication Library for JavaScript (MSAL.js) kunnen toepassingen werken met Azure AD B2C en tokens aanschaffen om beveiligde web-API's aan te roepen. Deze web-API's kunnen Microsoft Graph, andere Microsoft API's, webAPI's van anderen of uw eigen web-API zijn.
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
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534479"
---
# <a name="use-microsoft-authentication-library-for-javascript-to-work-with-azure-active-directory-b2c"></a>Microsoft-verificatiebibliotheek voor JavaScript gebruiken om te werken met Azure Active Directory B2C

[Met microsoft-verificatiebibliotheek voor JavaScript (MSAL.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js) kunnen JavaScript-ontwikkelaars gebruikers met sociale en lokale identiteiten verifiëren met [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Door Azure AD B2C te gebruiken als een identiteitsbeheerservice, u aanpassen en beheren hoe klanten zich aanmelden, zich aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken.

Azure AD B2C stelt u ook in staat om de gebruikersinterface van uw toepassingen te brandmerken en aan te passen tijdens het verificatieproces om uw klanten een naadloze ervaring te bieden.

In dit artikel wordt uitgelegd hoe u MSAL.js gebruiken om met Azure AD B2C te werken en worden belangrijke punten samengevat waarvan u zich bewust moet zijn. Raadpleeg [Azure AD B2C-documentatie](https://docs.microsoft.com/azure/active-directory-b2c/overview)voor een volledige discussie en zelfstudie.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C-tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)hebt gemaakt, u nu beginnen met het maken van een tenant (u ook een bestaande Azure AD B2C-tenant gebruiken als u er al een hebt).

Deze demonstratie bestaat uit twee delen:

- hoe een web-API te beschermen.
- een toepassing van één pagina registreren om *die* web-API te verifiëren en aan te roepen.

## <a name="nodejs-web-api"></a>Node.js-web-API

> [!NOTE]
> Op dit moment is MSAL.js voor Node nog in ontwikkeling (zie de [roadmap).](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap) In de tussentijd raden we u aan om [een verificatiebibliotheek](https://github.com/AzureAD/passport-azure-ad)voor Node.js te gebruiken die door Microsoft is ontwikkeld en ondersteund.

De volgende stappen laten zien hoe een **web-API** Azure AD B2C kan gebruiken om zichzelf te beschermen en geselecteerde scopes bloot te stellen aan een clienttoepassing.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u uw web-API wilt beveiligen met Azure AD B2C, moet u deze eerst registreren. Zie [Uw aanvraag registreren](https://docs.microsoft.com/azure/active-directory-b2c/add-web-application?tabs=applications) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: De voorbeeldtoepassing downloaden

Download het voorbeeld als zip-bestand of kloon het van GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: Verificatie configureren

1. Open het bestand `config.js` in het voorbeeld.

2. Configureer het voorbeeld met de toepassingsreferenties die u eerder hebt verkregen tijdens het registreren van uw toepassing. Wijzig de volgende regels code door de waarden te vervangen door de namen van uw clientID, host, tenantId en beleidsnaam.

```JavaScript
const clientID = "<Application ID for your Node.js web API - found on Properties page in Azure portal e.g. 93733604-cc77-4a3c-a604-87084dd55348>";
const b2cDomainHost = "<Domain of your B2C host eg. fabrikamb2c.b2clogin.com>";
const tenantId = "<your-tenant-ID>.onmicrosoft.com"; // Alternatively, you can use your Directory (tenant) ID (GUID)
const policyName = "<Name of your sign in / sign up policy, e.g. B2C_1_signupsignin1>";
```

Voor meer informatie, kijk op deze [Node.js B2C Web API sample](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).

---

## <a name="javascript-spa"></a>JavaScript SPA

De volgende stappen laten zien hoe een **toepassing van één pagina** Azure AD B2C kan gebruiken om u aan te melden, u aan te melden en een beveiligde web-API aan te roepen.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u verificatie wilt implementeren, moet u eerst uw toepassing registreren. Zie [Uw aanvraag registreren](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-register-applications) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: De voorbeeldtoepassing downloaden

Download het voorbeeld als zip-bestand of kloon het van GitHub:

```console
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: Verificatie configureren

Er zijn twee aandachtspunten bij het configureren van uw toepassing:

- API-eindpunt en blootgestelde scopes configureren
- Verificatieparameters en tokenscopes configureren

1. Open het bestand `apiConfig.js` in het voorbeeld.

2. Configureer het voorbeeld met de parameters die u eerder hebt verkregen tijdens het registreren van uw web-API. Wijzig de volgende regels code door de waarden te vervangen door het adres van uw web-API en blootgestelde scopes.

   ```javascript
    // The current application coordinates were pre-registered in a B2C tenant.
    const apiConfig = {
        b2cScopes: ["https://fabrikamb2c.onmicrosoft.com/helloapi/demo.read"], //API scopes you exposed during api registration
        webApi: "https://fabrikamb2chello.azurewebsites.net/hello"
    };
   ```

3. Open het bestand `authConfig.js` in het voorbeeld.

4. Configureer het voorbeeld met de parameters die u eerder hebt verkregen tijdens het registreren van uw toepassing op één pagina. Wijzig de volgende regels code door de waarden te vervangen door uw ClientId, metagegevens van de autoriteit en tokenaanvraagscopes.

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

Bekijk dit voorbeeld van [javascript B2C-toepassingen met één pagina](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp).

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:
- [Gebruikersstromen](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)
- [Aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started)
- [UX-aanpassing](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-configure-user-input)

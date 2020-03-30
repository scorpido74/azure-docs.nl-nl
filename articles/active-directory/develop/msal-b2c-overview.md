---
title: MSAL gebruiken met Azure Active Directory B2CLearn | Azure
titleSuffix: Microsoft identity platform
description: Met Microsoft Authentication Library (MSAL) kunnen toepassingen samenwerken met Azure AD B2C en tokens aanschaffen om beveiligde web-API's aan te roepen. Deze web-API's kunnen Microsoft Graph, andere Microsoft API's, webAPI's van anderen of uw eigen web-API zijn.
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
ms.openlocfilehash: e25564e64410701754390024a5bcfd39321343e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696449"
---
# <a name="use-microsoft-authentication-library-to-interoperate-with-azure-active-directory-b2c"></a>Microsoft-verificatiebibliotheek gebruiken om te werken met Azure Active Directory B2C

Met Microsoft Authentication Library (MSAL) kunnen toepassingsontwikkelaars gebruikers met sociale en lokale identiteiten verifiëren met [Azure Active Directory B2C (Azure AD B2C).](https://docs.microsoft.com/azure/active-directory-b2c/) Azure AD B2C is een service voor identiteitsbeheer. Door het te gebruiken, u aanpassen en bepalen hoe klanten zich aanmelden, zich aanmelden en hun profielen beheren wanneer ze uw toepassingen gebruiken.

Azure AD B2C stelt u ook in staat om de gebruikersinterface van uw toepassingen te brandmerken en aan te passen om uw klanten een naadloze ervaring te bieden.

In deze zelfstudie wordt uitgelegd hoe u MSAL gebruiken om te werken met Azure AD B2C.

## <a name="prerequisites"></a>Vereisten

Als u nog geen eigen [Azure AD B2C-tenant](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant)hebt gemaakt, maakt u er nu een. U ook een bestaande Azure AD B2C-tenant gebruiken.

## <a name="javascript"></a>JavaScript

De volgende stappen laten zien hoe een toepassing van één pagina Azure AD B2C kan gebruiken om u aan te melden, u aan te melden en een beveiligde web-API aan te roepen.

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

Als u verificatie wilt implementeren, moet u eerst uw toepassing registreren. Zie [Uw aanvraag registreren](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#step-4-register-your-own-web-application-with-azure-ad-b2c) voor gedetailleerde stappen.

### <a name="step-2-download-the-sample-application"></a>Stap 2: De voorbeeldtoepassing downloaden

Download het voorbeeld als zip-bestand of kloon het van GitHub:

```
git clone https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp.git
```

### <a name="step-3-configure-authentication"></a>Stap 3: Verificatie configureren

1. Open het **bestand index.html** in het voorbeeld.

1. Configureer het voorbeeld met de client-id en sleutel die u eerder hebt opgenomen tijdens het registreren van uw toepassing. Wijzig de volgende regels met code door de waarden te vervangen door de namen van uw map en API's:

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

De naam van de [gebruikersstroom](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies) in deze zelfstudie is **B2C_1_signupsignin1**. Als u een andere gebruikersnaam gebruikt, stelt u de **waarde van** de autoriteit in op die naam.

### <a name="step-4-configure-your-application-to-use-b2clogincom"></a>Stap 4: Uw toepassing configureren om te gebruiken`b2clogin.com`

U kunt `b2clogin.com` gebruiken `login.microsoftonline.com` in plaats van als een omleiding URL. U doet dit in uw Azure AD B2C-toepassing wanneer u een identiteitsprovider instelt voor aanmelden en aanmelden.

Gebruik `b2clogin.com` van in `https://your-tenant-name.b2clogin.com/your-tenant-guid` de context van heeft de volgende effecten:

- Microsoft-services verbruiken minder ruimte in de cookieheader.
- Uw URL's bevatten niet langer een verwijzing naar Microsoft. Uw Azure AD B2C-toepassing verwijst `login.microsoftonline.com`bijvoorbeeld waarschijnlijk naar .

 Als `b2clogin.com`u wilt gebruiken, moet u de configuratie van uw toepassing bijwerken.  

- Stel de eigenschap `false` **validateAuthority** in op `b2clogin.com` , zodat omleidingen kunnen worden uitgevoerd.

In het volgende voorbeeld ziet u hoe u de eigenschap instellen:

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
> Uw Azure AD B2C-toepassing verwijst waarschijnlijk naar `login.microsoftonline.com` op verschillende plaatsen, zoals uw gebruikersstroomverwijzingen en tokeneindpunten. Zorg ervoor dat uw autorisatieeindpunt, tokeneindpunt en uitgever `your-tenant-name.b2clogin.com`zijn bijgewerkt om te gebruiken .

Volg [dit MSAL JavaScript-voorbeeld](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp#single-page-application-built-on-msaljs-with-azure-ad-b2c) over het gebruik van de MSAL Preview voor JavaScript (MSAL.js). Het voorbeeld krijgt een toegangstoken en roept een API aan die is beveiligd door Azure AD B2C.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Aangepast beleid](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Aanpassing van de gebruikersinterface](https://docs.microsoft.com/azure/active-directory-b2c/customize-ui-overview)
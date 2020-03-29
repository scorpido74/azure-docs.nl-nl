---
title: MSAL gebruiken in een nationale cloud-app | Azure
titleSuffix: Microsoft identity platform
description: Microsoft Authentication Library (MSAL) stelt toepassingsontwikkelaars in staat om tokens te verwerven om beveiligde web-API's te bellen. Deze web-API's kunnen Microsoft Graph, andere Microsoft API's, partnerwebAPI's of uw eigen web-API zijn. MSAL ondersteunt meerdere applicatiearchitecturen en platformen.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/22/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: dfca2b1311f1b55f19d5709f7c9ca7c3e366769c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76695735"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>MSAL gebruiken in een nationale cloudomgeving

[Nationale wolken](authentication-national-cloud.md), ook wel bekend als Soevereine wolken, zijn fysiek geïsoleerde exemplaren van Azure. Deze regio's van Azure helpen ervoor te zorgen dat vereisten voor gegevensresidentie, soevereiniteit en naleving binnen geografische grenzen worden nageleefd.

Naast de wereldwijde cloud van Microsoft stelt de Microsoft Authentication Library (MSAL) applicatieontwikkelaars in nationale clouds in staat om tokens te verwerven om beveiligde web-API's te verifiëren en te bellen. Deze web-API's kunnen Microsoft Graph of andere Microsoft API's zijn.

Azure Active Directory (Azure AD) wordt met inbegrip van de globale cloud geïmplementeerd in de volgende nationale clouds:  

- Azure Government
- Azure China 21Vianet
- Azure Duitsland

In deze handleiding wordt uitgelegd hoe u zich aanmeldt voor werk- en schoolaccounts, een toegangstoken krijgen en de Microsoft Graph-API aanroepen in de [azure-overheidscloudomgeving.](https://azure.microsoft.com/global-infrastructure/government/)

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan deze voorwaarden voldoet voordat u begint.

### <a name="choose-the-appropriate-identities"></a>Kies de juiste identiteiten

[Azure Government-toepassingen](https://docs.microsoft.com/azure/azure-government/) kunnen azure AD-overheidsidentiteiten en Azure AD-openbare identiteiten gebruiken om gebruikers te verifiëren. Omdat u een van deze identiteiten gebruiken, moet u beslissen welk autoriteitseindpunt u moet kiezen voor uw scenario:

- Azure AD-openbaar: vaak wordt gebruikt als uw organisatie al een Azure AD Public-tenant heeft om Office 365 (Openbaar of GCC) of een andere toepassing te ondersteunen.
- Azure AD-overheid: vaak gebruikt als uw organisatie al een Azure AD-overheidstenant heeft om Office 365 (GCC High of DoD) te ondersteunen of een nieuwe tenant maakt in Azure AD-overheid.

Nadat u hebt besloten, is een speciale overweging waar u uw app-registratie uitvoert. Als u Azure AD Public-identiteiten kiest voor uw Azure Government-toepassing, moet u de toepassing registreren in uw Azure AD Public-tenant.

### <a name="get-an-azure-government-subscription"></a>Een Azure Government-abonnement aanschaffen

Zie Beheren en verbinding [maken met uw abonnement in Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)voor een Azure Government-abonnement.

Als u geen Azure Government-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/global-infrastructure/government/request/) voordat u begint.

Kies het tabblad dat overeenkomt met uw taal voor meer informatie over het gebruik van een nationale cloud met een bepaalde programmeertaal:

## <a name="net"></a>[.NET](#tab/donet)

U MSAL.NET gebruiken om gebruikers aan te melden, tokens te verwerven en de Microsoft Graph-API in nationale clouds aan te roepen.

De volgende zelfstudies laten zien hoe je een .NET Core 2.2 MVC-web-app bouwen. De app maakt gebruik van OpenID Connect om gebruikers aan te melden met een werk- en schoolaccount in een organisatie die deel uitmaakt van een nationale cloud.

- Als u zich wilt aanmelden en tokens wilt aanschaffen, volgt u deze zelfstudie: [Een ASP.NET Core Web-inloggebruikers in soevereine clouds bouwen met het Microsoft-identiteitsplatform.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform)
- Als u de Microsoft Graph API wilt aanroepen, volgt u deze zelfstudie: [Het Microsoft-identiteitsplatform gebruiken om de Microsoft Graph-API aan te roepen vanuit een An ASP.NET Core 2.x Web App, namens een gebruiker die zich aanmeldt met zijn werk- en schoolaccount in Microsoft National Cloud.](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)

## <a name="javascript"></a>[Javascript](#tab/javascript)

Ga als belangrijkste voor het inschakelen van uw MSAL.js-toepassing voor soevereine clouds:

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.us/).
    
   Zie Eindpunten voor [app-registratie](authentication-national-cloud.md#app-registration-endpoints)voor azure-portaleindpunten voor andere nationale clouds.

1. Als uw account u toegang geeft tot meer dan één tenant, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure AD-tenant.
1. Ga naar de pagina [App-registraties](https://aka.ms/ra/ff) op het Microsoft-identiteitsplatform voor ontwikkelaars.
1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
1. Selecteer **Accounts in een organisatiemap**onder Ondersteunde **accounttypen**.
1. Selecteer in de sectie **URI omleiden** het **webplatform** en stel de waarde in op de URL van de toepassing op basis van uw webserver. Zie de volgende secties voor instructies over het instellen en verkrijgen van de omleidings-URL in Visual Studio en Node.
1. Selecteer **Registreren**.
1. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht**.
1. Deze zelfstudie vereist dat u de [impliciete subsidiestroom](v2-oauth2-implicit-grant-flow.md)inschakelt. Selecteer **Verificatie**in het linkerdeelvenster van de geregistreerde toepassing .
1. Schakel in **Geavanceerde instellingen**onder **Impliciete toekenning**de **selectievakjes ID-tokens** en **Toegangstokens** in. ID-tokens en toegangstokens zijn vereist omdat deze app gebruikers moet aanmelden en een API moet aanroepen.
1. Selecteer **Opslaan**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Stap 2: Uw webserver of project instellen

- [Download de projectbestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) voor een lokale webserver, zoals Knooppunt.

  of

- [Download het Visual Studio-project](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ga vervolgens naar [Uw JavaScript SPA configureren](#step-4-configure-your-javascript-spa) om het codevoorbeeld te configureren voordat u het uitvoert.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Stap 3: De Microsoft-verificatiebibliotheek gebruiken om de gebruiker aan te melden

Volg stappen in de [JavaScript-zelfstudie](tutorial-v2-javascript-spa.md#create-your-project) om uw project te maken en te integreren met MSAL om u aan te melden bij de gebruiker.

### <a name="step-4-configure-your-javascript-spa"></a>Stap 4: Uw JavaScript SPA configureren

Voeg `index.html` in het bestand dat tijdens de projectinstelling is gemaakt, de registratiegegevens van de toepassing toe. Voeg bovenaan de `<script></script>` tags in de hoofdtekst `index.html` van het bestand de volgende code toe:

```javascript
const msalConfig = {
    auth:{
        clientId: "Enter_the_Application_Id_here",
        authority: "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here",
        }
}

const graphConfig = {
        graphEndpoint: "https://graph.microsoft.us",
        graphScopes: ["user.read"],
}

// create UserAgentApplication instance
const myMSALObj = new UserAgentApplication(msalConfig);
```

In die code:

- `Enter_the_Application_Id_here`is de **id-waarde van toepassing (client)** voor de toepassing die u hebt geregistreerd.
- `Enter_the_Tenant_Info_Here`is ingesteld op een van de volgende opties:
    - Als uw toepassing **Accounts in deze organisatiemap**ondersteunt, vervangt u deze waarde door de tenant-id of tenantnaam (bijvoorbeeld contoso.microsoft.com).
    - Als uw toepassing **Accounts in een organisatiemap**ondersteunt, vervangt u deze waarde door `organizations`.
    
    Zie Eindpunten voor Azure [AD-verificatie](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)voor het vinden van verificatieeindpunten voor alle nationale clouds.

    > [!NOTE]
    > Persoonlijke Microsoft-accounts worden niet ondersteund in nationale clouds.
  
- `graphEndpoint`is het Microsoft Graph-eindpunt voor de Microsoft-cloud voor de Amerikaanse overheid.

   Zie [Microsoft Graph-eindpunten in nationale clouds](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)als u Microsoft Graph-eindpunten voor alle nationale clouds wilt vinden.

## <a name="python"></a>[Python](#tab/python)

Ga als u uw MSAL Python-toepassing inschakelen voor soevereine clouds:

- Registreer uw toepassing in een specifieke portal, afhankelijk van de cloud. Voor meer informatie over het kiezen van de portal verwijzen [App registratie eindpunten](authentication-national-cloud.md#app-registration-endpoints)
- Gebruik een van de [voorbeelden](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) van de repo met een paar wijzigingen in de configuratie, afhankelijk van de cloud, die vervolgens wordt vermeld.
- Gebruik een specifieke autoriteit, afhankelijk van de cloud waarin u de toepassing hebt geregistreerd. Raadpleeg [Azure AD-verificatieeindpunten](authentication-national-cloud.md#azure-ad-authentication-endpoints)voor meer informatie over autoriteiten voor verschillende clouds.

    Hier is een voorbeeld autoriteit:

    ```json
    "authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
    ```
    
- Als u Microsoft-grafiek wilt aanroepen, is een specifieke URL van het eindpunt van grafiek vereist die afhankelijk is van de cloud die u gebruikt. Als u Microsoft Graph-eindpunten voor alle nationale clouds wilt vinden, raadpleegt u [de hoofdeindpunten voor microsoft graph en graph explorer-service.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

    Hier is een voorbeeld van een grafiekeindpunt, met bereik:
    
    ```json
    "endpoint" : "https://graph.microsoft.us/v1.0/me"
    "scope": "User.Read"
    ```
    
## <a name="java"></a>[Java](#tab/java)

Ga als object voor het inschakelen van uw MSAL voor Java-toepassing voor soevereine clouds:

- Registreer uw toepassing in een specifieke portal, afhankelijk van de cloud. Voor meer informatie over het kiezen van de portal verwijzen [App registratie eindpunten](authentication-national-cloud.md#app-registration-endpoints)
- Gebruik een van de [voorbeelden](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) van de repo met een paar wijzigingen in de configuratie, afhankelijk van de cloud, die vervolgens worden vermeld.
- Gebruik een specifieke autoriteit, afhankelijk van de cloud waarin u de toepassing hebt geregistreerd. Raadpleeg [Azure AD-verificatieeindpunten](authentication-national-cloud.md#azure-ad-authentication-endpoints)voor meer informatie over autoriteiten voor verschillende clouds.

Hier is een voorbeeld autoriteit:

```json
"authority": "https://login.microsoftonline.us/Enter_the_Tenant_Info_Here"
```

- Als u Microsoft-grafiek wilt aanroepen, is een specifieke URL van het eindpunt van grafiek vereist die afhankelijk is van de cloud die u gebruikt. Als u Microsoft Graph-eindpunten voor alle nationale clouds wilt vinden, raadpleegt u [de hoofdeindpunten voor microsoft graph en graph explorer-service.](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)

Hier is een voorbeeld van een grafiekeindpunt, met bereik:

```json
"endpoint" : "https://graph.microsoft.us/v1.0/me"
"scope": "User.Read"
```

## <a name="objective-c"></a>[Doelstelling-C](#tab/objc)

MSAL voor iOS en macOS kan worden gebruikt om tokens in `MSALPublicClientApplication`nationale clouds te verkrijgen, maar het vereist extra configuratie bij het maken.

Als u bijvoorbeeld wilt dat uw toepassing een multi-tenanttoepassing is in een nationale cloud (hier de Amerikaanse overheid), u schrijven:

```objc
MSALAADAuthority *aadAuthority =
                [[MSALAADAuthority alloc] initWithCloudInstance:MSALAzureUsGovernmentCloudInstance
                                                   audienceType:MSALAzureADMultipleOrgsAudience
                                                      rawTenant:nil
                                                          error:nil];
                                                          
MSALPublicClientApplicationConfig *config =
                [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"
                                                                redirectUri:@"<your-redirect-uri-here>"
                                                                  authority:aadAuthority];
                                                                  
NSError *applicationError = nil;
MSALPublicClientApplication *application =
                [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&applicationError];
```

## <a name="swift"></a>[Swift](#tab/swift)

MSAL voor iOS en macOS kan worden gebruikt om tokens in `MSALPublicClientApplication`nationale clouds te verkrijgen, maar het vereist extra configuratie bij het maken.

Als u bijvoorbeeld wilt dat uw toepassing een multi-tenanttoepassing is in een nationale cloud (hier de Amerikaanse overheid), u schrijven:

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Verificatie in nationale clouds](authentication-national-cloud.md)
- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Duitsland](https://docs.microsoft.com/azure/germany/)

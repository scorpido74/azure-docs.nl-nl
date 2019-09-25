---
title: Micro soft Authentication Library (MSAL) gebruiken in nationale Clouds-micro soft Identity-platform
description: Met micro soft Authentication Library (MSAL) kunnen ontwikkel aars van toepassingen tokens verkrijgen om beveiligde web-Api's aan te roepen. Deze web-Api's kunnen worden Microsoft Graph, andere Api's van micro soft, partner web-Api's of uw eigen web-API. MSAL ondersteunt meerdere toepassings architecturen en-platforms.
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
ms.date: 05/07/2019
ms.author: negoe
ms.reviewer: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 97855a52831a63a92a46bd0d25d23ba3fc91a07b
ms.sourcegitcommit: 263a69b70949099457620037c988dc590d7c7854
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71268566"
---
# <a name="use-msal-in-a-national-cloud-environment"></a>MSAL gebruiken in een nationale cloud omgeving

[Nationale Clouds](authentication-national-cloud.md) zijn fysiek geïsoleerde exemplaren van Azure. Deze regio's van Azure helpen ervoor te zorgen dat de vereisten voor gegevens locatie, soevereiniteit en naleving binnen geografische grenzen worden gerespecteerd.

Naast de wereld wijde cloud van micro soft, kunnen ontwikkel aars van micro soft-verificatie (MSAL) toepassings ontwikkelaars in nationale Clouds aanvragen om tokens te verkrijgen om beveiligde web-Api's te verifiëren en aan te roepen. Deze web-Api's kunnen worden Microsoft Graph of andere micro soft-Api's.

Met inbegrip van de wereld wijde Cloud, Azure Active Directory (Azure AD), wordt geïmplementeerd in de volgende nationale Clouds:  

- Azure Government
- Azure China 21Vianet
- Azure Duitsland

In deze hand leiding wordt beschreven hoe u zich aanmeldt bij werk-en school accounts, hoe u een toegangs token krijgt en hoe u de Microsoft Graph-API aanroept in de [Azure Government Cloud](https://azure.microsoft.com/global-infrastructure/government/) omgeving.

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u voldoet aan deze vereisten.

### <a name="choose-the-appropriate-identities"></a>Kies de juiste identiteiten

[Azure Government](https://docs.microsoft.com/azure/azure-government/) toepassingen kunnen gebruikers verifiëren met Azure AD Government-identiteiten en open bare Azure AD-identiteiten. Omdat u een van deze identiteiten kunt gebruiken, moet u bepalen welk instantie-eind punt u moet kiezen voor uw scenario:

- Azure AD openbaar: Dit wordt vaak gebruikt als uw organisatie al een open bare Azure AD-Tenant heeft ter ondersteuning van Office 365 (openbaar of GCC) of een andere toepassing.
- Azure AD government: Dit wordt vaak gebruikt als uw organisatie al een Azure AD Government-Tenant heeft ter ondersteuning van Office 365 (GCC High of DoD) of als u een nieuwe Tenant maakt in azure AD Government.

Nadat u hebt besloten, hebt u een speciale overweging waar u uw app-registratie uitvoert. Als u voor uw Azure Government-toepassing open bare Azure AD-identiteiten kiest, moet u de toepassing registreren in uw open bare Azure AD-Tenant.

### <a name="get-an-azure-government-subscription"></a>Een Azure Government-abonnement verkrijgen

Zie [uw abonnement beheren en er verbinding mee maken in azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-subscriptions)om een Azure Government-abonnement te krijgen.

Als u nog geen abonnement op Azure Government hebt, maakt u een [gratis account](https://azure.microsoft.com/global-infrastructure/government/request/) voordat u begint.

## <a name="javascript"></a>JavaScript

### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren

1. Meld u aan bij [Azure Portal](https://portal.azure.us/).
    
   Zie [app-registratie-eind punten](authentication-national-cloud.md#app-registration-endpoints)voor informatie over Azure Portal eind punten voor andere nationale Clouds.

1. Als uw account u toegang geeft tot meer dan één Tenant, selecteert u uw account in de rechter bovenhoek en stelt u uw portal sessie in op de gewenste Azure AD-Tenant.
1. Ga naar de pagina [app-registraties](https://aka.ms/ra/ff) op het micro soft-identiteits platform voor ontwikkel aars.
1. Wanneer de pagina **Een toepassing registreren** wordt weergegeven, voert u een naam in voor de toepassing.
1. Onder **ondersteunde account typen**selecteert u **accounts in elke organisatie Directory**.
1. Selecteer in de sectie de **omleidings-URI** het **webplatform en** Stel de waarde in op de URL van de toepassing op basis van de webserver. Zie de volgende secties voor instructies over het instellen en verkrijgen van de omleidings-URL in Visual Studio en het knoop punt.
1. Selecteer **Registreren**.
1. Noteer de waarde **Toepassings-id (client)** op de app-pagina **Overzicht**.
1. Voor deze zelf studie moet u de [impliciete toekennings stroom](v2-oauth2-implicit-grant-flow.md)inschakelen. Selecteer in het linkerdeel venster van de geregistreerde toepassing **verificatie**.
1. Selecteer in **Geavanceerde instellingen**onder **impliciete toekenning**de selectie vakjes **id-tokens** en **toegangs tokens** . ID-tokens en toegangs tokens zijn vereist omdat deze app gebruikers moet aanmelden en een API kan aanroepen.
1. Selecteer **Opslaan**.

### <a name="step-2--set-up-your-web-server-or-project"></a>Stap 2:  Uw webserver of project instellen

- [Down load de project bestanden](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/quickstart.zip) voor een lokale webserver, zoals het knoop punt.

  of

- [Down load het Visual Studio-project](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2/archive/vsquickstart.zip).

Ga vervolgens verder met het [configureren van uw Java script-beveiligd-wachtwoord](#step-4-configure-your-javascript-spa) verificatie voor het configureren van het code voorbeeld voordat het wordt uitgevoerd.

### <a name="step-3-use-the-microsoft-authentication-library-to-sign-in-the-user"></a>Stap 3: Gebruik de micro soft-verificatie bibliotheek om u aan te melden bij de gebruiker

Volg de stappen in de [Java script-zelf studie](tutorial-v2-javascript-spa.md#create-your-project) om uw project te maken en te integreren met MSAL om u aan te melden bij de gebruiker.

### <a name="step-4-configure-your-javascript-spa"></a>Stap 4: Uw Java script-SPA configureren

Voeg in `index.html` het bestand dat tijdens de installatie van project is gemaakt de registratie gegevens voor de toepassing toe. Voeg boven `<script></script>` aan de tags in de hoofd tekst `index.html` van het bestand de volgende code toe:

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

- `Enter_the_Application_Id_here`is de waarde van de **toepassings-id** voor de toepassing die u hebt geregistreerd.
- `Enter_the_Tenant_Info_Here`is ingesteld op een van de volgende opties:
    - Als uw toepassing **accounts in deze organisatie Directory**ondersteunt, vervangt u deze waarde door de Tenant-id of Tenant naam (bijvoorbeeld contoso.Microsoft.com).
    - Als uw toepassing **accounts in een organisatorische Directory**ondersteunt, vervangt u deze waarde `organizations`door.
    
    Zie [Azure AD-verificatie-eind punten](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud#azure-ad-authentication-endpoints)voor het vinden van verificatie-eind punten voor alle nationale Clouds.

    > [!NOTE]
    > Persoonlijke micro soft-accounts worden niet ondersteund in nationale Clouds.
  
- `graphEndpoint`is het Microsoft Graph-eind punt voor de micro soft-Cloud voor de Amerikaanse overheid.

   Zie [Microsoft Graph-eind punten in nationale Clouds](https://docs.microsoft.com/graph/deployments#microsoft-graph-and-graph-explorer-service-root-endpoints)om Microsoft Graph-eind punten te vinden voor alle nationale Clouds.

## <a name="net"></a>.NET

U kunt MSAL.NET gebruiken voor het aanmelden van gebruikers, het verkrijgen van tokens en het aanroepen van de Microsoft Graph-API in nationale Clouds.

De volgende zelf studies laten zien hoe u een .NET Core 2,2 MVC-Web-app bouwt. De app maakt gebruik van OpenID Connect Connect voor het aanmelden van gebruikers met een werk-en school account in een organisatie die tot een nationale Cloud behoort.

- Als u gebruikers wilt aanmelden en tokens wilt verkrijgen, volgt u [deze zelf studie](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-4-Sovereign#build-an-aspnet-core-web-app-signing-in-users-in-sovereign-clouds-with-the-microsoft-identity-platform).
- Volg [deze zelf studie](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/2-WebApp-graph-user/2-4-Sovereign-Call-MSGraph#using-the-microsoft-identity-platform-to-call-the-microsoft-graph-api-from-an-an-aspnet-core-2x-web-app-on-behalf-of-a-user-signing-in-using-their-work-and-school-account-in-microsoft-national-cloud)om de Microsoft Graph-API aan te roepen.

## <a name="msal-for-ios-and-macos"></a>MSAL voor iOS en macOS

MSAL voor iOS en macOS kunnen worden gebruikt voor het verkrijgen van tokens in nationale Clouds, maar er is aanvullende `MSALPublicClientApplication`configuratie vereist bij het maken van.

Als u bijvoorbeeld wilt dat uw toepassing een multi tenant toepassing in een nationale Cloud (hier de Amerikaanse overheid) is, kunt u het volgende schrijven:

Doel-C:

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

Swift

```swift
let authority = try? MSALAADAuthority(cloudInstance: .usGovernmentCloudInstance, audienceType: .azureADMultipleOrgsAudience, rawTenant: nil)
        
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>", redirectUri: "<your-redirect-uri-here>", authority: authority)
if let application = try? MSALPublicClientApplication(configuration: config) { /* Use application */}
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over:

- [Azure Government](https://docs.microsoft.com/azure/azure-government/)
- [Azure China 21Vianet](https://docs.microsoft.com/azure/china/)
- [Azure Duitsland](https://docs.microsoft.com/azure/germany/)

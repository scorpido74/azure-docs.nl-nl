---
title: Verificatie en autorisatie-Azure App Service | Microsoft Docs
description: Conceptuele Naslag informatie en overzicht van de functie voor verificatie/autorisatie voor Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/12/2019
ms.author: cephalin
ms.reviewer: mahender
ms.custom: seodec18
ms.openlocfilehash: e308b44fffff451daa92cbf19209a1bcbfd4bff6
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087989"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Verificatie en autorisatie in Azure App Service

> [!NOTE]
> Op dit moment wordt AAD v2 (inclusief MSAL) niet ondersteund voor Azure-app Services en Azure Functions. Ga terug naar updates.
>

Azure App Service biedt ingebouwde ondersteuning voor verificatie en autorisatie, zodat u gebruikers kunt aanmelden en toegang hebt tot gegevens door minimale of geen code te schrijven in uw web-app, de REST-API en de mobiele back-end en ook [Azure functions](../azure-functions/functions-overview.md). In dit artikel wordt beschreven hoe App Service de verificatie en autorisatie voor uw app vereenvoudigt. 

Voor beveiligde verificatie en autorisatie is grondige inzichten van beveiliging vereist, waaronder Federatie, versleuteling, [JSON-webtokens (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) -beheer, [toekennings typen](https://oauth.net/2/grant-types/), enzovoort. App Service biedt deze hulpprogram ma's, zodat u meer tijd en energie kunt best Eden aan het leveren van bedrijfs waarde aan uw klant.

> [!NOTE]
> U bent niet verplicht om App Service te gebruiken voor verificatie en autorisatie. Veel web frameworks worden gebundeld met beveiligings functies, en u kunt ze gebruiken als u wilt. Als u meer flexibiliteit nodig hebt dan App Service biedt, kunt u ook uw eigen hulpprogram ma's schrijven.  
>

Zie [gebruikers verificatie en autorisatie voor mobiele apps met Azure app service](../app-service-mobile/app-service-mobile-auth.md)voor meer informatie over systeem eigen mobiele apps.

## <a name="how-it-works"></a>Hoe werkt het?

De module authenticatie en autorisatie wordt uitgevoerd in dezelfde sandbox als de code van uw toepassing. Wanneer deze is ingeschakeld, stuurt elke binnenkomende HTTP-aanvraag deze door voordat deze wordt verwerkt door de code van uw toepassing.

![](media/app-service-authentication-overview/architecture.png)

Deze module verwerkt diverse dingen voor uw app:

- Verifieert gebruikers met de opgegeven provider
- Valideert, slaat en vernieuwt tokens
- Beheert de geverifieerde sessie
- Injecteert identiteits gegevens in aanvraag headers

De module wordt afzonderlijk van uw toepassings code uitgevoerd en wordt geconfigureerd met behulp van app-instellingen. Er zijn geen Sdk's, specifieke talen of wijzigingen in de toepassings code vereist. 

### <a name="user-claims"></a>Gebruikersclaims

Voor alle taal Frameworks maakt App Service de claims van de gebruiker beschikbaar voor uw code door ze in de aanvraag headers in te voeren. Voor ASP.net 4,6-apps vult app service [claimsprincipal is. current](/dotnet/api/system.security.claims.claimsprincipal.current) in met de claims van de geverifieerde gebruiker, zodat u het standaard .net-code patroon kunt volgen `[Authorize]` , inclusief het kenmerk. Op dezelfde manier wordt voor php-apps app service de `_SERVER['REMOTE_USER']` variabele ingevuld. Voor java-apps zijn de claims [toegankelijk via de Tomcat-servlet](containers/configure-language-java.md#authenticate-users-easy-auth).

Voor [Azure functions](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` is niet gehydrateerd voor .net-code, maar u kunt wel de gebruikers claims vinden in de aanvraag headers.

Zie voor meer informatie [gebruikers claims voor toegang](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Token opslag

App Service biedt een ingebouwde token opslag. Dit is een opslag plaats van tokens die zijn gekoppeld aan de gebruikers van uw web-apps, Api's of systeem eigen mobiele apps. Wanneer u verificatie met een wille keurige provider inschakelt, is deze token opslag direct beschikbaar voor uw app. Als uw toepassings code toegang moet hebben tot gegevens van deze providers namens de gebruiker, zoals: 

- plaatsen op de Facebook-tijd lijn van de geverifieerde gebruiker
- de Bedrijfs gegevens van de gebruiker lezen van de Azure Active Directory Graph API of zelfs de Microsoft Graph

Normaal gesp roken moet u code schrijven om deze tokens in uw toepassing te verzamelen, op te slaan en te vernieuwen. Met de token opslag haalt u alleen [de tokens](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) op wanneer u deze nodig hebt en [weet u app service deze te vernieuwen](app-service-authentication-how-to.md#refresh-identity-provider-tokens) wanneer ze ongeldig worden. 

De id-tokens, toegangs tokens en vernieuwings tokens in de cache voor de geauthenticeerde sessie, en ze zijn alleen toegankelijk voor de bijbehorende gebruiker.  

Als u niet met tokens in uw app hoeft te werken, kunt u de token opslag uitschakelen.

### <a name="logging-and-tracing"></a>Logboek registratie en tracering

Als u [toepassings logboeken](troubleshoot-diagnostic-logs.md)inschakelt, worden de verificatie-en autorisatie traceringen rechtstreeks in uw logboek bestanden weer geven. Als er een verificatie fout wordt weer geven die niet werd verwacht, kunt u alle gegevens gemakkelijk vinden door te kijken in uw bestaande toepassings Logboeken. Als u tracering van [mislukte aanvragen](troubleshoot-diagnostic-logs.md)inschakelt, kunt u precies zien welke rol de authenticatie-en autorisatie module mogelijk heeft afgespeeld in een mislukte aanvraag. Zoek in de traceer logboeken naar een module met de naam `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Id-providers

App Service maakt gebruik van [federatieve identiteiten](https://en.wikipedia.org/wiki/Federated_identity), waarin een id-provider van derden de gebruikers identiteiten en de verificatie stroom voor u beheert. Er zijn standaard vijf id-providers beschikbaar: 

| Provider | Aanmeldings eindpunt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-account](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Wanneer u verificatie en autorisatie met een van deze providers inschakelt, is het aanmeldings eindpunt beschikbaar voor gebruikers verificatie en voor validatie van verificatie tokens van de provider. U kunt uw gebruikers een onbeperkt aantal van deze aanmeldings opties voorzien. U kunt ook een andere ID-provider of [uw eigen aangepaste identiteits oplossing][custom-auth]integreren.

## <a name="authentication-flow"></a>Verificatie stroom

De verificatie stroom is hetzelfde voor alle providers, maar verschilt afhankelijk van of u zich wilt aanmelden met de SDK van de provider:

- Zonder provider-SDK: De toepassing heeft federatieve aanmelding gedelegeerd aan App Service. Dit is meestal het geval bij browser-apps, waarmee de aanmeldings pagina van de provider kan worden weer gegeven aan de gebruiker. De server code beheert het aanmeldings proces, zodat dit ook wel _Server gerichte stroom_ of _Server stroom_wordt genoemd. Dit geldt voor browser-apps. Dit geldt ook voor systeem eigen apps waarmee gebruikers worden ondertekend met de Mobile Apps client-SDK, omdat de SDK een webweergave opent om gebruikers met App Service-verificatie te ondertekenen. 
- Met provider-SDK: De toepassing ondertekent gebruikers hand matig aan de provider en stuurt het verificatie token naar App Service voor validatie. Dit is meestal het geval bij apps zonder browser, waarmee de aanmeldings pagina van de provider niet kan worden weer gegeven aan de gebruiker. De toepassings code beheert het aanmeldings proces, dus ook wel _client gerichte stroom_ of _client stroom_. Dit is van toepassing op REST Api's, [Azure functions](../azure-functions/functions-overview.md)en Java script-clients, evenals browser-apps die meer flexibiliteit in het aanmeldings proces hebben. Dit geldt ook voor systeem eigen mobiele apps waarmee gebruikers worden aangemeld met de SDK van de provider.

> [!NOTE]
> Aanroepen van een vertrouwde browser-app in App Service roept een andere REST API op in App Service of [Azure functions](../azure-functions/functions-overview.md) kan worden geverifieerd met behulp van de server-gerichte stroom. Zie voor meer informatie [verificatie en autorisatie aanpassen in app service](app-service-authentication-how-to.md).
>

In de volgende tabel ziet u de stappen van de verificatie stroom.

| Stap | Zonder provider-SDK | Met provider-SDK |
| - | - | - |
| 1. Gebruiker aanmelden | Stuurt de client door naar `/.auth/login/<provider>`. | Client code tekent de gebruiker rechtstreeks met de SDK van de provider en ontvangt een verificatie token. Zie de documentatie van de provider voor meer informatie. |
| 2. Na verificatie | Provider stuurt de client om naar `/.auth/login/<provider>/callback`. | Client code [boekt token van provider](app-service-authentication-how-to.md#validate-tokens-from-providers) naar `/.auth/login/<provider>` voor validatie. |
| 3. Een geverifieerde sessie tot stand brengen | App Service voegt een geverifieerde cookie toe aan het antwoord. | App Service retourneert een eigen verificatie token naar client code. |
| 4. Geverifieerde inhoud verwerken | Client bevat verificatie cookie in volgende aanvragen (automatisch verwerkt door browser). | Client code geeft een verificatie token `X-ZUMO-AUTH` weer in de header (automatisch verwerkt door Mobile apps client-sdk's). |

App Service kunt voor client browsers automatisch alle niet-geverifieerde gebruikers door `/.auth/login/<provider>`sturen naar. U kunt gebruikers ook voorzien van een of meer `/.auth/login/<provider>` koppelingen om u aan te melden bij uw app met behulp van de gewenste provider.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Autorisatie gedrag

In de [Azure Portal](https://portal.azure.com)kunt u app service-autorisatie configureren met een aantal gedragingen wanneer een binnenkomende aanvraag niet is geverifieerd.

![](media/app-service-authentication-overview/authorization-flow.png)

In de volgende koppen worden de opties beschreven.

### <a name="allow-anonymous-requests-no-action"></a>Anonieme aanvragen toestaan (geen actie)

Met deze optie wordt de autorisatie van niet-geverifieerde verkeer naar uw toepassings code uitgesteld. App Service worden voor geverifieerde aanvragen ook de verificatie gegevens in de HTTP-headers door gegeven. 

Deze optie biedt meer flexibiliteit bij het verwerken van anonieme aanvragen. U kunt bijvoorbeeld [meerdere aanmeldings providers](app-service-authentication-how-to.md#use-multiple-sign-in-providers) aan uw gebruikers aanbieden. U moet echter code schrijven. 

### <a name="allow-only-authenticated-requests"></a>Alleen geverifieerde aanvragen toestaan

De optie is **Aanmelden met \<provider >** . App service stuurt alle anonieme aanvragen door naar `/.auth/login/<provider>` de provider die u kiest. Als de anonieme aanvraag afkomstig is van een systeem eigen mobiele app, is het geretourneerde antwoord een `HTTP 401 Unauthorized`.

Met deze optie hoeft u geen verificatie code in uw app te schrijven. Nauw keurige autorisatie, zoals Role-specifieke autorisatie, kan worden verwerkt door de claims van de gebruiker te controleren (Zie [gebruikers claims voor toegang](app-service-authentication-how-to.md#access-user-claims)).

> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle aanroepen naar uw app. Dit is mogelijk niet wenselijk voor apps die een openbaar beschik bare start pagina willen, zoals in veel toepassingen met één pagina.

## <a name="more-resources"></a>Meer bronnen

[Zelfstudie: Gebruikers met end-to-end verifiëren en autoriseren in Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Zelfstudie: Gebruikers met end-to-end verifiëren en autoriseren in Azure App Service voor Linux](containers/tutorial-auth-aad.md)  
[Verificatie en autorisatie aanpassen in App Service](app-service-authentication-how-to.md)

Hand leidingen voor een specifieke provider:

* [Uw app configureren voor aanmelding met Azure Active Directory][AAD]
* [Uw app configureren voor aanmelding met Facebook][Facebook]
* [Uw app configureren voor aanmelding met Google][Google]
* [Uw app configureren voor aanmelding met een Microsoft Account][MSA]
* [Uw app configureren voor aanmelding met Twitter][Twitter]
* [Procedure: Aangepaste verificatie gebruiken voor uw toepassing][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

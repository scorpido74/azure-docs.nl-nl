---
title: Verificatie en autorisatie
description: Meer informatie over de ingebouwde verificatie- en autorisatieondersteuning in Azure App Service en Azure-functies en hoe deze uw app kan beveiligen tegen ongeautoriseerde toegang.
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.topic: article
ms.date: 04/15/2020
ms.reviewer: mahender
ms.custom:
- seodec18
- fasttrack-edit
ms.openlocfilehash: a4ceed0d897f069a7895a3eb6b10c327566afbe5
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81457855"
---
# <a name="authentication-and-authorization-in-azure-app-service-and-azure-functions"></a>Verificatie en autorisatie in Azure App Service en Azure-functies

> [!NOTE]
> Op dit moment ondersteunt ASP.NET Core momenteel geen ondersteuning voor het vullen van de huidige gebruiker met de functie Verificatie/autorisatie.
>

Azure App Service biedt ingebouwde verificatie- en autorisatieondersteuning, zodat u gebruikers aanmelden en toegang krijgen tot gegevens door minimale of geen code te schrijven in uw web-app, RESTful API en mobiele back-end, en ook [Azure-functies.](../azure-functions/functions-overview.md) In dit artikel wordt beschreven hoe App Service helpt bij het vereenvoudigen van verificatie en autorisatie voor uw app.

Veilige verificatie en autorisatie vereisen een diepgaand begrip van beveiliging, waaronder federatie, versleuteling, [JSON-webtokens (JWT)-beheer,](https://wikipedia.org/wiki/JSON_Web_Token) [subsidietypen,](https://oauth.net/2/grant-types/)enzovoort. App Service biedt deze hulpprogramma's, zodat u meer tijd en energie besteden aan het leveren van bedrijfswaarde aan uw klant.

> [!IMPORTANT]
> U hoeft deze functie niet te gebruiken voor verificatie en autorisatie. U de gebundelde beveiligingsfuncties gebruiken in uw webframework naar keuze, of u uw eigen hulpprogramma's schrijven. Houd er echter rekening mee dat [Chrome 80 baanbrekende wijzigingen aanbrengt in de implementatie van SameSite voor cookies](https://www.chromestatus.com/feature/5088147346030592) (releasedatum rond maart 2020) en aangepaste verificatie op afstand of andere scenario's die afhankelijk zijn van het plaatsen van cross-site cookies kunnen breken wanneer chrome-browsers van de client worden bijgewerkt. De tijdelijke oplossing is complex omdat deze verschillende SameSite-gedragingen voor verschillende browsers moet ondersteunen. 
>
> De ASP.NET Core 2.1 en hoger versies die worden gehost door App Service zijn al gepatcht voor deze baanbrekende wijziging en omgaan met Chrome 80 en oudere browsers op de juiste manier. Bovendien wordt dezelfde patch voor ASP.NET Framework 4.7.2 in januari 2020 geïmplementeerd in de app-service-exemplaren. Zie [Azure App Service SameSite-cookie-update](https://azure.microsoft.com/updates/app-service-samesite-cookie-update/)voor meer informatie, waaronder hoe u weet of uw app de patch heeft ontvangen.
>

Zie [Gebruikersverificatie en autorisatie voor mobiele apps met Azure App Service voor](../app-service-mobile/app-service-mobile-auth.md)informatie die specifiek is voor native mobiele apps.

## <a name="how-it-works"></a>Hoe werkt het?

De verificatie- en autorisatiemodule wordt uitgevoerd in dezelfde sandbox als uw toepassingscode. Wanneer deze is ingeschakeld, gaat elke binnenkomende HTTP-aanvraag erdoorheen voordat deze wordt verwerkt door uw toepassingscode.

![](media/app-service-authentication-overview/architecture.png)

Deze module behandelt verschillende dingen voor uw app:

- Hiermee worden gebruikers met de opgegeven provider geverifieerd
- Hiermee worden tokens gevalideerd, opgeslagen en vernieuwd
- Beheert de geverifieerde sessie
- Injecteert identiteitsgegevens in aanvraagkoppen

De module wordt apart uitgevoerd van uw toepassingscode en is geconfigureerd met behulp van app-instellingen. Er zijn geen SDK's, specifieke talen of wijzigingen in uw toepassingscode vereist. 

### <a name="userapplication-claims"></a>Gebruikers-/toepassingsclaims

Voor alle taalframeworks maakt App Service de claims in het binnenkomende token (of dat nu afkomstig is van een geverifieerde eindgebruiker of een clienttoepassing) beschikbaar voor uw code door ze in de aanvraagkoppen te injecteren. Voor ASP.NET 4.6-apps vult App Service [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) in met de claims van de geverifieerde gebruiker, `[Authorize]` zodat u het standaard .NET-codepatroon volgen, inclusief het kenmerk. Voor PHP-apps vult App Service `_SERVER['REMOTE_USER']` de variabele. Voor Java-apps zijn de claims [toegankelijk vanaf de Tomcat servlet.](containers/configure-language-java.md#authenticate-users-easy-auth)

Voor [Azure-functies](../azure-functions/functions-overview.md) `ClaimsPrincipal.Current` wordt deze niet ingevuld voor .NET-code, maar u de `ClaimsPrincipal` gebruikersclaims nog steeds vinden in de aanvraagkoppen of het object ophalen uit de aanvraagcontext of zelfs via een bindende parameter. Zie [werken met klantidentiteiten](../azure-functions/functions-bindings-http-webhook-trigger.md#working-with-client-identities) voor meer informatie.

Zie [Gebruikersclaims van Access](app-service-authentication-how-to.md#access-user-claims)voor meer informatie .

### <a name="token-store"></a>Tokenarchief

App Service biedt een ingebouwde tokenstore, een opslagplaats van tokens die zijn gekoppeld aan de gebruikers van uw web-apps, API's of native mobiele apps. Wanneer u verificatie inschakelt bij een provider, is deze tokenstore onmiddellijk beschikbaar voor uw app. Als uw toepassingscode toegang moet hebben tot gegevens van deze providers namens de gebruiker, zoals: 

- bericht op de Facebook-tijdlijn van de geverifieerde gebruiker
- de bedrijfsgegevens van de gebruiker lezen met behulp van de Microsoft Graph API

U moet doorgaans code schrijven om deze tokens in uw toepassing te verzamelen, op te slaan en te vernieuwen. Met de tokenwinkel [haalt](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) u de tokens op wanneer u ze nodig hebt en [vertelt u App Service om ze te vernieuwen](app-service-authentication-how-to.md#refresh-identity-provider-tokens) wanneer ze ongeldig worden. 

De id-tokens, toegangstokens en vernieuwingstokens worden in de cache opgeslagen voor de geverifieerde sessie en zijn alleen toegankelijk voor de gekoppelde gebruiker.  

Als u niet met tokens in uw app hoeft te werken, u de tokenstore uitschakelen.

### <a name="logging-and-tracing"></a>Logboekregistratie en tracering

Als u [toepassingslogboekregistratie inschakelt,](troubleshoot-diagnostic-logs.md)ziet u verificatie- en autorisatiesporen rechtstreeks in uw logboekbestanden. Als u een verificatiefout ziet die u niet had verwacht, u gemakkelijk alle details vinden door in uw bestaande toepassingslogboeken te kijken. Als u [mislukte aanvraagtracering](troubleshoot-diagnostic-logs.md)inschakelt, u precies zien welke rol de verificatie- en autorisatiemodule mogelijk heeft gespeeld in een mislukte aanvraag. Zoek in de traceerlogboeken naar `EasyAuthModule_32/64`verwijzingen naar een module met de naam . 

## <a name="identity-providers"></a>Id-providers

App Service maakt gebruik [van federatieve identiteit,](https://en.wikipedia.org/wiki/Federated_identity)waarin een externe identiteitsprovider de gebruikersidentiteiten en verificatiestroom voor u beheert. Er zijn standaard vijf identiteitsproviders beschikbaar: 

| Provider | Aanmeldingseindpunt |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Microsoft-account](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/identity/choose-auth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Wanneer u verificatie en autorisatie inschakelt bij een van deze providers, is het aanmeldingseindpunt beschikbaar voor gebruikersverificatie en voor validatie van verificatietokens van de provider. U uw gebruikers met gemak een aantal van deze aanmeldingsopties bieden. U ook een andere identiteitsprovider of [uw eigen aangepaste identiteitsoplossing][custom-auth]integreren.

## <a name="authentication-flow"></a>Verificatiestroom

De verificatiestroom is hetzelfde voor alle providers, maar verschilt afhankelijk van of u zich wilt aanmelden bij de SDK van de provider:

- Zonder provider SDK: de toepassing gemachtigden federatieve sign-in voor App Service. Dit is meestal het geval met browser-apps, die de aanmeldingspagina van de provider aan de gebruiker kunnen presenteren. De servercode beheert het aanmeldingsproces, dus het wordt ook wel _servergestuurde stroom_ of _serverstroom genoemd._ Deze case is van toepassing op browser-apps. Het is ook van toepassing op native apps die gebruikers aanmelden met de Mobile Apps-client SDK omdat de SDK een webweergave opent om gebruikers aan te melden met App Service-verificatie. 
- Met provider SDK: De toepassing meldt gebruikers handmatig in bij de provider en verzendt het verificatietoken vervolgens naar App Service voor validatie. Dit is meestal het geval met browserloze apps, die de aanmeldingspagina van de provider niet aan de gebruiker kunnen presenteren. De toepassingscode beheert het aanmeldingsproces, dus het wordt ook wel _clientgerichte stroom_ of _clientstroom_genoemd. Deze case is van toepassing op REST API's, [Azure Functions](../azure-functions/functions-overview.md)en JavaScript-browserclients, evenals browser-apps die meer flexibiliteit nodig hebben in het aanmeldingsproces. Het is ook van toepassing op native mobiele apps die gebruikers aanmelden met behulp van de SDK van de provider.

> [!NOTE]
> Oproepen van een vertrouwde browser-app in App-service naar een andere REST-API in App-service of [Azure-functies](../azure-functions/functions-overview.md) kunnen worden geverifieerd met behulp van de servergestuurde stroom. Zie [Verificatie en autorisatie aanpassen in App-service voor](app-service-authentication-how-to.md)meer informatie.
>

De onderstaande tabel toont de stappen van de verificatiestroom.

| Stap | Zonder provider SDK | Met provider SDK |
| - | - | - |
| 1. | Hiermee leidt `/.auth/login/<provider>`u de client om naar . | Clientcode meldt de gebruiker rechtstreeks bij de SDK van de provider en ontvangt een verificatietoken. Zie de documentatie van de provider voor informatie. |
| 2. Post-authenticatie | Provider verwijst client `/.auth/login/<provider>/callback`door naar . | Clientcode plaatst token `/.auth/login/<provider>` van [provider](app-service-authentication-how-to.md#validate-tokens-from-providers) naar voor validatie. |
| 3. Geverifieerde sessie instellen | App Service voegt geverifieerde cookie toe aan respons. | App Service retourneert zijn eigen verificatietoken naar clientcode. |
| 4. Geverifieerde inhoud serveren | Client bevat verificatiecookie in volgende verzoeken (automatisch behandeld door browser). | Clientcode presenteert verificatietoken in `X-ZUMO-AUTH` header (automatisch afgehandeld door SDK's van de Mobile Apps-client). |

Voor clientbrowsers kan App Service automatisch alle niet-geverifieerde gebruikers doorverwijzen naar `/.auth/login/<provider>`. U gebruikers ook een `/.auth/login/<provider>` of meer koppelingen aanbieden om zich bij uw app aan te melden met hun aanbieder naar keuze.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Autorisatiegedrag

In de [Azure-portal](https://portal.azure.com)u de autorisatie van App Service configureren met een aantal gedragingen wanneer de binnenkomende aanvraag niet is geverifieerd.

![](media/app-service-authentication-overview/authorization-flow.png)

In de volgende koppen worden de opties beschreven.

### <a name="allow-anonymous-requests-no-action"></a>Anonieme verzoeken toestaan (geen actie)

Met deze optie wordt de autorisatie van niet-geverifieerd verkeer naar uw toepassingscode uitgesteld. Voor geverifieerde aanvragen geeft App Service ook verificatiegegevens door in de HTTP-headers. 

Deze optie biedt meer flexibiliteit bij het afhandelen van anonieme verzoeken. Hiermee u bijvoorbeeld [meerdere aanmeldingsproviders aan](app-service-authentication-how-to.md#use-multiple-sign-in-providers) uw gebruikers presenteren. U moet echter wel code schrijven. 

### <a name="allow-only-authenticated-requests"></a>Alleen geverifieerde aanvragen toestaan

De optie is **Log in met \<provider>**. App Service verwijst alle `/.auth/login/<provider>` anonieme verzoeken door naar de provider die u kiest. Als de anonieme aanvraag afkomstig is van een `HTTP 401 Unauthorized`native mobiele app, is het geretourneerde antwoord een .

Met deze optie hoeft u geen verificatiecode in uw app te schrijven. Fijnere autorisatie, zoals functiespecifieke autorisatie, kan worden afgehandeld door de claims van de gebruiker te inspecteren (zie [Claimvan Access-gebruikers).](app-service-authentication-how-to.md#access-user-claims)

> [!CAUTION]
> Het beperken van de toegang op deze manier is van toepassing op alle oproepen naar uw app, wat mogelijk niet wenselijk is voor apps die een openbaar beschikbare startpagina willen, zoals in veel toepassingen met één pagina.

> [!NOTE]
> Authenticatie/autorisatie stond voorheen bekend als Easy Auth.
>

## <a name="more-resources"></a>Meer bronnen

[Zelfstudie: gebruikers end-to-end verifiëren en autoriseren in Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Zelfstudie: gebruikers end-to-end verifiëren en autoriseren in Azure App Service voor Linux](containers/tutorial-auth-aad.md)  
[Customize authentication and authorization in App Service](app-service-authentication-how-to.md)
Verificatie en autorisatie aanpassen in App Service[.NET Core-integratie van Azure AppService EasyAuth (derde partij)](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)
[Azure App Service-verificatie laten werken met .NET Core (derde partij)](https://github.com/kirkone/KK.AspNetCore.EasyAuthAuthentication)

Provider-specifieke handleidingen:

* [Uw app configureren voor aanmelding met Azure Active Directory][AAD]
* [Uw app configureren voor aanmelding met Facebook][Facebook]
* [Uw app configureren voor aanmelding met Google][Google]
* [Uw app configureren voor aanmelding met een Microsoft Account][MSA]
* [Uw app configureren voor aanmelding met Twitter][Twitter]
* [How to: Aangepaste verificatie gebruiken voor uw toepassing][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal

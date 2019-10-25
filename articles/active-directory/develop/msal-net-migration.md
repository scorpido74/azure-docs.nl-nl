---
title: Migreren naar MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen micro soft Authentication Library voor .NET (MSAL.NET) en Azure AD Authentication Library voor .NET (ADAL.NET) en hoe u migreert naar MSAL.NET.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d9f178df5e5fd9d2b70b7791588cfdc0652b217
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802776"
---
# <a name="migrating-applications-to-msalnet"></a>Toepassingen migreren naar MSAL.NET

Zowel micro soft Authentication Library voor .NET (MSAL.NET) als Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) worden gebruikt voor het verifiëren van Azure AD-entiteiten en het aanvragen van tokens van Azure AD. Tot nu toe hebben de meeste ontwikkel aars met Azure AD voor ontwikkel aars platform (v 1.0) gewerkt voor het verifiëren van Azure AD-identiteiten (werk-en school accounts) door tokens aan te vragen met behulp van Azure AD Authentication Library (ADAL). MSAL gebruiken:

- u kunt een bredere set met micro soft-identiteiten (Azure AD-identiteiten en micro soft-accounts en sociale en lokale accounts via Azure AD B2C) verifiëren, zoals het micro soft Identity platform-eind punt gebruikt.
- uw gebruikers krijgen de beste ervaring voor eenmalige aanmelding.
- uw toepassing kan stapsgewijze toestemming bieden en het ondersteunen van voorwaardelijke toegang is eenvoudiger
- u profiteert van de innovatie.

**MSAL.net is nu de aanbevolen verificatie bibliotheek voor gebruik met het micro soft Identity-platform**. Er worden geen nieuwe functies geïmplementeerd op ADAL.NET. De inspanningen zijn gericht op het verbeteren van MSAL.

In dit artikel worden de verschillen beschreven tussen de micro soft Authentication Library voor .NET (MSAL.NET) en Azure AD Authentication Library voor .NET (ADAL.NET) en kunt u migreren naar MSAL.  

## <a name="differences-between-adal-and-msal-apps"></a>Verschillen tussen ADAL-en MSAL-apps

In de meeste gevallen wilt u MSAL.NET en het micro soft Identity platform-eind punt gebruiken. Dit is de nieuwste generatie micro soft-verificatie bibliotheken. Met MSAL.NET kunt u tokens verkrijgen voor gebruikers die zich aanmelden bij uw toepassing met Azure AD (werk-en school accounts), micro soft (persoonlijk) accounts (MSA) of Azure AD B2C. 

Als u al bekend bent met het Azure AD voor ontwikkel aars (v 1.0)-eind punt (en ADAL.NET), wilt u wellicht lezen [wat er anders is dan het micro soft Identity platform (v 2.0)-eind punt?](active-directory-v2-compare.md).

U moet echter nog steeds ADAL.NET gebruiken als uw toepassing zich moet aanmelden bij eerdere versies van [Active Directory Federation Services (ADFS)](/windows-server/identity/active-directory-federation-services). Zie [ADFS-ondersteuning](https://aka.ms/msal-net-adfs-support)voor meer informatie.

De volgende afbeelding bevat een overzicht van enkele van de verschillen tussen ADAL.NET en MSAL.NET ![side-by-side code](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-pakketten en-naam ruimten

ADAL.NET wordt gebruikt vanuit het NuGet-pakket [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) . de naam ruimte die moet worden gebruikt, is `Microsoft.IdentityModel.Clients.ActiveDirectory`.

Als u MSAL.NET wilt gebruiken, moet u het NuGet-pakket [micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client) toevoegen en de naam ruimte `Microsoft.Identity.Client` gebruiken

### <a name="scopes-not-resources"></a>Scopes die geen resources zijn

ADAL.NET schaft tokens voor *bronnen*aan, maar MSAL.net verwerft tokens voor *scopes*. Voor een aantal MSAL.NET AcquireToken-onderdrukkingen is een para meter met de naam scopes (`IEnumerable<string> scopes`) vereist. Deze para meter is een eenvoudige lijst met teken reeksen die de gewenste machtigingen en benodigde bronnen declareren. Bekende bereiken zijn de [bereiken van de Microsoft Graph](/graph/permissions-reference).

Het is ook mogelijk in MSAL.NET om toegang te krijgen tot v 1.0-resources. Zie de details in [bereiken voor een v 1.0-toepassing](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Kern klassen

- ADAL.NET maakt gebruik van [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) als de weer gave van uw verbinding met de beveiligings token service (STS) of autorisatie server, via een instantie. In tegens telling tot MSAL.NET is ontworpen rond [client toepassingen](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications). Het biedt twee afzonderlijke klassen: `PublicClientApplication` en `ConfidentialClientApplication`

- Tokens ophalen: ADAL.NET en MSAL.NET hebben dezelfde authenticatie aanroepen (`AcquireTokenAsync` en `AcquireTokenSilentAsync` voor ADAL.NET, en `AcquireTokenInteractive` en `AcquireTokenSilent` in MSAL.NET), maar met andere vereiste para meters. Een verschil is het feit dat u in MSAL.NET de `ClientID` van uw toepassing niet meer hoeft door te geven in elke AcquireTokenXX-aanroep. De `ClientID` is inderdaad slechts eenmaal ingesteld bij het bouwen van de (`IPublicClientApplication` of `IConfidentialClientApplication`).

### <a name="iaccount-not-iuser"></a>IAccount niet IUser

Gemanipuleerde gebruikers ADAL.NET. Een gebruiker is echter een mens of een software-agent, maar kan wel of niet verantwoordelijk zijn voor een of meer accounts in het micro soft-identiteits systeem (verschillende Azure AD-accounts, Azure AD B2C, persoonlijke micro soft-accounts). 

MSAL.NET 2. x definieert nu het concept account (via de IAccount-Interface). Deze breuk wijziging biedt de juiste semantiek: het feit dat dezelfde gebruiker meerdere accounts kan hebben in verschillende Azure AD-directory's. MSAL.NET biedt ook betere informatie in gast scenario's, wanneer er informatie over het thuis account wordt verstrekt.

Zie [MSAL.net 2. x](https://aka.ms/msal-net-2-released)voor meer informatie over de verschillen tussen IUser en IAccount.

### <a name="exceptions"></a>Uitzonderingen

#### <a name="interaction-required-exceptions"></a>Interactie vereiste uitzonde ringen

MSAL.NET heeft meer expliciete uitzonde ringen. Wanneer bijvoorbeeld een stille verificatie mislukt in ADAL, is de procedure om de uitzonde ring te ondervangen en te zoeken naar de `user_interaction_required` fout code:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == “user_interaction_required”)
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Bekijk de details in [het aanbevolen patroon voor het verkrijgen van een token](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) met ADAL.net

Met MSAL.NET kunt u de `MsalUiRequiredException` opvangen zoals beschreven in [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Uitzonde ringen op claim controle verwerken

In ADAL.NET worden claim Challenge-uitzonde ringen op de volgende manier verwerkt:

- `AdalClaimChallengeException` is een uitzonde ring (afgeleid van `AdalServiceException`) die wordt veroorzaakt door de service voor het geval een resource meer claims van de gebruiker vereist (bijvoorbeeld verificatie met twee factoren). Het `Claims`-lid bevat een JSON-fragment met de claims die worden verwacht.
- De open bare client toepassing die deze uitzonde ring ontvangt, moet nog steeds in ADAL.NET aan`AcquireTokenInteractive` roepen met een claim parameter. Door deze onderdrukking van `AcquireTokenInteractive` wordt niet zelfs geprobeerd om de cache te raken, omdat dit niet nodig is. De reden hiervoor is dat het token in de cache niet beschikt over de juiste claims (anders kan er geen `AdalClaimChallengeException` worden gegenereerd). Daarom is het niet nodig om de cache te bekijken. Houd er rekening mee dat de `ClaimChallengeException` kan worden ontvangen in een WebAPI met OBO, terwijl de `AcquireTokenInteractive` moet worden aangeroepen in een open bare client toepassing die deze web-API aanroept.
- Zie voor meer informatie, inclusief voor beelden, [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception) verwerken

In MSAL.NET worden claim Challenge-uitzonde ringen op de volgende manier verwerkt:

- De `Claims` worden weer gegeven in de `MsalServiceException`.
- Er is een `.WithClaim(claims)`-methode die kan worden toegepast op de `AcquireTokenInteractive` Builder. 

### <a name="supported-grants"></a>Ondersteunde subsidies

Niet alle subsidies worden nog ondersteund in MSAL.NET en het v 2.0-eind punt. Hier volgt een samen vatting waarin ADAL.NET en MSAL worden vergeleken. Ondersteunde subsidies van NET.

#### <a name="public-client-applications"></a>Open bare client toepassingen

Dit zijn de subsidies die worden ondersteund in ADAL.NET en MSAL.NET voor desktop-en mobiele toepassingen

Geef | ADAL.NET | MSAL.NET
----- |----- | -----
Interactief | [Interactieve verificatie](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Tokens interactief ophalen in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Geïntegreerde Windows-verificatie | [Geïntegreerde verificatie op Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication)
Gebruikers naam/wacht woord | [Tokens ophalen met gebruikers naam en wacht woord](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Wachtwoord verificatie voor gebruikers naam](msal-authentication-flows.md#usernamepassword)
Toestel code stroom | [Apparaatprofiel voor apparaten zonder webbrowsers](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Toestel code stroom](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Vertrouwelijke client toepassingen

Hier volgen de subsidies die worden ondersteund in ADAL.NET en MSAL.NET voor webtoepassingen, Web-Api's en daemon-toepassingen:

Type app | Geef | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web-app, Web-API, daemon | Client referenties | [Client referentie stromen in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Client referentie stromen in MSAL.net](msal-authentication-flows.md#client-credentials))
Web-API | Namens | [Service voor het aanroepen van services namens de gebruiker met ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Namens in MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Web-app | Verificatie code | [Tokens ophalen met autorisatie codes op Web-apps met ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Tokens ophalen met autorisatie codes op Web-apps met een MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cache persistentie

Met ADAL.NET kunt u de `TokenCache` klasse uitbreiden voor het implementeren van de gewenste persistentie functionaliteit op platforms zonder een beveiligde opslag (.NET Framework en .NET core) door gebruik te maken van de `BeforeAccess`-en `BeforeWrite`-methoden. Zie [token cache serialisatie in ADAL.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)voor meer informatie.

MSAL.NET maakt de token cache een verzegelde klasse, waardoor de mogelijkheid om deze uit te breiden, wordt verwijderd. Daarom moet uw implementatie van token cache-persistentie in de vorm van een helper-klasse zijn die samenwerkt met de verzegelde token cache. Deze interactie wordt beschreven in de [token cache-serialisatie in MSAL.net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Signification van de algemene instantie

Als u in v 1.0 de https://login.microsoftonline.com/common Authority gebruikt, kunnen gebruikers zich aanmelden met een AAD-account (voor elke organisatie). Zie [verificatie van de certificerings instantie in ADAL.net](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Als u de https://login.microsoftonline.com/common -instantie in v 2.0 gebruikt, kunnen gebruikers zich aanmelden met een AAD-organisatie of een persoonlijk micro soft-account (MSA). Als u de aanmelding voor een AAD-account (hetzelfde gedrag als met ADAL.NET) wilt beperken, moet u in MSAL.NET de https://login.microsoftonline.com/organizations gebruiken. Zie de para meter `authority` in [open bare client toepassing](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication)voor meer informatie.

## <a name="v10-and-v20-tokens"></a>v 1.0 en v 2.0-tokens

Er zijn twee versies van tokens:
- v 1.0-tokens
- v 2.0-tokens 

Het eind punt v 1.0 (gebruikt door ADAL) verzendt alleen tokens van v 1.0.

Het v 2.0-eind punt (gebruikt door MSAL) verzendt echter de versie van het token dat door de Web-API wordt geaccepteerd. Een eigenschap van het toepassings manifest van de Web-API stelt ontwikkel aars in staat om te kiezen welke versie van het token wordt geaccepteerd. Zie `accessTokenAcceptedVersion` in de naslag documentatie over [toepassings manifesten](reference-app-manifest.md) .

Zie voor meer informatie over de tokens v 1.0 en v 2.0 [Azure Active Directory toegangs tokens](access-tokens.md)

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Bereiken voor een web-API die v 1.0-tokens accepteert

OAuth2 machtigingen zijn machtigings bereiken die een v 1.0 Web API (resource)-toepassing beschikbaar maakt voor client toepassingen. Deze machtigings bereiken kunnen tijdens de toestemming aan client toepassingen worden verleend. Zie de sectie over oauth2Permissions in [Azure Active Directory toepassings manifest](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Bereiken voor het aanvragen van toegang tot specifieke OAuth2-machtigingen van een v 1.0-toepassing

Als u tokens wilt verkrijgen voor specifieke bereiken van een v 1.0-toepassing (bijvoorbeeld de AAD-grafiek, die https://graph.windows.net) is, moet u `scopes` maken door een gewenste resource-id te koppelen met een gewenste OAuth2-machtiging voor die bron.

Bijvoorbeeld, om toegang te krijgen tot de naam van de gebruiker, een web-API van v 1.0 die de URI van de App-ID is `ResourceId`, wilt u het volgende gebruiken:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Als u wilt lezen en schrijven met MSAL.NET Azure Active Directory met behulp van de AAD Graph API (https://graph.windows.net/) , maakt u een lijst met bereiken zoals in het volgende code fragment:

```csharp
ResourceId = "https://graph.windows.net/";
var scopes = new [] { ResourceId + “Directory.Read”, ResourceID + “Directory.Write”}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Waarschuwing: als u een of twee schuine strepen hebt in het bereik dat overeenkomt met een v 1.0 Web-API

Als u het bereik wilt schrijven dat overeenkomt met de Azure Resource Manager-API (https://management.core.windows.net/) , moet u het volgende bereik aanvragen (Let op de twee slashes) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Dit komt doordat de Resource Manager-API een slash in de claim van de doel groep verwacht (`aud`) en vervolgens een slash is om de API-naam van het bereik te scheiden.

De logica die door Azure AD wordt gebruikt, is als volgt:
- Voor ADAL (v 1.0)-eind punt met een v 1.0-toegangs token (alleen mogelijk), AUD = resource
- Voor MSAL (v 2.0-eind punt) waarbij een toegangs token wordt gevraagd voor een resource die v 2.0-tokens accepteert, AUD = resource. AppId
- Voor MSAL (v 2.0-eind punt) waarbij een toegangs token wordt gevraagd voor een resource die een v 1.0-toegangs token accepteert (dit is het geval hierboven), parseert Azure AD de gewenste doel groep uit het aangevraagde bereik door alles vóór de laatste slash te nemen en deze als de resource-id te gebruiken. Als https:\/-database.windows.net de doel groep 'https://database.windows.net/ ' verwacht, moet u daarom een scope van HTTPS aanvragen:\/ /database.windows.net//.default. Zie ook #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): het afsluitende slash van de bron-URL wordt wegge laten, wat de oorzaak is van een mislukte SQL-verificatie #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Bereiken om toegang aan te vragen tot alle machtigingen van een v 1.0-toepassing

Als u bijvoorbeeld een token wilt verkrijgen voor alle statische bereiken van een v 1.0-toepassing, moet u het account gebruiken

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Bereiken die moeten worden aangevraagd in het geval van een client referentie stroom/daemon-app

In het geval van een client referentie stroom wordt het bereik dat moet worden door gegeven ook `/.default`. Met deze scope krijgt u toegang tot Azure AD: ' alle machtigingen op app-niveau die de beheerder heeft ingestemd in de registratie van de toepassing.

## <a name="adal-to-msal-migration"></a>Migratie van ADAL naar MSAL

In ADAL.NET v2. X werden de vernieuwings tokens beschikbaar gemaakt, zodat u oplossingen kunt ontwikkelen voor het gebruik van deze tokens door ze in de cache op te slaan en de `AcquireTokenByRefreshToken` methoden te gebruiken die worden geleverd door ADAL 2. x. Enkele van deze oplossingen zijn gebruikt in scenario's zoals:
* Langlopende services die acties uitvoeren, zoals het vernieuwen van Dash boards namens de gebruikers, terwijl de gebruikers niet meer zijn verbonden. 
* Webfarm-scenario's voor het inschakelen van de-client om de RT naar de webservice te brengen (caching wordt uitgevoerd aan client zijde, versleutelde cookie en niet aan server zijde)

MSAL.NET maakt geen vernieuwings tokens beschikbaar om veiligheids redenen: MSAL verwerkt de tokens voor u vernieuwen. 

Gelukkig heeft MSAL.NET nu een API waarmee u uw vorige vernieuwings tokens (verkregen met ADAL) kunt migreren naar de `IConfidentialClientApplication`:

```CSharp
/// <summary>
/// Acquires an access token from an existing refresh token and stores it and the refresh token into 
/// the application user token cache, where it will be available for further AcquireTokenSilent calls.
/// This method can be used in migration to MSAL from ADAL v2 and in various integration 
/// scenarios where you have a RefreshToken available. 
/// (see https://aka.ms/msal-net-migration-adal2-msal2)
/// </summary>
/// <param name="scopes">Scope to request from the token endpoint. 
/// Setting this to null or empty will request an access token, refresh token and ID token with default scopes</param>
/// <param name="refreshToken">The refresh token from ADAL 2.x</param>
IByRefreshToken.AcquireTokenByRefreshToken(IEnumerable<string> scopes, string refreshToken);
```
 
Met deze methode kunt u het eerder gebruikte vernieuwings token samen met eventuele bereiken (resources) opgeven die u wenst. Het vernieuwings token wordt uitgewisseld voor een nieuw account en in de cache van uw toepassing.  

Omdat deze methode is bedoeld voor scenario's die niet gebruikelijk zijn, is deze niet direct toegankelijk met de `IConfidentialClientApplication` zonder deze eerst te converteren naar `IByRefreshToken`.

Dit code fragment toont enkele migratie code in een vertrouwelijke client toepassing. `GetCachedRefreshTokenForSignedInUser` het vernieuwings token op te halen dat in sommige opslag is opgeslagen door een eerdere versie van de toepassing die is gebruikt voor het gebruik van ADAL 2. x. `GetTokenCacheForSignedInUser` deserialiseren van een cache voor de aangemelde gebruiker (als vertrouwelijke client toepassingen één cache per gebruiker moeten hebben).

```csharp
TokenCache userCache = GetTokenCacheForSignedInUser();
string rt = GetCachedRefreshTokenForSignedInUser();

IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(clientId)
 .WithAuthority(Authority)
 .WithRedirectUri(RedirectUri)
 .WithClientSecret(ClientSecret)
 .Build();
IByRefreshToken appRt = app as IByRefreshToken;
         
AuthenticationResult result = await appRt.AcquireTokenByRefreshToken(null, rt)
                                         .ExecuteAsync()
                                         .ConfigureAwait(false);
```

Er wordt een toegangs token en ID-token weer gegeven dat in uw AuthenticationResult is geretourneerd terwijl uw nieuwe vernieuwings token in de cache wordt opgeslagen.

U kunt deze methode ook gebruiken voor verschillende integratie scenario's waarin u een vernieuwings token beschikbaar hebt.

## <a name="next-steps"></a>Volgende stappen

U kunt meer informatie vinden over de bereiken in [bereiken, machtigingen en toestemming in het micro soft Identity platform-eind punt](v2-permissions-and-consent.md)

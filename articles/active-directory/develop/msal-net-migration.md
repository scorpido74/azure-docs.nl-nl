---
title: Migreren naar MSAL.NET
titleSuffix: Microsoft identity platform
description: Meer informatie over de verschillen tussen Microsoft Authentication Library voor .NET (MSAL.NET) en Azure AD-verificatiebibliotheek voor .NET (ADAL.NET) en hoe u migreren naar MSAL.NET.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: cccb886e13482292e8ab9afa2b34bd9dd2c3229b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050313"
---
# <a name="migrating-applications-to-msalnet"></a>Toepassingen migreren naar MSAL.NET

Zowel Microsoft Authentication Library for .NET (MSAL.NET) als Azure AD Authentication Library for .NET (ADAL.NET) worden gebruikt om Azure AD-entiteiten te verifiëren en tokens van Azure AD aan te vragen. Tot nu toe hebben de meeste ontwikkelaars samengewerkt met Azure AD voor ontwikkelaarsplatform (v1.0) om Azure AD-identiteiten (werk- en schoolaccounts) te verifiëren door tokens aan te vragen met Azure AD-verificatiebibliotheek (ADAL). Met behulp van MSAL:

- u een bredere set Microsoft-identiteiten (Azure AD-identiteiten en Microsoft-accounts en sociale en lokale accounts verifiëren via Azure AD B2C) terwijl het het eindpunt van het Microsoft-identiteitsplatform wordt gebruikt,
- uw gebruikers krijgen de beste single-sign-on ervaring.
- uw toepassing kan incrementele toestemming inschakelen en het ondersteunen van voorwaardelijke toegang is eenvoudiger
- u profiteert van de innovatie.

**MSAL.NET is nu de aanbevolen auth-bibliotheek om te gebruiken met het Microsoft-identiteitsplatform.** Er worden geen nieuwe functies geïmplementeerd op ADAL.NET. De inspanningen zijn gericht op het verbeteren van MSAL.

In dit artikel worden de verschillen beschreven tussen de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET) en Azure AD-verificatiebibliotheek voor .NET (ADAL.NET) en u migreren naar MSAL.  

## <a name="differences-between-adal-and-msal-apps"></a>Verschillen tussen ADAL- en MSAL-apps

In de meeste gevallen wilt u MSAL.NET en het eindpunt van het Microsoft-identiteitsplatform gebruiken, de nieuwste generatie Microsoft-verificatiebibliotheken. Met MSAL.NET, verwerft u tokens voor gebruikers die zich aanmelden bij uw toepassing met Azure AD (werk- en schoolaccounts), Microsoft (personal) accounts (MSA) of Azure AD B2C. 

Als u al bekend bent met het Azure AD voor ontwikkelaars (v1.0) eindpunt (en ADAL.NET), wilt u misschien lezen [Wat is er anders aan het Microsoft-identiteitsplatform (v2.0) eindpunt?](active-directory-v2-compare.md).

U moet echter nog steeds ADAL.NET gebruiken als uw toepassing gebruikers moet aanmelden met eerdere versies van [ADFS (Active Directory Federation Services).](/windows-server/identity/active-directory-federation-services) Zie [ADFS-ondersteuning](https://aka.ms/msal-net-adfs-support)voor meer informatie.

De volgende afbeelding vat enkele verschillen samen ![tussen ADAL.NET en MSAL.NET Side-by-SIDE-code](./media/msal-compare-msaldotnet-and-adaldotnet/differences.png)

### <a name="nuget-packages-and-namespaces"></a>NuGet-pakketten en naamruimten

ADAL.NET wordt verbruikt vanuit het [Microsoft.IdentityModel.Clients.ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory) NuGet-pakket. de naamruimte die `Microsoft.IdentityModel.Clients.ActiveDirectory`u wilt gebruiken is .

Als u MSAL.NET wilt gebruiken, moet u het [NuGet-pakket microsoft.identity.client](https://www.nuget.org/packages/Microsoft.Identity.Client) toevoegen en de `Microsoft.Identity.Client` naamruimte gebruiken

### <a name="scopes-not-resources"></a>Scopes niet resources

ADAL.NET verwerft tokens voor *resources,* maar MSAL.NET verwerft tokens voor *scopes*. Een aantal MSAL.NET AcquireToken-overschrijvingen vereisen een`IEnumerable<string> scopes`parameter genaamd scopes. Deze parameter is een eenvoudige lijst met tekenreeksen die de gewenste machtigingen en resources declareren die worden aangevraagd. Bekende scopes zijn de [scopes van Microsoft Graph.](/graph/permissions-reference)

Het is ook mogelijk in MSAL.NET toegang te krijgen tot v1.0-bronnen. Zie details in [scopes voor een v1.0-toepassing](#scopes-for-a-web-api-accepting-v10-tokens). 

### <a name="core-classes"></a>Kernklassen

- ADAL.NET gebruikt [AuthenticationContext](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD) als weergave van uw verbinding met de Security Token Service (STS) of autorisatieserver, via een Autoriteit. Integendeel, MSAL.NET is ontworpen rond [clienttoepassingen.](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications) Het biedt twee aparte `PublicClientApplication` klassen: en`ConfidentialClientApplication`

- Tokens verwerven: ADAL.NET en MSAL.NET hebben`AcquireTokenAsync` dezelfde `AcquireTokenSilentAsync` verificatiegesprekken `AcquireTokenInteractive` (en voor ADAL.NET en en `AcquireTokenSilent` in MSAL.NET), maar met verschillende vereiste parameters. Een verschil is het feit dat u in MSAL.NET `ClientID` niet meer in het van uw toepassing hoeft door te geven in elke AcquireTokenXX-oproep. Inderdaad, `ClientID` het is slechts eenmaal`IPublicClientApplication` ingesteld `IConfidentialClientApplication`bij het bouwen van de ( of ).

### <a name="iaccount-not-iuser"></a>IAccount niet IUser

ADAL.NET gemanipuleerde gebruikers. Een gebruiker is echter een mens of een softwareagent, maar kan een of meer accounts in het Microsoft-identiteitssysteem bezitten/bezitten/bezitten/zijn (meerdere Azure AD-accounts, Azure AD B2C, persoonlijke accounts van Microsoft). 

MSAL.NET 2.x definieert nu het concept van Account (via de IAccount-interface). Deze brekende wijziging biedt de juiste semantiek: het feit dat dezelfde gebruiker meerdere accounts kan hebben, in verschillende Azure AD-mappen. Ook MSAL.NET biedt betere informatie in gastscenario's, omdat thuisaccountgegevens worden verstrekt.

Zie [MSAL.NET 2.x voor](https://aka.ms/msal-net-2-released)meer informatie over de verschillen tussen IUser en IAccount.

### <a name="exceptions"></a>Uitzonderingen

#### <a name="interaction-required-exceptions"></a>Voor interactie vereiste uitzonderingen

MSAL.NET heeft meer expliciete uitzonderingen. Wanneer stille verificatie bijvoorbeeld mislukt in ADAL, is de procedure `user_interaction_required` om de uitzondering te vangen en de foutcode te zoeken:

```csharp
catch(AdalException exception)
{
 if (exception.ErrorCode == "user_interaction_required")
 {
  try
  {“try to authenticate interactively”}}
 }
}
```

Zie details in [Het aanbevolen patroon om een token te verkrijgen](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token#recommended-pattern-to-acquire-a-token) met ADAL.NET

Met behulp van `MsalUiRequiredException` MSAL.NET, vangt u zoals beschreven in [AcquireTokenSilent](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-a-cached-token).

```csharp
catch(MsalUiRequiredException exception)
{
 try {“try to authenticate interactively”}
}
```

#### <a name="handling-claim-challenge-exceptions"></a>Uitzonderingen op claimafhandelingsverzoeken afhandelen

In ADAL.NET worden claimchallenge-uitzonderingen op de volgende manier behandeld:

- `AdalClaimChallengeException`is een uitzondering (afkomstig `AdalServiceException`van) gegooid door de service in het geval een bron vereist meer claims van de gebruiker (bijvoorbeeld twee-factoren authenticatie). Het `Claims` lid bevat een aantal JSON fragment met de vorderingen, die worden verwacht.
- Nog steeds in ADAL.NET, de openbare client `AcquireTokenInteractive` applicatie die deze uitzondering moet de override met een claim parameter aan te roepen. Deze override `AcquireTokenInteractive` van niet eens proberen om de cache te raken als het niet nodig is. De reden is dat het token in de cache `AdalClaimChallengeException` niet de juiste claims heeft (anders zou er niet gegooid zijn). Daarom is het niet nodig om naar de cache te kijken. Houd er `ClaimChallengeException` rekening mee dat deze kan worden `AcquireTokenInteractive` ontvangen in een WebAPI die OBO doet, terwijl deze moet worden aangeroepen in een openbare clienttoepassing die deze web-API aanroept.
- voor meer informatie, inclusief monsters zie Handling [AdalClaimChallengeException](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Exceptions-in-ADAL.NET#handling-adalclaimchallengeexception)

In MSAL.NET worden uitzonderingen op claimchallenge op de volgende manier behandeld:

- De `Claims` zijn opgedoken `MsalServiceException`in de .
- Er is `.WithClaim(claims)` een methode die `AcquireTokenInteractive` van toepassing kan zijn op de bouwer. 

### <a name="supported-grants"></a>Ondersteunde subsidies

Nog niet alle subsidies worden ondersteund in MSAL.NET en het v2.0-eindpunt. Het volgende is een samenvatting waarin ADAL.NET en MSAL worden vergeleken. de ondersteunde subsidies van NET.

#### <a name="public-client-applications"></a>Openbare clienttoepassingen

Dit zijn de subsidies die worden ondersteund in ADAL.NET en MSAL.NET voor desktop- en mobiele toepassingen

Verlenen | ADAL.NET | MSAL.NET
----- |----- | -----
Interactief | [Interactieve Auth](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-interactively---Public-client-application-flows) | [Tokens interactief verwerven in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively)
Geïntegreerde Windows-authenticatie | [Geïntegreerde verificatie op Windows (Kerberos)](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AcquireTokenSilentAsync-using-Integrated-authentication-on-Windows-(Kerberos)) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication)
Gebruikersnaam / wachtwoord | [Tokens kopen met gebruikersnaam en wachtwoord](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-username-and-password)| [Verificatie van gebruikersnaamwachtwoord](msal-authentication-flows.md#usernamepassword)
Apparaatcodestroom | [Apparaatprofiel voor apparaten zonder webbrowser](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Device-profile-for-devices-without-web-browsers) | [Apparaatcodestroom](msal-authentication-flows.md#device-code)

#### <a name="confidential-client-applications"></a>Vertrouwelijke clienttoepassingen

Dit zijn de subsidies die worden ondersteund in ADAL.NET en MSAL.NET voor webtoepassingen, webAPI's en daemon-toepassingen:

Type of App | Verlenen | ADAL.NET | MSAL.NET
----- | ----- | ----- | -----
Web App, Web API, daemon | Clientreferenties | [Clientreferenties stromen in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Client-credential-flows) | [Clientreferentiesstromen in MSAL.NET](msal-authentication-flows.md#client-credentials))
Web-API | Namens | [Service tot service gesprekken namens de gebruiker met ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Service-to-service-calls-on-behalf-of-the-user) | [Namens in MSAL.NET](msal-authentication-flows.md#on-behalf-of)
Web-app | Auth-code | [Tokens verwerven met autorisatiecodes op web-apps met ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Acquiring-tokens-with-authorization-codes-on-web-apps) | [Tokens verwerven met autorisatiecodes op web-apps met A-MSAL.NET](msal-authentication-flows.md#authorization-code)

### <a name="cache-persistence"></a>Cache persistentie

ADAL.NET u `TokenCache` de klasse uitbreiden om de gewenste persistentiefunctionaliteit op platforms te implementeren zonder `BeforeAccess`een `BeforeWrite` veilige opslag (.NET Framework en .NET-kern) met behulp van de, en methoden. Zie [Serialisatie van tokencache in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization)voor meer informatie.

MSAL.NET maakt de tokencache een verzegelde klasse, waardoor de mogelijkheid om deze uit te breiden wordt verwijderd. Daarom moet uw implementatie van tokencache persistentie zijn in de vorm van een helperklasse die interageert met de verzegelde tokencache. Deze interactie wordt beschreven in [Token Cache Serialization in MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/token-cache-serialization).

## <a name="signification-of-the-common-authority"></a>Betekenis van de gemeenschappelijke autoriteit

In v1.0, als `https://login.microsoftonline.com/common` u de autoriteit gebruikt, u gebruikers toestaan om in te loggen met een AAD-account (voor elke organisatie). Zie [Authority Validation in ADAL.NET](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/AuthenticationContext:-the-connection-to-Azure-AD#authority-validation)

Als u `https://login.microsoftonline.com/common` de autoriteit in v2.0 gebruikt, u gebruikers toestaan zich aan te melden bij een AAD-organisatie of een persoonlijk Microsoft-account (MSA). Als u in MSAL.NET wilt inloggen tot een AAD-account (hetzelfde `https://login.microsoftonline.com/organizations`gedrag als bij ADAL.NET), moet u . Zie voor meer `authority` informatie de parameter in [openbare clienttoepassing](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Client-Applications#publicclientapplication).

## <a name="v10-and-v20-tokens"></a>v1.0- en v2.0-tokens

Er zijn twee versies van tokens:
- v1.0 tokens
- v2.0-tokens 

Het v1.0-eindpunt (gebruikt door ADAL) zendt alleen v1.0-tokens uit.

Het v2.0-eindpunt (gebruikt door MSAL) zendt echter de versie uit van het token dat de Web-API accepteert. Een eigenschap van het toepassingsmanifest van de Web API stelt ontwikkelaars in staat om te kiezen welke versie van token wordt geaccepteerd. Zie `accessTokenAcceptedVersion` in de [referentiedocumentatie van het toepassingsmanifest.](reference-app-manifest.md)

Zie [Azure Active Directory-toegangstokens](access-tokens.md) voor meer informatie over v1.0- en v2.0-tokens

## <a name="scopes-for-a-web-api-accepting-v10-tokens"></a>Scopes voor een Web API die v1.0-tokens accepteert

OAuth2-machtigingen zijn machtigingsscopes die een v1.0-webAPI-toepassing blootstelt aan clienttoepassingen. Deze machtigingsmogelijkheden kunnen worden verleend aan clienttoepassingen tijdens de toestemming. Zie de sectie over oauth2Permissions in [Azure Active Directory-toepassingsmanifest](active-directory-application-manifest.md).

### <a name="scopes-to-request-access-to-specific-oauth2-permissions-of-a-v10-application"></a>Scopes om toegang te vragen tot specifieke OAuth2-machtigingen van een v1.0-toepassing

Als u tokens wilt aanschaffen voor een toepassing die v1.0-tokens accepteert https://graph.microsoft.com)(bijvoorbeeld de Microsoft `scopes` Graph API, dat wil zeggen, moet u een gewenste resource-id maken met een gewenste OAuth2-machtiging voor die bron.

Bijvoorbeeld, om toegang te krijgen in de naam van de gebruiker `ResourceId`een v1.0 Web API die App ID URI is, zou je willen gebruiken:

```csharp
var scopes = new [] {  ResourceId+"/user_impersonation"};
```

Als u wilt lezen en schrijven met MSAL.NET Azurehttps://graph.microsoft.com/)Active Directory met behulp van de Microsoft Graph API ( , maakt u een lijst met scopes zoals in het volgende fragment:

```csharp
ResourceId = "https://graph.microsoft.com/";
var scopes = new [] { ResourceId + "Directory.Read", ResourceID + "Directory.Write"}
```

#### <a name="warning-should-you-have-one-or-two-slashes-in-the-scope-corresponding-to-a-v10-web-api"></a>Waarschuwing: Als u een of twee slashes in het bereik hebt dat overeenkomt met een v1.0-web-API

Als u het bereik wilt schrijven dat overeenkomthttps://management.core.windows.net/)met de Azure Resource Manager API ( , moet u het volgende bereik aanvragen (let op de twee slashes) 

```csharp
var scopes = new[] {"https://management.core.windows.net//user_impersonation"};
var result = await app.AcquireTokenInteractive(scopes).ExecuteAsync();

// then call the API: https://management.azure.com/subscriptions?api-version=2016-09-01
```

Dit komt omdat de Api Voor ResourceBeheer`aud`een slash verwacht in de doelgroepclaim ( ) en vervolgens een slash is om de API-naam van het bereik te scheiden.

De logica die wordt gebruikt door Azure AD is de volgende:
- Voor ADAL-eindpunt (v1.0) met een v1.0-toegangstoken (het enige mogelijke), aud=resource
- Voor MSAL (v2.0-eindpunt) die een toegangstoken vraagt voor een bron die v2.0-tokens accepteert, aud=resource. Appid
- Voor MSAL (v2.0-eindpunt) die een toegangstoken vraagt voor een bron die een v1.0-toegangstoken accepteert (wat het geval is hierboven), ontleedt Azure AD het gewenste publiek uit het gevraagde bereik door alles voor de laatste slash te nemen en het als resource-id te gebruiken. Dus als\/https: /database.windows.net eenhttps://database.windows.net/publiek van " " verwacht,\/moet je een scope van https aanvragen: /database.windows.net//.default. Zie ook issue #[747](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/747): Resource url's trailing slash is weggelaten, waardoor sql auth mislukking #747


### <a name="scopes-to-request-access-to-all-the-permissions-of-a-v10-application"></a>Scopes om toegang te vragen tot alle machtigingen van een v1.0-toepassing

Als u bijvoorbeeld een token wilt aanschaffen voor alle statische scopes van een v1.0-toepassing, moet u

```csharp
ResourceId = "someAppIDURI";
var scopes = new [] {  ResourceId+"/.default"};
```

### <a name="scopes-to-request-in-the-case-of-client-credential-flow--daemon-app"></a>Scopes op te vragen in het geval van client credential flow / daemon app

In het geval van clientreferentiestroom zou de `/.default`te passeren scope ook . Dit bereik vertelt aan Azure AD: "alle machtigingen op app-niveau waarmee de beheerder heeft ingestemd in de toepassingsregistratie.

## <a name="adal-to-msal-migration"></a>ADAL naar MSAL-migratie

In ADAL.NET v2. X, de refresh tokens werden blootgesteld zodat u oplossingen te ontwikkelen rond het `AcquireTokenByRefreshToken` gebruik van deze tokens door caching hen en met behulp van de methoden die door ADAL 2.x. Sommige van deze oplossingen werden gebruikt in scenario's zoals:
* Langlopende services die acties uitvoeren, waaronder vernieuwende dashboards namens de gebruikers, terwijl de gebruikers niet langer zijn verbonden. 
* WebFarm-scenario's voor het inschakelen van de client om de RT naar de webservice te brengen (caching wordt gedaan clientkant, versleutelde cookie en niet serverkant)

MSAL.NET stelt geen refresh-tokens bloot, om veiligheidsredenen: MSAL verwerkt verfrissende tokens voor u. 

Gelukkig heeft MSAL.NET nu een API waarmee u uw eerdere vernieuwingstokens (verkregen met ADAL) migreren naar de: `IConfidentialClientApplication`

```csharp
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
 
Met deze methode u het eerder gebruikte vernieuwingstoken bieden, samen met alle scopes (resources) die u wenst. Het vernieuwingstoken wordt ingeruild voor een nieuwe en in de cache opgeslagen in uw toepassing.  

Aangezien deze methode is bedoeld voor scenario's die niet typisch `IConfidentialClientApplication` zijn, is `IByRefreshToken`het niet gemakkelijk toegankelijk met de zonder eerst gieten naar .

Dit codefragment toont een migratiecode in een vertrouwelijke clienttoepassing. `GetCachedRefreshTokenForSignedInUser`het vernieuwingstoken ophalen dat in een bepaalde opslag is opgeslagen door een vorige versie van de toepassing die werd gebruikt om ADAL 2.x te gebruiken. `GetTokenCacheForSignedInUser`deserialiseert een cache voor de aangemelde gebruiker (omdat vertrouwelijke clienttoepassingen één cache per gebruiker moeten hebben).

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

U ziet een toegangstoken en ID-token worden geretourneerd in uw AuthenticationResult terwijl uw nieuwe vernieuwingstoken in de cache wordt opgeslagen.

U deze methode ook gebruiken voor verschillende integratiescenario's waarbij u een vernieuwingstoken beschikbaar hebt.

## <a name="next-steps"></a>Volgende stappen

U vindt meer informatie over de scopes in [scopes, machtigingen en toestemming in het eindpunt van het Microsoft-identiteitsplatform](v2-permissions-and-consent.md)

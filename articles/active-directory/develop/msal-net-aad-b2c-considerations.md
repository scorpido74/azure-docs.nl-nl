---
title: Azure AD B2C (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Azure AD B2C met de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/29/2019
ms.author: jeferrie
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 697b4bc8e3a25085ac6f7d600ea2227dd30a6624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262813"
---
# <a name="use-msalnet-to-sign-in-users-with-social-identities"></a>Gebruik MSAL.NET om zich aan te melden bij gebruikers met een sociale identiteit

U MSAL.NET gebruiken om gebruikers met een sociale identiteit aan te melden met [Azure Active Directory B2C (Azure AD B2C).](https://aka.ms/aadb2c) Azure AD B2C is gebouwd rond het begrip beleidsregels. In MSAL.NET vertaalt het specificeren van een beleid zich naar het verstrekken van een autoriteit.

- Wanneer u de openbare clienttoepassing instantiate, moet u het beleid in autoriteit opgeven.
- Wanneer u een beleid wilt toepassen, moet `AcquireTokenInteractive` u een `authority` overschrijving van het bevatten van een parameter aanroepen.

Deze pagina is voor MSAL 3.x. Als u geïnteresseerd bent in MSAL 2.x, raadpleegt u [Azure AD B2C-specificaties in MSAL 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-Specifics-MSAL-2.x).

## <a name="authority-for-a-azure-ad-b2c-tenant-and-policy"></a>Autoriteit voor een Azure AD B2C-tenant en -beleid

De autoriteit om `https://{azureADB2CHostname}/tfp/{tenant}/{policyName}` te gebruiken is waar:

- `azureADB2CHostname`is de naam van de Azure AD B2C-tenant plus de host (bijvoorbeeld `{your-tenant-name}.b2clogin.com`),
- `tenant`is de volledige naam van de Azure AD `{your-tenant-name}.onmicrosoft.com`B2C-tenant (bijvoorbeeld ) of de GUID voor de tenant, 
- `policyName`de naam van het beleid of de gebruikersstroom die u wilt toepassen (bijvoorbeeld "b2c_1_susi" voor aanmelden/aanmelden).

Zie deze [documentatie](/azure/active-directory-b2c/b2clogin)voor meer informatie over de Azure AD B2C-autoriteiten.

## <a name="instantiating-the-application"></a>De toepassing instantiëring

Bij het bouwen van de aanvraag, moet u de autoriteit te verstrekken.

```csharp
// Azure AD B2C Coordinates
public static string Tenant = "fabrikamb2c.onmicrosoft.com";
public static string AzureADB2CHostname = "fabrikamb2c.b2clogin.com";
public static string ClientID = "90c0fe63-bcf2-44d5-8fb7-b8bbc0b29dc6";
public static string PolicySignUpSignIn = "b2c_1_susi";
public static string PolicyEditProfile = "b2c_1_edit_profile";
public static string PolicyResetPassword = "b2c_1_reset";

public static string AuthorityBase = $"https://{AzureADB2CHostname}/tfp/{Tenant}/";
public static string Authority = $"{AuthorityBase}{PolicySignUpSignIn}";
public static string AuthorityEditProfile = $"{AuthorityBase}{PolicyEditProfile}";
public static string AuthorityPasswordReset = $"{AuthorityBase}{PolicyResetPassword}";

application = PublicClientApplicationBuilder.Create(ClientID)
               .WithB2CAuthority(Authority)
               .Build();
```

## <a name="acquire-a-token-to-apply-a-policy"></a>Een token aanschaffen om een beleid toe te passen

Voor het aanschaffen van een token voor een Azure AD B2C-beveiligde API in een openbare clienttoepassing moet u de overschrijvingen met een autoriteit gebruiken:

```csharp
IEnumerable<IAccount> accounts = await application.GetAccountsAsync();
AuthenticationResult ar = await application .AcquireTokenInteractive(scopes)
                                            .WithAccount(GetAccountByPolicy(accounts, policy))
                                            .WithParentActivityOrWindow(ParentActivityOrWindow)
                                            .ExecuteAsync();
```

door:

- `policy`een van de vorige tekenreeksen (bijvoorbeeld). `PolicySignUpSignIn`
- `ParentActivityOrWindow`is vereist voor Android (de activiteit) en optioneel voor andere platforms die de bovenliggende gebruikersinterface ondersteunen, zoals windows in Windows en UIViewController in iOS. Bekijk hier meer informatie [over het dialoogvenster Gebruikersinterface](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Acquiring-tokens-interactively#withparentactivityorwindow).
- `GetAccountByPolicy(IEnumerable<IAccount>, string)`is een methode die een account vindt voor een bepaald beleid. Bijvoorbeeld:

  ```csharp
  private IAccount GetAccountByPolicy(IEnumerable<IAccount> accounts, string policy)
  {
   foreach (var account in accounts)
   {
    string userIdentifier = account.HomeAccountId.ObjectId.Split('.')[0];
    if (userIdentifier.EndsWith(policy.ToLower()))
     return account;
   }
   return null;
  }
  ```

Het toepassen van een beleid of gebruikersstroom (bijvoorbeeld de eindgebruiker zijn profiel laten bewerken `AcquireTokenInteractive`of zijn wachtwoord opnieuw instellen) wordt momenteel uitgevoerd door te bellen. In het geval van deze twee beleidsregels gebruikt u het resultaat geretourneerde token/ verificatie niet.

## <a name="special-case-of-editprofile-and-resetpassword-policies"></a>Speciaal geval van beleid bewerkenProfiel en ResetPassword

Wanneer u een ervaring wilt bieden waarin uw eindgebruikers zich aanmelden met een sociale identiteit en vervolgens hun profiel bewerken, wilt u het Azure AD B2C-profielbeleid bewerken toepassen. De manier om dit `AcquireTokenInteractive` te doen is door te bellen met `Prompt.NoPrompt` de specifieke autoriteit voor dat beleid, en een prompt ingesteld om te voorkomen dat de account selectie dialoogvenster wordt weergegeven (als de gebruiker is al aangemeld en heeft een actieve cookie sessie).

```csharp
private async void EditProfileButton_Click(object sender, RoutedEventArgs e)
{
 IEnumerable<IAccount> accounts = await app.GetAccountsAsync();
 try
 {
  var authResult = await app.AcquireToken(scopes:App.ApiScopes)
                               .WithAccount(GetUserByPolicy(accounts, App.PolicyEditProfile)),
                               .WithPrompt(Prompt.NoPrompt),
                               .WithB2CAuthority(App.AuthorityEditProfile)
                               .ExecuteAsync();
  DisplayBasicTokenInfo(authResult);
 }
 catch
 {
  . . .
 }
}
```
## <a name="resource-owner-password-credentials-ropc-with-azure-ad-b2c"></a>Wachtwoordreferenties voor broneigenaren (ROPC) met Azure AD B2C
Voor meer informatie over de ROPC-stroom, zie deze [documentatie](v2-oauth-ropc.md).

Deze stroom wordt **niet aanbevolen** omdat uw toepassing die een gebruiker om zijn wachtwoord vraagt, niet veilig is. Zie [dit artikel](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)voor meer informatie over dit probleem. 

Door gebruikersnaam/wachtwoord te gebruiken, geeft u een aantal dingen op:
- Kernprincipes van de moderne identiteit: wachtwoord wordt gevist, opnieuw afgespeeld. Omdat we dit concept hebben van een aandelengeheim dat onderschept kan worden. Dit is niet compatibel met wachtwoordloos.
- Gebruikers die MFA moeten doen, kunnen zich niet aanmelden (omdat er geen interactie is).
- Gebruikers kunnen zich niet aanmelden.

### <a name="configure-the-ropc-flow-in-azure-ad-b2c"></a>De ROPC-stroom configureren in Azure AD B2C
Maak in uw Azure AD B2C-tenant een nieuwe gebruikersstroom en selecteer **Aanmelden met ROPC**. Hierdoor wordt het ROPC-beleid voor uw tenant ingeschakeld. Zie [De wachtwoordreferentiesstroom voor broneigenaren configureren](/azure/active-directory-b2c/configure-ropc) voor meer informatie.

`IPublicClientApplication`bevat een methode:
```csharp
AcquireTokenByUsernamePassword(
            IEnumerable<string> scopes,
            string username,
            SecureString password)
```

Deze methode neemt als parameters:
- De *scopes* waarvoor u een toegangstoken moet aanvragen.
- Een *gebruikersnaam*.
- Een *SecureString-wachtwoord* voor de gebruiker.

Vergeet niet de autoriteit te gebruiken die het ROPC-beleid bevat.

### <a name="limitations-of-the-ropc-flow"></a>Beperkingen van de ROPC-stroom
 - De ROPC-stroom **werkt alleen voor lokale accounts** (waarbij u zich registreert bij Azure AD B2C met een e-mail of gebruikersnaam). Deze stroom werkt niet als ze worden uitgevoerd naar een van de identiteitsproviders die worden ondersteund door Azure AD B2C (Facebook, Google, enz.).

## <a name="google-auth-and-embedded-webview"></a>Google Auth en Embedded Webview

Als u een Azure AD B2C-ontwikkelaar bent die Google als identiteitsprovider gebruikt, bevelen we u aan de systeembrowser te gebruiken, omdat Google [geen verificatie toestaat van ingesloten webweergaven.](https://developers.googleblog.com/2016/08/modernizing-oauth-interactions-in-native-apps.html) Momenteel `login.microsoftonline.com` is een vertrouwde autoriteit met Google. Het gebruik van deze autoriteit werkt met ingesloten webview. Het `b2clogin.com` gebruik is echter geen vertrouwde autoriteit bij Google, zodat gebruikers zich niet kunnen verifiëren.

We zullen een update van dit [probleem](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/issues/688) geven als er dingen veranderen.

## <a name="caching-with-azure-ad-b2c-in-msalnet"></a>Caching met Azure AD B2C in MSAL.Net 

### <a name="known-issue-with-azure-ad-b2c"></a>Bekend probleem met Azure AD B2C

MSAL.Net ondersteunt een [tokencache.](/dotnet/api/microsoft.identity.client.tokencache?view=azure-dotnet) De tokencachesleutel is gebaseerd op de claims die zijn geretourneerd door de identiteitsprovider. Momenteel heeft MSAL.Net twee claims nodig om een tokencachesleutel te bouwen:  
- `tid`dat is de Azure AD-tenant- id en 
- `preferred_username` 

Beide claims ontbreken in veel van de Azure AD B2C-scenario's. 

De impact van de klant is dat wanneer u het gebruikersnaamveld probeert weer te geven, u "Ontbreekt in de tokenrespons" als de waarde? Als dat het zo is, komt dit omdat Azure AD B2C geen waarde in de IdToken retourneert voor de preferred_username vanwege beperkingen met de sociale accounts en externe identiteitsproviders (IdP's). Azure AD retourneert een waarde voor preferred_username omdat deze weet wie de gebruiker is, maar voor Azure AD B2C, omdat de gebruiker zich kan aanmelden met een lokaal account, Facebook, Google, GitHub, enz preferred_username. Om MSAL te deblokkeren van de uitrol van cachecompatibiliteit met ADAL, hebben we besloten om "Ontbrekend in de tokenrespons" aan onze kant te gebruiken bij het omgaan met de Azure AD B2C-accounts wanneer de IdToken niets retourneert voor preferred_username. MSAL moet een waarde retourneren voor preferred_username om cachecompatibiliteit in bibliotheken te behouden.

### <a name="workarounds"></a>Tijdelijke oplossingen

#### <a name="mitigation-for-the-missing-tenant-id"></a>Mitigatie voor de ontbrekende tenant-id

De voorgestelde tijdelijke oplossing is het gebruik van de [Caching by Policy](#acquire-a-token-to-apply-a-policy)

U de `tid` claim ook gebruiken als u het aangepaste Beleid van [B2C](https://aka.ms/ief)gebruikt, omdat deze de mogelijkheid biedt om aanvullende claims naar de toepassing terug te sturen. Meer informatie over [claimtransformatie](/azure/active-directory-b2c/claims-transformation-technical-profile)

#### <a name="mitigation-for-missing-from-the-token-response"></a>Mitigatie voor 'Ontbreken in de tokenrespons'
Een optie is om de "naam" claim te gebruiken als de gewenste gebruikersnaam. Het proces wordt vermeld in dit [B2C-document](../../active-directory-b2c/user-flow-overview.md) -> "Kies in de kolom Claim retour de claims die u wilt retourneren in de autorisatietokens die naar uw aanvraag worden teruggestuurd na een succesvolle ervaring met profielbewerking. Selecteer bijvoorbeeld Weergavenaam, Postcode."

## <a name="next-steps"></a>Volgende stappen 

Meer details over het interactief aanschaffen van tokens met MSAL.NET voor Azure AD B2C-toepassingen vindt u in het volgende voorbeeld.

| Voorbeeld | Platform | Beschrijving|
|------ | -------- | -----------|
|[active-directory-b2c-xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Xamarin iOS, Xamarin Android, UWP | In een eenvoudige App Xamarin Forms wordt weergegeven hoe u MSAL.NET gebruikt om gebruikers te verifiëren via Azure AD B2C en toegang te krijgen tot een web-API met de resulterende tokens.|

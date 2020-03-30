---
title: Webbrowsers gebruiken (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over specifieke overwegingen bij het gebruik van Xamarin Android met de Microsoft-verificatiebibliotheek voor .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: ed1f47ae99f6346a932d0fe94be7586dc25a672f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262735"
---
# <a name="using-web-browsers-msalnet"></a>Webbrowsers gebruiken (MSAL.NET)

Webbrowsers zijn vereist voor interactieve verificatie. Standaard ondersteunt MSAL.NET de [systeemwebbrowser](#system-web-browser-on-xamarinios-xamarinandroid) op Xamarin.iOS en Xamarin.Android. Maar [u de ingesloten webbrowser ook inschakelen,](#enable-embedded-webviews-on-ios-and-android) afhankelijk van uw vereisten (UX, noodzaak voor single sign-on (SSO), beveiliging) in [Xamarin.iOS-](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) en [Xamarin.Android-apps.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) En u zelfs [dynamisch kiezen welke](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) webbrowser te gebruiken op basis van de aanwezigheid van Chrome of een browser die chrome aangepaste tabbladen in Android ondersteunt. MSAL.NET ondersteunt alleen de systeembrowser in .NET Core-bureaubladtoepassingen.

## <a name="web-browsers-in-msalnet"></a>Webbrowsers in MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interactie vindt plaats in een webbrowser

Het is belangrijk om te begrijpen dat bij het interactief aanschaffen van een token de inhoud van het dialoogvenster niet wordt geleverd door de bibliotheek, maar door de STS (Security Token Service). Het verificatieeindpunt stuurt een aantal HTML en JavaScript terug die de interactie regelt, die wordt weergegeven in een webbrowser of webbesturingselement. Het toestaan van de STS om de HTML-interactie te verwerken heeft vele voordelen:

- Het wachtwoord (als er een is getypt) wordt nooit opgeslagen door de toepassing, noch de verificatiebibliotheek.
- Hiermee u omleidingen naar andere identiteitsproviders (bijvoorbeeld inloggen met een werkschoolaccount of een persoonlijk account bij MSAL, of met een sociaal account bij Azure AD B2C).
- Hiermee kan de STS voorwaardelijke toegang beheren, bijvoorbeeld door de gebruiker meerdere factorauthenticatie (MFA) te laten doen tijdens de verificatiefase (het invoeren van een Windows Hello-pin, of wordt gebeld op hun telefoon of op een verificatie-app op hun telefoon). In gevallen waarin de vereiste multifactorauthenticatie nog niet is ingesteld, kan de gebruiker deze net op tijd instellen in hetzelfde dialoogvenster.  De gebruiker voert zijn mobiele telefoonnummer in en wordt begeleid om een authenticatietoepassing te installeren en een QR-tag te scannen om zijn/haar account toe te voegen. Deze server gedreven interactie is een geweldige ervaring!
- Hiermee kan de gebruiker zijn wachtwoord wijzigen in hetzelfde dialoogvenster wanneer het wachtwoord is verlopen (met extra velden voor het oude wachtwoord en het nieuwe wachtwoord).
- Hiermee u de branding van de tenant of de toepassing (afbeeldingen) die wordt beheerd door de Azure AD-tenantbeheerder / toepassingseigenaar.
- Hiermee kunnen de gebruikers toestemming geven om de toepassing toegang te geven tot bronnen / scopes in hun naam net na de verificatie.

### <a name="embedded-vs-system-web-ui"></a>Ingesloten vs Systeemweb-gebruikersinterface

MSAL.NET is een multi-framework bibliotheek en heeft framework-specifieke code om een browser te hosten in een UI-besturingselement (bijvoorbeeld op .Net Classic maakt gebruik van WinForms, op Xamarin maakt gebruik van native mobiele besturingselementen, enz.). Dit besturingselement `embedded` wordt webgebruikersinterface genoemd. Als alternatief is MSAL.NET ook in staat om de start van het systeem OS browser.

Over het algemeen wordt aanbevolen dat u het standaardplatform gebruikt, en dit is meestal de systeembrowser. De systeembrowser is beter in het onthouden van de gebruikers die al eerder zijn ingelogd. Als u dit gedrag wilt wijzigen, gebruikt u`WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>In één oogopslag

| Framework        | Ingesloten | Systeem | Standaard |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Ja | Ja^ | Ingesloten |
| .NET Core     | Nee | Ja^ | Systeem |
| .NET Standard | Nee | Ja^ | Systeem |
| UWP | Ja | Nee | Ingesloten |
| Xamarin.Android | Ja | Ja  | Systeem |
| Xamarin.iOS | Ja | Ja  | Systeem |
| Xamarin.Mac Xamarin.Mac| Ja | Nee | Ingesloten |

^ Vereisthttp://localhost" " redirect URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systeemwebbrowser op Xamarin.iOS, Xamarin.Android

Standaard ondersteunt MSAL.NET de systeemwebbrowser op Xamarin.iOS, Xamarin.Android en .NET Core. Voor alle platforms die ui bieden (dat wil zeggen niet .NET Core), wordt een dialoogvenster weergegeven door de bibliotheek die een webbrowserbesturingselement insluit. MSAL.NET maakt ook gebruik van een ingesloten webweergave voor het .NET Desktop en WAB voor het UWP-platform. Echter, het maakt gebruik van standaard het **systeem webbrowser** voor Xamarin iOS en Xamarin Android-toepassingen. Op iOS kiest het zelfs de webweergave die u wilt gebruiken, afhankelijk van de versie van het besturingssysteem (iOS12, iOS11 en eerder).

Met behulp van het systeem browser heeft het aanzienlijke voordeel van het delen van de SSO staat met andere toepassingen en met webapplicaties zonder dat een makelaar (Company portal / Authenticator). De systeembrowser werd standaard gebruikt in MSAL.NET voor de Xamarin iOS- en Xamarin Android-platforms, omdat op deze platforms de systeemwebbrowser het hele scherm bezet en de gebruikerservaring beter is. De webweergave van het systeem is niet te onderscheiden van een dialoogvenster. Op iOS moet de gebruiker echter toestemming geven voor de browser om de toepassing terug te bellen, wat vervelend kan zijn.

## <a name="system-browser-experience-on-net-core"></a>Systeembrowserervaring op .NET Core

Op .NET Core start MSAL.NET de systeembrowser als een apart proces. MSAL.NET heeft geen controle over deze browser, maar zodra de gebruiker klaar is met authenticatie, wordt de webpagina omgeleid op een zodanige wijze dat MSAL.NET de Uri kan onderscheppen.

U ook apps die voor .NET Classic zijn geschreven configureren om deze browser te gebruiken door op te geven

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET kan niet detecteren of de gebruiker wegnavigeert of gewoon sluit de browser. Apps die deze techniek gebruiken, worden `CancellationToken`aangemoedigd om een time-out te definiëren (via). We raden een time-out van ten minste een paar minuten aan om rekening te houden met gevallen waarin de gebruiker wordt gevraagd om het wachtwoord te wijzigen of multi-factor-authenticatie uit te voeren.

### <a name="how-to-use-the-default-os-browser"></a>De standaardbesturingssysteembrowser gebruiken

MSAL.NET moet luisteren `http://localhost:port` en de code onderscheppen die AAD verzendt wanneer de gebruiker klaar is met authenticeren (Zie [Autorisatiecode](v2-oauth2-auth-code-flow.md) voor meer informatie)

Ga als u de systeembrowser inschakelen:

1. Configureren `http://localhost` tijdens app-registratie als omleidingsuri (momenteel niet ondersteund door B2C)
2. Wanneer u uw PublicClientApplication maakt, geeft u deze omleidingsuri op:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Als u `http://localhost`configureert, vindt MSAL.NET intern een willekeurige open poort en gebruikt deze.

### <a name="linux-and-mac"></a>Linux en MAC

Op Linux opent MSAL.NET de standaard-OS-browser met de xdg-open tool. Als u problemen wilt oplossen, voert u het gereedschap uit vanaf een terminal,`xdg-open "https://www.bing.com"`  
Op Mac wordt de browser geopend door een beroep te doen op`open <url>`

### <a name="customizing-the-experience"></a>De ervaring aanpassen

> [!NOTE]
> Maatwerk is beschikbaar in MSAL.NET 4.1.0 of hoger.

MSAL.NET kan reageren met een HTTP-bericht wanneer een token wordt ontvangen of in geval van een fout. U een HTML-bericht weergeven of doorverwijzen naar een url van uw keuze:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Een specifieke browser openen (Experimenteel)

U de manier aanpassen waarop MSAL.NET de browser opent. Bijvoorbeeld in plaats van het gebruik van welke browser is de standaard, u dwingen het openen van een specifieke browser:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP maakt geen gebruik van de System Webview

Voor desktoptoepassingen leidt het starten van een System Webview echter tot een subpar-gebruikerservaring, omdat de gebruiker de browser ziet, waar mogelijk al andere tabbladen zijn geopend. En wanneer verificatie is gebeurd, krijgen de gebruikers een pagina waarin hen wordt gevraagd dit venster te sluiten. Als de gebruiker niet oplet, kan hij het hele proces sluiten (inclusief andere tabbladen die niets met de verificatie te maken hebben). Als u gebruik maakt van de systeembrowser op het bureaublad, moeten er ook lokale poorten worden geopend en moet er worden geluisterd, waarvoor mogelijk geavanceerde machtigingen voor de toepassing nodig zijn. U, als ontwikkelaar, gebruiker of beheerder, terughoudend zijn over deze vereiste.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Ingesloten webweergaven inschakelen op iOS en Android

U ook ingesloten webweergaven inschakelen in Xamarin.iOS- en Xamarin.Android-apps. Vanaf MSAL.NET 2.0.0-preview ondersteunt MSAL.NET ook met de **ingesloten** webview-optie. Voor ADAL.NET is ingesloten webview de enige ondersteuning.

Als ontwikkelaar die MSAL.NET xamarin target, u ervoor kiezen om ingesloten webweergaven of systeembrowsers te gebruiken. Dit is uw keuze, afhankelijk van de gebruikerservaring en beveiligingsproblemen die u wilt targeten.

Op dit moment ondersteunt MSAL.NET nog geen Android- en iOS-brokers. Daarom als u eenmalige aanmelding (SSO) moet bieden, is de systeembrowser mogelijk nog steeds een betere optie. Ondersteuning van makelaars met de ingebouwde webbrowser is op de MSAL.NET achterstand.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Verschillen tussen ingesloten webview en systeembrowser
Er zijn enkele visuele verschillen tussen embedded webview en systeembrowser in MSAL.NET.

**Interactieve aanmelding met MSAL.NET met de ingesloten webweergave:**

![ingesloten](media/msal-net-web-browsers/embedded-webview.png)

**Interactieve aanmelding met MSAL.NET met de systeembrowser:**

![Systeembrowser](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opties voor ontwikkelaars

Als ontwikkelaar die MSAL.NET gebruikt, hebt u verschillende opties voor het weergeven van het interactieve dialoogvenster van STS:

- **Systeembrowser.** De systeembrowser is standaard ingesteld in de bibliotheek. Als u Android gebruikt, leest u [systeembrowsers](msal-net-system-browser-android-considerations.md) voor specifieke informatie over welke browsers worden ondersteund voor verificatie. Wanneer u de systeembrowser in Android gebruikt, raden we het apparaat aan een browser te hebben die aangepaste tabbladen van Chrome ondersteunt.  Anders kan verificatie mislukken.
- **Ingesloten webview.** Als u alleen ingesloten webweergave `AcquireTokenInteractively` in MSAL.NET `WithUseEmbeddedWebView()` wilt gebruiken, bevat de parametersbouwer een methode.

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Kiezen tussen ingesloten webbrowser of systeembrowser op Xamarin.iOS

In uw iOS-app `AppDelegate.cs` u `ParentWindow` de `null`naar. Het wordt niet gebruikt in iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Kiezen tussen ingesloten webbrowser of systeembrowser op Xamarin.Android

In uw Android-app `MainActivity.cs` u de bovenliggende activiteit instellen, zodat het verificatieresultaat wordt teruggezet:

```csharp
 App.ParentWindow = this;
```

Dan in `MainPage.xaml.cs`de :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Het detecteren van de aanwezigheid van aangepaste tabbladen op Xamarin.Android

Als u de webbrowser van het systeem wilt gebruiken om SSO in te schakelen met de apps die in de browser worden uitgevoerd, maar `IsSystemWebViewAvailable()` zich `IPublicClientApplication`zorgen maakt over de gebruikerservaring voor Android-apparaten die geen browser met aangepaste tabondersteuning hebben, hebt u de mogelijkheid om te beslissen door de methode in te roepen. Deze methode `true` retourneert als De `false` PackageManager aangepaste tabbladen detecteert en als ze niet worden gedetecteerd op het apparaat.

Op basis van de waarde die door deze methode wordt geretourneerd en uw vereisten, u een beslissing nemen:

- U een aangepast foutbericht naar de gebruiker retourneren. Bijvoorbeeld: "Installeer Chrome om door te gaan met authenticatie" -OR-
- U terugvallen naar de ingesloten webview-optie en de gebruikersinterface starten als een ingesloten webview.

De onderstaande code toont de ingesloten webview-optie:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>.NET Core ondersteunt geen interactieve verificatie met een ingesloten browser

Voor .NET Core is het interactief verkrijgen van tokens alleen beschikbaar via de systeemwebbrowser, niet met ingesloten webweergaven. Inderdaad, .NET Core biedt nog geen ui.
Als u de surfervaring met de webbrowser van het systeem wilt aanpassen, u de [IWithCustomUI-interface](scenario-desktop-acquire-token.md#withcustomwebui) implementeren en zelfs uw eigen browser bieden.

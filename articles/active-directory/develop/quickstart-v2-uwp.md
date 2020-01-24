---
title: Micro soft Identity platform Windows UWP Quick Start | Azure
description: Meer informatie over hoe een Universeel Windows-platform-toepassing (XAML) een toegangs token kan krijgen en een API kan aanroepen die wordt beveiligd door het micro soft Identity platform-eind punt.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 12/12/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started, languages:UWP
ms.openlocfilehash: 5ebc1e6df85a521ef7a03bfce8e062fc0fbf734b
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76703283"
---
# <a name="quickstart-call-the-microsoft-graph-api-from-a-universal-windows-platform-uwp-application"></a>Quickstart: De Microsoft Graph-API aanroepen vanuit een UWP-toepassing (Universeel Windows-platform)

Deze Quick Start bevat een code voorbeeld dat laat zien hoe een Universeel Windows-platform-toepassing (UWP) zich kan aanmelden bij gebruikers met persoonlijke accounts of werk-en school accounts, een toegangs token kan ophalen en de Microsoft Graph-API kunt aanroepen.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-uwp/uwp-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De snelstart-app registreren en downloaden
> [!div renderon="docs" class="sxs-lookup"]
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens de voorbeeldcode](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/UwpQuickstartPage/sourceType/docs) .
> 1. Voer een naam in voor de toepassing en klik op **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld
> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app toe te voegen aan de oplossing:
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://aka.ms/MobileAppReg) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die zichtbaar is voor gebruikers van de app. Bijvoorbeeld: `UWP-App-calling-MsGraph`.
>      - Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com**.
>      - Selecteer **Registreren** om de toepassing te maken.
> 1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
> 1. Controleer in de sectie **omleidings-uri's** | **voorgestelde omleidings-uri's voor open bare clients (Mobile, Desktop)** **https://login.microsoftonline.com/common/oauth2/nativeclient** .
> 1. Selecteer **Opslaan**.

> [!div renderon="portal" class="sxs-lookup"]
> #### <a name="step-1-configure-your-application"></a>Stap 1: Uw toepassing configureren
> U moet een omleidings-URI toevoegen als **https://login.microsoftonline.com/common/oauth2/nativeclient** als u het code voorbeeld wilt gebruiken.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-uwp/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-visual-studio-project"></a>Stap 2: Uw Visual Studio-project downloaden

 - [Download het Visual Studio-project](https://github.com/Azure-Samples/active-directory-dotnet-native-uwp-v2/archive/msal3x.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Stap 3: Uw Visual Studio-project configureren

1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld **C:\Azure-Samples**.
1. Open het project in Visual Studio. U wordt mogelijk gevraagd om een UWP-SDK te installeren. Accepteer in dat geval.
1. Bewerk **MainPage.xaml.cs** en vervang de waarden van het veld `ClientId`:

    ```csharp
    private const string ClientId = "Enter_the_Application_Id_here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Deze Quick Start ondersteunt Enter_the_Supported_Account_Info_Here.    

> [!div renderon="docs"]
> Waar:
> - `Enter_the_Application_Id_here`: de toepassings-id voor de toepassing die u hebt geregistreerd.
>
> > [!TIP]
> > Als u de waarde van de *toepassings-id*wilt weten, gaat u naar de sectie **overzicht** in de portal

#### <a name="step-4-run-your-application"></a>Stap 4: uw toepassing uitvoeren

Als u de Snelstartgids op uw Windows-computer wilt proberen:

1. Kies op de werk balk van Visual Studio het juiste platform (waarschijnlijk **x64** of **x86**, geen arm).
   > Controleren of het doel apparaat wordt gewijzigd van het *apparaat* naar de *lokale computer*
1. Selecteer fouten opsporen | **Starten zonder fout opsporing**

## <a name="more-information"></a>Meer informatie

Deze sectie biedt meer informatie over de quickstart.

### <a name="msalnet"></a>MSAL.NET

MSAL ([micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) is de bibliotheek die wordt gebruikt om gebruikers aan te melden en beveiligings tokens aan te vragen. De beveiligings tokens worden gebruikt om toegang te krijgen tot een API die wordt beveiligd door het micro soft-identiteits platform voor ontwikkel aars. U kunt MSAL installeren door de volgende opdracht uit te voeren in *Package Manager Console* van Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```csharp
using Microsoft.Identity.Client;
```

MSAL wordt vervolgens geïnitialiseerd met de volgende code:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApp = PublicClientApplicationBuilder.Create(ClientId)
                                                    .Build();
```

> |Waar: ||
> |---------|---------|
> | `ClientId` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |

### <a name="requesting-tokens"></a>Tokens aanvragen

MSAL heeft twee methoden voor het verkrijgen van tokens in een UWP-app: `AcquireTokenInteractive` en `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

Sommige situaties vereisen dat gebruikers geforceerd met het micro soft Identity platform-eind punt werken via een pop-upvenster om hun referenties te valideren of om toestemming te geven. Voorbeelden zijn:

- De eerste keer dat gebruikers zich aanmelden bij de toepassing
- Wanneer gebruikers mogelijk hun referenties opnieuw moeten opgeven omdat het wachtwoord is verlopen
- Wanneer uw toepassing toegang tot een bron vraagt, die de gebruiker moet toestemming geven
- Wanneer tweeledige verificatie is vereist

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(scopes)
                      .ExecuteAsync();
```

> |Waar:||
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd, bijvoorbeeld `{ "user.read" }` voor Microsoft Graph of `{ "api://<Application ID>/access_as_user" }` voor aangepaste web-API's. |

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

Gebruik de methode `AcquireTokenSilent` om tokens te verkrijgen voor toegang tot beveiligde resources na de eerste `AcquireTokenInteractive` methode. U wilt de gebruiker niet verplichten om hun referenties te valideren telkens wanneer deze nodig zijn om toegang te krijgen tot een resource. De meeste tijd voor het ophalen van tokens en verlengingen zonder tussen komst van de gebruiker

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Waar: ||
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd, bijvoorbeeld `{ "user.read" }` voor Microsoft Graph of `{ "api://<Application ID>/access_as_user" }` voor aangepaste web-API's |
> | `firstAccount` | Hiermee geeft u het eerste gebruikers account in de cache op (MSAL ondersteunt meerdere gebruikers in één app) |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de zelfstudie voor Windows-bureaublad voor een volledige stapsgewijze handleiding voor het bouwen van toepassingen en nieuwe functies, waaronder een volledige uitleg van deze quickstart.

> [!div class="nextstepaction"]
> [UWP - Zelfstudie voor het aanroepen van Graph API](tutorial-v2-windows-uwp.md)

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

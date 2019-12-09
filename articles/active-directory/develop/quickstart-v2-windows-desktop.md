---
title: Micro soft Identity platform Windows Desktop Quick Start | Microsoft Docs
description: Meer informatie over hoe een Windows Desktop .NET-toepassing (XAML) een toegangs token kan krijgen en een API kan aanroepen die wordt beveiligd door een micro soft Identity platform-eind punt
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38ebf817d80fb1afdd3642f648d8e881b2e9d7de
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920443"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-windows-desktop-app"></a>Quickstart: Een token verkrijgen en Microsoft Graph API aanroepen vanuit een Windows-bureaublad-app

In deze quickstart leert u hoe u een .NET-toepassing (WPF) voor Windows-bureaublad schrijft waarmee privé-, werk- en schoolaccounts kunnen worden aangemeld, een toegangstoken kan worden opgehaald en de Microsoft Graph API kan worden aangeroepen.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-windows-desktop/windesktop-intro.svg)

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden
> U hebt twee opties voor het starten van de snelstarttoepassing:
> * [Express] [Optie 1: registreer de toepassing en laat deze automatisch configureren. Download vervolgens de voorbeeldcode](#option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample)
> * [Handmatig] [Optie 2: registreer de toepassing en configureer handmatig de toepassing en het codevoorbeeld](#option-2-register-and-manually-configure-your-application-and-code-sample)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar de nieuwe [Azure Portal-app-registraties](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: de toepassing en voorbeeldcode registreren en handmatig configureren
>
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://aka.ms/MobileAppReg) .
> 1. Selecteer **nieuwe registratie**.
>      - Voer in de sectie **Naam** een beschrijvende toepassingsnaam. Deze wordt zichtbaar voor gebruikers van de app. Bijvoorbeeld: `Win-App-calling-MsGraph`.
>      - Selecteer in de sectie **Ondersteunde accounttypen** de optie **Accounts in alle organisatiemappen en persoonlijke Microsoft-accounts (bijvoorbeeld Skype, Xbox, Outlook.com**.
>      - Selecteer **Registreren** om de toepassing te maken.
> 1. Selecteer in de lijst met pagina’s voor de app de optie **Verificatie**.
> 1. Vouw de sectie **bureau blad + apparaten** uit.  (Als **Desktop + apparaten** niet zichtbaar zijn, klikt u eerst op de bovenste banner om de preview-verificatie-ervaring weer te geven)
> 1. Selecteer onder de sectie **omleidings-URI** de optie **URI toevoegen**.  Typ **urn: IETF: WG: OAuth: 2.0: OOB**.
> 1. Selecteer **Opslaan**.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
> Voor een juiste werking van het codevoorbeeld uit deze quickstart moet u een antwoord-URL toevoegen als **urn:ietf:wg:oauth:2.0:oob**.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Deze wijziging voor mij maken]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-windows-desktop/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-visual-studio-project"></a>Stap 2: Uw Visual Studio-project downloaden

[Down load het Visual Studio-project](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/archive/msal3x.zip) ([project weer geven op github](https://github.com/Azure-Samples/active-directory-dotnet-desktop-msgraph-v2/))

#### <a name="step-3-configure-your-visual-studio-project"></a>Stap 3: Uw Visual Studio-project configureren

1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld **C:\Azure-Samples**.
1. Open het project in Visual Studio.
1. Bewerk **App.Xaml.cs** en vervang de waarden van de velden `ClientId` en `Tenant` door de volgende code:

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    private static string Tenant = "Enter_the_Tenant_Info_Here";
    ```
> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > Deze Quick Start ondersteunt Enter_the_Supported_Account_Info_Here.

> [!div renderon="docs"]
> Waar:
> - `Enter_the_Application_Id_here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
> - `Enter_the_Tenant_Info_Here`: is ingesteld op een van de volgende opties:
>   - Als uw toepassing **Alleen accounts in deze organisatiemap** ondersteunt, vervang deze waarde dan door de **Tenant-id** of **Tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>   - Als uw toepassing **Accounts in elke organisatiemap** ondersteunt, vervang deze waarde dan door `organizations`
>   - Als uw toepassing **Accounts in elke organisatiemap en persoonlijke Microsoft-accounts** ondersteunt, vervang deze waarde dan door `common`
>
> > [!TIP]
> > Om de waarden van **Toepassings-id (client-id)** , **Map-id (tenant-id)** en **Ondersteunde accounttypen** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal.

## <a name="more-information"></a>Meer informatie

### <a name="msalnet"></a>MSAL.NET

MSAL ([micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door micro soft Identity platform. U kunt MSAL installeren door de volgende opdracht uit te voeren in **Package Manager Console** van Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client -IncludePrerelease
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```csharp
using Microsoft.Identity.Client;
```

Vervolgens initialiseert u MSAL met de volgende code:

```csharp
public static IPublicClientApplication PublicClientApp;
PublicClientApplicationBuilder.Create(ClientId)
                .WithAuthority(AzureCloudInstance.AzurePublic, Tenant)
                .Build();
```

> |Waar: ||
> |---------|---------|
> | `ClientId` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |

### <a name="requesting-tokens"></a>Tokens aanvragen

MSAL biedt twee methoden om tokens te verkrijgen: `AcquireTokenInteractive` en `AcquireTokenSilent`.

#### <a name="get-a-user-token-interactively"></a>Een gebruikerstoken interactief ophalen

In sommige situaties is het afdwingen van gebruikers om te communiceren met het micro soft Identity platform-eind punt via een pop-upvenster om hun referenties te valideren of om toestemming te geven. Voorbeelden zijn:

- De eerste keer dat gebruikers zich aanmelden bij de toepassing
- Wanneer gebruikers mogelijk hun referenties opnieuw moeten opgeven omdat het wachtwoord is verlopen
- Wanneer via de toepassing toegang wordt aangevraagd tot een resource waarvoor de gebruiker toestemming moet geven
- Wanneer tweeledige verificatie is vereist

```csharp
authResult = await App.PublicClientApp.AcquireTokenInteractive(_scopes)
                                      .ExecuteAsync();
```

> |Waar:||
> |---------|---------|
> | `_scopes` | Bevat de bereiken die worden aangevraagd, bijvoorbeeld `{ "user.read" }` voor Microsoft Graph of `{ "api://<Application ID>/access_as_user" }` voor aangepaste web-API's. |

#### <a name="get-a-user-token-silently"></a>Een gebruikerstoken op de achtergrond ophalen

U wilt niet vereisen dat de gebruiker telkens wanneer deze toegang nodig heeft tot een resource zijn/haar referenties moet valideren. In de meeste gevallen wilt u tokens ophalen en verlengen zonder tussenkomst van de gebruiker. U kunt de methode `AcquireTokenSilent` voor het verkrijgen van tokens gebruiken voor toegang tot beveiligde resources na de eerste methode `AcquireTokenInteractive`:

```csharp
var accounts = await App.PublicClientApp.GetAccountsAsync();
var firstAccount = accounts.FirstOrDefault();
authResult = await App.PublicClientApp.AcquireTokenSilent(scopes, firstAccount)
                                      .ExecuteAsync();
```

> |Waar: ||
> |---------|---------|
> | `scopes` | Bevat de bereiken die worden aangevraagd, bijvoorbeeld `{ "user.read" }` voor Microsoft Graph of `{ "api://<Application ID>/access_as_user" }` voor aangepaste web-API's. |
> | `firstAccount` | Geeft de eerste gebruiker in de cache op (MSAL biedt ondersteuning voor meerdere gebruikers in één app). |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Volg de zelfstudie voor Windows-bureaublad voor een volledige stapsgewijze handleiding voor het bouwen van toepassingen en nieuwe functies, waaronder een volledige uitleg van deze quickstart.

> [!div class="nextstepaction"]
> [Zelfstudie voor het aanroepen van Graph API](https://docs.microsoft.com/azure/active-directory/develop/guidedsetups/active-directory-windesktop)

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

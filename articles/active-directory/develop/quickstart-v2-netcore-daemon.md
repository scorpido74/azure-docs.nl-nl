---
title: Microsoft Graph voor het aanroepen van token & ophalen met console-app-identiteit | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het verkrijgen van een token en het aanroepen van een beveiligde Microsoft Graph-API van een .NET core-app
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.workload: identity
ms.date: 07/16/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: edcfe4d7dff5d84ceaf07a8aafd5f9b485308bd5
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74920664"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-using-console-apps-identity"></a>Snelstartgids: een token verkrijgen en Microsoft Graph-API aanroepen met behulp van de identiteit van de console-app

In deze quickstart leert u hoe u een .NET Core-toepassing schrijft die een toegangstoken kan ophalen met behulp van de eigen identiteit van de app en hoe u vervolgens de Microsoft Graph API aanroept om een [lijst met gebruikers](https://docs.microsoft.com/graph/api/user-list) in de map weer te geven. Dit scenario is nuttig in situaties waar een headless taak zonder toezicht of een Windows-service moet worden uitgevoerd met een toepassings-id in plaats van de identiteit van een gebruiker.

![Toont hoe de voor beeld-app die door deze Quick start is gegenereerd, werkt](media/quickstart-v2-netcore-daemon/netcore-daemon-intro.svg)

## <a name="prerequisites"></a>Vereisten

Voor deze Snelstartgids is [.net Core 2,2](https://www.microsoft.com/net/download/dotnet-core/2.2)vereist.

> [!div renderon="docs"]
> ## <a name="register-and-download-your-quickstart-app"></a>De quickstart-app registreren en downloaden

> [!div renderon="docs" class="sxs-lookup"]
>
> U hebt twee opties om uw Quick Start-toepassing te starten: Express (optie 1 hieronder) en hand matig (optie 2)
>
> ### <a name="option-1-register-and-auto-configure-your-app-and-then-download-your-code-sample"></a>Optie 1: de app registreren en automatisch configureren, en vervolgens de voorbeeldcode downloaden
>
> 1. Ga naar het deel venster nieuwe [Azure Portal-app-registraties](https://portal.azure.com/?Microsoft_AAD_RegisteredApps=true#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/DotNetCoreDaemonQuickstartPage/sourceType/docs) .
> 1. Voer een naam in voor de toepassing en selecteer **Registreren**.
> 1. Volg de instructies om de nieuwe toepassing met slechts één klik te downloaden en automatisch te configureren.
>
> ### <a name="option-2-register-and-manually-configure-your-application-and-code-sample"></a>Optie 2: de toepassing en voorbeeldcode registreren en handmatig configureren

> [!div renderon="docs"]
> #### <a name="step-1-register-your-application"></a>Stap 1: Uw toepassing registreren
> Volg deze stappen om de toepassing te registreren en de registratiegegevens van de app handmatig toe te voegen aan uw oplossing:
>
> 1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
> 1. Als u via uw account toegang tot meer dan één tenant hebt, selecteert u uw account in de rechterbovenhoek en stelt u uw portalsessie in op de gewenste Azure Active Directory-tenant.
> 1. Navigeer naar de pagina micro soft-identiteits platform voor ontwikkel aars [app-registraties](https://go.microsoft.com/fwlink/?linkid=2083908) .
> 1. Selecteer **nieuwe registratie**.
> 1. Wanneer de pagina **een toepassing registreren** wordt weer gegeven, voert u de registratie gegevens van uw toepassing in. 
> 1. Voer in de sectie **Naam** een beschrijvende toepassingsnaam in die wordt weergegeven voor gebruikers van de app, zoals `Daemon-console`. Selecteer vervolgens **Registreren** om de toepassing te maken.
> 1. Na het registreren opent u het menu **Certificaten en geheimen**.
> 1. Onder **Clientgeheimen** selecteert u **+ Nieuw clientgeheim**. Geef het clientgeheim een naam en selecteer **Toevoegen**. Kopieer het geheim naar een veilige locatie. U hebt dit nodig voor gebruik in uw code.
> 1. Open nu het menu **API-machtigingen**, selecteer de knop **+ Een geheim toevoegen** en selecteer **Microsoft Graph**.
> 1. Selecteer **Toepassingsmachtigingen**.
> 1. Selecteer onder het knooppunt **Gebruiker** de optie **User.Read.All** en selecteer vervolgens **Machtigingen toevoegen**

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-your-quickstart-app"></a>Uw snelstart-app downloaden en configureren
> 
> #### <a name="step-1-configure-your-application-in-azure-portal"></a>Stap 1: Uw toepassing configureren in Azure Portal
> Om ervoor te zorgen dat het codevoorbeeld voor deze quickstart werkt, moet u een clientgeheim maken en de toepassingstoestemming **User.Read.All** van Graph API toevoegen.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Breng deze wijzigingen voor mij aan]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Al geconfigureerd](media/quickstart-v2-netcore-daemon/green-check.png) Uw toepassing is al geconfigureerd met deze kenmerken.

#### <a name="step-2-download-your-visual-studio-project"></a>Stap 2: Uw Visual Studio-project downloaden

[Download het Visual Studio-project](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/archive/master.zip)

#### <a name="step-3-configure-your-visual-studio-project"></a>Stap 3: Uw Visual Studio-project configureren

1. Pak het zip-bestand uit in een lokale map dicht bij de hoofdmap van de schijf, bijvoorbeeld **C:\Azure-Samples**.
1. Open de oplossing in Visual Studio- **1-call-MSGraph\daemon-console.SLN** (optioneel).
1. Bewerk **appsettings.json** en vervang de waarden van de velden `ClientId`, `Tenant` en `ClientSecret` door het volgende:

    ```json
    "Tenant": "Enter_the_Tenant_Id_Here",
    "ClientId": "Enter_the_Application_Id_Here",
    "ClientSecret": "Enter_the_Client_Secret_Here"
    ```
    > > [!div renderon="portal" id="certandsecretspage" class="sxs-lookup"]
    > > [Een nieuw clientgeheim genereren]()
    
    > [!div class="sxs-lookup" renderon="portal"]
    > > [!NOTE]
    > > Deze Quick Start ondersteunt Enter_the_Supported_Account_Info_Here.
    
    > [!div renderon="docs"]
    >> Waar:
    >> * `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.
    >> * `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **Tenantnaam** (bijvoorbeeld contoso.microsoft.com)
    >> * `Enter_the_Client_Secret_Here`: vervang deze waarde door het clientgeheim dat is gemaakt in stap 1.

    > [!div renderon="docs"]
    > > [!TIP]
    > > Om de waarden van **Toepassings-id (client-id)** en **Map-id (tenant-id)** te achterhalen, gaat u naar de **Overzichtspagina** van de app in de Azure-portal. Voor het genereren van een nieuwe sleutel gaat u naar de pagina **Certificaten en geheimen**.
    
#### <a name="step-4-admin-consent"></a>Stap 4: Toestemming van de beheerder

Als u de toepassing op dit moment probeert uit te voeren, ontvangt u *HTTP 403-verboden* fout: `Insufficient privileges to complete the operation`. Dit gebeurt omdat een *machtiging alleen* voor de beheerder vereist, wat betekent dat een globale beheerder van uw directory toestemming moet geven voor uw toepassing. Selecteer een van de onderstaande opties, afhankelijk van uw rol:

##### <a name="global-tenant-administrator"></a>Globale tenantbeheerder

> [!div renderon="docs"]
> Als u een globale tenantbeheerder bent, gaat u naar de pagina **API-machtigingen** in de registratie van toepassingen (preview) in de Azure-portal en selecteer **Beheerder toestemming verlenen voor {tenantnaam}** (waarbij {tenantnaam} de naam van uw map is).

> [!div renderon="portal" class="sxs-lookup"]
> Als u een globale beheerder bent, gaat u naar de pagina **API-machtigingen** en selecteert u **Beheerder toestemming verlenen voor Enter_the_Tenant_Name_Here**
> > [!div id="apipermissionspage"]
> > [Ga naar de pagina API-machtigingen]()

##### <a name="standard-user"></a>Standaardgebruiker

Als u een standaard gebruiker bent van uw Tenant, moet u een globale beheerder vragen om toestemming van de beheerder voor uw toepassing te verlenen. Daarvoor verstrekt u de volgende URL aan uw beheerder:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Waar:
>> * `Enter_the_Tenant_Id_Here`: vervang deze waarde door de **Tenant-id** of **Tenantnaam** (bijvoorbeeld contoso.microsoft.com)
>> * `Enter_the_Application_Id_Here`: is de **toepassings-id (client-id)** voor de toepassing die u hebt geregistreerd.

> [!NOTE]
> Mogelijk ziet u de fout *'AADSTS50011: Er is geen antwoordadres geregistreerd voor de toepassing'* na het verlenen van toestemming voor de app met behulp van de voorgaande URL. Dit gebeurt omdat deze toepassing en de URL geen omleidings-URI hebben. Negeer deze fout.

#### <a name="step-5-run-the-application"></a>Stap 5: De toepassing uitvoeren

Als u Visual Studio gebruikt, drukt u op **F5** om de toepassing uit te voeren. anders voert u de toepassing uit via de opdracht prompt of de-console:

```console
cd {ProjectFolder}\daemon-console\1-Call-Graph
dotnet run
```

> Waar:
> * *{Projectmap}*  is de map waar u het zip-bestand hebt uitgepakt. Voorbeeld **C:\Azure-Samples\active-directory-dotnetcore-daemon-v2**

U ziet een lijst met gebruikers in uw Azure AD-directory als resultaat.

> [!IMPORTANT]
> Deze quickstarttoepassing gebruikt een clientgeheim om zichzelf te identificeren als vertrouwelijke client. Omdat het clientgeheim als platte tekst aan uw projectbestanden wordt toegevoegd, wordt u om veiligheidsredenen aangeraden een certificaat te gebruiken in plaats van een clientgeheim voordat u de toepassing als productietoepassing beschouwt. Zie voor meer informatie over het gebruik van een certificaat [deze instructies](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2/#variation-daemon-application-using-client-credentials-with-certificates) in de GitHub-opslagplaats voor dit voorbeeld.

## <a name="more-information"></a>Meer informatie

### <a name="msalnet"></a>MSAL.NET

MSAL ([micro soft. Identity. client](https://www.nuget.org/packages/Microsoft.Identity.Client)) is de bibliotheek die wordt gebruikt voor het aanmelden van gebruikers en het aanvragen van tokens die worden gebruikt voor toegang tot een API die wordt beveiligd door micro soft Identity platform. Zoals beschreven, verzoekt deze Quick Start tokens met behulp van de eigen identiteit van de toepassing in plaats van gedelegeerde machtigingen. De verificatiestroom die in dit voorbeeld wordt gebruikt, staat bekend als de *[oauth-stroom voor clientreferenties](v2-oauth2-client-creds-grant-flow.md)* . Zie [dit artikel](https://aka.ms/msal-net-client-credentials)voor meer informatie over het gebruik van MSAL.net met client referenties flow.

 U kunt MSAL.NET installeren door de volgende opdracht uit te voeren in **Package Manager Console** van Visual Studio:

```powershell
Install-Package Microsoft.Identity.Client
```

Als u Visual Studio niet gebruikt, kunt u ook de volgende opdracht uitvoeren om MSAL aan uw project toe te voegen:

```console
dotnet add package Microsoft.Identity.Client
```

### <a name="msal-initialization"></a>MSAL initialiseren

U kunt de verwijzing voor MSAL toevoegen door de volgende code toe te voegen:

```csharp
using Microsoft.Identity.Client;
```

Vervolgens initialiseert u MSAL met de volgende code:

```csharp
IConfidentialClientApplication app;
app = ConfidentialClientApplicationBuilder.Create(config.ClientId)
                                          .WithClientSecret(config.ClientSecret)
                                          .WithAuthority(new Uri(config.Authority))
                                          .Build();
);
```

> | Waar: ||
> |---------|---------|
> | `config.ClientSecret` | Het clientgeheim is dat voor de toepassing in Azure-portal wordt gemaakt. |
> | `config.ClientId` | Is de **Toepassings-id (client-id)** voor de toepassing die is geregistreerd in de Azure-portal. U vindt deze waarde op de pagina **Overzicht** in de Azure-portal. |
> | `config.Authority`    | (Optioneel) Het STS-eindpunt voor de gebruiker voor verificatie. Dat is meestal <https://login.microsoftonline.com/{tenant}> voor de openbare cloud, waarbij {tenant} de naam van uw tenant of uw tenant-id is.|

Zie de [naslagdocumentatie voor `ConfidentialClientApplication`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.iconfidentialclientapplication?view=azure-dotnet) voor meer informatie

### <a name="requesting-tokens"></a>Tokens aanvragen

Als u een token wilt aanvragen met behulp van de identiteit van de app, gebruikt u de `AcquireTokenForClient`-methode:

```csharp
result = await app.AcquireTokenForClient(scopes)
                  .ExecuteAsync();
```

> |Waar:| |
> |---------|---------|
> | `scopes` | De aangevraagde bereiken bevat. Voor vertrouwelijke clients moet hiervoor de indeling worden gebruikt die vergelijkbaar is met `{Application ID URI}/.default` om aan te geven dat de aangevraagde bereiken dezelfde zijn die statisch zijn gedefinieerd in het app-object dat is ingesteld in de Azure-portal (voor Microsoft Graph verwijst `{Application ID URI}` naar `https://graph.microsoft.com`). Voor aangepaste Web-API's wordt `{Application ID URI}` gedefinieerd in de sectie **Een API beschikbaar maken** in de registratie van toepassingen (preview) van de Azure-portal. |

Zie de [naslagdocumentatie voor `AcquireTokenForClient`](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplication.acquiretokenforclient?view=azure-dotnet) voor meer informatie

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over daemon-toepassingen raadpleegt u de pagina scenario overloop

> [!div class="nextstepaction"]
> [Daemon-toepassing die web-Api's aanroept](scenario-daemon-overview.md)

Voor de zelf studie over de daemon-toepassing raadpleegt u:

> [!div class="nextstepaction"]
> [Zelf studie voor daemon .NET Core-Console](https://github.com/Azure-Samples/active-directory-dotnetcore-daemon-v2)

Meer informatie over machtigingen en toestemming:

> [!div class="nextstepaction"]
> [Machtigingen en toestemming](v2-permissions-and-consent.md)

Zie de Oauth 2.0-clientreferentiestroom voor meer informatie over de auth-stroom voor dit scenario:

> [!div class="nextstepaction"]
> [Oauth-clientreferentiestroom](v2-oauth2-client-creds-grant-flow.md)

Help ons het micro soft Identity-platform te verbeteren. Vertel ons wat u denkt door een korte enquête met twee vragen te volt ooien.

> [!div class="nextstepaction"]
> [Micro soft Identity platform-enquête](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRyKrNDMV_xBIiPGgSvnbQZdUQjFIUUFGUE1SMEVFTkdaVU5YT0EyOEtJVi4u)

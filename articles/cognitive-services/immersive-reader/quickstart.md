---
title: 'Snelstart: maak een web-app die de immersive reader met C lanceert #'
titleSuffix: Azure Cognitive Services
description: In deze quickstart bouwt u vanaf nul een web-app en voegt u de Immersive Reader API-functionaliteit toe.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 01/14/2020
ms.author: metan
ms.openlocfilehash: 8dd8459922caa9f765d59bc28fbf050b86834b46
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845242"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Snelstart: maak een web-app die de Immersive Reader (C#)

De [Immersive Reader](https://www.onenote.com/learningtools) is een inclusief ontworpen tool die beproefde technieken implementeert om het begrijpen van lezen te verbeteren.

In deze quickstart bouw je een web-app vanaf nul en integreer je de Immersive Reader met behulp van de Immersive Reader SDK. Een volledig werkend voorbeeld van deze quickstart is [hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)beschikbaar.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Een Immersive Reader-bron die is geconfigureerd voor Azure Active Directory-verificatie. Volg [deze instructies](./how-to-create-immersive-reader.md) om je op te stellen. U hebt een aantal waarden nodig die hier zijn gemaakt bij het configureren van de projecteigenschappen van het voorbeeld. Sla de uitvoer van uw sessie op in een tekstbestand voor toekomstige verwijzingen.

## <a name="create-a-web-app-project"></a>Een web-app-project maken

Maak een nieuw project in Visual Studio met de sjabloon ASP.NET Core Web Application met ingebouwde Model-View-Controller en ASP.NET Core 2.1. Noem het project "QuickstartSampleWebApp".

![Nieuw project](./media/quickstart-csharp/1-createproject.png)

![Nieuw project configureren](./media/quickstart-csharp/2-configureproject.png)

![Nieuwe ASP.NET Core-webtoepassing](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Verificatie instellen

### <a name="configure-authentication-values"></a>Verificatiewaarden configureren

Klik met de rechtermuisknop op het project in de _Solution Explorer_ en kies **Gebruikersgeheimen beheren.** Dit opent een bestand genaamd _secrets.json_. Dit bestand wordt niet ingecheckt in bronbeheer. Lees [hier](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows)meer . Vervang de inhoud van _secrets.json_ door het volgende, het leveren van de waarden die worden gegeven toen u uw Immersive Reader-bron maakte.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Het Microsoft.IdentityModel.Clients.ActiveDirectory NuGet-pakket toevoegen

In de volgende code worden objecten uit het **Microsoft.IdentityModel.Clients.ActiveDirectory** NuGet-pakket gebruikt, dus u moet een verwijzing naar dat pakket toevoegen aan uw project.

Open de NuGet Package Manager Console vanuit **Extra-> NuGet Package Manager -> Package Manager Console** en voer de volgende opdracht uit:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.2.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>De controller bijwerken om het token te verkrijgen 

Open _Controllers\HomeController.cs_en voeg de volgende code toe na het _gebruik van_ instructies boven aan het bestand.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Nu configureren we de controller om de Azure AD-waarden te verkrijgen van _secrets.json_. Aan de bovenkant van de ```public class HomeController : Controller {``` _HomeController-klasse,_ na , voeg de volgende code.

```csharp
private readonly string TenantId;     // Azure subscription TenantId
private readonly string ClientId;     // Azure AD ApplicationId
private readonly string ClientSecret; // Azure AD Application Service Principal password
private readonly string Subdomain;    // Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')

public HomeController(Microsoft.Extensions.Configuration.IConfiguration configuration)
{
    TenantId = configuration["TenantId"];
    ClientId = configuration["ClientId"];
    ClientSecret = configuration["ClientSecret"];
    Subdomain = configuration["Subdomain"];

    if (string.IsNullOrWhiteSpace(TenantId))
    {
        throw new ArgumentNullException("TenantId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientId))
    {
        throw new ArgumentNullException("ClientId is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(ClientSecret))
    {
        throw new ArgumentNullException("ClientSecret is null! Did you add that info to secrets.json?");
    }

    if (string.IsNullOrWhiteSpace(Subdomain))
    {
        throw new ArgumentNullException("Subdomain is null! Did you add that info to secrets.json?");
    }
}

/// <summary>
/// Get an Azure AD authentication token
/// </summary>
private async Task<string> GetTokenAsync()
{
    string authority = $"https://login.windows.net/{TenantId}";
    const string resource = "https://cognitiveservices.azure.com/";

    AuthenticationContext authContext = new AuthenticationContext(authority);
    ClientCredential clientCredential = new ClientCredential(ClientId, ClientSecret);

    AuthenticationResult authResult = await authContext.AcquireTokenAsync(resource, clientCredential);

    return authResult.AccessToken;
}

[HttpGet]
public async Task<JsonResult> GetTokenAndSubdomain()
{
    try
    {
        string tokenResult = await GetTokenAsync();

        return new JsonResult(new { token = tokenResult, subdomain = Subdomain });
    }
    catch (Exception e)
    {
        string message = "Unable to acquire Azure AD token. Check the debugger for more information.";
        Debug.WriteLine(message, e);
        return new JsonResult(new { error = message });
    }
}
```

## <a name="add-sample-content"></a>Voorbeeldinhoud toevoegen
Open eerst _Weergaven\Gedeeld\Layout.cshtml_. Voeg vóór de regel ```</head>```de volgende code toe:

```html
@RenderSection("Styles", required: false)
```

Nu voegen we voorbeeldinhoud toe aan deze web-app. Open _Weergaven\Home\Index.cshtml_ en vervang alle automatisch gegenereerde code door dit voorbeeld:

```html
@{
    ViewData["Title"] = "Immersive Reader C# Quickstart";
}

@section Styles {
    <style type="text/css">
        .immersive-reader-button {
            background-color: white;
            margin-top: 5px;
            border: 1px solid black;
            float: right;
        }
    </style>
}

<div class="container">
    <button class="immersive-reader-button" data-button-style="iconAndText" data-locale="en"></button>

    <h1 id="ir-title">About Immersive Reader</h1>
    <div id="ir-content" lang="en-us">
        <p>
            Immersive Reader is a tool that implements proven techniques to improve reading comprehension for emerging readers, language learners, and people with learning differences.
            The Immersive Reader is designed to make reading more accessible for everyone. The Immersive Reader
            <ul>
                <li>
                    Shows content in a minimal reading view
                </li>
                <li>
                    Displays pictures of commonly used words
                </li>
                <li>
                    Highlights nouns, verbs, adjectives, and adverbs
                </li>
                <li>
                    Reads your content out loud to you
                </li>
                <li>
                    Translates your content into another language
                </li>
                <li>
                    Breaks down words into syllables
                </li>
            </ul>
        </p>
        <h3>
            The Immersive Reader is available in many languages.
        </h3>
        <p lang="es-es">
            El Lector inmersivo está disponible en varios idiomas.
        </p>
        <p lang="zh-cn">
            沉浸式阅读器支持许多语言
        </p>
        <p lang="de-de">
            Der plastische Reader ist in vielen Sprachen verfügbar.
        </p>
        <p lang="ar-eg" dir="rtl" style="text-align:right">
            يتوفر \"القارئ الشامل\" في العديد من اللغات.
        </p>
    </div>
</div>
```

Merk op dat alle tekst een **lang** attribuut heeft, dat de talen van de tekst beschrijft. Met dit kenmerk biedt de immersive reader relevante taal- en grammaticafuncties.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>JavaScript toevoegen om de lancering van de meeslepende lezer te verwerken

De Immersive Reader-bibliotheek biedt functionaliteit zoals het lanceren van de Immersive Reader en het renderen van immersive reader-knoppen. Lees [hier](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference)meer .

Voeg onder aan _Weergaven\Home\Index.cshtml_de volgende code toe:

```html
@section Scripts
{
    <script src="https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.1.0.0.js"></script>
    <script>
        function getTokenAndSubdomainAsync() {
            return new Promise(function (resolve, reject) {
                $.ajax({
                    url: "@Url.Action("GetTokenAndSubdomain", "Home")",
                    type: "GET",
                    success: function (data) {
                        if (data.error) {
                            reject(data.error);
                        } else {
                            resolve(data);
                        }
                    },
                    error: function (err) {
                        reject(err);
                    }
                });
            });
        }
    
        $(".immersive-reader-button").click(function () {
            handleLaunchImmersiveReader();
        });
    
        function handleLaunchImmersiveReader() {
            getTokenAndSubdomainAsync()
                .then(function (response) {
                    const token = response["token"];
                    const subdomain = response["subdomain"];
    
                    // Learn more about chunk usage and supported MIME types https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#chunk
                    const data = {
                        title: $("#ir-title").text(),
                        chunks: [{
                            content: $("#ir-content").html(),
                            mimeType: "text/html"
                        }]
                    };
    
                    // Learn more about options https://docs.microsoft.com/en-us/azure/cognitive-services/immersive-reader/reference#options
                    const options = {
                        "onExit": exitCallback,
                        "uiZIndex": 2000
                    };
    
                    ImmersiveReader.launchAsync(token, subdomain, data, options)
                        .catch(function (error) {
                            alert("Error in launching the Immersive Reader. Check the console.");
                            console.log(error);
                        });
                })
                .catch(function (error) {
                    alert("Error in getting the Immersive Reader token and subdomain. Check the console.");
                    console.log(error);
                });
        }
    
        function exitCallback() {
            console.log("This is the callback function. It is executed when the Immersive Reader closes.");
        }
    </script>
}
```

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Selecteer **foutopsporing > foutopsporing van de foutopsporing**van start in de menubalk of druk op **F5** om de toepassing te starten.

In uw browser ziet u het:

![Voorbeeldapp](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>Start de meeslepende lezer

Wanneer u op de knop 'Meeslepende lezer' klikt, ziet u de meeslepende lezer die is gestart met de inhoud op de pagina.

![Insluitende lezer](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Node.js snel start](./quickstart-nodejs.md) om te zien wat je nog meer doen met de Immersive Reader SDK met behulp van Node.js
* Bekijk de [Python-zelfstudie](./tutorial-python.md) om te zien wat u nog meer doen met de Immersive Reader SDK met Python
* Bekijk de [iOS-zelfstudie](./tutorial-ios-picture-immersive-reader.md) om te zien wat je nog meer doen met de Immersive Reader SDK met Swift
* Ontdek de [Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) en de [Immersive Reader SDK Reference](./reference.md)
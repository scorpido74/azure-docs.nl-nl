---
title: 'Snelstartgids: een web-app maken waarmee de insluitende lezer wordt gestartC#'
titleSuffix: Azure Cognitive Services
description: In deze Quick Start bouwt u een volledig nieuwe web-app en voegt u de functionaliteit van de insluitende Reader API toe.
services: cognitive-services
author: metanMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: quickstart
ms.date: 06/20/2019
ms.author: metan
ms.openlocfilehash: dd3e1e6de886b24a2912fe6a12b47a852d8956d0
ms.sourcegitcommit: 49e14e0d19a18b75fd83de6c16ccee2594592355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75945359"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Snelstartgids: een web-app maken waarmee de insluitende lezerC#wordt gestart ()

De [insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulp programma waarmee bewezen technieken worden geïmplementeerd om de Lees vaardigheid te verbeteren.

In deze Snelstartgids bouwt u een volledig nieuwe web-app en integreert u de insluitende lezer met behulp van de insluitende lezer-SDK. [Hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)vindt u een volledig werkend voor beeld van deze Quick Start.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2019](https://visualstudio.microsoft.com/downloads)
* Een resource voor insluitende lezer die is geconfigureerd voor Azure Active Directory authenticatie. Volg [deze instructies om de](./how-to-create-immersive-reader.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de voorbeeld project eigenschappen. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.

## <a name="create-a-web-app-project"></a>Een web-app-project maken

Maak een nieuw project in Visual Studio met behulp van de sjabloon webtoepassing ASP.NET Core met ingebouwde model-view-controller en ASP.NET Core 2,1. Geef het project de naam ' QuickstartSampleWebApp '.

![Nieuw project](./media/quickstart-csharp/1-createproject.png)

![Nieuw project configureren](./media/quickstart-csharp/2-configureproject.png)

![Nieuwe ASP.NET Core-webtoepassing](./media/quickstart-csharp/3-createmvc.png)

## <a name="set-up-authentication"></a>Verificatie instellen

### <a name="configure-authentication-values"></a>Verificatie waarden configureren

Klik met de rechter muisknop op het project in de _Solution Explorer_ en kies **gebruikers geheimen beheren**. Hiermee opent u een bestand met de naam _Secrets. json_. Dit bestand is niet ingecheckt in broncode beheer. Klik [hier](https://docs.microsoft.com/aspnet/core/security/app-secrets?view=aspnetcore-3.1&tabs=windows) voor meer informatie. Vervang de inhoud van _geheimen. json_ door het volgende, waarbij u de waarden opgeeft die zijn opgegeven bij het maken van uw insluitende lezer-resource.

```json
{
  "TenantId": "YOUR_TENANT_ID",
  "ClientId": "YOUR_CLIENT_ID",
  "ClientSecret": "YOUR_CLIENT_SECRET",
  "Subdomain": "YOUR_SUBDOMAIN"
}
```

### <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Het NuGet-pakket micro soft. Identity model. clients. ActiveDirectory toevoegen

De volgende code maakt gebruik van objecten uit het pakket **micro soft. Identity model. clients. ActiveDirectory** NuGet, zodat u een verwijzing naar dat pakket in uw project moet toevoegen.

Open de NuGet Package Manager-console vanuit **extra-> NuGet package manager-> Package Manager-console** en voer de volgende opdracht uit:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

### <a name="update-the-controller-to-acquire-the-token"></a>De controller bijwerken om het token te verkrijgen 

Open _Controllers\HomeController.cs_en voeg de volgende code toe na de instructies _using_ boven aan het bestand.

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

Nu gaan we de controller configureren voor het verkrijgen van de Azure AD-waarden van _geheimen. json_. Voeg de volgende code toe aan het begin van de klasse _HomeController_ , na het ```public class HomeController : Controller {```.

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

## <a name="add-sample-content"></a>Voorbeeld inhoud toevoegen
Open eerst _Views\Shared\Layout.cshtml_. Voeg vóór de regel ```</head>```de volgende code toe:

```html
@RenderSection("Styles", required: false)
```

Nu gaan we voorbeeld inhoud toevoegen aan deze web-app. Open _Views\Home\Index.cshtml_ en vervang alle automatisch gegenereerde code door dit voor beeld:

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

U ziet dat alle tekst een kenmerk **lang** heeft, waarin de talen van de tekst worden beschreven. Dit kenmerk helpt de insluitende lezer de relevante taal-en grammatica functies te bieden.

## <a name="add-javascript-to-handle-launching-the-immersive-reader"></a>Java script toevoegen aan de greep voor het starten van de insluitende lezer

De bibliotheek voor insluitende lezers biedt functionaliteit, zoals het starten van de insluitende lezer en het renderen van insluitende lezers knoppen. Klik [hier](https://docs.microsoft.com/azure/cognitive-services/immersive-reader/reference) voor meer informatie.

Voeg aan de onderkant van _Views\Home\Index.cshtml_de volgende code toe:

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

Selecteer in de menu balk **fout opsporing > fout opsporing starten**of druk op **F5** om de toepassing te starten.

In uw browser ziet u het volgende:

![Voorbeeldapp](./media/quickstart-csharp/4-buildapp.png)

## <a name="launch-the-immersive-reader"></a>De insluitende lezer starten

Wanneer u op de knop ' insluitende lezer ' klikt, ziet u dat de insluitende lezer wordt gestart met de inhoud op de pagina.

![Insluitende lezer](./media/quickstart-csharp/5-viewimmersivereader.png)

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [Snelstartgids voor node. js](./quickstart-nodejs.md) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van node. js
* Bekijk de [python-zelf studie](./tutorial-python.md) om te zien wat u nog meer kunt doen met de insluitende Reader SDK met behulp van python
* Bekijk de [IOS-zelf studie](./tutorial-ios-picture-immersive-reader.md) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van SWIFT
* Verken de [insluitende lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de [insluitende lezer SDK](./reference.md)
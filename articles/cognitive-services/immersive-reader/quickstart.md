---
title: 'Quickstart: Een web-app maken waarmee de insluitende lezer wordt gestartC#'
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
ms.openlocfilehash: ed49f834cb7cd4f649d84aea9e549e212771eead
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69899355"
---
# <a name="quickstart-create-a-web-app-that-launches-the-immersive-reader-c"></a>Quickstart: Een web-app maken waarmee de insluitende lezerC#wordt gestart ()

De [insluitende lezer](https://www.onenote.com/learningtools) is een inclusief ontworpen hulp programma waarmee bewezen technieken worden geïmplementeerd om de Lees vaardigheid te verbeteren.

In deze Snelstartgids bouwt u een volledig nieuwe web-app en integreert u de insluitende lezer met behulp van de insluitende lezer-SDK. [Hier](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/quickstart-csharp)vindt u een volledig werkend voor beeld van deze Quick Start.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* [Visual Studio 2017](https://visualstudio.microsoft.com/downloads)
* Een insluitende lezer-resource die is geconfigureerd voor Azure Active Directory-verificatie (Azure AD). Volg [deze instructies om de](./azure-active-directory-authentication.md) instellingen op te halen. U hebt enkele van de waarden nodig die u hier hebt gemaakt bij het configureren van de voorbeeld project eigenschappen. Sla de uitvoer van uw sessie op in een tekst bestand voor toekomstig naslag doeleinden.

## <a name="create-a-web-app-project"></a>Een web-app-project maken

Maak een nieuw project in Visual Studio met behulp van de sjabloon webtoepassing ASP.NET Core met ingebouwde model-view-controller.

![Nieuw project](./media/vswebapp.png)

![Nieuwe ASP.NET Core-webtoepassing](./media/vsmvc.png)

## <a name="acquire-an-azure-ad-authentication-token"></a>Een Azure AD-verificatie token verkrijgen

U hebt een aantal waarden nodig van de hierboven genoemde Azure AD-verificatie configuratie voor dit onderdeel. Ga terug naar het tekst bestand dat u van deze sessie hebt opgeslagen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

Klik met de rechter muisknop op het project in de _Solution Explorer_ en kies **gebruikers geheimen beheren**. Hiermee opent u een bestand met de naam _Secrets. json_. Vervang de inhoud van het bestand door het volgende, waarbij u de waarden van de aangepaste eigenschappen hierboven opgeeft.

```json
{
  "TenantId": YOUR_TENANT_ID,
  "ClientId": YOUR_CLIENT_ID,
  "ClientSecret": YOUR_CLIENT_SECRET,
  "Subdomain": YOUR_SUBDOMAIN
}
```

Open _Controllers\HomeController.cs_en vervang het bestand door de volgende code.

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.IdentityModel.Clients.ActiveDirectory;

namespace QuickstartSampleWebApp.Controllers
{
    public class HomeController : Controller
    {
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

        public IActionResult Index()
        {
            return View();
        }

        [Route("subdomain")]
        public string GetSubdomain()
        {
            return Subdomain;
        }

        [Route("token")]
        public async Task<string> GetToken()
        {
            return await GetTokenAsync();
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
    }
}
```

## <a name="add-the-microsoftidentitymodelclientsactivedirectory-nuget-package"></a>Het NuGet-pakket micro soft. Identity model. clients. ActiveDirectory toevoegen

De bovenstaande code maakt gebruik van objecten uit het pakket **micro soft. Identity model. clients. ActiveDirectory** NuGet, zodat u een verwijzing naar dat pakket in uw project moet toevoegen.

Open de NuGet Package Manager-console vanuit **extra-> NuGet package manager-> Package Manager-console** en typ het volgende:

```powershell
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 5.1.0
```

## <a name="add-sample-content"></a>Voorbeeld inhoud toevoegen

Nu voegen we een aantal voorbeeld inhoud toe aan deze web-app. Open _Views\Home\Index.cshtml_ en vervang de automatisch gegenereerde code door dit voor beeld:

```html
<h1 id='title'>Geography</h1>
<span id='content'>
    <p>The study of Earth's landforms is called physical geography. Landforms can be mountains and valleys. They can also be glaciers, lakes or rivers. Landforms are sometimes called physical features. It is important for students to know about the physical geography of Earth. The seasons, the atmosphere and all the natural processes of Earth affect where people are able to live. Geography is one of a combination of factors that people use to decide where they want to live.</p>
</span>

<div class='immersive-reader-button' data-button-style='iconAndText' onclick='launchImmersiveReader()'></div>

@section scripts {
    <script type='text/javascript' src='https://contentstorage.onenote.office.net/onenoteltir/immersivereadersdk/immersive-reader-sdk.0.0.2.js'></script>
    <script type='text/javascript' src='https://code.jquery.com/jquery-3.3.1.min.js'></script>
    <script type='text/javascript'>
    function getImmersiveReaderTokenAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/token',
                type: 'GET',
                success: token => {
                    resolve(token);
                }
            });
        });
    }

    function getSubdomainAsync() {
        return new Promise((resolve) => {
            $.ajax({
                url: '/subdomain',
                type: 'GET',
                success: subdomain => {
                    resolve(subdomain);
                }
            });
        });
    }

    async function launchImmersiveReader() {
        const content = {
            title: document.getElementById('title').innerText,
            chunks: [ {
                content: document.getElementById('content').innerText,
                lang: 'en'
            } ]
        };

        const token = await getImmersiveReaderTokenAsync();
        var subdomain = await getSubdomainAsync();

        ImmersiveReader.launchAsync(token, subdomain, content, { uiZIndex: 1000000 });
    }
    </script>
}
```

## <a name="build-and-run-the-app"></a>De app bouwen en uitvoeren

Selecteer in de menu balk **fout opsporing > fout opsporing starten**of druk op **F5** om de toepassing te starten.

In uw browser ziet u het volgende:

![Voor beeld-app](./media/quickstart-result.png)

Wanneer u op de knop ' insluitende lezer ' klikt, ziet u dat de insluitende lezer wordt gestart met de inhoud op de pagina.

![Insluitende lezer](./media/quickstart-immersive-reader.png)

## <a name="next-steps"></a>Volgende stappen

* Bekijk de [node. js-zelf studie](./tutorial-nodejs.md) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van node. js
* Bekijk de [python-zelf studie](./tutorial-python.md) om te zien wat u nog meer kunt doen met de insluitende Reader SDK met behulp van python
* Bekijk de [IOS-zelf studie](./tutorial-ios-picture-immersive-reader.md) om te zien wat u nog meer kunt doen met de insluitende lezer-SDK met behulp van SWIFT
* Verken de insluitende [Lezer SDK](https://github.com/microsoft/immersive-reader-sdk) en de referentie voor de insluitende [Lezer SDK](./reference.md)

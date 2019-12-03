---
title: Verbinding maken met de Bing Nieuws zoeken-API met behulp van Connected Services in Visual Studio en C#
titleSuffix: Azure Cognitive Services
description: Met behulp van de Bing Nieuws zoeken-API kunt u apps en services de mogelijkheid bieden om gebruik te maken van de kracht van een zoekprogramma voor het internet zonder advertenties.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: tutorial
ms.date: 11/29/2019
ms.author: aahi
ms.openlocfilehash: 9cac13f8fb42959e79cafedcc7057d1cb7c2f7d5
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689385"
---
# <a name="tutorial-connect-to-bing-news-search-api-with-connected-services-in-visual-studio-and-c"></a>Zelf studie: verbinding maken met Bing Nieuws zoeken-API met verbonden services in Visual Studio enC#

Met behulp van de Bing Nieuws zoeken-API kunt u apps en services de mogelijkheid bieden om gebruik te maken van de kracht van een zoekprogramma voor het internet zonder advertenties. Bing News Search is een van de zoekservices die beschikbaar is met Cognitive Services.

Dit artikel bevat informatie voor het gebruik van de functie Connected Services van Visual Studio voor de Bing Nieuws zoeken-API. De mogelijkheid is beschikbaar in Visual Studio 2017 15.7 of hoger, met de extensie Cognitive Services geïnstalleerd.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/pricing/free-trial/).
- Visual Studio 2019, waarbij de werk belasting voor Web Development is geïnstalleerd. [Download nu](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs).

[!INCLUDE [vs-install-cognitive-services-vsix](../../../includes/vs-install-cognitive-services-vsix.md)]

## <a name="add-support-to-your-project-for-bing-news-search-api"></a>Ondersteuning voor de Bing Nieuws zoeken-API toevoegen aan uw project

1. Maak een nieuw ASP.NET Core-web-project met de naam MyWebApplication. Gebruik de projectsjabloon **Web Application (Model-View-Controller)** , met alle standaardinstellingen. Het is belangrijk om het project de naam MyWebApplication te geven zodat de naamruimte overeenkomt wanneer u code hebt gekopieerd in het project. 

1. In **Solution Explorer** kiest u **Connected Service** > **Toevoegen**.
   De Connected Service-pagina wordt weergegeven met services die u aan uw project toevoegen kunt.

   ![Schermafbeelding van het menu-item Add > Connected Service](../media/vs-common/Connected-Service-Menu.PNG)

1. Kies **Bring Intelligent Search To Your Apps** in het menu met beschikbare services.

   ![Schermafbeelding van lijst met verbonden services](./media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-0.PNG)

   Als u bent aangemeld bij Visual Studio en een Azure-abonnement hebt dat is gekoppeld aan uw account, wordt een pagina weergegeven met een vervolgkeuzelijst met uw abonnementen. Selecteer het abonnement dat u wilt gebruiken en kies vervolgens een naam voor de Bing Nieuws zoeken-API. U kunt ook **Edit** kiezen om de automatisch gegenereerde naam te wijzigen.

   ![Schermafbeelding van de velden voor het abonnement en de naam](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-1.PNG)

1. Kies de resourcegroep (Resource Group) en de prijscategorie (Pricing tier).

   ![Schermafbeelding van de resourcegroep en prijscategorie-velden](media/vs-bing-news-search-connected-service/Cog-Search-Connected-Service-2.PNG)

   Als u meer informatie wilt over de prijscategorieën, selecteert u **Review pricing**.

1. Kies **Toevoegen** om ondersteuning van Connected Service toe te voegen.
   Visual Studio past uw project aan om de NuGet-pakketten toe te voegen, evenals vermeldingen in het configuratiebestand en andere wijzigingen voor de ondersteuning van een verbinding met de Bing Nieuws zoeken-API. In de uitvoer ziet u een logboek van wat er gebeurt met uw project. Er verschijnt informatie die er ongeveer als volgt uitziet:

   ```output
   [5/4/2018 12:41:21.084 PM] Adding Intelligent Search to the project.
   [5/4/2018 12:41:21.271 PM] Creating new Intelligent Search...
   [5/4/2018 12:41:24.128 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.ImageSearch' version 1.2.0...
   [5/4/2018 12:41:24.135 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.NewsSearch' version 1.2.0...
   [5/4/2018 12:41:24.154 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.WebSearch' version 1.2.0...
   [5/4/2018 12:41:24.168 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.CustomSearch' version 1.2.0...
   [5/4/2018 12:41:24.187 PM] Installing NuGet package 'Microsoft.Azure.CognitiveServices.Search.VideoSearch' version 1.2.0...
   [5/4/2018 12:42:07.287 PM] Retrieving keys...
   [5/4/2018 12:42:07.741 PM] Updating appsettings.json setting: 'ServiceKey' = 'c271412f3e4c4e1dacc7c4145fa0572a'
   [5/4/2018 12:42:07.745 PM] Updating appsettings.json setting: 'ServiceEndPoint' = 'https://api.cognitive.microsoft.com/bing/v7.0'
   [5/4/2018 12:42:07.749 PM] Updating appsettings.json setting: 'Name' = 'WebApplicationCore-Search_IntelligentSearch'
   [5/4/2018 12:42:10.217 PM] Successfully added Intelligent Search to the project.
   ```

   Het bestand appsettings.json bevat nu de volgende nieuwe instellingen:

   ```json
   "CognitiveServices": {
     "IntelligentSearch": {
       "ServiceKey": "<your service key>",
       "ServiceEndPoint": "https://api.cognitive.microsoft.com/bing/v7.0",
       "Name": "WebApplicationCore-Search_IntelligentSearch"
     }
   }
   ```
 
## <a name="use-the-bing-news-search-api-to-add-search-functionality-to-a-web-page"></a>Zoekfunctionaliteit toevoegen aan een webpagina met de Bing Nieuws zoeken-API

Nu u ondersteuning voor de Bing Nieuws zoeken-API hebt toegevoegd aan uw project, kunt u de API als volgt gebruiken om een slimme zoekfunctie toe te voegen aan een webpagina.

1. Voeg in het bestand *Startup.cs*, in de methode `ConfigureServices`, een aanroep naar `IServiceCollection.AddSingleton` toe. Hierdoor is het configuratieobject met de belangrijkste instellingen beschikbaar voor de code in uw project.
 
   ```csharp
       public void ConfigureServices(IServiceCollection services)
       {
           services.AddMvc();
           services.AddSingleton<IConfiguration>(Configuration);
       }
   ```


1. Voeg een nieuw klassebestand toe onder de map **Models**, met de naam *BingNewsModel.cs*. Als u het project een andere naam hebt gegeven, gebruikt u de naamruimte van uw eigen project in plaats van MyWebApplication. Vervang de inhoud door de volgende code:
 
    ```csharp
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch.Models;
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Threading.Tasks;
    
    namespace MyWebApplication.Models
    {
        public class BingNewsModel
        {
            public News SearchResult { get; set; } 
            public string SearchText { get; set; }
        }
    }
    ```

   Dit model wordt gebruikt voor het opslaan van de resultaten van een aanroep naar de Bing News Search-service.
 
1. Voeg in de map **Controllers** een nieuwe klassebestand toe met de naam *IntelligentSearchController.cs*. Vervang de inhoud door de volgende code:

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using MyWebApplication.Models;
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.Azure.CognitiveServices.Search.NewsSearch;
    using Microsoft.Extensions.Configuration;
    
    namespace MyWebApplication.Controllers
    {
        // A controller to handle News Search requests
        public class IntelligentSearchController : Controller
        {
            private IConfiguration configuration;
  
            // Set up the configuration that contains the keys
            // (from the appsettings.json file)
            // that you will use to access the service  
            public IntelligentSearchController(IConfiguration configuration)
            {
                this.configuration = configuration;
            }

            // Call the Bing News Search API and put the result in the model object.    
            public async Task<IActionResult> BingSearchResult(BingNewsModel model)
            {
                if (!string.IsNullOrWhiteSpace(model.SearchText))
                { 
                    INewsSearchAPI client = this.GetNewsSearchClient(new MyHandler());
                    model.SearchResult = await client.News.SearchAsync(model.SearchText);
                }
                return View(model);
            }
    
            // Forward requests to the Search endpoint to the BingSearchResult method
            [HttpPost("Search")]
            public IActionResult Search(BingNewsModel model)
            {
                return RedirectToAction("BingSearchResult", model);
            }

            // Get the search client object
            private INewsSearchAPI GetNewsSearchClient(DelegatingHandler handler)
            {
                string key =
                   configuration.GetSection("CognitiveServices")["IntelligentSearch:ServiceKey"];
    
                INewsSearchAPI client = new NewsSearchAPI(
                   new ApiKeyServiceClientCredentials(key), handlers: handler);
    
                return client;
            }
        }
    }
   ```

   In deze code definieert de constructor het configuratieobject dat uw sleutels bevat. De methode voor de `Search`-route is niet meer dan een omleiding naar de functie `BingSearchResult`. Hiermee wordt de methode `GetNewsSearchClient` aangeroepen om het clientobject `NewsSearchAPI` op te halen.  Het clientobject `NewsSearchAPI` bevat de methode `SearchAsync`, waarmee de werkelijke aanroep naar de service wordt gedaan en de resultaten worden geretourneerd naar het model `SearchResult` dat u net hebt gemaakt. 

1. Voeg de klasse `MyHandler` toe, waarna wordt verwezen in de bovenstaande code. Hiermee wordt de asynchrone aanroep van de zoekservice gedelegeerd naar de basisklasse, `DelegatingHandler`.

   ```csharp
    using System.Net.Http;
    using System.Threading.Tasks;
    using System.Threading;

    class MyHandler : DelegatingHandler
    {
        protected async override Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Call the inner handler.
            var response = await base.SendAsync(request, cancellationToken);
            
            return response;
        }
    }
   ```

1. Als u ondersteuning wilt toevoegen voor het versturen van zoekopdrachten en het weergeven van de resultaten, maakt u in de map **Views** een nieuwe map met de naam **IntelligentSearch**. Voeg in deze nieuwe map een weergave toe met de naam *BingSearchResult.cshtml*. Kopieer de volgende code:

    ```cshtml
    @using System
    @model MyWebApplication.Models.BingNewsModel
    
    @{
        ViewData["Title"] = "BingSearchResult";
    }
    
    <h2>Search News</h2>
    
    <div class="row">
        <section>
            <form asp-controller="IntelligentSearch" asp-action="Search" method="POST"
                  class="form-horizontal" enctype="multipart/form-data">
                <table width ="90%">
                    <tr>
                        <td>
                            <input type="text" name="SearchText" class="form-control" />
                        </td>
                        <td>
                            <button type="submit" class="btn btn-default">Search</button>
                        </td>
                    </tr>
                </table>
            </form>
        </section>
    </div>
    <h2>Search Result</h2=
    <table>
    @if (!string.IsNullOrEmpty(Model.SearchText)) {
        foreach (var item in Model.SearchResult.Value) {
        <tr>
            <td rowspan="2" width="90">
               <img src=@item?.Image?.Thumbnail?.ContentUrl width="80" height="80" />
            </td>
            <td><a href=@item.Url>@item.Name</a></td>
        </tr>   
        <tr>
            <td>@item.Description</td>
        </tr>
        <tr height="10">
            <td/><td/>
         </tr>
        } }
     </table>
    <div>
        <hr />
        <p>
            <a asp-controller="Home" asp-action="Index">Return to Index</a>
        </p>
    </div>
    ```

1. Start de webtoepassing lokaal, voer de URL in voor de pagina die u zojuist hebt gemaakt (/IntelligentSearch/BingSearchResult) en verstuur een zoekaanvraag met behulp van de knop Search.

   ![Schermafbeelding van resultaten van Bing Nieuws zoeken-API](media/vs-bing-news-search-connected-service/Cog-News-Search-Results.PNG)
           
## <a name="clean-up-resources"></a>Resources opschonen

Als u de resourcegroep niet meer nodig hebt, kunt u deze verwijderen. Hiermee verwijdert u de Cognitive Service en gerelateerde resources. De resourcegroep verwijderen via de portal:

1. Typ de naam van uw resourcegroep in het zoekvak bovenaan de portal. Selecteer de resourcegroep die u wilt verwijderen.
2. Selecteer **Resourcegroep verwijderen**.
3. Typ in het vak **Typ de naam van de resourcegroep** de naam van de resourcegroep en selecteer **Verwijderen**.

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Bing News Search?](index.yml) voor meer informatie over de Bing Nieuws zoeken-API.

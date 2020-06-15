---
title: Web-app-persoonlijker gebruiken
description: Een C# .NET-Web-App aanpassen met een Personaler-lus om de juiste inhoud te bieden aan een gebruiker op basis van acties (met functies) en context functies.
ms.topic: troubleshooting
ms.date: 06/10/2020
ms.author: diberry
ms.openlocfilehash: 9def69a1540e81b99723c16ad34ba522d1737c7f
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84713915"
---
# <a name="add-personalizer-to-a-net-web-app"></a>Persoonlijker toevoegen aan een .NET-Web-app

Een C# .NET-Web-App aanpassen met een Personaler-lus om de juiste inhoud te bieden aan een gebruiker op basis van acties (met functies) en context functies.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Persoonlijke sleutel en eind punt instellen
> * Functies verzamelen
> * Classificatie-en belonings-Api's aanroepen
> * Meest voorkomende actie weer geven, aangeduid als _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>De beste inhoud voor een web-app selecteren

Een web-app moet Personaler gebruiken als er een lijst met _acties_ (een type inhoud) op de webpagina moet worden aangepast aan één topitem (rewardActionId) om weer te geven. Voor beelden van actie lijsten zijn nieuws artikelen, knoppen plaatsen en woord keuzes voor product namen.

U verzendt de lijst met acties, samen met context functies, naar de aangepaste lus. Met persoonlijke instellingen selecteert u één beste actie, waarna uw web-app die actie weergeeft.

In deze zelf studie zijn de volgende typen voedings middelen:

* pasta
* ijs
* SAP
* salad
* popcorn
* koffie
* soep

Als u persoonlijker wilt weten over uw acties, kunt u beide __acties verzenden met functies_ en _context functies_ met elke rangorde API-aanvraag.

Een **functie** van het model is informatie over de actie of context die kan worden geaggregeerd (gegroepeerd) voor leden van de gebruikers database van uw web-app. Een functie _is niet_ afzonderlijk specifiek (zoals een gebruikers-id) of zeer specifiek (zoals een exacte tijd van de dag).

### <a name="actions-with-features"></a>Acties met functies

Elke actie (inhouds item) heeft functies waarmee u het voedings middel kunt onderscheiden.

De functies zijn niet geconfigureerd als onderdeel van de configuratie van de lus in de Azure Portal. In plaats daarvan worden ze verzonden als een JSON-object met elke Rank API-aanroep. Dit biedt flexibiliteit voor de acties en hun functies om de tijd te verg Roten, te wijzigen en te verkleinen, waardoor uw persoonlijke voor keuren trends kunnen volgen.

```csharp
 /// <summary>
  /// Creates personalizer actions feature list.
  /// </summary>
  /// <returns>List of actions for personalizer.</returns>
  private IList<RankableAction> GetActions()
  {
      IList<RankableAction> actions = new List<RankableAction>
      {
          new RankableAction
          {
              Id = "pasta",
              Features =
              new List<object>() { new { taste = "savory", spiceLevel = "medium" }, new { nutritionLevel = 5, cuisine = "italian" } }
          },

          new RankableAction
          {
              Id = "ice cream",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionalLevel = 2 } }
          },

          new RankableAction
          {
              Id = "juice",
              Features =
              new List<object>() { new { taste = "sweet", spiceLevel = "none" }, new { nutritionLevel = 5 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "salad",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "low" }, new { nutritionLevel = 8 } }
          },

          new RankableAction
          {
              Id = "popcorn",
              Features =
              new List<object>() { new { taste = "salty", spiceLevel = "none" }, new { nutritionLevel = 3 } }
          },

          new RankableAction
          {
              Id = "coffee",
              Features =
              new List<object>() { new { taste = "bitter", spiceLevel = "none" }, new { nutritionLevel = 3 }, new { drink = true } }
          },

          new RankableAction
          {
              Id = "soup",
              Features =
              new List<object>() { new { taste = "sour", spiceLevel = "high" }, new { nutritionLevel =  7} }
          }
      };

      return actions;
  }
```


## <a name="context-features"></a>Context functies

Context functies helpen persoonlijker de context van de acties te begrijpen. De context voor deze voorbeeld toepassing omvat:

* tijdstip van de dag-morgen, middag, avond, nacht
* voor keur van de gebruiker voor smaak-zouty, zoete, bitter, zure of Savory
* context gebruikers agent van de browser, geografische locatie, verwijzende site

```csharp
/// <summary>
/// Get users time of the day context.
/// </summary>
/// <returns>Time of day feature selected by the user.</returns>
private string GetUsersTimeOfDay()
{
    Random rnd = new Random();
    string[] timeOfDayFeatures = new string[] { "morning", "noon", "afternoon", "evening", "night", "midnight" };
    int timeIndex = rnd.Next(timeOfDayFeatures.Length);
    return timeOfDayFeatures[timeIndex];
}

/// <summary>
/// Gets user food preference.
/// </summary>
/// <returns>Food taste feature selected by the user.</returns>
private string GetUsersTastePreference()
{
    Random rnd = new Random();
    string[] tasteFeatures = new string[] { "salty", "bitter", "sour", "savory", "sweet" };
    int tasteIndex = rnd.Next(tasteFeatures.Length);
    return tasteFeatures[tasteIndex];
}
```

## <a name="how-does-the-web-app-use-personalizer"></a>Hoe wordt persoonlijker gebruikt de web-app?

De web-app gebruikt persoonlijke instellingen om de beste actie te selecteren in de lijst met voedings opties. Dit doet u door de volgende informatie te verzenden bij elke rangorde API-aanroep:
* **acties** met hun functies, zoals `taste` en`spiceLevel`
* **context** functies, zoals `time` de dag, de `taste` voor keur van de gebruiker en de gegevens van de gebruikers agent van de browser en de context functies
* **acties die moeten worden uitgesloten** , zoals SAP
* **eventid**voor elke aanroep naar Rank API.

## <a name="personalizer-model-features-in-a-web-app"></a>Model functies personaliseren in een web-app

Personaler vereist functies voor de acties (inhoud) en de huidige context (gebruiker en omgeving). Functies worden gebruikt om acties uit te lijnen op de huidige context in het model. Het model is een weer gave van de laatste kennis over acties, context en hun functies van Personaler, waarmee IT getrainde beslissingen kan nemen.

Het model, inclusief functies, wordt bijgewerkt volgens een schema op basis van de instelling van uw **model update frequentie** in de Azure Portal.

> [!CAUTION]
> Functies in deze toepassing zijn bedoeld om functies en onderdeel waarden te illustreren, maar niet noodzakelijkerwijs de beste functies die in een web-app kunnen worden gebruikt.

### <a name="plan-for-features-and-their-values"></a>Plannen voor functies en hun waarden

Functies moeten worden geselecteerd met dezelfde planning en hetzelfde ontwerp die u zou Toep assen op elk schema of model in uw technische architectuur. De functie waarden kunnen worden ingesteld met bedrijfs logica of systemen van derden. Onderdeel waarden moeten niet zo zeer specifiek zijn dat ze niet van toepassing zijn op een groep of een klasse van functies.

### <a name="generalize-feature-values"></a>Functie waarden generaliseren

#### <a name="generalize-into-categories"></a>Generaliseren in categorieën

Deze app maakt gebruik van `time` als een functie, maar groeps tijd in categorieën zoals `morning` , `afternoon` , en `evening` `night` . Dit is een voor beeld van het gebruik van de informatie over tijd, maar niet op een zeer specifieke manier, zoals `10:05:01 UTC+2` .

#### <a name="generalize-into-parts"></a>Generaliseren in onderdelen

Deze app maakt gebruik van de functies van de HTTP-aanvraag vanuit de browser. Dit begint met een zeer specifieke teken reeks met alle gegevens, bijvoorbeeld:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

De **HttpRequestFeatures** -klassebibliotheek generaliseert deze teken reeks in een **userAgentInfo** -object met afzonderlijke waarden. Waarden die te specifiek zijn, worden ingesteld op een lege teken reeks. Wanneer de context functies voor de aanvraag worden verzonden, heeft deze de volgende JSON-indeling:

```JSON
{
  "httpRequestFeatures": {
    "_synthetic": false,
    "OUserAgent": {
      "_ua": "",
      "_DeviceBrand": "",
      "_DeviceFamily": "Other",
      "_DeviceIsSpider": false,
      "_DeviceModel": "",
      "_OSFamily": "Windows",
      "_OSMajor": "10",
      "DeviceType": "Desktop"
    }
  }
}
```


## <a name="using-sample-web-app"></a>Voor beeld-web-app gebruiken

De voor beeld-web-app op basis van een browser (alle code wordt meegeleverd) vereist de volgende toepassingen die zijn geïnstalleerd om de app uit te voeren.

Installeer de volgende software:

* [.Net core 2,1](https://dotnet.microsoft.com/download/dotnet-core/2.1) -de back-end-voorbeeld server maakt gebruik van .net core
* [Node.js](https://nodejs.org/) -de client-front-end is afhankelijk van deze toepassing
* [Visual studio 2019](https://visualstudio.microsoft.com/vs/)of [.net core SLI](https://docs.microsoft.com/dotnet/core/tools/) : gebruik de ontwikkel omgeving van Visual studio 2019 of de .net core SLI om de app te bouwen en uit te voeren

### <a name="set-up-the-sample"></a>Het voor beeld instellen
1. Kloon de Azure Personaler-voor beelden opslag plaats.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Navigeer naar _samples/HttpRequestFeatures_ om de oplossing te openen `HttpRequestFeaturesExample.sln` .

    Indien aangevraagd, kan Visual Studio het .NET-pakket voor Personaler bijwerken.

### <a name="set-up-azure-personalizer-service"></a>Azure Personaler service instellen

1. [Maak een persoonlijke resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) in de Azure Portal.

1. Zoek in de Azure Portal de `Endpoint` en een `Key1` of `Key2` (werk) op het tabblad **sleutels en eind punten** . Dit zijn je `PersonalizerServiceEndpoint` en je `PersonalizerApiKey` .
1. Vul de `PersonalizerServiceEndpoint` in **appsettings.js**in.
1. Configureer de certificaat `PersonalizerApiKey` geheimen als een [app](https://docs.microsoft.com/aspnet/core/security/app-secrets) op een van de volgende manieren:

    * Als u de .NET Core SLI gebruikt, kunt u de opdracht gebruiken `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` .
    * Als u Visual Studio gebruikt, kunt u met de rechter muisknop op het project klikken en de menu optie **gebruikers geheimen beheren** selecteren om de sleutels van de persoonlijker te configureren. Als u dit doet, wordt in Visual Studio een `secrets.json` bestand geopend waarin u de sleutels als volgt kunt toevoegen:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Bouw en voer HttpRequestFeaturesExample uit met een van de volgende methoden:

* Visual Studio 2019: druk op **F5**
* .NET Core SLI: `dotnet build` then`dotnet run`

U kunt via een webbrowser een rang aanvraag en een belonings aanvraag verzenden en hun antwoorden zien, evenals de functies van de HTTP-aanvraag die zijn geëxtraheerd uit uw omgeving.

> [!div class="mx-imgBorder"]
> ![Bouw en voer het project HTTPRequestFeaturesExample uit. Er wordt een browser venster geopend waarin de toepassing met één pagina wordt weer gegeven.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>De Personaler-lus demonstreren

1. Selecteer de knop **nieuwe positie aanvragen genereren** om een nieuw JSON-object te maken voor de positie-API-aanroep. Hiermee worden de acties (met functies) en context functies gemaakt en worden de waarden weer gegeven, zodat u kunt zien hoe de JSON eruit ziet.

    Voor uw eigen toekomstige toepassing kunnen er acties en functies worden gegenereerd op de client, op de server, een combi natie van de twee of met aanroepen naar andere services.

1. Selecteer **positie aanvraag verzenden** om het JSON-object naar de server te verzenden. De server roept de Personaler Rank-API aan. De server ontvangt het antwoord en retourneert de bovenste geclassificeerde actie naar de client om weer te geven.

1. Stel de belonings waarde in en selecteer vervolgens de knop **belonings aanvraag verzenden** . Als u de belonings waarde niet wijzigt, verzendt de client toepassing altijd de waarde `1` naar personaler.

    > [!div class="mx-imgBorder"]
    > ![Bouw en voer het project HTTPRequestFeaturesExample uit. Er wordt een browser venster geopend waarin de toepassing met één pagina wordt weer gegeven.](./media/tutorial-web-app/reward-score-api-call.png)

    Voor uw eigen toepassing is het mogelijk dat er een belonings score wordt gegenereerd na het verzamelen van informatie van het gedrag van de gebruiker op de client, samen met de bedrijfs logica op de server.

## <a name="understand-the-sample-web-app"></a>Meer informatie over de voor beeld-web-app

De voor beeld-web-app heeft een **C# .net** -server, die het verzamelen van functies beheert en http-aanroepen naar uw personaler-eind punt verzendt en ontvangt.

De voor beeld-Web-App maakt gebruik van een **afdek front-end-client toepassing** voor het vastleggen van functies en het verwerken van gebruikers interface acties, zoals het klikken op knoppen en het verzenden van gegevens naar de .NET-Server.

In de volgende secties worden de onderdelen van de server en client uitgelegd die een ontwikkelaar nodig heeft om persoonlijker te gebruiken.

## <a name="rank-api-client-application-sends-context-to-server"></a>Rank API: client toepassing stuurt context naar server

De client toepassing verzamelt de _gebruikers agent_van de browser van de gebruiker.

> [!div class="mx-imgBorder"]
> ![Bouw en voer het project HTTPRequestFeaturesExample uit. Er wordt een browser venster geopend waarin de toepassing met één pagina wordt weer gegeven.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rank API: Server toepassing aanroepen persoonlijker

Dit is een typische .NET-Web-app met een client toepassing. veel van de code van de ketel plaat wordt voor u gegeven. Code die niet specifiek is voor Personaler, wordt verwijderd uit de volgende code fragmenten, zodat u zich kunt concentreren op de code van uw persoonlijke voor keur.

### <a name="create-personalizer-client"></a>Personaler-client maken

In de **Startup.cs**van de server worden de personaler-eind punt en-sleutel gebruikt om de personaler-client te maken. De client toepassing hoeft niet te communiceren met Personaler in deze app, in plaats van op de server te vertrouwen om deze SDK-aanroepen uit te voeren.

De .NET-opstart code van de webserver is:

```csharp
using Microsoft.Azure.CognitiveServices.Personalizer;
// ... other using statements removed for brevity

namespace HttpRequestFeaturesExample
{
    public class Startup
    {
        public Startup(IConfiguration configuration)
        {
            Configuration = configuration;
        }

        public IConfiguration Configuration { get; }

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            string personalizerApiKey = Configuration.GetSection("PersonalizerApiKey").Value;
            string personalizerEndpoint = Configuration.GetSection("PersonalizerConfiguration:ServiceEndpoint").Value;
            if (string.IsNullOrEmpty(personalizerEndpoint) || string.IsNullOrEmpty(personalizerApiKey))
            {
                throw new ArgumentException("Missing Azure Personalizer endpoint and/or api key.");
            }
            services.AddSingleton(client =>
            {
                return new PersonalizerClient(new ApiKeyServiceClientCredentials(personalizerApiKey))
                {
                    Endpoint = personalizerEndpoint
                };
            });

            services.AddMvc();
        }

        // ... code removed for brevity
    }
}
```

### <a name="select-best-action"></a>Selecteer de aanbevolen actie

In de **PersonalizerController.cs**van de server wordt de **GenerateRank** -Server-API samen vatting gegeven van de voor bereiding om de positie-API aan te roepen

* Nieuwe maken `eventId` voor de classificatie oproep
* De lijst met acties ophalen
* De lijst met functies van de gebruiker ophalen en context functies maken
* Eventueel uitgesloten acties instellen
* Aanroep van de classificatie-API, retour neren van resultaten naar client

```csharp
/// <summary>
/// Creates a RankRequest with user time of day, HTTP request features,
/// and taste as the context and several different foods as the actions
/// </summary>
/// <returns>RankRequest with user info</returns>
[HttpGet("GenerateRank")]
public RankRequest GenerateRank()
{
    string eventId = Guid.NewGuid().ToString();

    // Get the actions list to choose from personalizer with their features.
    IList<RankableAction> actions = GetActions();

    // Get context information from the user.
    HttpRequestFeatures httpRequestFeatures = GetHttpRequestFeaturesFromRequest(Request);
    string timeOfDayFeature = GetUsersTimeOfDay();
    string tasteFeature = GetUsersTastePreference();

    // Create current context from user specified data.
    IList<object> currentContext = new List<object>() {
            new { time = timeOfDayFeature },
            new { taste = tasteFeature },
            new { httpRequestFeatures }
    };

    // Exclude an action for personalizer ranking. This action will be held at its current position.
    IList<string> excludeActions = new List<string> { "juice" };

    // Rank the actions
    return new RankRequest(actions, currentContext, excludeActions, eventId);
}
```

De JSON die wordt verzonden naar Personaler, met beide acties (met functies) en de huidige context functies, ziet er als volgt uit:

```json
{
    "contextFeatures": [
        {
            "time": "morning"
        },
        {
            "taste": "savory"
        },
        {
            "httpRequestFeatures": {
                "_synthetic": false,
                "MRefer": {
                    "referer": "http://localhost:51840/"
                },
                "OUserAgent": {
                    "_ua": "",
                    "_DeviceBrand": "",
                    "_DeviceFamily": "Other",
                    "_DeviceIsSpider": false,
                    "_DeviceModel": "",
                    "_OSFamily": "Windows",
                    "_OSMajor": "10",
                    "DeviceType": "Desktop"
                }
            }
        }
    ],
    "actions": [
        {
            "id": "pasta",
            "features": [
                {
                    "taste": "savory",
                    "spiceLevel": "medium"
                },
                {
                    "nutritionLevel": 5,
                    "cuisine": "italian"
                }
            ]
        },
        {
            "id": "ice cream",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionalLevel": 2
                }
            ]
        },
        {
            "id": "juice",
            "features": [
                {
                    "taste": "sweet",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 5
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "salad",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "low"
                },
                {
                    "nutritionLevel": 8
                }
            ]
        },
        {
            "id": "popcorn",
            "features": [
                {
                    "taste": "salty",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                }
            ]
        },
        {
            "id": "coffee",
            "features": [
                {
                    "taste": "bitter",
                    "spiceLevel": "none"
                },
                {
                    "nutritionLevel": 3
                },
                {
                    "drink": true
                }
            ]
        },
        {
            "id": "soup",
            "features": [
                {
                    "taste": "sour",
                    "spiceLevel": "high"
                },
                {
                    "nutritionLevel": 7
                }
            ]
        }
    ],
    "excludedActions": [
        "juice"
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "deferActivation": null
}
```

### <a name="return-personalizer-rewardactionid-to-client"></a>Personaler rewardActionId retour neren aan client

De positie-API retourneert de geselecteerde best mogelijke actie **rewardActionId** naar de server.

De actie weer geven die is geretourneerd in **rewardActionId**.

```json
{
    "ranking": [
        {
            "id": "popcorn",
            "probability": 0.833333254
        },
        {
            "id": "salad",
            "probability": 0.03333333
        },
        {
            "id": "juice",
            "probability": 0
        },
        {
            "id": "soup",
            "probability": 0.03333333
        },
        {
            "id": "coffee",
            "probability": 0.03333333
        },
        {
            "id": "pasta",
            "probability": 0.03333333
        },
        {
            "id": "ice cream",
            "probability": 0.03333333
        }
    ],
    "eventId": "82ac52da-4077-4c7d-b14e-190530578e75",
    "rewardActionId": "popcorn"
}
```

### <a name="client-displays-the-rewardactionid-action"></a>Client geeft de rewardActionId-actie

In deze zelf studie `rewardActionId` wordt de waarde weer gegeven.

In uw eigen toekomstige toepassing is dit mogelijk een exacte tekst, een knop of een sectie van de webpagina gemarkeerd. De lijst wordt geretourneerd voor een post analyse van scores, niet voor het ordenen van de inhoud. Alleen de `rewardActionId` inhoud moet worden weer gegeven.

## <a name="reward-api-collect-information-for-reward"></a>Belonings-API: informatie verzamelen voor beloning

De [belonings Score](concept-rewards.md) moet zorgvuldig worden gepland, net zoals de functies gepland zijn. De belonings score moet meestal een waarde tussen 0 en 1 zijn. De waarde _kan_ gedeeltelijk worden berekend in de client toepassing, op basis van gedrag van gebruikers en gedeeltelijk op de server, op basis van bedrijfs logica en doel stellingen.

Als de server geen belonings-API aanroept binnen de **tijd** die is geconfigureerd in de Azure portal voor uw personaler-resource, wordt de **standaard beloning** (ook geconfigureerd in de Azure Portal) gebruikt voor die gebeurtenis.

In deze voorbeeld toepassing kunt u een waarde selecteren om te zien hoe de beloning van invloed is op de selecties.

## <a name="additional-ways-to-learn-from-this-sample"></a>Meer manieren om te leren van dit voor beeld

In het voor beeld wordt gebruikgemaakt van verschillende op tijd gebaseerde gebeurtenissen die zijn geconfigureerd in de Azure Portal voor uw persoonlijke resource. Als u met deze waarden speelt, keert u terug naar deze voor beeld-web-app om te zien hoe de wijzigingen van invloed zijn op de rang en beloningen:

* Wacht tijd op beloning
* Frequentie van model updates

Aanvullende instellingen voor afspelen met zijn onder andere:
* Standaard beloning
* Exploratie percentage


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelf studie, moet u de volgende resources opschonen:

* Verwijder uw voor beeld-projectmap.
* Verwijder uw persoonlijke resource-beschouw een persoonlijker resource als toegewezen aan de acties en pas de resource alleen opnieuw toe als u nog steeds het domein levens middelen als acties gebruikt.


## <a name="next-steps"></a>Volgende stappen
* [Hoe Personalizer werkt](how-personalizer-works.md)
* [Functies](concepts-features.md): concepten leren over functies met behulp van acties en context
* [Beloningen](concept-rewards.md): meer informatie over het berekenen van beloningen
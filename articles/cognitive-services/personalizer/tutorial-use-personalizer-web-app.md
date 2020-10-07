---
title: Web-app gebruiken - Personalizer
description: Een C# .NET-web-app aanpassen met een Personalizer-lus om de juiste inhoud te bieden aan een gebruiker op basis van acties (met functies) en contextfuncties.
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 59881a749676f4f5408c1ffc87b3bcf75c512e1e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91316219"
---
# <a name="tutorial-add-personalizer-to-a-net-web-app"></a>Zelfstudie: Personalizer toevoegen aan een .NET-web-app

Een C# .NET-web-app aanpassen met een Personalizer-lus om de juiste inhoud te bieden aan een gebruiker op basis van acties (met functies) en contextfuncties.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Personalizer-sleutel en -eindpunt instellen
> * Functies verzamelen
> * Rank- en Reward-API's aanroepen
> * Beste actie weergeven, aangeduid als _rewardActionId_



## <a name="select-the-best-content-for-a-web-app"></a>De beste inhoud voor een web-app selecteren

Een web-app moet Personalizer gebruiken als er een lijst is met _acties_ (een bepaald type inhoud) op de webpagina die moet worden aangepast aan één topitem (rewardActionId) dat moet worden weergegeven. Voorbeelden van actielijsten zijn nieuwsartikelen, locaties voor het plaatsen van knoppen en woordkeuzes voor productnamen.

U verzendt de lijst met acties, samen met contextfuncties, naar de Personalizer-lus. Personalizer selecteert één beste actie, en daarna geeft uw web-app die actie weer.

In deze zelfstudie zijn de acties typen voedingsmiddelen:

* pasta
* ice cream
* juice
* salad
* popcorn
* coffee
* soup

Om Personalizer te helpen meer te weten te komen over uw acties, verzendt u zowel _acties met functies_ als _contextfuncties_ bij elke Rank API-aanvraag.

Een **functie** van het model is informatie over de actie of context die kan worden geaggregeerd (gegroepeerd) voor leden van de gebruikersdatabase van uw web-app. Een functie _is niet_ individueel specifiek (zoals een gebruikers-ID) of uiterst specifiek (zoals een exacte tijdstip op de dag).

### <a name="actions-with-features"></a>Acties met functies

Elke actie (inhoudsitem) heeft functies waarmee u het voedingsmiddel kunt onderscheiden.

De functies zijn niet geconfigureerd als onderdeel van de lusconfiguratie in de Azure Portal. In plaats daarvan worden ze verzonden als een JSON-object met elke Rank API-aanroep. Dit biedt flexibiliteit voor de acties en hun functies om in de loop van de tijd te groeien, te wijzigen en te krimpen, waardoor Personalizer trends kan volgen.

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


## <a name="context-features"></a>Contextfuncties

Contextfuncties helpen Personalizer om de context van de acties te begrijpen. De context voor deze voorbeeldtoepassing omvat:

* tijdstip van de dag: morning, afternoon, evening, night
* voorkeur van de gebruiker voor smaak: salty, sweet, bitter, sour of savory
* context van de browser: user agent, geographical location, referrer

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

## <a name="how-does-the-web-app-use-personalizer"></a>Hoe wordt Personalizer gebruikt door de web-app?

De web-app gebruikt Personalizer om de beste actie te selecteren in de lijst met opties voor voedingsmiddelen. Dit doet de web-app door de volgende informatie te verzenden bij elke Rank API-aanroep:
* **acties** met hun functies, zoals `taste` en `spiceLevel`
* **context**functies, zoals `time` van de dag, de voorkeuren voor `taste` van de gebruiker en de gegevens van de gebruikersagent van de browser en contextfuncties
* **uit te sluiten acties**, zoals juice
* **eventId**, die verschillend is voor elke aanroep van de Rank API.

## <a name="personalizer-model-features-in-a-web-app"></a>Personalizer-modelfuncties in een web-app

Personalizer vereist functies voor de acties (inhoud) en de huidige context (gebruiker en omgeving). Functies worden gebruikt om acties af te stemmen met de huidige context in het model. Het model is een weergave van de laatste kennis van Personalizer over acties, context en hun functies, waarmee deze gefundeerde beslissingen kan nemen.

Het model, inclusief functies, wordt bijgewerkt volgens een schema op basis van de updatefrequentie van uw **model** in Azure Portal.

> [!CAUTION]
> Functies in deze toepassing zijn bedoeld om functies en functiewaarden te illustreren, maar niet noodzakelijkerwijs de beste functies om in een web-app te gebruiken.

### <a name="plan-for-features-and-their-values"></a>Functies en hun waarden plannen

Functies moeten worden geselecteerd met dezelfde planning en hetzelfde ontwerp als u zou toepassen op elk schema of model in uw technische architectuur. De functiewaarden kunnen worden ingesteld met bedrijfslogica of systemen van derden. Functiewaarden moeten niet zo uiterst specifiek zijn dat ze niet van toepassing zijn op een groep of een klasse van functies.

### <a name="generalize-feature-values"></a>Functiewaarden generaliseren

#### <a name="generalize-into-categories"></a>Generaliseren in categorieën

Deze app maakt gebruik van `time` als een functie, maar groepeert tijd in categorieën als `morning`, `afternoon`, `evening` en `night`. Dit is een voorbeeld van het gebruik van de informatie over tijd, maar niet op een uiterst specifieke manier, zoals `10:05:01 UTC+2`.

#### <a name="generalize-into-parts"></a>Generaliseren in delen

Deze app maakt gebruik van de HTTP-aanvraagfuncties vanuit de browser. Dit begint met een zeer specifieke tekenreeks met alle gegevens, bijvoorbeeld:

```http
Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/530.99 (KHTML, like Gecko) Chrome/80.0.3900.140 Safari/537.36
```

De klassebibliotheek **HttpRequestFeatures** generaliseert deze tekenreeks in een **userAgentInfo**-object met afzonderlijke waarden. Waarden die te specifiek zijn, worden ingesteld op een lege tekenreeks. Wanneer de contextfuncties voor de aanvraag worden verzonden, heeft deze de volgende JSON-indeling:

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


## <a name="using-sample-web-app"></a>De voorbeeld-web-app gebruiken

Voor de voorbeeld-web-app op basis van een browser (alle code wordt meegeleverd) moeten de volgende toepassingen zijn geïnstalleerd om de app uit te voeren.

Installeer de volgende software:

* [.NET Core 2.1](https://dotnet.microsoft.com/download/dotnet-core/2.1): de voorbeeld-back-endserver gebruikt .NET core
* [Node. js](https://nodejs.org/): de client/front-end is afhankelijk van deze toepassing
* [Visual Studio 2019](https://visualstudio.microsoft.com/vs/) of [.NET Core SLI](https://docs.microsoft.com/dotnet/core/tools/): gebruik de ontwikkelomgeving van Visual Studio 2019 of de .NET Core SLI om de app te bouwen en uit te voeren

### <a name="set-up-the-sample"></a>Het voorbeeld instellen
1. Kloon de opslagplaats met Azure Personalizer-voorbeelden.

    ```bash
    git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
    ```

1. Navigeer naar _samples/HttpRequestFeatures_ om de oplossing te openen, `HttpRequestFeaturesExample.sln`.

    Sta Visual Studio toe het .NET-pakket voor Personalizer bij te werken als u hierom wordt gevraagd.

### <a name="set-up-azure-personalizer-service"></a>Azure Personalizer-service instellen

1. [Maak een Personalizer-resource](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer) in de Azure-portal.

1. Zoek in de Azure-portal de `Endpoint` en een `Key1` of `Key2` (beide werken) op het tabblad **Sleutels en eindpunten**. Dit zijn uw `PersonalizerServiceEndpoint` en uw `PersonalizerApiKey`.
1. Vul de `PersonalizerServiceEndpoint` in **appsettings.json** in.
1. Configureer de `PersonalizerApiKey` als een [app-geheim](https://docs.microsoft.com/aspnet/core/security/app-secrets) op een van de volgende manieren:

    * Als u de .NET Core CLI gebruikt, kunt u de opdracht `dotnet user-secrets set "PersonalizerApiKey" "<API Key>"` gebruiken.
    * Als u Visual Studio gebruikt, kunt u met de rechtermuisknop op het project klikken en de menuoptie **Gebruikersgeheimen beheren** selecteren om de Personalizer-sleutels te configureren. Als u dit doet, wordt in Visual Studio een `secrets.json`-bestand geopend waaraan u de sleutels als volgt kunt toevoegen:

    ```JSON
    {
      "PersonalizerApiKey": "<your personalizer key here>",
    }
    ```

## <a name="run-the-sample"></a>De voorbeeldtoepassing uitvoeren

Bouw en voer HttpRequestFeaturesExample uit met een van de volgende methoden:

* Visual Studio 2019: Druk op **F5**
* .NET Core CLI: `dotnet build` en vervolgens `dotnet run`

U kunt via een webbrowser een Rank-aanvraag en een Reward-aanvraag verzenden en de antwoorden zien, evenals de HTTP-aanvraagfuncties die zijn geëxtraheerd uit uw omgeving.

> [!div class="mx-imgBorder"]
> ![Schermafbeelding van de functie HTTP-aanvraag in een webbrowser.](./media/tutorial-web-app/web-app-single-page.png)

## <a name="demonstrate-the-personalizer-loop"></a>De Personalizer-lus tonen

1. Selecteer de knop **Generate new Rank Request** om een nieuw JSON-object te maken voor de Rank API-aanroep. Hiermee worden de acties (met functies) en contextfuncties gemaakt en worden de waarden weergegeven, zodat u kunt zien hoe de JSON eruitziet.

    Voor uw eigen toekomstige toepassing kunnen er acties en functies worden gegenereerd op de client, op de server, een combinatie van de twee of met aanroepen naar andere services.

1. Selecteer **Send Rank Request** om het JSON-object naar de server te verzenden. De server roept de Rank API van de Personalizer aan. De server ontvangt het antwoord en retourneert de hoogst geclassificeerde actie aan de client voor weergave.

1. Stel de beloningswaarde in en selecteer de knop **Send Reward Request**. Als u de beloningswaarde niet wijzigt, verzendt de clienttoepassing altijd de waarde `1` naar Personalizer.

    > [!div class="mx-imgBorder"]
    > ![Schermafbeelding van de sectie Beloningsaanvraag.](./media/tutorial-web-app/reward-score-api-call.png)

    Voor uw eigen toepassing is het mogelijk dat er een beloningsscore wordt gegenereerd na het verzamelen van informatie van het gedrag van de gebruiker op de client, samen met de bedrijfslogica op de server.

## <a name="understand-the-sample-web-app"></a>De voorbeeld-web-app begrijpen

De voorbeeld-web-app bevat een **C# .NET**-server, die het verzamelen van functies en het verzenden en ontvangen van HTTP-aanroepen naar uw Personalizer-eindpunt beheert.

De voorbeeld-web-app maakt gebruik van een **knockout front-end clienttoepassing** om functies vast te leggen en acties voor de gebruikersinterface te verwerken, zoals het klikken op knoppen en het verzenden van gegevens naar de .NET-server.

In de volgende secties worden de onderdelen van de server en client uitgelegd die een ontwikkelaar nodig heeft om Personalizer te gebruiken.

## <a name="rank-api-client-application-sends-context-to-server"></a>Rank API: Clienttoepassing verzendt context naar server

De clienttoepassing haalt de _gebruikersagent_ van de browser van de gebruiker op.

> [!div class="mx-imgBorder"]
> ![Bouw en voer het project HTTPRequestFeaturesExample uit. Er wordt een browservenster geopend waarin de toepassing met één pagina wordt weergegeven.](./media/tutorial-web-app/user-agent.png)

## <a name="rank-api-server-application-calls-personalizer"></a>Rank API: Servertoepassing roept Personalizer aan

Dit is een typische .NET-web-app met een clienttoepassing, waarvan veel van de standaardcode aan u wordt verstrekt. Code die niet specifiek is voor Personalizer wordt verwijderd uit de volgende codefragmenten, zodat u zich kunt concentreren op de voor Personalizer specifieke code.

### <a name="create-personalizer-client"></a>Personalizer-client maken

In de **Startup.cs** van de server worden het Personalizer-eindpunt en de Personalizer-sleutel gebruikt om de Personalizer-client te maken. De clienttoepassing hoeft niet te communiceren met Personalizer in deze app, en vertrouwt in plaats daarvan op de server om deze SDK-aanroepen uit te voeren.

De .NET-opstartcode van de webserver is:

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

### <a name="select-best-action"></a>Beste actie selecteren

In de **PersonalizerController.cs** van de server, geeft de server-API **GenerateRank** de voorbereiding voor het aanroepen van de Rank API weer

* Nieuwe `eventId` maken voor de Rank-aanroep
* De lijst met acties ophalen
* De lijst met functies van de gebruiker ophalen en contextfuncties maken
* Desgewenst uitgesloten acties instellen
* Rank API aanroepen, resultaten aan client retourneren

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

De JSON die wordt verzonden naar Personalizer, die beide acties (met functies) en de huidige contextfuncties bevat, ziet er als volgt uit:

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

### <a name="return-personalizer-rewardactionid-to-client"></a>rewardActionId van Personalizer retourneren aan client

De Rank API retourneert de geselecteerde beste actie **rewardActionId** aan de server.

De actie weergeven die is geretourneerd in **rewardActionId**.

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

### <a name="client-displays-the-rewardactionid-action"></a>Client geeft de actie rewardActionId weer

In deze zelfstudie wordt de waarde `rewardActionId` weergegeven.

In uw eigen toekomstige toepassing is dit mogelijk een exacte tekst, een knop of een gemarkeerde sectie van de webpagina. De lijst wordt geretourneerd voor een post-analyse van scores, niet voor het ordenen van de inhoud. Alleen de inhoud van `rewardActionId` moet worden weergegeven.

## <a name="reward-api-collect-information-for-reward"></a>Reward API: informatie voor beloning verzamelen

De [beloningsscore](concept-rewards.md) moet zorgvuldig worden gepland, net zoals de functies zijn gepland. De beloningsscore moet meestal een waarde tussen 0 en 1 zijn. De waarde _kan_ gedeeltelijk worden berekend in de clienttoepassing, op basis van gedrag van gebruikers, en gedeeltelijk op de server, op basis van bedrijfslogica en doelstellingen.

Als de server de Reward API niet aanroept binnen de **beloningswachttijd** die voor uw Personalizer-resource in de Azure-portal is geconfigureerd, wordt de **standaardbeloning** (ook in de Azure-portal geconfigureerd) voor die gebeurtenis gebruikt.

In deze voorbeeldtoepassing kunt u een waarde selecteren om te zien hoe de beloning van invloed is op de selecties.

## <a name="additional-ways-to-learn-from-this-sample"></a>Meer manieren om te leren van dit voorbeeld

In het voorbeeld wordt gebruikgemaakt van verschillende op tijd gebaseerde gebeurtenissen die voor uw Personalizer-resource zijn geconfigureerd in Azure Portal. Als u met deze waarden oefent, keert u terug naar deze voorbeeld-web-app om te zien hoe de wijzigingen van invloed zijn op de Rank- en Reward-aanroepen:

* Wachttijd voor beloning
* Bijwerkfrequentie voor model

Aanvullende instellingen om mee te oefenen zijn onder meer:
* Standaardbeloning
* Verkenningspercentage


## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelfstudie, moet u de volgende resources opschonen:

* Verwijder de map met uw voorbeeldproject.
* Verwijder uw Personalizer-resource. Zie een Personalizer-resource als toegewezen aan de acties en context. Gebruik de resource alleen opnieuw als u nog steeds de levensmiddelen als onderwerpdomein voor acties gebruikt.


## <a name="next-steps"></a>Volgende stappen
* [Hoe Personalizer werkt](how-personalizer-works.md)
* [Functies](concepts-features.md): meer informatie over concepten over functies met behulp van acties en context
* [Beloningen](concept-rewards.md): meer informatie over het berekenen van beloningen

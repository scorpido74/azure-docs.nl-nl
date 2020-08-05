---
title: Personalizer gebruiken in chatbot - Personalizer
description: Pas een C# .NET-chatbot aan met een Personalizer-lus om de juiste inhoud te bieden aan een gebruiker op basis van acties (met functies) en contextfuncties.
ms.topic: tutorial
ms.date: 07/17/2020
ms.openlocfilehash: 8e936b4017ad50434bc7d6b05b4217f82a9080e0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87131664"
---
# <a name="tutorial-use-personalizer-in-net-chat-bot"></a>Zelfstudie: Personalizer gebruiken in een .NET-chatbot

Gebruik een C# .NET-chatbot met een Personalizer-lus om de juiste inhoud te bieden aan een gebruiker. Deze chatbot biedt gebruikers een suggestie voor een specifieke soort koffie of thee. De gebruiker kan deze suggestie accepteren of afwijzen. Hierdoor krijgt Personalizer informatie die helpt om de volgende suggestie geschikter te maken.

**In deze zelfstudie leert u het volgende:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Azure-resources instellen
> * De bot configureren en uitvoeren
> * Werken met de bot met behulp van Bot Emulator
> * Begrijpen waar en hoe de bot gebruikmaakt van Personalizer


## <a name="how-does-the-chat-bot-work"></a>Hoe werkt de chatbot?

Een chatbot is doorgaans een tweezijdig gesprek met een gebruiker. Deze specifieke chatbot maakt gebruik van Personalizer om de beste actie (koffie of thee) voor te stellen voor de gebruiker. Personalizer maakt gebruik van conditionering om deze selectie te maken.

De chatbot moet wendingen in het gesprek beheren. De chatbot gebruikt [Bot Framework](https://github.com/microsoft/botframework-sdk) om de architectuur van de bot en het gesprek te beheren, en gebruikt de cognitieve service, [LUIS (Language Understanding)](../LUIS/index.yml), om de intentie van de natuurlijke taal van de gebruiker te begrijpen.

De chatbot is een website met een specifieke route die beschikbaar is voor het beantwoorden van aanvragen, `http://localhost:3978/api/messages`. U kunt de Bot Emulator gebruiken om visueel te werken met de actieve chatbot terwijl u lokaal een bot ontwikkelt.

### <a name="user-interactions-with-the-bot"></a>Gebruikersinteracties met de bot

Dit is een eenvoudige chatbot waarmee u tekstquery's kunt invoeren.

|Gebruiker voert tekst in|Bot reageert met tekst|Beschrijving van de actie die de bot onderneemt om de antwoordtekst te bepalen|
|--|--|--|
|Geen tekst ingevoerd - bot begint het gesprek.|`This is a simple chatbot example that illustrates how to use Personalizer. The bot learns what coffee or tea order is preferred by customers given some context information (such as weather, temperature, and day of the week) and information about the user.`<br>`To use the bot, just follow the prompts. To try out a new imaginary context, type “Reset” and a new one will be randomly generated.`<br>`Welcome to the coffee bot, please tell me if you want to see the menu or get a coffee or tea suggestion for today. Once I’ve given you a suggestion, you can reply with ‘like’ or ‘don’t like’. It’s Tuesday today and the weather is Snowy.`|De bot begint het gesprek met instructietekst en laat u weten wat de context is: `Tuesday`, `Snowy`.|
|`Show menu`|`Here is our menu: Coffee: Cappuccino Espresso Latte Macchiato Mocha Tea: GreenTea Rooibos`|Bepaalt de intentie van de query met behulp van LUIS, en geeft vervolgens de menuopties voor typen koffie en thee weer. Functies van de acties zijn |
|`What do you suggest`|`How about Latte?`|Bepaalt de intentie van de query met behulp van LUIS, roept vervolgens de **Rank API** aan, en geeft de bovenste keuze weer als vraag `How about {response.RewardActionId}?`. Geeft ter illustratie ook een JSON-aanroep en -respons weer.|
|`I like it`|`That’s great! I’ll keep learning your preferences over time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Bepaalt de intentie van de query met behulp van LUIS, roept vervolgens de **Reward API** aan met een beloning van `1`, en geeft ter illustratie een JSON-aanroep en -respons weer.|
|`I don't like it`|`Oh well, maybe I’ll guess better next time.`<br>`Would you like to get a new suggestion or reset the simulated context to a new day?`|Bepaalt de intentie van de query met behulp van LUIS, roept vervolgens de **Reward API** aan met een beloning van `0`, en geeft ter illustratie een JSON-aanroep en -respons weer.|
|`Reset`|Retourneert instructietekst.|Bepaalt de intentie van de query met behulp van LUIS, geeft vervolgens de instructietekst weer, en stelt de context opnieuw in.|


### <a name="personalizer-in-this-bot"></a>Personalizer in deze bot

Deze chatbot maakt gebruik van Personalizer om de bovenste actie (specifiek type koffie of thee) te selecteren, op basis van een lijst met _acties_ (bepaald type inhoud) en contextfuncties.

De bot verzendt de lijst met acties, samen met contextfuncties, naar de Personalizer-lus. Personalizer retourneert de beste actie voor de bot. Vervolgens wordt deze actie weergegeven.

In deze zelfstudie zijn de **acties** typen koffie en thee:

|Koffie|Thee|
|--|--|
|Cappuccino<br>Espresso<br>Latte<br>Mokka|Groene thee<br>Rooibos|

**Rank API:** De bot verzendt met elke Rank API-aanvraag het volgende om te helpen met het trainen van Personalizer op basis van uw acties:

* Acties _met functies_
* Contextfuncties

Een **functie** van het model is informatie over de actie of context die kan worden geaggregeerd (gegroepeerd) voor leden van de gebruikersdatabase van uw chatbot. Een functie _is niet_ individueel specifiek (zoals een gebruikers-ID) of uiterst specifiek (zoals een exacte tijdstip op de dag).

Functies worden gebruikt om acties af te stemmen met de huidige context in het model. Het model is een weergave van de laatste kennis van Personalizer over acties, context en hun functies, waarmee deze gefundeerde beslissingen kan nemen.

Het model, inclusief functies, wordt bijgewerkt volgens een schema op basis van de updatefrequentie van uw **model** in Azure Portal.

Functies moeten worden geselecteerd met dezelfde planning en hetzelfde ontwerp als u zou toepassen op elk schema of model in uw technische architectuur. De functiewaarden kunnen worden ingesteld met bedrijfslogica of systemen van derden.

> [!CAUTION]
> Functies in deze toepassing zijn voor demonstratiedoeleinden. Dit zijn niet noodzakelijkerwijs de beste functies om voor uw gebruikscase te gebruiken in een web-app.

#### <a name="action-features"></a>Actiefuncties

Elke actie (inhoudsitem) heeft functies waarmee u het type koffie of thee kunt onderscheiden.

De functies zijn niet geconfigureerd als onderdeel van de lusconfiguratie in de Azure Portal. In plaats daarvan worden ze verzonden als een JSON-object met elke Rank API-aanroep. Dit biedt flexibiliteit voor de acties en hun functies om in de loop van de tijd te groeien, te wijzigen en te krimpen, waardoor Personalizer trends kan volgen.

Functies voor koffie en thee omvatten:

* Herkomstlocatie van de koffiebonen, zoals Kenia en Brazilië
* Of de koffie of thee organisch is.
* Licht of donker gebrande koffie

Koffie heeft in de voorafgaande lijst drie functies, terwijl thee er maar één heeft. Geef alleen functies door aan Personalizer die zinvol zijn voor de actie. Geef geen lege waarde door voor een functie als deze niet van toepassing is op de actie.

#### <a name="context-features"></a>Contextfuncties

Contextfuncties helpen Personalizer de context van de omgeving te begrijpen, zoals het weergaveapparaat, de gebruiker, de locatie, en andere functies die relevant zijn voor uw gebruikerscase.

De context voor deze chatbot omvat:

* Type weer (sneeuw, regen, zon)
* Dag van de week

Functies worden in deze chatbot in willekeurige volgorde geselecteerd. In een echte bot gebruikt u echte gegevens voor uw contextfuncties.

### <a name="design-considerations-for-this-bot"></a>Ontwerpoverwegingen voor deze bot

Er zijn enkele dingen waar u rekening mee moet houden met betrekking tot dit gesprek:
* **Botinteractie**: Het gesprek is zeer eenvoudig omdat Rank en Reward in een eenvoudige gebruikerscase worden gedemonstreerd. Het biedt geen demonstratie van de volledige functionaliteit van de Bot Framework SDK of van de Emulator.
* **Personalizer**: De functies worden willekeurig geselecteerd om het gebruik te simuleren. Selecteer functies in een productiescenario van Personalizer niet willekeurig.
* **LUIS (Language Understanding)** : De paar voorbeelduitingen van het LUIS-model zijn alleen bedoeld voor dit voorbeeld. Gebruik voor uw LUIS-toepassing in productie niet zo weinig voorbeelduitingen.


## <a name="install-required-software"></a>Vereiste software installeren
- [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/). De opslagplaats met downloadbare voorbeelden omvat instructies, als u liever de .NET Core CLI gebruikt.
- [Microsoft Bot Framework Emulator](https://aka.ms/botframeworkemulator) is een desktoptoepassing waarmee botontwikkelaars hun bots kunnen testen en fouten erin kunnen opsporen, op localhost of extern uitgevoerd via een tunnel.

## <a name="download-the-sample-code-of-the-chat-bot"></a>Download de voorbeeldcode van de chatbot

De chatbot is beschikbaar in de opslagplaats met Personalizer-voorbeelden. Kloon of [download](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/archive/master.zip) de opslagplaats. Open vervolgens het voorbeeld in de map `/samples/ChatbotExample` met Visual Studio 2019.

Als u de opslagplaats wilt klonen, gebruikt u de volgende Git-opdracht in een Bash-shell (terminal).

```bash
git clone https://github.com/Azure-Samples/cognitive-services-personalizer-samples.git
```

## <a name="create-and-configure-personalizer-and-luis-resources"></a>Personalizer en LUIS-resources maken en configureren

### <a name="create-azure-resources"></a>Azure-resources maken

Als u deze chatbot wilt gebruiken, moet u Azure-resources maken voor Personalizer en LUIS (Language Understanding).

* [LUIS-resources maken](../luis/luis-how-to-azure-subscription.md#create-luis-resources-in-azure-portal). Selecteer in de stap voor het maken **beide**, omdat u zowel creatie- als voorspellingsresources nodig hebt.
* [Maak een Personalizer-resource](how-to-create-resource.md) en kopieer vervolgens de sleutel en het eindpunt in de Azure-portal. U moet deze waarden instellen in het `appsettings.json`-bestand van het .NET-project.

### <a name="create-luis-app"></a>Een LUIS-app maken

Als u nog niet bekend bent met LUIS, moet u zich [aanmelden](https://www.luis.ai) en uw account meteen migreren. U hoeft geen nieuwe resources te maken. Selecteer in plaats hiervan de resources die u hebt gemaakt in de vorige sectie van deze zelfstudie.

1. Als u een nieuwe LUIS-toepassing wilt maken, selecteert u in de [LUIS-portal](https://www.luis.ai) uw abonnement en creatieresource.
1. Selecteer vervolgens op dezelfde pagina de optie **+ Nieuwe app voor gesprek** en **Importeren als JSON**.
1. Selecteer in het pop-upvenster de optie **Bestand kiezen** en selecteer vervolgens het `/samples/ChatbotExample/CognitiveModels/coffeebot.json`-bestand. Voer de naam `Personalizer Coffee bot` in.
1. Selecteer de knop **Trainen** in het navigatievenster rechtsboven in de LUIS-portal.
1. Selecteer de knop **Publiceren** om de app te publiceren in de **Productiesleuf** voor de voorspellingsruntime.
1. Selecteer **Beheren** en vervolgens **Instellingen**. Kopieer de waarde van de **App-id**. U moet deze waarde instellen in het `appsettings.json`-bestand van het .NET-project.
1. Blijf in de sectie **Beheren** en selecteer **Azure-resources**. Hiermee worden de gekoppelde resources in de app weergegeven.
1. Selecteer **Voorspellingsresource toevoegen**. Selecteer in het pop-updialoogvenster uw abonnement en de voorspellingsresource die u in een eerdere sectie van deze zelfstudie hebt gemaakt. Selecteer vervolgens **Gereed**.
1. Kopieer de waarden van **Primaire sleutel** en **Eindpunt-URL**. U moet deze waarden instellen in het `appsettings.json`-bestand van het .NET-project.

### <a name="configure-bot-with-appsettingsjson-file"></a>Bot configureren met het appsettings.json-bestand

1. Open het oplossingsbestand van de chatbot, `ChatbotSamples.sln`, met Visual Studio 2019.
1. Open `appsettings.json` in de hoofdmap van het project.
1. Stel alle vijf de instellingen in die u in de vorige sectie van deze zelfstudie hebt gekopieerd.

    ```json
    {
      "PersonalizerChatbot": {
        "LuisAppId": "",
        "LuisAPIKey": "",
        "LuisServiceEndpoint": "",
        "PersonalizerServiceEndpoint": "",
        "PersonalizerAPIKey": ""
      }
    }
    ```

### <a name="build-and-run-the-bot"></a>De bot bouwen en uitvoeren

Zodra u de `appsettings.json` hebt geconfigureerd, bent u klaar om de chatbot te bouwen en uit te voeren. Wanneer u dit doet, wordt een browser geopend met de actieve website, `http://localhost:3978`.

:::image type="content" source="media/tutorial-chat-bot/chat-bot-web-site.png" alt-text="Schermopname van de browser waarin de website van de chatbot wordt weergegeven.":::

Zorg ervoor dat de website actief blijft, omdat in de zelfstudie wordt uitgelegd wat de bot doet, zodat u kunt werken met de bot.


## <a name="set-up-the-bot-emulator"></a>De Bot Emulator instellen

1. Open de Bot Emulator en selecteer **Bot openen**.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-startup.png" alt-text="Schermopname van het opstartscherm van de Bot Emulator.":::


1. Configureer de bot met de volgende **bot-URL**. Selecteer vervolgens **Verbinding maken**:

    `http://localhost:3978/api/messages`

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-open-bot-settings.png" alt-text="Schermopname van Bot openen in de Bot Emulator-instellingen.":::

    De emulator maakt verbinding met de chatbot en geeft de instructietekst weer, samen met informatie over logboekregistratie en foutopsporing die nuttig is voor lokale ontwikkeling.

    :::image type="content" source="media/tutorial-chat-bot/bot-emulator-bot-conversation-first-turn.png" alt-text="Schermopname van de Bot Emulator bij de eerste wending in het gesprek.":::

## <a name="use-the-bot-in-the-bot-emulator"></a>De bot gebruiken in de Bot Emulator

1. Vraag de bot om het menu weer te geven, door `I would like to see the menu` in te voeren. De chatbot geeft de items weer.
1. Laat de bot een item voorstellen door `Please suggest a drink for me.` in te voeren. De Emulator geeft in het chatvenster Rank-aanvraag en -antwoord weer, zodat u de volledige JSON kunt zien. En de bot doet een suggestie, bijvoorbeeld `How about Latte?`
1. Antwoord dat u dit bevalt. Dit betekent dat u de bovenste geclassificeerde selectie van Personalizer accepteert, `I like it.`. De Emulator geeft in het chatvenster de Reward-aanvraag weer met beloningsscore 1, zodat u de volledige JSON kunt zien. De bot reageert met `That’s great! I’ll keep learning your preferences over time.` en `Would you like to get a new suggestion or reset the simulated context to a new day?`

    Als u met `no` antwoordt op de selectie, wordt de beloningsscore 0 verzonden naar Personalizer.


## <a name="understand-the-net-code-using-personalizer"></a>De .NET-code begrijpen met behulp van Personalizer

De .NET-oplossing is een eenvoudige Bot Framework-chatbot. De code die betrekking heeft op Personalizer, bevindt zich in de volgende mappen:
* `/samples/ChatbotExample/Bots`
    * `PersonalizerChatbot.cs`: bestand voor de interactie tussen bot en Personalizer
* `/samples/ChatbotExample/ReinforcementLearning`: beheert de acties en functies voor het Personalizer-model
* `/samples/ChatbotExample/Model`: bestanden voor de Personalizer-acties en -functies, en voor de LUIS-intenties

### <a name="personalizerchatbotcs---working-with-personalizer"></a>PersonalizerChatbot.cs: werken met Personalizer

De klasse `PersonalizerChatbot` is afgeleid van de `Microsoft.Bot.Builder.ActivityHandler`. Deze heeft drie eigenschappen en methoden voor het beheren van de gespreksstroom.

> [!CAUTION]
> Kopieer niet de code uit deze zelfstudie. Gebruik de voorbeeldcode in de [opslagplaats met Personalizer-voorbeelden](https://github.com/Azure-Samples/cognitive-services-personalizer-samples).

```csharp
public class PersonalizerChatbot : ActivityHandler
{

    private readonly LuisRecognizer _luisRecognizer;
    private readonly PersonalizerClient _personalizerClient;

    private readonly RLContextManager _rlFeaturesManager;

    public PersonalizerChatbot(LuisRecognizer luisRecognizer, RLContextManager rlContextManager, PersonalizerClient personalizerClient)
            {
                _luisRecognizer = luisRecognizer;
                _rlFeaturesManager = rlContextManager;
                _personalizerClient = personalizerClient;
            }
    }

    public override async Task OnTurnAsync(ITurnContext turnContext, CancellationToken cancellationToken = default(CancellationToken))
    {
        await base.OnTurnAsync(turnContext, cancellationToken);

        if (turnContext.Activity.Type == ActivityTypes.Message)
        {
            // Check LUIS model
            var recognizerResult = await _luisRecognizer.RecognizeAsync(turnContext, cancellationToken);
            var topIntent = recognizerResult?.GetTopScoringIntent();
            if (topIntent != null && topIntent.HasValue && topIntent.Value.intent != "None")
            {
                Intents intent = (Intents)Enum.Parse(typeof(Intents), topIntent.Value.intent);
                switch (intent)
                {
                    case Intents.ShowMenu:
                        await turnContext.SendActivityAsync($"Here is our menu: \n Coffee: {CoffeesMethods.DisplayCoffees()}\n Tea: {TeaMethods.DisplayTeas()}", cancellationToken: cancellationToken);
                        break;
                    case Intents.ChooseRank:
                        // Here we generate the event ID for this Rank.
                        var response = await ChooseRankAsync(turnContext, _rlFeaturesManager.GenerateEventId(), cancellationToken);
                        _rlFeaturesManager.CurrentPreference = response.Ranking;
                        await turnContext.SendActivityAsync($"How about {response.RewardActionId}?", cancellationToken: cancellationToken);
                        break;
                    case Intents.RewardLike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 1, cancellationToken);
                            await turnContext.SendActivityAsync($"That's great! I'll keep learning your preferences over time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you like. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.RewardDislike:
                        if (!string.IsNullOrEmpty(_rlFeaturesManager.CurrentEventId))
                        {
                            await RewardAsync(turnContext, _rlFeaturesManager.CurrentEventId, 0, cancellationToken);
                            await turnContext.SendActivityAsync($"Oh well, maybe I'll guess better next time.", cancellationToken: cancellationToken);
                            await SendByebyeMessageAsync(turnContext, cancellationToken);
                        }
                        else
                        {
                            await turnContext.SendActivityAsync($"Not sure what you dislike. Did you ask for a suggestion?", cancellationToken: cancellationToken);
                        }

                        break;
                    case Intents.Reset:
                        _rlFeaturesManager.GenerateRLFeatures();
                        await SendResetMessageAsync(turnContext, cancellationToken);
                        break;
                    default:
                        break;
                }
            }
            else
            {
                var msg = @"Could not match your message with any of the following LUIS intents:
                        'ShowMenu'
                        'ChooseRank'
                        'RewardLike'
                        'RewardDislike'.
                        Try typing 'Show me the menu','What do you suggest','I like it','I don't like it'.";
                await turnContext.SendActivityAsync(msg);
            }
        }
        else if (turnContext.Activity.Type == ActivityTypes.ConversationUpdate)
        {
            // Generate a new weekday and weather condition
            // These will act as the context features when we call rank with Personalizer
            _rlFeaturesManager.GenerateRLFeatures();

            // Send a welcome message to the user and tell them what actions they may perform to use this bot
            await SendWelcomeMessageAsync(turnContext, cancellationToken);
        }
        else
        {
            await turnContext.SendActivityAsync($"{turnContext.Activity.Type} event detected", cancellationToken: cancellationToken);
        }
    }

    // code removed for brevity, full sample code available for download
    private async Task SendWelcomeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendResetMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task SendByebyeMessageAsync(ITurnContext turnContext, CancellationToken cancellationToken)
    private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
    private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
}
```

Met de methoden die worden voorafgegaan door `Send`, worden gesprekken met de bot en LUIS beheerd. De methoden `ChooseRankAsync` en `RewardAsync` werken met Personalizer.

#### <a name="calling-rank-api-and-display-results"></a>Rank API aanroepen en resultaten weergeven

Met de methode `ChooseRankAsync` worden de JSON-gegevens gebouwd die moeten worden verzonden naar de Rank API van Personalizer, door de acties met functies en de contextfuncties te verzamelen.

```csharp
private async Task<RankResponse> ChooseRankAsync(ITurnContext turnContext, string eventId, CancellationToken cancellationToken)
{
    IList<object> contextFeature = new List<object>
    {
        new { weather = _rlFeaturesManager.RLFeatures.Weather.ToString() },
        new { dayofweek = _rlFeaturesManager.RLFeatures.DayOfWeek.ToString() },
    };

    Random rand = new Random(DateTime.UtcNow.Millisecond);
    IList<RankableAction> actions = new List<RankableAction>();
    var coffees = Enum.GetValues(typeof(Coffees));
    var beansOrigin = Enum.GetValues(typeof(CoffeeBeansOrigin));
    var organic = Enum.GetValues(typeof(Organic));
    var roast = Enum.GetValues(typeof(CoffeeRoast));
    var teas = Enum.GetValues(typeof(Teas));

    foreach (var coffee in coffees)
    {
        actions.Add(new RankableAction
        {
            Id = coffee.ToString(),
            Features =
            new List<object>()
            {
                new { BeansOrigin = beansOrigin.GetValue(rand.Next(0, beansOrigin.Length)).ToString() },
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
                new { Roast = roast.GetValue(rand.Next(0, roast.Length)).ToString() },
            },
        });
    }

    foreach (var tea in teas)
    {
        actions.Add(new RankableAction
        {
            Id = tea.ToString(),
            Features =
            new List<object>()
            {
                new { Organic = organic.GetValue(rand.Next(0, organic.Length)).ToString() },
            },
        });
    }

    // Sending a rank request to Personalizer
    // Here we are asking Personalizer to decide which drink the user is most likely to want
    // based on the current context features (weather, day of the week generated in RLContextManager)
    // and the features of the drinks themselves
    var request = new RankRequest(actions, contextFeature, null, eventId);
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL TO RANK =====\n" +
        "This is what is getting sent to Rank:\n" +
        $"{JsonConvert.SerializeObject(request, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    var response = await _personalizerClient.RankAsync(request, cancellationToken);
    await turnContext.SendActivityAsync(
        $"===== DEBUG MESSAGE RETURN FROM RANK =====\n" +
        "This is what Rank returned:\n" +
        $"{JsonConvert.SerializeObject(response, Formatting.Indented)}\n",
        cancellationToken: cancellationToken);
    return response;
}
```

#### <a name="calling-reward-api-and-display-results"></a>Reward API aanroepen en resultaten weergeven

Met de methode `RewardAsync` worden de JSON-gegevens gebouwd die moeten worden verzonden naar de Reward API van Personalizer, door de score te bepalen. De score wordt bepaald op basis van de LUIS-intentie die is geïdentificeerd in de gebruikerstekst en verzonden vanuit de methode `OnTurnAsync`.

```csharp
private async Task RewardAsync(ITurnContext turnContext, string eventId, double reward, CancellationToken cancellationToken)
{
    await turnContext.SendActivityAsync(
        "===== DEBUG MESSAGE CALL REWARD =====\n" +
        "Calling Reward:\n" +
        $"eventId = {eventId}, reward = {reward}\n",
        cancellationToken: cancellationToken);

    // Sending a reward request to Personalizer
    // Here we are responding to the drink ranking Personalizer provided us
    // If the user liked the highest ranked drink, we give a high reward (1)
    // If they did not, we give a low reward (0)
    await _personalizerClient.RewardAsync(eventId, new RewardRequest(reward), cancellationToken);
}
```

## <a name="design-considerations-for-a-bot"></a>Ontwerpoverwegingen voor een bot

Dit voorbeeld is bedoeld om een eenvoudige end-to-end-oplossing van Personalizer in een bot te demonstreren. Uw gebruikscase is mogelijk ingewikkelder.

Als u Personalizer wilt gebruiken in een productiebot, plant u:
* Toegang tot Personalizer in realtime _telkens_ wanneer u een geclassificeerde selectie nodig hebt. De Rank API kan niet in een batch worden geplaatst of in de cache worden opgeslagen.  De Reward-aanroep kan worden vertraagd of geoffload naar een afzonderlijk proces. Als u geen beloning retourneert in de opgegeven tijdsperiode, wordt een standaardbeloningswaarde ingesteld voor de gebeurtenis.
* Gebruikscase op basis van berekening van de beloning: In dit voorbeeld hebben we twee beloningen weergegeven, van nul en één, zonder bereik ertussen en zonder negatieve waarde voor een score. Uw systeem heeft mogelijk meer gedetailleerde scores nodig.
* Bot-kanalen: In dit voorbeeld wordt één kanaal gebruikt. Als u van plan bent meerdere kanalen te gebruiken, of variaties van bots op één kanaal, moet dit mogelijk worden overwogen als onderdeel van de contextfuncties van het Personalizer-model.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u klaar bent met deze zelfstudie, moet u de volgende resources opschonen:

* Verwijder de map met uw voorbeeldproject.
* Verwijder Personalizer en uw LUIS-resource in de Azure-portal.

## <a name="next-steps"></a>Volgende stappen
* [Hoe Personalizer werkt](how-personalizer-works.md)
* [Functies](concepts-features.md): meer informatie over concepten over functies met behulp van acties en context
* [Beloningen](concept-rewards.md): meer informatie over het berekenen van beloningen

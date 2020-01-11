---
title: 'Zelf studie: Application Insights C# ,-Luis'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie worden bot en Language Understanding informatie toegevoegd voor het Application Insights van telemetriegegevens.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: b9c47685253e2a70c7b5e947debaac6f5f3264b2
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75888291"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-c"></a>Zelf studie: LUIS-resultaten toevoegen aan Application Insights vanuit een bot inC#

In deze zelf studie worden bot en Language Understanding informatie toegevoegd voor het [Application Insights](https://azure.microsoft.com/services/application-insights/) van telemetriegegevens. Wanneer u deze gegevens hebt, kunt u deze zoeken in de Kusto-taal of Power BI voor het analyseren, samen voegen en rapporteren van de intenties en entiteiten van de utterance in realtime. Met deze analyse kunt u bepalen of u moet toevoegen of bewerken van de intenties en entiteiten van uw LUIS-app.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Leg bot en taal informatie in Application Insights
> * Query Application Insights voor Language Understanding gegevens

## <a name="prerequisites"></a>Vereisten

* Een Azure bot service-bot, gemaakt met Application Insights ingeschakeld.
* Gedownloade bot-code uit de vorige bot- **[zelf studie](luis-csharp-tutorial-bf-v4.md)** . 
* [Botemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Alle code in deze zelf studie is beschikbaar in de [github-opslag plaats Azure-samples language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-csharp-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Application Insights toevoegen aan Web app bot-project

Op dit moment verzamelt de Application Insights-service, die wordt gebruikt in deze web-app-bot, telemetrie van de algemene status voor de bot. Er worden geen LUIS-gegevens verzameld. 

Om de LUIS-informatie vast te leggen, moet het **[micro soft. ApplicationInsights](https://www.nuget.org/packages/Microsoft.ApplicationInsights/)** NuGet-pakket zijn geïnstalleerd en geconfigureerd.  

1. Voeg in Visual Studio de afhankelijkheid toe aan de oplossing. Klik in de **Solution Explorer**met de rechter muisknop op de project naam en selecteer **NuGet-pakketten beheren...** . In het NuGet Package Manager wordt een lijst met geïnstalleerde pakketten weer gegeven. 
1. Selecteer **Bladeren** en zoek naar **micro soft. ApplicationInsights**.
1. Installeer het pakket. 

## <a name="capture-and-send-luis-query-results-to-application-insights"></a>Vastleggen en LUIS queryresultaten verzenden naar Application Insights

1. Open het `LuisHelper.cs`-bestand en vervang de inhoud door de volgende code. De **LogToApplicationInsights** -methode legt de bot-en Luis-gegevens vast en verzendt deze naar Application Insights als een tracerings gebeurtenis met de naam `LUIS`.

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System;
    using System.Linq;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.AI.Luis;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Logging;
    using Microsoft.ApplicationInsights;
    using System.Collections.Generic;
    
    namespace Microsoft.BotBuilderSamples
    {
        public static class LuisHelper
        {
            public static async Task<BookingDetails> ExecuteLuisQuery(IConfiguration configuration, ILogger logger, ITurnContext turnContext, CancellationToken cancellationToken)
            {
                var bookingDetails = new BookingDetails();
    
                try
                {
                    // Create the LUIS settings from configuration.
                    var luisApplication = new LuisApplication(
                        configuration["LuisAppId"],
                        configuration["LuisAPIKey"],
                        "https://" + configuration["LuisAPIHostName"]
                    );
    
                    var recognizer = new LuisRecognizer(luisApplication);
    
                    // The actual call to LUIS
                    var recognizerResult = await recognizer.RecognizeAsync(turnContext, cancellationToken);
    
                    LuisHelper.LogToApplicationInsights(configuration, turnContext, recognizerResult);
    
                    var (intent, score) = recognizerResult.GetTopScoringIntent();
                    if (intent == "Book_flight")
                    {
                        // We need to get the result from the LUIS JSON which at every level returns an array.
                        bookingDetails.Destination = recognizerResult.Entities["To"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
                        bookingDetails.Origin = recognizerResult.Entities["From"]?.FirstOrDefault()?["Airport"]?.FirstOrDefault()?.FirstOrDefault()?.ToString();
    
                        // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                        // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                        bookingDetails.TravelDate = recognizerResult.Entities["datetime"]?.FirstOrDefault()?["timex"]?.FirstOrDefault()?.ToString().Split('T')[0];
                    }
                }
                catch (Exception e)
                {
                    logger.LogWarning($"LUIS Exception: {e.Message} Check your LUIS configuration.");
                }
    
                return bookingDetails;
            }
            public static void LogToApplicationInsights(IConfiguration configuration, ITurnContext turnContext, RecognizerResult result)
            {
                // Create Application Insights object
                TelemetryClient telemetry = new TelemetryClient();
    
                // Set Application Insights Instrumentation Key from App Settings
                telemetry.InstrumentationKey = configuration["BotDevAppInsightsKey"];
    
                // Collect information to send to Application Insights
                Dictionary<string, string> logProperties = new Dictionary<string, string>();
    
                logProperties.Add("BotConversation", turnContext.Activity.Conversation.Name);
                logProperties.Add("Bot_userId", turnContext.Activity.Conversation.Id);
    
                logProperties.Add("LUIS_query", result.Text);
                logProperties.Add("LUIS_topScoringIntent_Name", result.GetTopScoringIntent().intent);
                logProperties.Add("LUIS_topScoringIntentScore", result.GetTopScoringIntent().score.ToString());
    
    
                // Add entities to collected information
                int i = 1;
                if (result.Entities.Count > 0)
                {
                    foreach (var item in result.Entities)
                    {
                        logProperties.Add("LUIS_entities_" + i++ + "_" + item.Key, item.Value.ToString());
                    }
                }
    
                // Send to Application Insights
                telemetry.TrackTrace("LUIS", ApplicationInsights.DataContracts.SeverityLevel.Information, logProperties);
    
            }
        }
    }
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights instrumentatie sleutel toevoegen 

Als u gegevens wilt toevoegen aan Application Insights, hebt u de instrumentatie sleutel nodig.

1. Ga in een browser naar het [Azure Portal](https://portal.azure.com)en zoek de **Application Insights** resource van uw bot. De naam heeft de meeste naam van de bot, en wille keurige tekens aan het einde van de naam, bijvoorbeeld `luis-csharp-bot-johnsmithxqowom`. 
1. Kopieer de **instrumentatie sleutel**op de pagina **overzicht** van de Application Insights resource.
1. Open in Visual Studio het bestand **appSettings. json** in de hoofdmap van het bot-project. Dit bestand bevat alle omgevings variabelen.
1. Voeg een nieuwe variabele toe `BotDevAppInsightsKey` met de waarde van uw instrumentatie sleutel. De waarde in moet tussen aanhalings tekens staan. 

## <a name="build-and-start-the-bot"></a>De bot bouwen en starten

1. In Visual Studio bouwt u de bot en voert u deze uit. 
1. Start de bot-emulator en open de bot. Deze [stap](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) is opgenomen in de vorige zelf studie.

1. Stel een vraag aan de bot. Deze [stap](luis-csharp-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) is opgenomen in de vorige zelf studie.

## <a name="view-luis-entries-in-application-insights"></a>LUIS-items weergeven in Application Insights

Open Application Insights om te zien van de LUIS-vermeldingen. Het kan enkele minuten duren voordat de gegevens in Application Insights worden weer gegeven.

1. Open in de [Azure Portal](https://portal.azure.com)de Application Insights resource van de bot. 
1. Wanneer de resource wordt geopend, selecteert u **zoeken** en zoekt u naar alle gegevens in de afgelopen **30 minuten** met het gebeurtenis type **tracering**. Selecteer de tracering met de naam **Luis**. 
1. De bot-en LUIS-informatie is beschikbaar onder **aangepaste eigenschappen**. 

    ![Aangepaste eigenschappen van LUIS controleren die zijn opgeslagen in Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-csharp.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Application Insights-query voor het doel, score en utterance
Application Insights biedt u de mogelijkheid om een query uit te voeren op de gegevens in de [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) -taal en deze te exporteren naar [Power bi](https://powerbi.microsoft.com). 

1. Selecteer **logboek (Analytics)** . Er wordt een nieuw venster geopend met een query aan de bovenkant en een venster van de tabel gegevens hieronder die. Als u databases hebt gebruikt, wordt deze rangschikking bekend is. De query vertegenwoordigt uw vorige gefilterde gegevens. De kolom **CustomDimensions** heeft de bot-en Luis-informatie.
1. Als u de hoogste intentie, Score en utterance wilt oplichten, voegt u het volgende toe boven de laatste regel (de `|top...` regel) in het query venster:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_topScoringIntent_Name)
    | extend score = todouble(customDimensions.LUIS_topScoringIntentScore)
    | extend utterance = tostring(customDimensions.LUIS_query)
    ```

1. Voer de query uit. De nieuwe kolommen topIntent, score en utterance zijn beschikbaar. Selecteer de kolom topIntent die u wilt sorteren.

Meer informatie over de [Kusto-query taal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) of [het exporteren van de gegevens naar Power bi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 


## <a name="learn-more-about-bot-framework"></a>Meer informatie over Bot Framework

Meer informatie over [Bot Framework](https://dev.botframework.com/).

## <a name="next-steps"></a>Volgende stappen

Andere informatie die u mogelijk wilt toevoegen aan de application insights-gegevens omvatten app-ID, versie-ID, datum laatste model, laatste datum van de trein, de laatste publicatiedatum. Deze waarden kunnen worden opgehaald uit de eind punt-URL (App-ID en versie-ID), of vanuit een API-aanroep van de ontwerp functie en vervolgens worden ingesteld in de bot-instellingen van de web-app en daar worden opgehaald.  

Als u van hetzelfde abonnement endpoint voor meer dan één LUIS-app gebruikmaakt, moet u ook de abonnements-ID en een eigenschap met de mededeling dat het een gedeelde sleutel opnemen.

> [!div class="nextstepaction"]
> [Meer informatie over de voorbeeld-uitingen](luis-how-to-add-example-utterances.md)

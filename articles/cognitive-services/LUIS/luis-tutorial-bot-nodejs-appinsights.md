---
title: 'Zelf studie: Application Insights, node. js-LUIS'
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
ms.openlocfilehash: b0bc4a93df7db7145accf2b485c45256f53c324d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498957"
---
# <a name="tutorial-add-luis-results-to-application-insights-from-a-bot-in-nodejs"></a>Zelf studie: LUIS-resultaten toevoegen aan Application Insights vanuit een bot in node. js
In deze zelf studie worden bot en Language Understanding informatie toegevoegd voor het [Application Insights](https://azure.microsoft.com/services/application-insights/) van telemetriegegevens. Wanneer u deze gegevens hebt, kunt u deze zoeken in de Kusto-taal of Power BI voor het analyseren, samen voegen en rapporteren van de intenties en entiteiten van de utterance in realtime. Met deze analyse kunt u bepalen of u de intenties en entiteiten van uw LUIS-app moet toevoegen of bewerken.

[!INCLUDE [Waiting for Bot refresh](./includes/wait-bot-upgrade.md)]

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Leg bot en taal informatie in Application Insights
> * Query Application Insights voor Language Understanding gegevens

## <a name="prerequisites"></a>Vereisten

* Een Azure bot service-bot, gemaakt met Application Insights ingeschakeld.
* Gedownloade bot-code uit de vorige bot- **[zelf studie](luis-nodejs-tutorial-bf-v4.md)** . 
* [Botemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)

Alle code in deze zelf studie is beschikbaar in de [github-opslag plaats Azure-samples language Understanding](https://github.com/Azure-Samples/cognitive-services-language-understanding/tree/master/documentation-samples/tutorial-web-app-bot-application-insights/v4/luis-nodejs-bot-johnsmith-src-telemetry). 

## <a name="add-application-insights-to-web-app-bot-project"></a>Application Insights toevoegen aan Web app bot-project
Op dit moment verzamelt de Application Insights-service, die wordt gebruikt in deze web-app-bot, algemene status-telemetrie voor de bot. Er worden geen LUIS-gegevens verzameld. 

Voor het vastleggen van de LUIS-informatie **[Application Insights](https://www.npmjs.com/package/applicationinsights)** moet het NPM-pakket van de web-app zijn geïnstalleerd en geconfigureerd.  

1. Voeg in de geïntegreerde VSCode-Terminal, in de hoofdmap voor het bot-project, de volgende NPM-pakketten toe met behulp van de opdracht die wordt weer gegeven: 

    ```console
    npm install applicationinsights && npm install underscore
    ```
    
    Het **onderstrepings** pakket wordt gebruikt voor het afvlakken van de Luis JSON-structuur, zodat deze eenvoudiger te zien is en in Application Insights kan worden gebruikt.
    


## <a name="capture-and-send-luis-query-results-to-application-insights"></a>LUIS-query resultaten vastleggen en verzenden naar Application Insights

1. Maak in VSCode een nieuw bestand **appInsightsLog. js** en voeg de volgende code toe:

    ```javascript
    const appInsights = require('applicationinsights');
    const _ = require("underscore");
    
    // Log LUIS results to Application Insights
    // must flatten as name/value pairs
    var appInsightsLog = (botContext,luisResponse) => {

        appInsights.setup(process.env.MicrosoftApplicationInsightsInstrumentationKey).start();
        const appInsightsClient = appInsights.defaultClient;

        // put bot context and LUIS results into single object
        var data = Object.assign({}, {'botContext': botContext._activity}, {'luisResponse': luisResponse});
    
        // Flatten data into name/value pairs
        flatten = (x, result, prefix) => {
            if(_.isObject(x)) {
                _.each(x, (v, k) => {
                    flatten(v, result, prefix ? prefix + '_' + k : k)
                })
            } else {
                result["LUIS_" + prefix] = x
            }
            return result;
        }
    
        // call fn to flatten data
        var flattenedData = flatten(data, {});
    
        // ApplicationInsights Trace 
        console.log(JSON.stringify(flattenedData));
    
        // send data to Application Insights
        appInsightsClient.trackTrace({message: "LUIS", severity: appInsights.Contracts.SeverityLevel.Information, properties: flattenedData});
    }
    
    module.exports.appInsightsLog = appInsightsLog;
    ```

    In dit bestand worden de bot-context en het Luis-antwoord gebruikt, worden beide objecten afgevlakt en ingevoegd in een **tracerings** gebeurtenis in Application Insights. De naam van de gebeurtenis is **Luis**. 

1. Open de map **dialogen** en vervolgens het bestand **luisHelper. js** . Voeg het nieuwe **appInsightsLog. js** toe als een vereist bestand en leg de bot-context en het Luis-antwoord vast. De volledige code voor dit bestand is: 

    ```javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    const { appInsightsLog } = require('../appInsightsLog');
    
    class LuisHelper {
        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {*} logger
         * @param {TurnContext} context
         */
        static async executeLuisQuery(logger, context) {
            const bookingDetails = {};
    
            try {
                const recognizer = new LuisRecognizer({
                    applicationId: process.env.LuisAppId,
                    endpointKey: process.env.LuisAPIKey,
                    endpoint: `https://${ process.env.LuisAPIHostName }`
                }, {}, true);
    
                const recognizerResult = await recognizer.recognize(context);
    
                // APPINSIGHT: Log results to Application Insights
                appInsightsLog(context,recognizerResult);
    
    
                const intent = LuisRecognizer.topIntent(recognizerResult);
    
                bookingDetails.intent = intent;
    
                if (intent === 'Book_flight') {
                    // We need to get the result from the LUIS JSON which at every level returns an array
    
                    bookingDetails.destination = LuisHelper.parseCompositeEntity(recognizerResult, 'To', 'Airport');
                    bookingDetails.origin = LuisHelper.parseCompositeEntity(recognizerResult, 'From', 'Airport');
    
                    // This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
                    // TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
                    bookingDetails.travelDate = LuisHelper.parseDatetimeEntity(recognizerResult);
                }
            } catch (err) {
                logger.warn(`LUIS Exception: ${ err } Check your LUIS configuration`);
            }
            return bookingDetails;
        }
    
        static parseCompositeEntity(result, compositeName, entityName) {
            const compositeEntity = result.entities[compositeName];
            if (!compositeEntity || !compositeEntity[0]) return undefined;
    
            const entity = compositeEntity[0][entityName];
            if (!entity || !entity[0]) return undefined;
    
            const entityValue = entity[0][0];
            return entityValue;
        }
    
        static parseDatetimeEntity(result) {
            const datetimeEntity = result.entities['datetime'];
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;
    
            const timex = datetimeEntity[0]['timex'];
            if (!timex || !timex[0]) return undefined;
    
            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }
    
    module.exports.LuisHelper = LuisHelper;
    ```

## <a name="add-application-insights-instrumentation-key"></a>Application Insights instrumentatie sleutel toevoegen 

Als u gegevens wilt toevoegen aan Application Insights, hebt u de instrumentatie sleutel nodig.

1. Ga in een browser naar het [Azure Portal](https://portal.azure.com)en zoek de **Application Insights** resource van uw bot. De naam heeft de meeste naam van de bot, en wille keurige tekens aan het einde van de naam, bijvoorbeeld `luis-nodejs-bot-johnsmithxqowom`. 
1. Kopieer de **instrumentatie sleutel**op de pagina **overzicht** van de Application Insights resource.
1. Open in VSCode het **. env** -bestand in de hoofdmap van het bot-project. Dit bestand bevat alle omgevings variabelen.  
1. Voeg een nieuwe variabele toe `MicrosoftApplicationInsightsInstrumentationKey` met de waarde van uw instrumentatie sleutel. Plaats de waarde in aanhalings tekens niet. 

## <a name="start-the-bot"></a>De bot starten

1. Op de geïntegreerde VSCode-Terminal start u de bot:
    
    ```console
    npm start
    ```

1. Start de bot-emulator en open de bot. Deze [stap](luis-nodejs-tutorial-bf-v4.md#use-the-bot-emulator-to-test-the-bot) is opgenomen in de vorige zelf studie.

1. Stel een vraag aan de bot. Deze [stap](luis-nodejs-tutorial-bf-v4.md#ask-bot-a-question-for-the-book-flight-intent) is opgenomen in de vorige zelf studie.

## <a name="view-luis-entries-in-application-insights"></a>LUIS-vermeldingen in Application Insights weer geven

Open Application Insights om de LUIS-vermeldingen weer te geven. Het kan enkele minuten duren voordat de gegevens in Application Insights worden weer gegeven.

1. Open in de [Azure Portal](https://portal.azure.com)de Application Insights resource van de bot. 
1. Wanneer de resource wordt geopend, selecteert u **zoeken** en zoekt u naar alle gegevens in de afgelopen **30 minuten** met het gebeurtenis type **tracering**. Selecteer de tracering met de naam **Luis**. 
1. De bot-en LUIS-informatie is beschikbaar onder **aangepaste eigenschappen**. 

    ![Aangepaste eigenschappen van LUIS controleren die zijn opgeslagen in Application Insights](./media/luis-tutorial-appinsights/application-insights-luis-trace-custom-properties-nodejs.png)

## <a name="query-application-insights-for-intent-score-and-utterance"></a>Query Application Insights voor intentie, Score en utterance
Application Insights biedt u de mogelijkheid om een query uit te voeren op de gegevens in de [Kusto](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview#what-language-do-log-queries-use) -taal en deze te exporteren naar [Power bi](https://powerbi.microsoft.com). 

1. Selecteer **logboek (Analytics)** . Er wordt een nieuw venster geopend met een query venster bovenaan en een gegevens tabel venster hieronder. Als u data bases eerder hebt gebruikt, is deze overeenkomst bekend. De query vertegenwoordigt uw vorige gefilterde gegevens. De kolom **CustomDimensions** heeft de bot-en Luis-informatie.
1. Als u de hoogste intentie, Score en utterance wilt oplichten, voegt u het volgende toe boven de laatste regel (de `|top...` regel) in het query venster:

    ```kusto
    | extend topIntent = tostring(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_intent)
    | extend score = todouble(customDimensions.LUIS_luisResponse_luisResult_topScoringIntent_score)
    | extend utterance = tostring(customDimensions.LUIS_luisResponse_text)
    ```

1. Voer de query uit. De nieuwe kolommen van topIntent, Score en utterance zijn beschikbaar. Selecteer de kolom topIntent die u wilt sorteren.

Meer informatie over de [Kusto-query taal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries) of [het exporteren van de gegevens naar Power bi](https://docs.microsoft.com/azure/application-insights/app-insights-export-power-bi). 

## <a name="next-steps"></a>Volgende stappen

Andere informatie die u mogelijk wilt toevoegen aan de Application Insights-gegevens omvat app-ID, versie-ID, laatste model wijzigings datum, laatste Train datum, laatste publicatie datum. Deze waarden kunnen worden opgehaald uit de eind punt-URL (App-ID en versie-ID), of vanuit een API-aanroep van de ontwerp functie en vervolgens worden ingesteld in de bot-instellingen van de web-app en daar worden opgehaald.  

Als u hetzelfde eindpunt abonnement gebruikt voor meer dan één LUIS-app, moet u ook de abonnements-ID en een eigenschap die aangeeft dat het een gedeelde sleutel is, bevatten. 

> [!div class="nextstepaction"]
> [Meer informatie over voorbeeld uitingen](luis-how-to-add-example-utterances.md)

---
title: 'Zelfstudie: Language Understanding bot C# v4'
titleSuffix: Azure Cognitive Services
description: Bouw met behulp van C# een chatbot met ingebouwd taalbegrip (LUIS). De bot is gebouwd met de bot Framework versie 4 en de Azure web app bot-service.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 79f75fbae9a13db985f46408145c7d6762ff56b9
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70375589"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-c"></a>Zelfstudie: Een web-app-bot gebruiken die is ingeschakeld met Language Understanding inC#

Gebruik C# om een chat-bot te maken die is geïntegreerd met language Standing (Luis). De bot is gebouwd met de Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) -resource en [bot Framework-versie](https://github.com/Microsoft/botbuilder-dotnet) v4.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een web-app-bot maken. Bij dit proces wordt een nieuwe LUIS-app gemaakt.
> * Het bot-project downloaden dat is gemaakt door de Web bot service
> * De bot en emulator lokaal op uw computer starten
> * De uitingsresultaten in de bot bekijken

## <a name="prerequisites"></a>Vereisten

* [Botemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio](https://visualstudio.microsoft.com/downloads/)


## <a name="create-a-web-app-bot-resource"></a>Een web-app-bot-resource maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Nieuwe resource maken**.

1. Zoek via het zoekvak naar **Web-app-bot** en selecteer deze optie. Selecteer **Maken**.

1. In **Bot Service** geeft u de vereiste informatie op:

    |Instelling|Doel|Aanbevolen instelling|
    |--|--|--|
    |Botnaam|Resourcenaam|`luis-csharp-bot-` + `<your-name>`, bijvoorbeeld, `luis-csharp-bot-johnsmith`|
    |Subscription|Het abonnement waarvoor de bot moet worden gemaakt.|Uw primaire abonnement.
    |Resource group|Logische groep van Azure-resources|Maak een nieuwe groep voor het opslaan van alle resources die worden gebruikt met deze bot. Geef de groep de naam `luis-csharp-bot-resource-group`.|
    |Location|Azure-regio: deze hoeft niet dezelfde te zijn als de LUIS-regio voor maken en publiceren.|`westus`|
    |Prijscategorie|Wordt gebruikt voor serviceaanvraaglimieten en facturatie.|`F0` is de gratis laag.
    |Naam van app|Deze naam wordt gebruikt als subdomein wanneer uw bot wordt geïmplementeerd in de cloud (bijvoorbeeld humanresourcesbot.azurewebsites.net).|`luis-csharp-bot-` + `<your-name>`, bijvoorbeeld, `luis-csharp-bot-johnsmith`|
    |Botsjabloon|Instellingen voor het botframework - zie de volgende tabel|
    |Locatie van LUIS-app|Moet hetzelfde zijn als de regio waarin de LUIS-resource zich bevindt|`westus`|
    |App service-plan/-locatie|Wijzig niet van de beschik bare standaard waarde.|
    |Application Insights|Wijzig niet van de beschik bare standaard waarde.|
    |Micro soft App-ID en-wacht woord|Wijzig niet van de beschik bare standaard waarde.|

1. Selecteer in de **bot-sjabloon**het volgende en kies vervolgens de knop **selecteren** onder deze instellingen:

    |Instelling|Doel|Selectie|
    |--|--|--|
    |SDK-versie|Versie van het botframework|**SDK v4**|
    |SDK-taal|Computertaal van de bot|**C#**|
    |Bot|Type bot|**Basisbot**|
    
1. Selecteer **Maken**. Hiermee maakt u de botservice en implementeert u deze in Azure. Bij dit proces wordt een LUIS-app gemaakt met de naam `luis-csharp-bot-XXXX`. Deze naam is gebaseerd op de naam van de/Azure bot service-app.

    [![Een web-app-bot maken](./media/bfv4-csharp/create-web-app-service.png)](./media/bfv4-csharp/create-web-app-service.png#lightbox)

    Wacht tot de bot-service is gemaakt voordat u doorgaat.

## <a name="the-bot-has-a-language-understanding-model"></a>De bot heeft een Language Understanding model

Tijdens het maken van de bot-service wordt ook een nieuwe LUIS-app gemaakt met intents en voor beeld-uitingen. Met de bot kunnen de volgende intenties worden toegewezen aan de nieuwe LUIS-app: 

|LUIS-intenties van de basisbot|voorbeelduiting|
|--|--|
|Boek vlucht|`Travel to Paris`|
|Annuleren|`bye`|
|Geen|Alles buiten het domein van de app.|

## <a name="test-the-bot-in-web-chat"></a>De bot in Web Chat testen

1. Selecteer in de Azure Portal voor de nieuwe bot nog **testen in Web Chat**. 
1. Voer in het tekstvak **type uw bericht** de tekst `hello`in. De bot reageert op informatie over het bot-Framework, evenals voor beelden van query's voor het specifieke LUIS-model, zoals het reserveren van een vlucht naar Parijs. 

    ![Scherm opname van Azure Portal, voer de tekst ' Hello ' in.](./media/bfv4-csharp/ask-bot-question-in-portal-test-in-web-chat.png)

    U kunt de test functionaliteit gebruiken om snel uw bot te testen. Down load de bot-code en gebruik Visual Studio als u meer tests wilt uitvoeren, inclusief fout opsporing. 

## <a name="download-the-web-app-bot-source-code"></a>De bron code voor de web-app-bot downloaden
Als u de code van de web-app-bot verder wilt ontwikkelen, downloadt u de code en gebruikt u deze op uw lokale computer. 

1. In de Azure-portal selecteert u **Bouwen** in het gedeelte **Botbeheer**. 

1. Selecteer **Broncode bot downloaden**. 

    [![Download de broncode van de web-app-bot voor de basisbot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Wanneer het pop-updialoogvenster de **app-instellingen in het gedownloade zip-bestand**opvraagt, selecteert u **Ja**.

1. Als de broncode is ingepakt, ziet u een bericht met een link voor het downloaden van de code. Selecteer de link. 

1. Sla het zip-bestand op uw lokale computer op en pak de bestanden uit. Open het project met Visual Studio. 

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Code controleren om utterance te verzenden naar LUIS en antwoord te ontvangen

1. Open het **LuisHelper.cs** -bestand. Hier wordt de uiting die de gebruiker heeft ingevoerd in de bot naar LUIS verzonden. Het antwoord van LUIS wordt geretourneerd van de methode als een **BookDetails** -object. Wanneer u uw eigen bot maakt, moet u ook uw eigen object maken om de details van LUIS te retour neren. 


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
        }
    }
    ```

1. Open **BookingDetails.cs** om te zien hoe het object de Luis-informatie samen vattingt. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    namespace Microsoft.BotBuilderSamples
    {
        public class BookingDetails
        {
            public string Destination { get; set; }
    
            public string Origin { get; set; }
    
            public string TravelDate { get; set; }
        }
    }
    ```

1. Open **dialoog vensters-> BookingDialog.cs** om te begrijpen hoe het BookingDetails-object wordt gebruikt voor het beheren van de conversatie stroom. De reis gegevens worden in stappen gesteld, waarna de volledige boeking wordt bevestigd en tot slot terugkeert naar de gebruiker. 

    ```csharp
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Bot.Builder;
    using Microsoft.Bot.Builder.Dialogs;
    using Microsoft.Recognizers.Text.DataTypes.TimexExpression;
    
    namespace Microsoft.BotBuilderSamples.Dialogs
    {
        public class BookingDialog : CancelAndHelpDialog
        {
            public BookingDialog()
                : base(nameof(BookingDialog))
            {
                AddDialog(new TextPrompt(nameof(TextPrompt)));
                AddDialog(new ConfirmPrompt(nameof(ConfirmPrompt)));
                AddDialog(new DateResolverDialog());
                AddDialog(new WaterfallDialog(nameof(WaterfallDialog), new WaterfallStep[]
                {
                    DestinationStepAsync,
                    OriginStepAsync,
                    TravelDateStepAsync,
                    ConfirmStepAsync,
                    FinalStepAsync,
                }));
    
                // The initial child Dialog to run.
                InitialDialogId = nameof(WaterfallDialog);
            }
    
            private async Task<DialogTurnResult> DestinationStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                if (bookingDetails.Destination == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where would you like to travel to?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Destination, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> OriginStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Destination = (string)stepContext.Result;
    
                if (bookingDetails.Origin == null)
                {
                    return await stepContext.PromptAsync(nameof(TextPrompt), new PromptOptions { Prompt = MessageFactory.Text("Where are you traveling from?") }, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.Origin, cancellationToken);
                }
            }
            private async Task<DialogTurnResult> TravelDateStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.Origin = (string)stepContext.Result;
    
                if (bookingDetails.TravelDate == null || IsAmbiguous(bookingDetails.TravelDate))
                {
                    return await stepContext.BeginDialogAsync(nameof(DateResolverDialog), bookingDetails.TravelDate, cancellationToken);
                }
                else
                {
                    return await stepContext.NextAsync(bookingDetails.TravelDate, cancellationToken);
                }
            }
    
            private async Task<DialogTurnResult> ConfirmStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                var bookingDetails = (BookingDetails)stepContext.Options;
    
                bookingDetails.TravelDate = (string)stepContext.Result;
    
                var msg = $"Please confirm, I have you traveling to: {bookingDetails.Destination} from: {bookingDetails.Origin} on: {bookingDetails.TravelDate}";
    
                return await stepContext.PromptAsync(nameof(ConfirmPrompt), new PromptOptions { Prompt = MessageFactory.Text(msg) }, cancellationToken);
            }
    
            private async Task<DialogTurnResult> FinalStepAsync(WaterfallStepContext stepContext, CancellationToken cancellationToken)
            {
                if ((bool)stepContext.Result)
                {
                    var bookingDetails = (BookingDetails)stepContext.Options;
    
                    return await stepContext.EndDialogAsync(bookingDetails, cancellationToken);
                }
                else
                {
                    return await stepContext.EndDialogAsync(null, cancellationToken);
                }
            }
    
            private static bool IsAmbiguous(string timex)
            {
                var timexProperty = new TimexProperty(timex);
                return !timexProperty.Types.Contains(Constants.TimexTypes.Definite);
            }
        }
    }
    ```


## <a name="start-the-bot-code-in-visual-studio"></a>De bot-code starten in Visual Studio

Start de bot in Visual Studio. Er wordt een browservenster geopend met de website van de web-app-bot op `http://localhost:3978/`. Er wordt een start pagina weer gegeven met informatie over uw bot.

![Er wordt een start pagina weer gegeven met informatie over uw bot.](./media/bfv4-csharp/running-bot-web-home-page-success.png)

## <a name="use-the-bot-emulator-to-test-the-bot"></a>De bot-emulator gebruiken om de bot te testen

1. Start de bot-emulator en selecteer **Open bot**.
1. Voer in het pop-upvenster **een bot openen** uw bot-URL in, bijvoorbeeld `http://localhost:3978/api/messages`. De `/api/messages` route is het webadres voor de bot.
1. Voer de **micro soft app-id** en het **micro soft app-wacht woord**in die u hebt gevonden in het bestand **appSettings. json** in de hoofdmap van de bot-code die u hebt gedownload.

    U kunt desgewenst een nieuwe bot-configuratie maken en de `appId` en `appPassword` kopiëren van het bestand **appSettings. json** in het Visual Studio-project voor de bot. De naam van het bot-configuratie bestand moet hetzelfde zijn als de naam van de bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from appsettings.json>",
                "appPassword": "<appPassword from appsettings.json>",
                "endpoint": "http://localhost:3978/api/messages",
                "id": "<don't change this value>",
                "name": "http://localhost:3978/api/messages"
            }
        ],
        "padlock": "",
        "version": "2.0",
        "overrides": null,
        "path": "<local path to .bot file>"
    }
    ```

1. Voer `Hello` in de bot-emulator hetzelfde antwoord in voor de Basic-bot die u hebt ontvangen tijdens de **test in Web Chat**.

    [![Antwoord van de basisbot in de emulator](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-csharp/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Vraag bot een vraag voor de bedoeling van de boek vlucht

1. Boek in de bot-emulator een vlucht door de volgende utterance in te voeren: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    De bot-emulator vraagt om bevestiging. 

1. Selecteer **Ja**. De bot reageert met een overzicht van de bijbehorende acties. 
1. Selecteer de regel die is opgenomen `Luis Trace`in het logboek van de bot-emulator. Hiermee wordt het JSON-antwoord van LUIS voor de intentie en entiteiten van de utterance weer gegeven.

    [![Antwoord van de basisbot in de emulator](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-csharp/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)

[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk meer [voorbeelden](https://github.com/microsoft/botframework-solutions) met gespreksbots. 

> [!div class="nextstepaction"]
> [Een Language Understanding-app bouwen met een aangepast onderwerps domein](luis-quickstart-intents-only.md)

---
title: Language Understanding bot-node. js v4
titleSuffix: Azure Cognitive Services
description: Bouw met behulp van Node.js een chatbot met ingebouwd taalbegrip (LUIS). Deze chatbot maakt gebruik van de app Human Resources om snel een botoplossing te implementeren. De bot is gebouwd met Bot Framework versie 4 en de Azure-web-app-bot.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: diberry
ms.openlocfilehash: 8455a9c9ecff89643e090f1d763a44f97f5779f5
ms.sourcegitcommit: 5f67772dac6a402bbaa8eb261f653a34b8672c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/01/2019
ms.locfileid: "70206893"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Zelfstudie: Een web-app-bot gebruiken die is ingeschakeld met Language Understanding in node. js 

Gebruik node. js om een chat-bot te maken die is geïntegreerd met language Standing (LUIS). De bot is gebouwd met de Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) -resource en [bot Framework-versie](https://github.com/Microsoft/botbuilder-dotnet) v4.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een web-app-bot maken. Bij dit proces wordt een nieuwe LUIS-app gemaakt.
> * Het bot-project downloaden dat is gemaakt door de Web bot service
> * De bot en emulator lokaal op uw computer starten
> * De uitingsresultaten in de bot bekijken

## <a name="prerequisites"></a>Vereisten

* [Botemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio Code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Een web-app-bot-resource maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Nieuwe resource maken**.

1. Zoek via het zoekvak naar **Web-app-bot** en selecteer deze optie. Selecteer **Maken**.

1. In **Bot Service** geeft u de vereiste informatie op:

    |Instelling|Doel|Aanbevolen instelling|
    |--|--|--|
    |Botnaam|Resourcenaam|`luis-nodejs-bot-` + `<your-name>`, bijvoorbeeld, `luis-nodejs-bot-johnsmith`|
    |Subscription|Het abonnement waarvoor de bot moet worden gemaakt.|Uw primaire abonnement.
    |Resource group|Logische groep van Azure-resources|Maak een nieuwe groep voor het opslaan van alle resources die worden gebruikt met deze bot. Geef de groep de naam `luis-nodejs-bot-resource-group`.|
    |Location|Azure-regio: deze hoeft niet dezelfde te zijn als de LUIS-regio voor maken en publiceren.|`westus`|
    |Prijscategorie|Wordt gebruikt voor serviceaanvraaglimieten en facturatie.|`F0` is de gratis laag.
    |Naam van app|Deze naam wordt gebruikt als subdomein wanneer uw bot wordt geïmplementeerd in de cloud (bijvoorbeeld humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, bijvoorbeeld, `luis-nodejs-bot-johnsmith`|
    |Botsjabloon|Instellingen voor het botframework - zie de volgende tabel|
    |Locatie van LUIS-app|Moet hetzelfde zijn als de regio waarin de LUIS-resource zich bevindt|`westus`|
    |App service-plan/-locatie|Wijzig niet van de beschik bare standaard waarde.|
    |Application Insights|Wijzig niet van de beschik bare standaard waarde.|
    |Micro soft App-ID en-wacht woord|Wijzig niet van de beschik bare standaard waarde.|

1. Selecteer in de **bot-sjabloon**het volgende en kies vervolgens de knop **selecteren** onder deze instellingen:

    |Instelling|Doel|Selectie|
    |--|--|--|
    |SDK-versie|Versie van het botframework|**SDK v4**|
    |SDK-taal|Computertaal van de bot|**Node.js**|
    |Bot|Type bot|**Basisbot**|
    
1. Selecteer **Maken**. Hiermee maakt u de botservice en implementeert u deze in Azure. Bij dit proces wordt een LUIS-app gemaakt met de naam `luis-nodejs-bot-XXXX`. Deze naam is gebaseerd op de naam van de/Azure bot service-app.

    [![Een web-app-bot maken](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

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

    ![Scherm opname van Azure Portal, voer de tekst ' Hello ' in.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

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

1. Open de **dialoog vensters-> luisHelper. js-** bestand. Hier wordt de uiting die de gebruiker heeft ingevoerd in de bot naar LUIS verzonden. Het antwoord van LUIS wordt geretourneerd van de methode als een **bookDetails** JSON-object. Wanneer u uw eigen bot maakt, moet u ook uw eigen object maken om de details van LUIS te retour neren. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { LuisRecognizer } = require('botbuilder-ai');
    
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

1. Open **dialogen-> bookingDialog. js** om te begrijpen hoe het BookingDetails-object wordt gebruikt voor het beheren van de conversatie stroom. De reis gegevens worden in stappen gesteld, waarna de volledige boeking wordt bevestigd en tot slot terugkeert naar de gebruiker. 

    ```nodejs
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.
    
    const { TimexProperty } = require('@microsoft/recognizers-text-data-types-timex-expression');
    const { ConfirmPrompt, TextPrompt, WaterfallDialog } = require('botbuilder-dialogs');
    const { CancelAndHelpDialog } = require('./cancelAndHelpDialog');
    const { DateResolverDialog } = require('./dateResolverDialog');
    
    const CONFIRM_PROMPT = 'confirmPrompt';
    const DATE_RESOLVER_DIALOG = 'dateResolverDialog';
    const TEXT_PROMPT = 'textPrompt';
    const WATERFALL_DIALOG = 'waterfallDialog';
    
    class BookingDialog extends CancelAndHelpDialog {
        constructor(id) {
            super(id || 'bookingDialog');
    
            this.addDialog(new TextPrompt(TEXT_PROMPT))
                .addDialog(new ConfirmPrompt(CONFIRM_PROMPT))
                .addDialog(new DateResolverDialog(DATE_RESOLVER_DIALOG))
                .addDialog(new WaterfallDialog(WATERFALL_DIALOG, [
                    this.destinationStep.bind(this),
                    this.originStep.bind(this),
                    this.travelDateStep.bind(this),
                    this.confirmStep.bind(this),
                    this.finalStep.bind(this)
                ]));
    
            this.initialDialogId = WATERFALL_DIALOG;
        }
    
        /**
         * If a destination city has not been provided, prompt for one.
         */
        async destinationStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            if (!bookingDetails.destination) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'To what city would you like to travel?' });
            } else {
                return await stepContext.next(bookingDetails.destination);
            }
        }
    
        /**
         * If an origin city has not been provided, prompt for one.
         */
        async originStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the response to the previous step's prompt
            bookingDetails.destination = stepContext.result;
            if (!bookingDetails.origin) {
                return await stepContext.prompt(TEXT_PROMPT, { prompt: 'From what city will you be travelling?' });
            } else {
                return await stepContext.next(bookingDetails.origin);
            }
        }
    
        /**
         * If a travel date has not been provided, prompt for one.
         * This will use the DATE_RESOLVER_DIALOG.
         */
        async travelDateStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.origin = stepContext.result;
            if (!bookingDetails.travelDate || this.isAmbiguous(bookingDetails.travelDate)) {
                return await stepContext.beginDialog(DATE_RESOLVER_DIALOG, { date: bookingDetails.travelDate });
            } else {
                return await stepContext.next(bookingDetails.travelDate);
            }
        }
    
        /**
         * Confirm the information the user has provided.
         */
        async confirmStep(stepContext) {
            const bookingDetails = stepContext.options;
    
            // Capture the results of the previous step
            bookingDetails.travelDate = stepContext.result;
            const msg = `Please confirm, I have you traveling to: ${ bookingDetails.destination } from: ${ bookingDetails.origin } on: ${ bookingDetails.travelDate }.`;
    
            // Offer a YES/NO prompt.
            return await stepContext.prompt(CONFIRM_PROMPT, { prompt: msg });
        }
    
        /**
         * Complete the interaction and end the dialog.
         */
        async finalStep(stepContext) {
            if (stepContext.result === true) {
                const bookingDetails = stepContext.options;
    
                return await stepContext.endDialog(bookingDetails);
            } else {
                return await stepContext.endDialog();
            }
        }
    
        isAmbiguous(timex) {
            const timexPropery = new TimexProperty(timex);
            return !timexPropery.types.has('definite');
        }
    }
    
    module.exports.BookingDialog = BookingDialog;
    ```


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Afhankelijkheden installeren en de bot-code starten in Visual Studio

1. In VSCode kunt u vanuit de geïntegreerde Terminal afhankelijkheden installeren met behulp van de opdracht `npm install`.
1. U kunt ook vanaf de geïntegreerde Terminal de bot starten met de `npm start`opdracht. 


## <a name="use-the-bot-emulator-to-test-the-bot"></a>De bot-emulator gebruiken om de bot te testen

1. Start de bot-emulator en selecteer **Open bot**.
1. Voer in het pop-upvenster **een bot openen** uw bot-URL in, bijvoorbeeld `http://localhost:3978/api/messages`. De `/api/messages` route is het webadres voor de bot.
1. Voer de **micro soft app-id** en het **micro soft app-wacht woord**in die u in het **. env** -bestand hebt gevonden in de hoofdmap van de bot-code die u hebt gedownload.

    U kunt desgewenst een nieuwe bot-configuratie maken en het `MicrosoftAppId` en `MicrosoftAppPassword` kopiëren van het **. env** -bestand in het Visual Studio-project voor de bot. De naam van het bot-configuratie bestand moet hetzelfde zijn als de naam van de bot. 

    ```json
    {
        "name": "<bot name>",
        "description": "<bot description>",
        "services": [
            {
                "type": "endpoint",
                "appId": "<appId from .env>",
                "appPassword": "<appPassword from .env>",
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

    [![Antwoord van de basisbot in de emulator](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)


## <a name="ask-bot-a-question-for-the-book-flight-intent"></a>Vraag bot een vraag voor de bedoeling van de boek vlucht

1. Boek in de bot-emulator een vlucht door de volgende utterance in te voeren: 

    ```console
    Book a flight from Paris to Berlin on March 22, 2020
    ```

    De bot-emulator vraagt om bevestiging. 

1. Selecteer **Ja**. De bot reageert met een overzicht van de bijbehorende acties. 
1. Selecteer de regel die is opgenomen `Luis Trace`in het logboek van de bot-emulator. Hiermee wordt het JSON-antwoord van LUIS voor de intentie en entiteiten van de utterance weer gegeven.

    [![Antwoord van de basisbot in de emulator](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk meer [voorbeelden](https://github.com/microsoft/botframework-solutions) met gespreksbots. 

> [!div class="nextstepaction"]
> [Een Language Understanding-app bouwen met een aangepast onderwerps domein](luis-quickstart-intents-only.md)
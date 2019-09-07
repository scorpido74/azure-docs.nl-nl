---
title: 'Zelfstudie: Language Understanding bot-node. js v4'
titleSuffix: Azure Cognitive Services
description: Bouw met behulp van Node.js een chatbot met ingebouwd taalbegrip (LUIS). Deze chatbot maakt gebruik van de app Human Resources om snel een botoplossing te implementeren. De bot is gebouwd met Bot Framework versie 4 en de Azure-web-app-bot.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: diberry
ms.openlocfilehash: 63a0717e615ff85dbc5cfc06567f83cb9aa83a30
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735043"
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
|GetWeather|`what's the weather like?`|
|Geen|Alles buiten het domein van de app.|

## <a name="test-the-bot-in-web-chat"></a>De bot in Web Chat testen

1. Selecteer in de Azure Portal voor de nieuwe bot nog **testen in Web Chat**. 
1. Voer in het tekstvak **type uw bericht** de tekst `Book a flight from Seattle to Berlin tomorrow`in. De bot reageert met de verificatie dat u een vlucht wilt boeken. 

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

1. Als u de gebruiker utterance naar het LUIS-Voorspellings eindpunt wilt verzenden, opent u het bestand **Dialogs-> flightBookingRecognizer. js** . Hier wordt de uiting die de gebruiker heeft ingevoerd in de bot naar LUIS verzonden. Het antwoord van LUIS wordt geretourneerd door de methode **executeLuisQuery** .  

    ````javascript
    class FlightBookingRecognizer {

        ...

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        ...

    }
    ````

1. De **dialoog vensters-> mainDialog** legt de utterance vast en verzendt deze naar de executeLuisQuery in de actStep-methode.


    ````javascript
    class MainDialog extends ComponentDialog {

        ...

        /**
         * Second step in the waterfall.  This will use LUIS to attempt to extract the origin, destination and travel dates.
         * Then, it hands off to the bookingDialog child dialog to collect any remaining details.
         */
        async actStep(stepContext) {

            ...

            const luisResult = await this.luisRecognizer.executeLuisQuery(stepContext.context);

            switch (LuisRecognizer.topIntent(luisResult)) {
                    case 'BookFlight':
                        // Extract the values for the composite entities from the LUIS result.
                        const fromEntities = this.luisRecognizer.getFromEntities(luisResult);
                        const toEntities = this.luisRecognizer.getToEntities(luisResult);
            
                        // Show a warning for Origin and Destination if we can't resolve them.
                        await this.showWarningForUnsupportedCities(stepContext.context, fromEntities, toEntities);
            
                        // Initialize BookingDetails with any entities we may have found in the response.
                        bookingDetails.destination = toEntities.airport;
                        bookingDetails.origin = fromEntities.airport;
                        bookingDetails.travelDate = this.luisRecognizer.getTravelDate(luisResult);
                        console.log('LUIS extracted these booking details:', JSON.stringify(bookingDetails));
            
                        // Run the BookingDialog passing in whatever details we have from the LUIS call, it will fill out the remainder.
                        return await stepContext.beginDialog('bookingDialog', bookingDetails);
            
                    case 'GetWeather':
                        // We haven't implemented the GetWeatherDialog so we just display a TODO message.
                        const getWeatherMessageText = 'TODO: get weather flow here';
                        await stepContext.context.sendActivity(getWeatherMessageText, getWeatherMessageText, InputHints.IgnoringInput);
                        break;
            
                    default:
                        // Catch all for unhandled intents
                        const didntUnderstandMessageText = `Sorry, I didn't get that. Please try asking in a different way (intent was ${ LuisRecognizer.topIntent(luisResult) })`;
                        await stepContext.context.sendActivity(didntUnderstandMessageText, didntUnderstandMessageText, InputHints.IgnoringInput);
                    }
            
                    return await stepContext.next();

        }

        ...

    }
    ````


## <a name="install-dependencies-and-start-the-bot-code-in-visual-studio"></a>Afhankelijkheden installeren en de bot-code starten in Visual Studio

1. In VSCode kunt u vanuit de geïntegreerde Terminal afhankelijkheden installeren met behulp van de opdracht `npm install`.
1. U kunt ook vanaf de geïntegreerde Terminal de bot starten met de `npm start`opdracht. Hiermee wordt een web-app voor uw bot gestart met een HTTP-eind punt. De-console geeft de URL en het poort nummer voor toegang tot de actieve website. U hebt het poort nummer nodig in de volgende sectie van deze zelf studie.

    ```console
    > core-bot@1.0.0 start C:\Users\diberry\repos\bots\2019-bot-nodejs-basic
    > node ./index.js
    
    
    restify listening to http://[::]:3978
    
    Get Bot Framework Emulator: https://aka.ms/botframework-emulator
    ```

## <a name="create-an-environment-file-and-add-luis-values"></a>Een omgevings bestand maken en LUIS-waarden toevoegen

De bot-emulator moet toegang hebben tot uw LUIS-resource om gedetailleerde LUIS-resultaten te kunnen bieden.

1. Maak in de hoofdmap van het project een bestand met de `.env` naam en voeg de volgende omgevings variabelen toe:

    ```console
    LuisAppId= 
    LuisAPIKey=
    LuisAPIHostName=
    ```

1. Open vanuit de Azure Portal voor uw bot-resource de configuratie-instellingen van de App Service voor de toepassing.
1. Open **Geavanceerde bewerkingen**om de waarde voor elke instelling te bekijken.

    ![Open * * Geavanceerd bewerken * * om de waarde voor elke instelling weer te geven.](./media/bfv4-nodejs/environment-settings-for-luis-app.png)

<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>De bot-emulator gebruiken om de bot te testen

Vraag bot een vraag voor de bedoeling van de Book-vlucht.

1. Start de bot-emulator en selecteer **Open bot**.
1. Voer in het pop-upvenster **een bot openen** uw bot-URL in, bijvoorbeeld `http://localhost:3978/api/messages`. De `/api/messages` route is het webadres voor de bot.

1. Voer `Book a flight from Seattle to Berlin tomorrow` in de bot-emulator hetzelfde antwoord in voor de Basic-bot die u hebt ontvangen tijdens de **test in Web Chat**.

    [![Antwoord van de basisbot in de emulator](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecteer **Ja**. De bot reageert met een overzicht van de bijbehorende acties. 
1. Selecteer de regel die is opgenomen `Luis Trace`in het logboek van de bot-emulator. Hiermee wordt het JSON-antwoord van LUIS voor de intentie en entiteiten van de utterance weer gegeven.

    [![Antwoord van de basisbot in de emulator](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk meer [voorbeelden](https://github.com/microsoft/botframework-solutions) met gespreksbots. 

> [!div class="nextstepaction"]
> [Een Language Understanding-app bouwen met een aangepast onderwerps domein](luis-quickstart-intents-only.md)
---
title: 'Zelfstudie: Language Understanding Bot Node.js v4'
description: Met Behulp van Node.js, bouwen van een chat bot geïntegreerd met taal begrip (LUIS) in deze tutorial. Deze chatbot maakt gebruik van de app Human Resources om snel een botoplossing te implementeren. De bot is gebouwd met Bot Framework versie 4 en de Azure-web-app-bot.
ms.topic: tutorial
ms.date: 02/03/2020
ms.openlocfilehash: 3ce12176957412a5599ced8b043f553969194efb
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76987834"
---
# <a name="tutorial-use-a-web-app-bot-enabled-with-language-understanding-in-nodejs"></a>Zelfstudie: Gebruik een Web App Bot ingeschakeld met taalbegrip in Node.js

Gebruik Node.js om een chatbot te bouwen die is geïntegreerd met taalbegrip (LUIS). De bot is gebouwd met de Azure [Web app bot](https://docs.microsoft.com/azure/bot-service/) resource en Bot Framework [versie](https://github.com/Microsoft/botbuilder-dotnet) V4.

**In deze zelfstudie leert u het volgende:**

> [!div class="checklist"]
> * Een web-app-bot maken. Bij dit proces wordt een nieuwe LUIS-app gemaakt.
> * Download het botproject dat is gemaakt door de webbotservice
> * De bot en emulator lokaal op uw computer starten
> * De uitingsresultaten in de bot bekijken

## <a name="prerequisites"></a>Vereisten

* [Botemulator](https://aka.ms/abs/build/emulatordownload)
* [Visual Studio-code](https://code.visualstudio.com/Download)


## <a name="create-a-web-app-bot-resource"></a>Een botbron voor web-apps maken

1. Selecteer in de [Azure-portal](https://portal.azure.com) de optie **Nieuwe resource maken**.

1. Zoek via het zoekvak naar **Web-app-bot** en selecteer deze optie. Selecteer **Maken**.

1. In **Bot Service** geeft u de vereiste informatie op:

    |Instelling|Doel|Aanbevolen instelling|
    |--|--|--|
    |Botnaam|Resourcenaam|`luis-nodejs-bot-` + `<your-name>`, bijvoorbeeld, `luis-nodejs-bot-johnsmith`|
    |Abonnement|Het abonnement waarvoor de bot moet worden gemaakt.|Uw primaire abonnement.
    |Resourcegroep|Logische groep van Azure-resources|Maak een nieuwe groep voor het opslaan van alle resources die worden gebruikt met deze bot. Geef de groep de naam `luis-nodejs-bot-resource-group`.|
    |Locatie|Azure-regio: deze hoeft niet dezelfde te zijn als de LUIS-regio voor maken en publiceren.|`westus`|
    |Prijscategorie|Wordt gebruikt voor serviceaanvraaglimieten en facturatie.|`F0` is de gratis laag.
    |Naam van app|Deze naam wordt gebruikt als subdomein wanneer uw bot wordt geïmplementeerd in de cloud (bijvoorbeeld humanresourcesbot.azurewebsites.net).|`luis-nodejs-bot-` + `<your-name>`, bijvoorbeeld, `luis-nodejs-bot-johnsmith`|
    |Botsjabloon|Instellingen voor het botframework - zie de volgende tabel|
    |Locatie van LUIS-app|Moet hetzelfde zijn als de regio waarin de LUIS-resource zich bevindt|`westus`|
    |App-serviceplan/locatie|Wijzig niet van de opgegeven standaardwaarde.|
    |Application Insights|Wijzig niet van de opgegeven standaardwaarde.|
    |Microsoft App ID en wachtwoord|Wijzig niet van de opgegeven standaardwaarde.|

1. Selecteer in de **sjabloon Bot**het volgende en kies de knop **Selecteren** onder deze instellingen:

    |Instelling|Doel|Selectie|
    |--|--|--|
    |SDK-versie|Versie van het botframework|**SDK v4**|
    |SDK-taal|Computertaal van de bot|**Node.js**|
    |Bot|Type bot|**Basisbot**|

1. Selecteer **Maken**. Hiermee maakt u de botservice en implementeert u deze in Azure. Bij dit proces wordt een LUIS-app gemaakt met de naam `luis-nodejs-bot-XXXX`. Deze naam is gebaseerd op de naam van de /Azure Bot Service-app.

    > [!div class="mx-imgBorder"]
    > [![Web-app-bot maken](./media/bfv4-nodejs/create-web-app-service.png)](./media/bfv4-nodejs/create-web-app-service.png#lightbox)

    Wacht tot de botservice is gemaakt voordat u verdergaat.

## <a name="the-bot-has-a-language-understanding-model"></a>De bot heeft een taalbegripsmodel

Het proces voor het maken van botservice maakt ook een nieuwe LUIS-app met intents en voorbeelduitingen. Met de bot kunnen de volgende intenties worden toegewezen aan de nieuwe LUIS-app:

|LUIS-intenties van de basisbot|voorbeelduiting|
|--|--|
|Boek vlucht|`Travel to Paris`|
|Annuleren|`bye`|
|GetWeather GetWeather|`what's the weather like?`|
|Geen|Alles buiten het domein van de app.|

## <a name="test-the-bot-in-web-chat"></a>De bot testen in Web Chat

1. Terwijl u zich nog in de Azure-portal voor de nieuwe bot bevindt, selecteert u **Testen in webchat**.
1. Voer in het tekstvak Uw `Book a flight from Seattle to Berlin tomorrow`bericht **typen** de tekst in . De bot reageert met verificatie dat u een vlucht wilt boeken.

    ![Schermafbeelding van azure-portal, voer de tekst 'hallo' in.](./media/bfv4-nodejs/ask-bot-question-in-portal-test-in-web-chat.png)

    U de testfunctionaliteit gebruiken om uw bot snel te testen. Voor meer volledige tests, inclusief foutopsporing, download je de botcode en gebruik je Visual Studio.

## <a name="download-the-web-app-bot-source-code"></a>Download de broncode van de web-app bot
Als u de code van de web-app-bot verder wilt ontwikkelen, downloadt u de code en gebruikt u deze op uw lokale computer.

1. In de Azure-portal selecteert u **Bouwen** in het gedeelte **Botbeheer**.

1. Selecteer **Broncode bot downloaden**.

    [![Download web app bot broncode voor basisbot](../../../includes/media/cognitive-services-luis/bfv4/download-code.png)](../../../includes/media/cognitive-services-luis/bfv4/download-code.png#lightbox)

1. Wanneer in het pop-updialoogvenster wordt gevraagd **om app-instellingen opnemen in het gedownloade zip-bestand?** **Yes** Dit biedt de LUIS-instellingen.

1. Als de broncode is ingepakt, ziet u een bericht met een link voor het downloaden van de code. Selecteer de link.

1. Sla het zip-bestand op uw lokale computer op en pak de bestanden uit. Open het project met Visual Studio.

## <a name="review-code-to-send-utterance-to-luis-and-get-response"></a>Code controleren om utterance naar LUIS te verzenden en antwoord te krijgen

1. Als u de gebruiker wilt verzenden naar het EINDPUNT voor luis-voorspelling, opent u de **dialoogvensters -> flightBookingRecognizer.js-bestand.** Hier wordt de uiting die de gebruiker heeft ingevoerd in de bot naar LUIS verzonden. Het antwoord van LUIS wordt geretourneerd vanuit de **methode executeLuisQuery.**

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

1. De **dialogen -> mainDialog** legt de utterance vast en stuurt deze naar de executeLuisQuery in de actStep-methode.


    ````javascript
    // Copyright (c) Microsoft Corporation. All rights reserved.
    // Licensed under the MIT License.

    const { LuisRecognizer } = require('botbuilder-ai');

    class FlightBookingRecognizer {
        constructor(config) {
            const luisIsConfigured = config && config.applicationId && config.endpointKey && config.endpoint;
            if (luisIsConfigured) {
                this.recognizer = new LuisRecognizer(config, {}, true);
            }
        }

        get isConfigured() {
            return (this.recognizer !== undefined);
        }

        /**
         * Returns an object with preformatted LUIS results for the bot's dialogs to consume.
         * @param {TurnContext} context
         */
        async executeLuisQuery(context) {
            return await this.recognizer.recognize(context);
        }

        getFromEntities(result) {
            let fromValue, fromAirportValue;
            if (result.entities.$instance.From) {
                fromValue = result.entities.$instance.From[0].text;
            }
            if (fromValue && result.entities.From[0].Airport) {
                fromAirportValue = result.entities.From[0].Airport[0][0];
            }

            return { from: fromValue, airport: fromAirportValue };
        }

        getToEntities(result) {
            let toValue, toAirportValue;
            if (result.entities.$instance.To) {
                toValue = result.entities.$instance.To[0].text;
            }
            if (toValue && result.entities.To[0].Airport) {
                toAirportValue = result.entities.To[0].Airport[0][0];
            }

            return { to: toValue, airport: toAirportValue };
        }

        /**
         * This value will be a TIMEX. And we are only interested in a Date so grab the first result and drop the Time part.
         * TIMEX is a format that represents DateTime expressions that include some ambiguity. e.g. missing a Year.
         */
        getTravelDate(result) {
            const datetimeEntity = result.entities.datetime;
            if (!datetimeEntity || !datetimeEntity[0]) return undefined;

            const timex = datetimeEntity[0].timex;
            if (!timex || !timex[0]) return undefined;

            const datetime = timex[0].split('T')[0];
            return datetime;
        }
    }

    module.exports.FlightBookingRecognizer = FlightBookingRecognizer;
    ````
<a name="ask-bot-a-question-for-the-book-flight-intent"></a>

## <a name="use-the-bot-emulator-to-test-the-bot"></a>Gebruik de bot emulator om de bot te testen

Stel bot een vraag voor de bedoeling van de boekvlucht.

1. Begin de Bot Emulator en selecteer **Bot openen**.
1. Voer in het pop-updialoogvenster **Een bot** openen `http://localhost:3978/api/messages`de URL van uw bot in, zoals . De `/api/messages` route is het webadres voor de bot.
1. Voer het wachtwoord **microsoft-app-id** en **Microsoft App**in, gevonden in het **.env-bestand** in de hoofdmap van de botcode die u hebt gedownload.

1. Voer in de `Book a flight from Seattle to Berlin tomorrow` botemulator dezelfde respons in voor de basisbot als in de **test in Web Chat.**

    [![Basis bot reactie in emulator](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png)](./media/bfv4-nodejs/ask-bot-emulator-a-question-and-get-response.png#lightbox)

1. Selecteer **Ja**. De bot reageert met een samenvatting van zijn acties.
1. Selecteer in het logboek van de botemulator de regel die bevat. `Luis Trace` Hiermee wordt het JSON-antwoord van LUIS weergegeven voor de intentie en entiteiten van de utterance.

    [![Basis bot reactie in emulator](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png)](./media/bfv4-nodejs/ask-luis-book-flight-question-get-json-response-in-bot-emulator.png#lightbox)


[!INCLUDE [Bot Information](../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>Volgende stappen

Bekijk meer [voorbeelden](https://github.com/microsoft/botframework-solutions) met gespreksbots.

> [!div class="nextstepaction"]
> [Een app Taalbegrip maken met een aangepast onderwerpdomein](luis-quickstart-intents-only.md)
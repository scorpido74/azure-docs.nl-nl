---
title: 'Zelfstudie: Een ruimte controleren met Azure Digital Twins | Microsoft Docs'
description: Leer hoe u uw ruimtelijke resources kunt inrichten en de werkomstandigheden kunt bewaken met Azure Digital Twins door de stappen in deze zelfstudie te volgen.
services: digital-twins
author: alinamstanciu
ms.custom: seodec18
ms.service: digital-twins
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: alinast
ms.openlocfilehash: bdf37225e815d3848a87b88737daf4b5a5d2560c
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/26/2019
ms.locfileid: "71300053"
---
# <a name="tutorial-provision-your-building-and-monitor-working-conditions-with-azure-digital-twins-preview"></a>Zelfstudie: Uw bedrijfs voorwaarden inrichten en bewaken met Azure Digital Apparaatdubbels preview

In deze zelf studie wordt gedemonstreerd hoe u met Azure Digital Apparaatdubbels Preview uw Spaces bewaakt voor gewenste temperatuur omstandigheden en comfort niveau. Nadat u uw [voorbeeldgebouw hebt geconfigureerd](tutorial-facilities-setup.md), kunt u het gebouw inrichten en aangepaste functies uitvoeren voor uw sensorgegevens met behulp van de stappen in deze zelfstudie.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Werkomstandigheden definiëren die bewaakt moeten worden.
> * Een door de gebruiker gedefinieerde functie (UDF) maken.
> * Sensorgegevens simuleren.
> * Resultaten ophalen van een door de gebruiker gedefinieerde functie.

## <a name="prerequisites"></a>Vereisten

In deze zelfstudie wordt ervan uitgegaan dat u de [Azure Digital Twins-installatie hebt uitgevoerd](tutorial-facilities-setup.md). Voordat u doorgaat, moet u ervoor zorgen voor dat u beschikt over:

- Een [Azure-account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Een actief exemplaar van Digital Twins. 
- De gedownloade en uitgepakte [Digital Twins C#-voorbeelden](https://github.com/Azure-Samples/digital-twins-samples-csharp) op een werkcomputer. 
- [.NET Core SDK-versie 2.1.403 of hoger](https://www.microsoft.com/net/download) op een ontwikkelcomputer om het voorbeeld te bouwen en uit te voeren. Voer `dotnet --version` uit om te controleren of de juiste versie is geïnstalleerd. 
- [Visual Studio Code](https://code.visualstudio.com/) om de voorbeeldcode mee te verkennen. 

> [!TIP]
> Gebruik een unieke Digital Apparaatdubbels-exemplaar naam als u een nieuw exemplaar inricht.

## <a name="define-conditions-to-monitor"></a>Werkomstandigheden definiëren die gecontroleerd moeten worden

U kunt een reeks specifieke omstandigheden definiëren die in het apparaat of sensorgegevens moeten worden bewaakt. Deze worden *matchers* genoemd. Vervolgens kunt u functies definiëren die *door de gebruiker gedefinieerde functies* worden genoemd. Door de gebruiker gedefinieerde functies voeren aangepaste logica uit op gegevens die afkomstig zijn van uw ruimten en apparaten, wanneer de omstandigheden zich voordoen die overeenkomen met wat er als matchers is opgegeven. Lees [Gegevensverwerking en door gebruikers gedefinieerde functies](concepts-user-defined-functions.md) voor meer informatie. 

Open vanuit het voorbeeldproject **occupancy-quickstart** het bestand **src\actions\provisionSample.yaml** in Visual Studio Code. Let op de sectie die begint met het type **matchers**. Elk item onder dit type maakt een matcher met de opgegeven **Naam**. De matcher bewaakt een sensor van het type **dataTypeValue**. Kijk hoe deze zich verhoudt tot de ruimte met de naam *Focus Room A1*, die een knooppunt **devices** heeft waarin zich een aantal sensoren bevinden. Als u een matcher wilt inrichten om een van deze sensoren te volgen, moet u ervoor zorgen dat de bijbehorende **dataTypeValue** overeenkomt met het **dataType** van de sensor. 

Voeg de volgende matcher toe onder de bestaande matchers. Zorg ervoor dat de sleutels zijn uitgelijnd en ruimtes niet zijn vervangen door tabs. Deze regels zijn ook aanwezig in het bestand *provisionSample.yaml* in de vorm van commentaarregels. U kunt er coderegels van maken door het teken `#` aan het begin van elke regel te verwijderen.

```yaml
      - name: Matcher Temperature
        dataTypeValue: Temperature
```

Met deze overeenkomst wordt de `SAMPLE_SENSOR_TEMPERATURE` sensor gevolgd die u in [de eerste zelf studie](tutorial-facilities-setup.md)hebt toegevoegd. 

## <a name="create-a-user-defined-function"></a>Een door de gebruiker gedefinieerde functie maken

Met behulp van door gebruikers gedefinieerde functies kunt u aanpassen hoe uw sensorgegevens worden verwerkt. Ze bestaan uit aangepaste JavaScript-code die in uw Azure Digital Twins-instantie wordt uitgevoerd als er zich omstandigheden voordoen die door de matchers worden beschreven. U kunt matchers en door gebruikers gedefinieerde functies maken voor elke sensor die u wilt controleren. Lees [Gegevensverwerking en door gebruikers gedefinieerde functies](concepts-user-defined-functions.md) voor meer informatie. 

Zoek in het voor beeld *provisionSample. yaml* -bestand naar een sectie die begint met het type **userdefinedfunctions**. In deze sectie wordt een door de gebruiker gedefinieerde functie met een bepaalde **Naam** ingericht. Deze UDF werkt met de lijst met matchers onder **matcherNames**. Kijk ook hoe u een eigen JavaScript-kunt gebruiken als **script** voor de door de gebruiker gedefinieerde functie.

Let ook op de sectie met de naam **roleassignments**. Hiermee wordt de rol van Space Administrator toegewezen aan de door de gebruiker gedefinieerde functie. Met deze rol wordt toegang verkregen tot de gebeurtenissen die afkomstig zijn van een van de ingerichte ruimtes. 

1. Configureer de door de gebruiker gedefinieerde functie zodanig dat de temperatuurmatcher erin wordt opgenomen door de volgende regel in het knooppunt `matcherNames` aan het bestand *provisionSample.yaml* toe te voegen, of door ervoor te zorgen dat deze regel niet als opmerking wordt gezien:

    ```yaml
            - Matcher Temperature
    ```

1. Open het bestand **src\actions\userDefinedFunctions\availability.js** in uw editor. Dit is het bestand waarnaar wordt verwezen in het element **script** van *provisionSample. yaml*. Met de door de gebruiker gedefinieerde functie in dit bestand wordt gezocht naar omstandigheden waarbij geen beweging in de ruimte wordt gedetecteerd en waarbij het koolstofdioxideniveau zich onder 1000 ppm bevindt. 

   Wijzig het JavaScript-bestand zodat op temperatuur en andere omstandigheden kan worden gecontroleerd. Voeg de volgende regels code toe om te zoeken naar omstandigheden waarbij geen beweging wordt gedetecteerd in de kamer, het koolstofdioxideniveau lager is dan 1000 ppm en de temperatuur lager is dan 78 graden Fahrenheit.

   > [!NOTE]
   > In deze sectie wordt het bestand *src\actions\userDefinedFunctions\availability.js* gewijzigd, zodat u tot in detail kunt zien hoe u een door de gebruiker gedefinieerde functie kunt schrijven. U kunt er echter ook voor kiezen om het bestand [src\actions\userDefinedFunctions\availabilityForTutorial.js](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/userDefinedFunctions/availabilityForTutorial.js) rechtstreeks in uw installatie te gebruiken. Dit bestand bevat alle wijzigingen die u voor deze zelfstudie nodig hebt. Als u dit bestand gebruikt, moet u er wel voor zorgen dat u de juiste bestandsnaam gebruikt voor de **script**-sleutel in [src\actions\provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

    a. Boven in het bestand moet u onder de opmerking `// Add your sensor type here` de volgende regels voor de temperatuur toevoegen:

    ```JavaScript
        var temperatureType = "Temperature";
        var temperatureThreshold = 78;
    ```

    b. Voeg onder de opmerking `// Add your sensor variable here` de volgende regels toe na de instructie waarmee `var motionSensor` wordt gedefinieerd:

     ```JavaScript
        var temperatureSensor = otherSensors.find(function(element) {
            return element.DataType === temperatureType;
        });
    ```

    c. Voeg onder de opmerking `// Add your sensor latest value here` de volgende regel toe na de instructie waarmee `var carbonDioxideValue` wordt gedefinieerd:

    ```JavaScript
        var temperatureValue = getFloatValue(temperatureSensor.Value().Value);
    ```

    d. Verwijder de volgende regels code onder de opmerking `// Modify this line to monitor your sensor value`:

     ```JavaScript
        if(carbonDioxideValue === null || motionValue === null) {
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide or motion are null, returning");
            return;
        }
    ```

    Vervang deze door de volgende regels:

    ```JavaScript
        if(carbonDioxideValue === null || motionValue === null || temperatureValue === null){
            sendNotification(telemetry.SensorId, "Sensor", "Error: Carbon dioxide, motion, or temperature are null, returning");
            return;
        }
    ```

    e. Verwijder de volgende regels code onder de opmerking `// Modify these lines as per your sensor`:

    ```JavaScript
        var availableFresh = "Room is available and air is fresh";
        var noAvailableOrFresh = "Room is not available or air quality is poor";
    ```

    Vervang deze door de volgende regels:

    ```JavaScript
        var alert = "Room with fresh air and comfortable temperature is available.";
        var noAlert = "Either room is occupied, or working conditions are not right.";
    ```

    f. Verwijder het volgende *if-else*-codeblok na de opmerking `// Modify this code block for your sensor`:

    ```JavaScript
        // If carbonDioxide less than threshold and no presence in the room => log, notify and set parent space computed value
        if(carbonDioxideValue < carbonDioxideThreshold && !presence) {
            log(`${availableFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, availableFresh);
        }
        else {
            log(`${noAvailableOrFresh}. Carbon Dioxide: ${carbonDioxideValue}. Presence: ${presence}.`);
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAvailableOrFresh);

            // Set up custom notification for poor air quality
            parentSpace.Notify(JSON.stringify(noAvailableOrFresh));
        }
    ```

    En vervang dit door het volgende *if-else*-codeblok:

    ```JavaScript
        // If sensor values are within range and room is available
        if(carbonDioxideValue < carbonDioxideThreshold && temperatureValue < temperatureThreshold && !presence) {
            log(`${alert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, alert);

            // Set up notification for this alert
            parentSpace.Notify(JSON.stringify(alert));
        }
        else {
            log(`${noAlert}. Carbon Dioxide: ${carbonDioxideValue}. Temperature: ${temperatureValue}. Presence: ${presence}.`);

            // log, notify and set parent space computed value
            setSpaceValue(parentSpace.Id, spaceAvailFresh, noAlert);
        }
    ```

    Met de gewijzigde door de gebruiker gedefinieerde functie wordt gezocht naar omstandigheden waarbij een kamer beschikbaar wordt en het koolstofdioxideniveau en de temperatuur ervan binnen de toegestane limieten vallen. Er wordt een melding met de instructie `parentSpace.Notify(JSON.stringify(alert));` gegenereerd als deze omstandigheid zich voordoet. Met het bijbehorende bericht wordt de waarde van de gecontroleerde ruimte ingesteld, ongeacht of de omstandigheid zich voordoet of niet.

    g. Sla het bestand op.

1. Open een opdrachtvenster en ga naar de map **occupancy-quickstart\src**. Voer de volgende opdracht uit om de grafiek met gegevens over de ruimte en de door de gebruiker gedefinieerde functie in te richten:

    ```cmd/sh
    dotnet run ProvisionSample
    ```

   > [!IMPORTANT]
   > Om te voorkomen dat onbevoegden toegang hebben tot uw beheer-API van Digital Twins, wordt u door de toepassing **occupancy-quickstart** gedwongen u aan te melden met uw Azure-accountreferenties. Uw referenties blijven een korte periode bewaard, zodat u zich niet steeds hoeft aan te melden als u deze wilt uitvoeren. De eerste keer dat dit programma wordt uitgevoerd, en wanneer uw opgeslagen referenties daarna verlopen, wordt u omgeleid naar een aanmeldingspagina en ontvangt u een sessiespecifieke code die u op die pagina moet invoeren. Volg de aanwijzingen om u aan te melden met uw Azure-account.

1. Nadat uw account is geverifieerd, begint de toepassing met het maken van een ruimtelijke voorbeeld grafiek zoals geconfigureerd in *provisionSample. yaml*. Wacht tot het inrichtingsproces is voltooid. Dit duurt enkele minuten. Vervolgens bekijkt u de berichten in het opdrachtvenster en u kunt zien hoe uw ruimtelijke grafiek wordt gemaakt. Observeer hoe een IoT-hub wordt gemaakt op het hoofdknooppunt of de `Venue`.

1. Kopieer de waarde van de `ConnectionString` uit de uitvoer in het opdrachtvenster onder de sectie `Devices` naar het Klembord. U hebt deze waarde nodig om de apparaatverbinding in de volgende sectie te simuleren.

    [![Voor beeld van inrichting](./media/tutorial-facilities-udf/run-provision-sample.png)](./media/tutorial-facilities-udf/run-provision-sample.png#lightbox)

> [!TIP]
> Als er tijdens het inrichtingsproces een foutbericht wordt weergegeven dat lijkt op 'De I/O-bewerking is afgebroken vanwege een afgesloten thread of een toepassingsaanvraag', moet u de opdracht opnieuw uitvoeren. Dit kan gebeuren als er een time-out van de HTTP-client heeft plaatsgevonden vanwege een netwerkprobleem.

## <a name="simulate-sensor-data"></a>Sensorgegevens simuleren

In deze sectie gebruikt u het project met de naam *device-connectivity* in het voorbeeld. U gaat sensorgegevens voor het detecteren van beweging, temperatuur en kooldioxide simuleren. Door dit project worden willekeurige waarden voor de sensors gegenereerd en worden deze naar de IoT-hub verzonden met behulp van de verbindingsreeks van het apparaat.

1. Ga in een afzonderlijke opdrachtvenster naar het Azure Digital Twins-voorbeeld en ga van daaruit naar de map **device-connectivity**.

1. Voer de volgende opdracht uit om te controleren of de afhankelijkheden voor het project juist zijn:

    ```cmd/sh
    dotnet restore
    ```

1. Open het bestand [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) in een editor en bewerk de volgende waarden:

   a. **DeviceConnectionString**: wijs de waarde van `ConnectionString` toe in het uitvoervenster uit het vorige gedeelte. Kopieer de volledige tekenreeks (tussen de aanhalingstekens), zodat de simulator correct verbinding maakt met de IoT-hub.

   b. **HardwareId** binnen de matrix **Sensors**: Omdat u gebeurtenissen simuleert van Sens oren die zijn ingericht voor uw Azure Digital apparaatdubbels-exemplaar, moeten de hardware-id en de namen van de Sens oren in `sensors` dit bestand overeenkomen met het knoop punt van het bestand *provisionSample. yaml* .

      Voeg een nieuwe vermelding voor de temperatuursensor toe. Het knoop punt **Sens oren** in *appSettings. json* moet er als volgt uitzien:

      ```JSON
      "Sensors": [{
        "DataType": "Motion",
        "HardwareId": "SAMPLE_SENSOR_MOTION"
      },{
        "DataType": "CarbonDioxide",
        "HardwareId": "SAMPLE_SENSOR_CARBONDIOXIDE"
      },{
        "DataType": "Temperature",
        "HardwareId": "SAMPLE_SENSOR_TEMPERATURE"
      }]
      ```

1. Voer de volgende opdracht uit om te beginnen met het simuleren van apparaatgebeurtenissen voor temperatuur, beweging en kooldioxide:

    ```cmd/sh
    dotnet run
    ```

   > [!NOTE]
   > Omdat het simulatievoorbeeld niet rechtstreeks met uw instantie van Digital Twins communiceert, hoeft u zich niet te verifiëren.

## <a name="get-results-of-the-user-defined-function"></a>Resultaten ophalen van een door de gebruiker gedefinieerde functie

De door de gebruiker gedefinieerde functie wordt telkens uitgevoerd als uw instantie gegevens van het apparaat en de sensor ontvangt. In deze sectie voert de Azure Digital Twins-instantie een query uit om de resultaten van de door de gebruiker gedefinieerde functie op te halen. In bijna realtime ziet u wanneer een kamer beschikbaar is, dat de lucht vers is en dat de temperatuur klopt. 

1. Open het opdrachtvenster dat u heb gebruikt voor het inrichten van het voorbeeld, of open een nieuw opdrachtvenster, en ga weer naar de map **occupancy-quickstart\src** van het voorbeeld.

1. Voer de volgende opdracht uit en meld u aan wanneer u hierom wordt gevraagd:

    ```cmd/sh
    dotnet run GetAvailableAndFreshSpaces
    ```

In het uitvoervenster ziet u hoe de door de gebruiker gedefinieerde functie wordt uitgevoerd en gebeurtenissen van de apparaatsimulatie onderschept. 

   [![Uitvoer voor de UDF](./media/tutorial-facilities-udf/udf-running.png)](./media/tutorial-facilities-udf/udf-running.png#lightbox)

Als er aan de gecontroleerd voorwaarde wordt voldaan, stelt de door de gebruiker gedefinieerde functie de waarde van de ruimte in met het betreffende bericht, zoals we [eerder](#create-a-user-defined-function) hebben gezien. De functie `GetAvailableAndFreshSpaces` drukt het bericht op de console af.

## <a name="clean-up-resources"></a>Resources opschonen

Als u Azure Digital Twins niet verder wilt verkennen, kunt u de resources die in deze zelfstudie zijn gemaakt, gerust verwijderen:

1. Klik in het linkermenu in de [Azure-portal](https://portal.azure.com) op **Alle resources**, selecteer de Digital Twins-resourcegroep en selecteer **Verwijderen**.

    > [!TIP]
    > Als u problemen hebt bij het verwijderen van uw Digital Twins-exemplaar, is er een service-update met de oplossing hiervoor beschikbaar. Probeer opnieuw of u het exemplaar kunt verwijderen.

2. Verwijder zo nodig de voorbeeldtoepassingen van uw werkcomputer.

## <a name="next-steps"></a>Volgende stappen

Nu de ruimten zijn ingericht en u een framework voor het activeren van aangepaste meldingen hebt gemaakt, kunt u doorgaan met een van de volgende zelfstudies:

> [!div class="nextstepaction"]
> [Zelfstudie: Meldingen ontvangen uit uw Azure Digital Twins-ruimten met behulp van Logic Apps](tutorial-facilities-events.md)

> [!div class="nextstepaction"]
> [Zelfstudie: Gebeurtenissen uit Azure Digital Twins-ruimten visualiseren en analyseren met Time Series Insights](tutorial-facilities-analyze.md)

---
title: 'Zelfstudie: Gesimuleerde apparaatgegevens genereren - Machine Learning op Azure IoT Edge'
description: Maak virtuele apparaten die gesimuleerde telemetrie genereren die later kunnen worden gebruikt om een machine learning-model te trainen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76722364"
---
# <a name="tutorial-generate-simulated-device-data"></a>Zelfstudie: Gesimuleerde apparaatgegevens genereren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelfstudie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks tot dit artikel bent gekomen, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de serie voor de beste resultaten.

In dit artikel gebruiken we trainingsgegevens voor machine learning om een apparaat te simuleren dat telemetrie naar Azure IoT Hub verzendt. Zoals vermeld in de inleiding, deze tutorial maakt gebruik van de [Turbofan motor degradatie simulatie gegevens set](https://c3.nasa.gov/dashlink/resources/139/) om gegevens te simuleren van een set van vliegtuigmotoren voor training en testen.

In ons experimentele scenario weten we dat:

* De gegevens bestaan uit meerdere multivariate tijdreeksen.
* Elke gegevensset is onderverdeeld in trainings- en testsubsets.
* Elke tijd serie is van een andere motor.
* Elke motor begint met verschillende graden van initiële slijtage en productie variatie.

Voor deze zelfstudie gebruiken we de subset van trainingsgegevens van één gegevensset (FD003).

In werkelijkheid zou elke motor een onafhankelijk IoT-apparaat zijn. Ervan uitgaande dat u niet beschikt over een verzameling van internet-aangesloten turbofan motoren beschikbaar, zullen we een software stand-in te bouwen voor deze apparaten.

De simulator is een C#-programma dat de IoT Hub API's gebruikt om virtuele apparaten programmatisch te registreren met IoT Hub. Vervolgens lezen we de gegevens voor elk apparaat uit de door NASA geleverde gegevenssubset en sturen deze naar uw IoT-hub met behulp van een gesimuleerd IoT-apparaat. Alle code voor dit gedeelte van de zelfstudie is te vinden in de DeviceHarness-map van de repository.

Het DeviceHarness-project is een .NET-kernproject geschreven in C# dat bestaat uit vier klassen:

* **Programma:** Het toegangspunt voor uitvoering dat verantwoordelijk is voor de afhandeling van gebruikersinput en algehele coördinatie.
* **TrainingFileManager:** Verantwoordelijk voor het lezen en ontsmetten van het geselecteerde gegevensbestand.
* **CycleData:** Vertegenwoordigt één rij gegevens in een bestand dat is geconverteerd naar berichtindeling.
* **TurbofanDevice:** Verantwoordelijk voor het maken van een IoT-apparaat, dat overeenkomt met één apparaat (tijdreeks), in de gegevens en het verzenden van de gegevens naar IoT Hub.

De taken die in dit artikel worden beschreven, moeten ongeveer 20 minuten in beslag nemen.

De real-world equivalent van het werk in deze stap zou waarschijnlijk worden uitgevoerd door apparaatontwikkelaars en cloud-ontwikkelaars.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio Code configureren en DeviceHarness-project bouwen

1. Open een externe desktopsessie op uw ontwikkel-VM.

1. Open de `C:\source\IoTEdgeAndMlSample\DeviceHarness` map in Visual Studio-code.

1. Aangezien u voor het eerst extensies op deze machine gebruikt, worden sommige extensies bijgewerkt en hun afhankelijkheden geïnstalleerd. Mogelijk wordt u gevraagd om de extensie bij te werken. Selecteer venster **opnieuw laden dan**.

   Als OmniSharp-fouten worden weergegeven in het uitvoervenster, moet u de C#-extensie verwijderen.

1. U wordt gevraagd vereiste elementen toe te voegen voor DeviceHarness. Selecteer **Ja** om ze toe te voegen.

   * Het kan enkele seconden duren voordat de melding wordt weergegeven.
   * Als u deze melding hebt gemist, controleert u het belpictogram in de rechterbenedenhoek.

   ![POP-up vs-code-extensie](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecteer **Herstellen** om de pakketafhankelijkheden te herstellen.

   ![VS-code-prompt herstellen](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Als u deze meldingen niet ontvangt, sluit u Visual Studio Code, verwijdert u de opslaglocatie en obj-mappen in `C:\source\IoTEdgeAndMlSample\DeviceHarness`Visual Studio Code en opent u de map DeviceHarness opnieuw.

1. Controleer of uw omgeving goed is ingesteld door een build-, **Ctrl** + **Shift** + **B-** of **Terminal** > **Run Build-taak**te activeren.

1. U wordt gevraagd de buildtaak te selecteren die u wilt uitvoeren. Selecteer **Bouwen**.

1. De build loopt en outputs een succes bericht.

   ![Geslaagd uitvoerbericht bouwen](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. U deze build de standaardbuildtaak maken door De**standaardbuildtaak voor** **terminal** > configureren te selecteren... en **Build** te kiezen op de prompt.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Verbinding maken met IoT Hub en DeviceHarness uitvoeren

Nu we het projectgebouw hebben, maak je verbinding met je IoT-hub om toegang te krijgen tot de verbindingstekenreeks en de voortgang van de gegevensgeneratie te controleren.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Aanmelden bij Azure in Visual Studio Code

1. Meld u aan bij uw Azure-abonnement in `Ctrl + Shift + P` Visual Studio Code door het opdrachtpalet te openen of**Opdrachtpalet** **weergeven** > .

1. Zoeken naar de opdracht **Azure: Aanmelden.**

   Er wordt een browservenster geopend en wordt u gevraagd om uw referenties. Wanneer u wordt doorgestuurd naar een succespagina, u de browser sluiten.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Verbinding maken met uw IoT-hub en hubverbindingstekenreeks ophalen

1. Selecteer in het onderste gedeelte van de Visual Studio Code explorer het **Azure IoT Hub-frame** om het uit te vouwen.

1. Klik in het uitgebreide frame op **IoT-hub selecteren**.

1. Selecteer desgevraagd uw Azure-abonnement en vervolgens uw IoT-hub.

1. Klik op de **...** rechts van **Azure IoT Hub** voor meer acties. Selecteer **De verbindingstekenreeks voor IoT-hub kopiëren**.

   ![Verbindingstekenreeks voor IoT-hub kopiëren](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Het DeviceHarness-project uitvoeren

1. Selecteer **Weergaveterminal** > **Terminal** om de Visual Studio Code-terminal te openen.

   Als u geen prompt ziet, drukt u op Enter.

1. Voer `dotnet run` in de terminal in.

1. Plak de verbindingstekenreeks die in de vorige sectie is gekopieerd wanneer u wordt gevraagd om de Verbindingstekenreeks voor IoT-hub.

1. Klik in het frame **Azure IoT Hub-apparaten** op de knop Vernieuwen.

   ![Lijst met IoT-hub-apparaten vernieuwen](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Houd er rekening mee dat apparaten worden toegevoegd aan de IoT-hub en dat de apparaten in het groen worden weergegeven om aan te geven dat gegevens via dat apparaat worden verzonden. Nadat de apparaten berichten naar de IoT-hub hebben verzonden, worden ze losgekoppeld en blauw weergegeven.

1. U berichten die naar de hub worden verzonden bekijken door met de rechtermuisknop op elk apparaat te klikken en **Ingebouwde gebeurteniseindpunt starten te selecteren.** De berichten worden weergegeven in het uitvoervenster in Visual Studio Code.

1. Stop met controleren door in het uitvoervenster **Azure IoT Hub** te klikken en kies **Ingebouwde gebeurteniseindpunt stoppen.**

1. Laat de toepassing tot voltooiing worden uitgevoerd, wat enkele minuten duurt.

## <a name="check-iot-hub-for-activity"></a>IoT-hub controleren op activiteit

De gegevens die door de DeviceHarness worden verzonden, gingen naar uw IoT-hub, waar u verifiëren in de Azure-portal.

1. Open de [Azure-portal](https://portal.azure.com/) en navigeer naar de IoT-hub die voor deze zelfstudie is gemaakt.

1. Selecteer In het menu van het linkerdeelvenster onder **Controle**de optie **Metrische gegevens**.

1. Klik op de pagina grafiekdefinitie op de **vervolgkeuzelijst Metrische,** scrol naar beneden in de lijst en selecteer **Routering: gegevens die worden geleverd aan opslag**. De grafiek moet de piek weergeven van wanneer de gegevens naar opslag zijn gerouteerd.

   ![Grafiek toont piek wanneer gegevens die aan opslag worden geleverd](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Gegevens valideren in Azure Storage

De gegevens die we zojuist naar uw IoT-hub hebben verzonden, zijn doorgestuurd naar de opslagcontainer die we in het vorige artikel hebben gemaakt. Laten we eens kijken naar de gegevens in onze opslag account.

1. Ga in Azure Portal naar het nieuwe opslagaccount.

1. Selecteer **Storage Explorer (voorbeeld)** in de navigator van het opslagaccount .

1. Selecteer blobcontainers in de `devicedata`opslagverkenner en selecteert vervolgens **Blob-containers** .

1. Klik in het inhoudsvenster op de map voor de naam van de IoT-hub, gevolgd door jaar, maand, dag en uur. U ziet verschillende mappen die de minuten weergeven waarop de gegevens zijn geschreven.

   ![Mappen weergeven in blobopslag](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klik in een van die mappen om gegevensbestanden met het label **00** en **01** te vinden die overeenkomen met de partitie.

1. De bestanden zijn geschreven in [Avro](https://avro.apache.org/) formaat. Dubbelklik op een van deze bestanden om een ander browsertabblad te openen en de gegevens gedeeltelijk weer te geven. Als u wordt gevraagd het bestand in een programma te openen, u VS-code kiezen en correct worden weergegeven.

1. Het is niet nodig om te proberen de gegevens nu te lezen of te interpreteren; we zullen het doen in het volgende artikel.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een .NET Core-project gebruikt om een set virtuele IoT-apparaten te maken en gegevens via deze apparaten naar onze IoT-hub en naar een Azure Storage-container te verzenden. Dit project simuleert een real-world scenario waarbij fysieke IoT-apparaten gegevens naar een IoT Hub verzenden en verder naar een samengestelde opslag. Deze gegevens omvatten sensormetingen, operationele instellingen, storingssignalen en -modi, enzovoort. Zodra er voldoende gegevens zijn verzameld, gebruiken we deze om modellen te trainen die de resterende gebruiksduur (RUL) voor het apparaat voorspellen. We demonstreren deze machine learning in het volgende artikel.

Ga verder naar het volgende artikel om een machine learning-model met de gegevens te trainen.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-model trainen en implementeren](tutorial-machine-learning-edge-04-train-model.md)

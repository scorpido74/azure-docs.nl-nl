---
title: 'Zelf studie: gesimuleerde apparaatgegevens genereren-Machine Learning op Azure IoT Edge'
description: Maak virtuele apparaten die gesimuleerde telemetrie genereren die later kunnen worden gebruikt om een machine learning model te trainen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8f7a971315183e867ae06b58801d5855f90462a1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76722364"
---
# <a name="tutorial-generate-simulated-device-data"></a>Zelf studie: gesimuleerde apparaatgegevens genereren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel gebruiken we machine learning-trainings gegevens om een apparaat te simuleren dat telemetrie naar Azure IoT Hub verzendt. Zoals vermeld in de inleiding, gebruikt deze zelf studie de [simulatie gegevens](https://c3.nasa.gov/dashlink/resources/139/) van de turbofan-engine voor het simuleren van gegevens van een set vliegtuig motoren voor training en testen.

In het experimentele scenario weten we dat:

* De gegevens bestaan uit meerdere multidimensionale tijd reeksen.
* Elke gegevensset is onderverdeeld in trainings-en test subsets.
* Elke tijd reeks is afkomstig uit een andere engine.
* Elke engine begint met verschillende graden van eerste slijtage en productie variaties.

Voor deze zelf studie gebruiken we de subset met trainings gegevens van één gegevensset (FD003).

Elke engine is in werkelijkheid een onafhankelijk IoT-apparaat. Ervan uitgaande dat u geen verzameling met internet verbonden turbofan-engines hebt, zullen we een software-inrichting voor deze apparaten bouwen.

De Simulator is een C#-programma dat gebruikmaakt van de IoT Hub Api's voor het programmatisch registreren van virtuele apparaten met IoT Hub. Vervolgens lezen we de gegevens voor elk apparaat uit de subset met NASA-gegevens en verzenden deze naar uw IoT-hub met behulp van een gesimuleerd IoT-apparaat. Alle code voor dit deel van de zelf studie vindt u in de map DeviceHarness van de opslag plaats.

Het DeviceHarness-project is een .NET core-project dat is geschreven in C# en bestaat uit vier klassen:

* **Programma:** Het ingangs punt voor uitvoering dat verantwoordelijk is voor het verwerken van gebruikers invoer en algemene coördinatie.
* **TrainingFileManager:** Verantwoordelijk voor het lezen en parseren van het geselecteerde gegevens bestand.
* **CycleData:** Vertegenwoordigt één rij met gegevens in een bestand dat is geconverteerd naar een bericht indeling.
* **TurbofanDevice:** Verantwoordelijk voor het maken van een IoT-apparaat, dat overeenkomt met één apparaat (tijd reeks), in de gegevens en het verzenden van de gegevens naar IoT Hub.

De taken die in dit artikel worden beschreven, moeten ongeveer 20 minuten duren.

Het werkelijke equivalent van het werk in deze stap wordt waarschijnlijk uitgevoerd door ontwikkel aars van apparaten en Cloud ontwikkelaars.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio code configureren en DeviceHarness-project bouwen

1. Open een extern-bureaublad sessie naar uw ontwikkel-VM.

1. Open de `C:\source\IoTEdgeAndMlSample\DeviceHarness` map in Visual Studio code.

1. Omdat u voor de eerste keer extensies op deze computer gebruikt, worden de afhankelijkheden van sommige extensies bijgewerkt en geïnstalleerd. U wordt mogelijk gevraagd om de extensie bij te werken. Als dit het geval is, selecteert u **venster opnieuw laden**.

   Als er OmniSharp-fouten in het uitvoer venster worden weer gegeven, moet u de C#-extensie verwijderen.

1. U wordt gevraagd om de vereiste activa voor DeviceHarness toe te voegen. Selecteer **Ja** om ze toe te voegen.

   * Het kan een paar seconden duren voordat de melding wordt weer gegeven.
   * Als u deze melding hebt gemist, controleert u het klok pictogram in de rechter benedenhoek.

   ![VS-code uitbreiding popup](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecteer **herstellen** om de pakket afhankelijkheden te herstellen.

   ![VS code herstel prompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Als u deze meldingen niet ontvangt, sluit u Visual Studio code, verwijdert u de mappen bin en obj `C:\source\IoTEdgeAndMlSample\DeviceHarness`in, opent u Visual Studio code en opent u de map DeviceHarness opnieuw.

1. Controleer of uw omgeving juist is ingesteld door een build, **CTRL** + **+ SHIFT** + of **Terminal** > **Run****te activeren**.

1. U wordt gevraagd om de taak te selecteren die u wilt uitvoeren. Selecteer **Build**.

1. De build wordt uitgevoerd en voert een bericht uit.

   ![Gegenereerd uitvoer bericht](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. U kunt de standaard taak voor het maken van deze build maken door **Terminal** > **configureren standaard taak maken...** te selecteren en op basis van de **prompt te kiezen** .

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Verbinding maken met IoT Hub en DeviceHarness uitvoeren

Nu het project is gemaakt, maakt u verbinding met uw IoT-hub om toegang te krijgen tot de connection string en de voortgang van het genereren van de gegevens te controleren.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Aanmelden bij Azure in Visual Studio code

1. Meld u aan bij uw Azure-abonnement in Visual Studio code door het opdracht `Ctrl + Shift + P` palet te openen of het**opdracht palet** **weer te geven** > .

1. Zoek naar de opdracht **Azure: Sign in** .

   Er wordt een browser venster geopend en u wordt gevraagd om uw referenties. Wanneer u bent omgeleid naar een succes volle pagina, kunt u de browser sluiten.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Verbinding maken met uw IoT hub en hub ophalen connection string

1. Selecteer in het onderste gedeelte van de Visual Studio code Explorer het IoT Hub frame van **Azure** om het uit te vouwen.

1. Klik in het uitgevouwen frame op **IOT hub selecteren**.

1. Wanneer u hierom wordt gevraagd, selecteert u uw Azure-abonnement en vervolgens uw IoT-hub.

1. Klik op de **..** . rechts van **Azure IOT hub** voor meer acties. Selecteer **IoT Hub Connection String kopiëren**.

   ![IoT Hub connection string kopiëren](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Het DeviceHarness-project uitvoeren

1. Selecteer**Terminal** **weer geven** > om de Visual Studio code-Terminal te openen.

   Als er geen prompt wordt weer gegeven, drukt u op ENTER.

1. Voer `dotnet run` in de terminal in.

1. Wanneer u wordt gevraagd om de IoT Hub verbindings reeks, plakt u de connection string die u in de vorige sectie hebt gekopieerd.

1. Klik in het frame **Azure IOT Hub-apparaten** op de knop Vernieuwen.

   ![IoT Hub apparaten lijst vernieuwen](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Houd er rekening mee dat apparaten worden toegevoegd aan de IoT Hub en dat de apparaten groen worden weer gegeven om aan te geven dat gegevens via dat apparaat worden verzonden. Nadat de apparaten berichten naar de IoT-hub hebben verzonden, worden ze losgekoppeld en worden ze blauw weer gegeven.

1. U kunt berichten weer geven die naar de hub zijn verzonden door met de rechter muisknop op een apparaat te klikken en **controle van het ingebouwde gebeurtenis-eind punt starten**te selecteren. De berichten worden weer gegeven in het deel venster uitvoer in Visual Studio code.

1. Stop de bewaking door te klikken in het deel venster **Azure IOT hub** -uitvoer en kies **controle stoppen ingebouwd gebeurtenis eindpunt**.

1. Laat de toepassing uitvoeren, wat enkele minuten in beslag neemt.

## <a name="check-iot-hub-for-activity"></a>IoT Hub controleren op activiteit

De gegevens die door de DeviceHarness worden verzonden, zijn naar uw IoT-hub gegaan, waar u kunt controleren in de Azure Portal.

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar de IOT-hub die u voor deze zelf studie hebt gemaakt.

1. Selecteer in het menu van het linkerdeel venster, onder **bewaking**, **metrische gegevens**.

1. Klik op de pagina grafiek definitie op de vervolg keuzelijst **metrische gegevens** , Schuif omlaag in de lijst en selecteer **route ring: gegevens die worden geleverd aan de opslag**. In de grafiek moet de Prikker worden weer gegeven wanneer de gegevens naar de opslag zijn doorgestuurd.

   ![Diagram toont de Prikker wanneer gegevens aan de opslag worden geleverd](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Gegevens in Azure Storage valideren

De gegevens die we zojuist naar uw IoT-hub hebben verzonden, worden doorgestuurd naar de opslag container die we in het vorige artikel hebben gemaakt. Laten we eens kijken naar de gegevens in het opslag account.

1. Ga in Azure Portal naar het nieuwe opslagaccount.

1. Selecteer **Storage Explorer (preview)** in de Navigator voor opslag accounts.

1. Selecteer **BLOB-containers** in de opslag Verkenner en `devicedata`klik vervolgens op.

1. Klik in het deel venster inhoud op de map voor de naam van de IoT-hub, gevolgd door het jaar, de maand, de dag en het uur. U ziet een aantal mappen waarin de minuten worden weer gegeven wanneer de gegevens zijn geschreven.

   ![Mappen in Blob Storage weer geven](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klik in een van deze mappen om gegevens bestanden met het label **00** en **01** te vinden dat overeenkomt met de partitie.

1. De bestanden worden geschreven in de [Avro](https://avro.apache.org/) -indeling. Dubbel klik op een van deze bestanden om een ander browser tabblad te openen en de gegevens gedeeltelijk weer te geven. Als u wordt gevraagd om het bestand in een programma te openen, kunt u VS code kiezen. dit wordt correct weer gegeven.

1. Het is niet nodig om de gegevens nu te lezen of te interpreteren. We doen dit in het volgende artikel.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een .NET core-project gebruikt voor het maken van een set virtuele IoT-apparaten en kunt u gegevens verzenden naar onze IoT-hub en naar een Azure Storage-container. In dit project wordt een praktijk scenario gesimuleerd waarbij fysieke IoT-apparaten gegevens verzenden naar een IoT Hub en vervolgens naar een opslag met een curator worden verzonden. Deze gegevens zijn onder andere de Lees bewerkingen van de sensor, operationele instellingen, fout signalen en modi, enzovoort. Wanneer er voldoende gegevens zijn verzameld, gebruiken we deze om modellen te trainen die de resterende levens duur (resterende levens duur) voor het apparaat voors pellen. In het volgende artikel worden deze machine learning gedemonstreerd.

Ga door naar het volgende artikel om een machine learning model met de gegevens te trainen.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning-model trainen en implementeren](tutorial-machine-learning-edge-04-train-model.md)

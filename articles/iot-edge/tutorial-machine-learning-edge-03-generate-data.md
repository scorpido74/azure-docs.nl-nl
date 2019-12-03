---
title: 'Zelf studie: gesimuleerde apparaatgegevens genereren-Machine Learning op Azure IoT Edge'
description: In deze zelf studie maakt u virtuele apparaten die gesimuleerde telemetrie genereren die later kunnen worden gebruikt om een machine learning model te trainen.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d51a577b8a460e872b2baddcb668b13981609e40
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707750"
---
# <a name="tutorial-generate-simulated-device-data"></a>Zelf studie: gesimuleerde apparaatgegevens genereren

> [!NOTE]
> Dit artikel maakt deel uit van een reeks voor een zelf studie over het gebruik van Azure Machine Learning op IoT Edge. Als u rechtstreeks in dit artikel hebt gearriveerd, raden we u aan om te beginnen met het [eerste artikel](tutorial-machine-learning-edge-01-intro.md) in de reeks voor de beste resultaten.

In dit artikel gebruiken we machine learning trainings gegevens om een apparaat te simuleren dat telemetrie naar IoT Hub verzendt. Zoals vermeld in de inleiding, gebruikt deze end-to-end-zelf studie de [simulatie gegevens](https://c3.nasa.gov/dashlink/resources/139/) van de turbofan-engine voor het simuleren van gegevens uit een set vliegtuig motoren voor training en testen.

In het meegeleverde Leesmij. txt weten we dat:

* De gegevens bestaan uit meerdere multidimensionale tijd reeksen
* Elke gegevensset is onderverdeeld in trainings-en test subsets
* Elke tijd reeks is afkomstig uit een andere engine
* Elke engine begint met verschillende graden van eerste slijtage en productie variatie

Voor deze zelf studie gebruiken we de subset met trainings gegevens van één gegevensset (FD003).

Elke engine is in werkelijkheid een onafhankelijk IoT-apparaat. Ervan uitgaande dat u geen verzameling met internet verbonden turbofan-engines hebt, zullen we een software-inrichting voor deze apparaten bouwen.

De Simulator is een C# programma dat gebruikmaakt van de IOT hub api's voor het programmatisch registreren van virtuele apparaten met IOT hub. Vervolgens lezen we de gegevens voor elk apparaat uit de subset met NASA-gegevens en verzenden deze naar uw IoT-hub met behulp van een gesimuleerd IoT-apparaat. Alle code voor dit deel van de zelf studie vindt u in de map DeviceHarness van de opslag plaats.

Het DeviceHarness-project is een .NET core-project C# dat is geschreven in bestaande uit vier klassen:

* **Programma:** Het ingangs punt voor uitvoering dat verantwoordelijk is voor het verwerken van gebruikers invoer en algemene coördinatie.
* **TrainingFileManager:** verantwoordelijk voor het lezen en parseren van het geselecteerde gegevens bestand.
* **CycleData:** dit is een enkele rij met gegevens in een bestand dat is geconverteerd naar een bericht indeling.
* **TurbofanDevice:** verantwoordelijk voor het maken van een IOT-apparaat dat overeenkomt met één apparaat (tijd reeks) in de gegevens en het verzenden van gegevens naar IOT hub via het IOT-apparaat.

De taken die in dit artikel worden beschreven, moeten ongeveer 20 minuten duren.

Het werkelijke equivalent van het werk in deze stap wordt waarschijnlijk uitgevoerd door ontwikkel aars van apparaten en Cloud ontwikkelaars.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Visual Studio code configureren en DeviceHarness-project bouwen

1. Open een extern-bureaublad sessie naar uw virtuele machine, zoals wordt getoond in het vorige artikel.

1. Open Visual Studio Code.

1. Selecteer in Visual Studio code **File** > **map openen...** .

1. Voer in het tekstvak **map** `C:\source\IoTEdgeAndMlSample\DeviceHarness` in en klik op de knop **map selecteren** .

   Als OmniSharp-fouten in het uitvoer venster worden weer gegeven, moet u de C# uitbrei ding verwijderen, de code sluiten en opnieuw openen C# , de uitbrei ding installeren en vervolgens het venster opnieuw laden.

1. Omdat u voor de eerste keer extensies op deze computer gebruikt, worden de afhankelijkheden van sommige extensies bijgewerkt en geïnstalleerd. U wordt mogelijk gevraagd om de extensie bij te werken. Als dit het geval is, selecteert u **venster opnieuw laden**.

1. U wordt gevraagd om de vereiste activa voor DeviceHarness toe te voegen. Selecteer **Ja** om ze toe te voegen.

   * Het kan een paar seconden duren voordat de melding wordt weer gegeven.
   * Als u deze melding hebt gemist, controleert u het pictogram ' Bell ' in de rechter benedenhoek.

   ![VS-code uitbreiding popup](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Selecteer **herstellen** om de pakket afhankelijkheden te herstellen.

   ![VS code herstel prompt](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

1. Controleer of uw omgeving juist is ingesteld door een build-, `Ctrl + Shift + B`-of **Terminal** - > een **Build-taak uit te voeren**.

1. U wordt gevraagd om de te starten taak te selecteren. Selecteer **Build**.

1. De build wordt uitgevoerd en voert een bericht uit.

   ![Gegenereerd uitvoer bericht](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. U kunt dit maken van de standaard taak voor het maken van de build door **Terminal** te selecteren > **standaard build-taak configureren...** en vervolgens **Build** te kiezen bij de prompt.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Verbinding maken met IoT Hub en DeviceHarness uitvoeren

Nu het project is gemaakt, maakt u verbinding met uw IoT-hub om toegang te krijgen tot de connection string en de voortgang van het genereren van de gegevens te controleren.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Aanmelden bij Azure in Visual Studio code

1. Meld u aan bij uw Azure-abonnement in Visual Studio code door het opdracht palet te openen, `Ctrl + Shift + P` of **weer te geven** > **opdracht palet...** .

1. Bij de prompt zoeken naar en selecteer **Azure: Meld**u aan.

1. Er wordt een browser venster geopend en u wordt gevraagd om uw referenties. Wanneer u wordt omgeleid naar een succes volle pagina, kunt u de browser sluiten.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Verbinding maken met uw IoT hub en hub ophalen connection string

1. Selecteer in het onderste gedeelte van de Visual Studio code Explorer het **Azure IOT Hub-apparaten** frame om het uit te vouwen.

1. Klik in het uitgevouwen frame op **IOT hub selecteren**.

1. Wanneer u hierom wordt gevraagd, selecteert u uw Azure-abonnement en vervolgens uw IoT-hub.

1. Klik in het **Azure IOT Hub-apparaten** frame en klik op **...** voor meer acties. Selecteer **IoT Hub Connection String kopiëren**.

   ![IoT Hub connection string kopiëren](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Het DeviceHarness-project uitvoeren

1. Selecteer **weer gave** > **Terminal** om de Visual Studio code-Terminal te openen.

   Als er geen prompt wordt weer gegeven, selecteert u ENTER.

1. Voer `dotnet run` in de terminal in.

1. Wanneer u wordt gevraagd om de IoT Hub verbindings reeks, plakt u de connection string die u in de vorige sectie hebt gekopieerd.

1. Klik in het frame **Azure IOT Hub-apparaten** op de knop Vernieuwen.

   ![IoT Hub apparaten lijst vernieuwen](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Houd er rekening mee dat apparaten worden toegevoegd aan de IoT Hub en dat de apparaten groen worden weer gegeven om aan te geven dat gegevens via dat apparaat worden verzonden.

1. U kunt de berichten die worden verzonden naar de hub weer geven door met de rechter muisknop op een apparaat te klikken en **controle van het ingebouwde gebeurtenis-eind punt starten**te selecteren. De berichten worden weer gegeven in het deel venster uitvoer in Visual Studio code.

1. Stop de bewaking door te klikken in het deel venster uitvoer van de **Azure IOT hub Toolkit** en kies **controle van ingebouwd gebeurtenis stop stoppen**.

1. Laat de toepassing uitvoeren, wat enkele minuten in beslag neemt.

## <a name="check-iot-hub-for-activity"></a>IoT Hub controleren op activiteit

De gegevens die door de DeviceHarness worden verzonden, zijn naar uw IoT-hub gegaan. Het is eenvoudig om te controleren of gegevens uw hub hebben bereikt met behulp van de Azure Portal.

1. Open de [Azure Portal](https://portal.azure.com/) en navigeer naar uw IOT-hub.

1. Op de overzichts pagina ziet u dat gegevens naar de hub zijn verzonden:  

   ![Het apparaat weer geven in Cloud berichten in IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Gegevens in Azure Storage valideren

De gegevens die we zojuist naar uw IoT-hub hebben verzonden, worden doorgestuurd naar de opslag container die we in het vorige artikel hebben gemaakt. Laten we eens kijken naar de gegevens in het opslag account.

1. Ga in Azure Portal naar uw opslagaccount.

1. Selecteer **Storage Explorer (preview)** in de Navigator voor opslag accounts.

1. Selecteer in opslag Verkenner de optie **BLOB-containers** en vervolgens **devicedata**.

1. Klik in het inhouds venster op de map voor de naam van de IoT-hub, het jaar, de maand, de dag en het uur. U ziet een aantal mappen waarin de minuten worden weer gegeven wanneer de gegevens zijn geschreven.

   ![Mappen in Blob Storage weer geven](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Klik in een van deze mappen om gegevens bestanden met het label **00** en **01** te vinden dat overeenkomt met de partitie.

1. De bestanden worden geschreven in de [Avro](https://avro.apache.org/) -indeling, maar door te dubbel klikken op een van deze bestanden wordt een nieuw browser tabblad geopend en worden de gegevens gedeeltelijk weer gegeven. Als u in plaats daarvan wordt gevraagd om het bestand in een programma te openen, kunt u VS code kiezen. dit wordt correct weer gegeven.

1. Het is niet nodig om de gegevens nu te lezen of te interpreteren. We doen dit in het volgende artikel.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebben we een .NET core-project gebruikt voor het maken van een set virtuele apparaten en het verzenden van gegevens via die apparaten via onze IoT Hub en naar een Azure Storage-container. In dit project wordt een praktijk scenario gesimuleerd waarbij fysieke apparaten gegevens verzenden, inclusief Lees bewerkingen van de sensor, operationele instellingen, fout signalen en-modi, enzovoort, naar een IoT Hub en naar een nagemaakte opslag. Wanneer er voldoende gegevens zijn verzameld, gebruiken we deze voor het trainen van modellen die de resterende levens duur (resterende levens duur) voor het apparaat voors pellen, die we in het volgende artikel zullen zien.

Ga door naar het volgende artikel om een machine learning model met de gegevens te trainen.

> [!div class="nextstepaction"]
> [Een Azure Machine Learning model trainen en implementeren](tutorial-machine-learning-edge-04-train-model.md)

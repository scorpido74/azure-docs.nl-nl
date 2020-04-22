---
title: 'Zelfstudie: Voorbeeld van IoT-visuele waarschuwingen'
titleSuffix: Azure Cognitive Services
description: In deze zelfstudie gebruikt u Aangepaste visie met een IoT-apparaat om visuele toestanden te herkennen en te rapporteren uit de videofeed van een camera.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ac7609d49631fb2ed16fa129f8dc4099cc166247
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81769876"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Zelfstudie: Aangepaste visie gebruiken met een IoT-apparaat om visuele statussen te rapporteren

Deze voorbeeld-app illustreert hoe u Custom Vision gebruiken om een apparaat met een camera te trainen om visuele toestanden te detecteren. U dit detectiescenario uitvoeren op een IoT-apparaat met behulp van een geëxporteerd ONNX-model.

Een visuele toestand beschrijft de inhoud van een afbeelding: een lege kamer of een kamer met mensen, een lege oprit of een oprit met een vrachtwagen, enzovoort. In de afbeelding hieronder zie je de app detecteren wanneer een banaan of een appel voor de camera wordt geplaatst.

![Animatie van een UI die fruit voor de camera etikettaand t.o.v.](./media/iot-visual-alerts-tutorial/scoring.gif)

In deze zelfstudie leert u hoe u het volgende kunt doen:
> [!div class="checklist"]
> * Configureer de voorbeeld-app om uw eigen Aangepaste Visie- en IoT Hub-bronnen te gebruiken.
> * Gebruik de app om uw Custom Vision-project te trainen.
> * Gebruik de app om nieuwe afbeeldingen in realtime te scoren en de resultaten naar Azure te verzenden.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint. 

## <a name="prerequisites"></a>Vereisten

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Dit project moet een **Compact** image classification project zijn, omdat we het model later naar ONNX exporteren.
* U moet ook [een IoT Hub-bron maken](https://ms.portal.azure.com/#create/Microsoft.IotHub) op Azure.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com/downloads/)
* Optioneel een IoT-apparaat met Windows 10 IoT Core-versie 17763 of hoger. U de app ook rechtstreeks vanaf uw pc uitvoeren.
   * Voor Raspberry Pi 2 en 3 u Windows 10 rechtstreeks instellen vanuit de IoT Dashboard-app. Voor andere apparaten, zoals DrangonBoard, moet u het flashen met behulp van de [eMMC-methode.](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices) Zie Uw [apparaat instellen](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) in de Windows IoT-documentatie als u hulp nodig hebt bij het instellen van een nieuw apparaat.

## <a name="about-the-visual-alerts-app"></a>Informatie over de app Visuele waarschuwingen

De IoT Visual Alerts-app wordt continu uitgevoerd en schakelt zo nodig tussen vier verschillende toestanden:

* **Geen model:** een no-op staat. De app zal voortdurend slapen voor een seconde en controleer de camera.
* **Trainingsafbeeldingen vastleggen:** In deze toestand legt de app een afbeelding vast en uploadt deze als trainingsafbeelding naar het doel-Custom Vision-project. De app slaapt vervolgens 500 ms en herhaalt de bewerking totdat het ingestelde doelaantal afbeeldingen is vastgelegd. Dan activeert de training van de Custom Vision model.
* **Wachten op getraind model:** In deze toestand roept de app elke seconde de Custom Vision API aan om te controleren of het doelproject een getrainde iteratie bevat. Wanneer het een wordt gevonden, downloadt het het bijbehorende ONNX-model naar een lokaal bestand en schakelt het over naar de **scorestatus.**
* **Scoren:** In deze toestand gebruikt de app Windows ML om een enkel frame van de camera te evalueren ten opzichte van het lokale ONNX-model. De resulterende beeldclassificatie wordt op het scherm weergegeven en als bericht naar de IoT Hub verzonden. De app slaapt vervolgens een seconde voordat u een nieuwe afbeelding scoort.

## <a name="understand-the-code-structure"></a>De codestructuur begrijpen

De volgende bestanden behandelen de hoofdfunctionaliteit van de app.

| File | Beschrijving |
|-------------|-------------|
| [MainPage.xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Dit bestand definieert de XAML-gebruikersinterface. Het host het webcamerabesturingselement en bevat de labels die worden gebruikt voor statusupdates.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Deze code bepaalt het gedrag van de XAML-gebruikersinterface. Het bevat de code voor het verwerken van de statusmachine.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Deze klasse is een wrapper die de integratie met de Custom Vision Service verwerkt.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Deze klasse is een wrapper die de integratie met Windows ML verwerkt voor het laden van het ONNX-model en het scoren van afbeeldingen ertegen.|
| [IoTHub\iotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Deze klasse is een wrapper die de integratie met IoT Hub verwerkt voor het uploaden van scoreresultaten naar Azure.|

## <a name="set-up-the-visual-alerts-app"></a>De app Visuele waarschuwingen instellen

Voer deze stappen uit om de IoT Visual Alerts-app te laten draaien op uw pc of IoT-apparaat.

1. Kloon of download het [IoTVisualAlerts-voorbeeld](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) op GitHub.
1. Open de oplossing _IoTVisualAlerts.sln_ in Visual Studio
1. Integreer uw Custom Vision project:
    1. Werk in het _script CustomVision\CustomVisionServiceWrapper.cs_ de `ApiKey` variabele bij met uw trainingssleutel.
    1. Werk vervolgens `Endpoint` de variabele bij met de URL van het eindpunt die aan uw sleutel is gekoppeld.
    1. Werk `targetCVSProjectGuid` de variabele bij met de bijbehorende ID van het Aangepaste Visie-project dat u wilt gebruiken. 
1. De IoT Hub-bron instellen:
    1. Werk in het _IoTHub\IotHubWrapper.cs-script_ de `s_connectionString` variabele bij met de juiste verbindingstekenreeks voor uw apparaat. 
    1. Laad op de Azure-portal uw IoT Hub-exemplaar, klik op **IoT-apparaten** onder **Explorers,** selecteer op uw doelapparaat (of maak er indien nodig een) en zoek de verbindingstekenreeks onder **Primaire verbindingstekenreeks**. De tekenreeks bevat de naam van de IoT-hub, de apparaat-id en de gedeelde toegangssleutel. het heeft de `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`volgende indeling: .

## <a name="run-the-app"></a>De app uitvoeren

Als u de app op uw pc uitvoert, selecteert u **Lokale machine** voor het doelapparaat in Visual Studio en selecteert u **x64** of **x86** voor het doelplatform. Druk vervolgens op F5 om het programma uit te voeren. De app moet de live feed van de camera en een statusbericht starten en weergeven.

Als u implementeert op een IoT-apparaat met een ARM-processor, moet u **ARM** selecteren als doelplatform en **Externe machine** als doelapparaat. Geef het IP-adres van uw apparaat op wanneer dit wordt gevraagd (het moet zich op hetzelfde netwerk als uw pc bevinden). U het IP-adres ophalen van de standaard-app van Windows IoT zodra u het apparaat opstart en het aansluit op het netwerk. Druk op F5 om het programma uit te voeren.

Wanneer u de app voor de eerste keer uitvoert, heeft deze geen kennis van visuele toestanden. Er wordt een statusbericht weergegeven dat er geen model beschikbaar is. 

## <a name="capture-training-images"></a>Trainingsbeelden vastleggen

Als u een model wilt instellen, moet u de app in de status **Trainingsafbeeldingen vastleggen** plaatsen. Een van de volgende stappen uitvoeren:
* Als u de app op de pc gebruikt, gebruikt u de knop in de rechterbovenhoek van de gebruikersinterface.
* Als u de app op een IoT-apparaat uitvoert, belt u de `EnterLearningMode` methode op het apparaat via de IoT-hub. U het aanroepen via de apparaatinvoer in het menu IoT Hub op de Azure-portal of met een hulpprogramma zoals [IoT Hub Device Explorer.](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer)
 
Wanneer de app de status **Trainingsafbeeldingen vastleggen** invoert, worden ongeveer twee afbeeldingen per seconde vastgelegd totdat het doelaantal afbeeldingen is bereikt. Standaard is het doel 30 afbeeldingen, maar u deze parameter instellen `EnterLearningMode` door het gewenste getal door te geven als argument voor de IoT Hub-methode. 

Terwijl de app beelden vastlegt, moet u de camera blootstellen aan de typen visuele toestanden die u wilt detecteren (bijvoorbeeld een lege kamer, een kamer met mensen, een leeg bureau, een bureau met een speelgoedtruck, enzovoort).

## <a name="train-the-custom-vision-model"></a>Train het Custom Vision-model

Zodra de app klaar is met het vastleggen van afbeeldingen, wordt deze geüpload en vervolgens overgeschakeld naar de status **Wachten op getraind model.** Op dit punt moet je naar de [Custom Vision portal](https://www.customvision.ai/) gaan en een model bouwen op basis van de nieuwe trainingsafbeeldingen. De volgende animatie toont een voorbeeld van dit proces.

![Animatie: het taggen van meerdere afbeeldingen van bananen](./media/iot-visual-alerts-tutorial/labeling.gif)

Ga als volgt te werk om dit proces te herhalen met uw eigen scenario:

1. Meld u aan bij de [Portal Aangepast zicht](http://customvision.ai).
1. Zoek uw doelproject, dat nu alle trainingsafbeeldingen moet hebben die de app heeft geüpload.
1. Selecteer voor elke visuele status die u wilt identificeren de juiste afbeeldingen en pas de tag handmatig toe.
    * Als je bijvoorbeeld doel hebt om onderscheid te maken tussen een lege ruimte en een kamer met mensen erin, raden we je aan om vijf of meer afbeeldingen te taggen met mensen als een nieuwe klasse, **Personen**en vijf of meer afbeeldingen te taggen zonder dat mensen de negatieve tag **zijn.** Dit zal helpen het model onderscheid te maken tussen de twee staten.
    * Als een ander voorbeeld, als je doel is om bij benadering hoe volledig een plank is, dan u gebruik maken van tags zoals **EmptyShelf**, **GedeeltelijkFullShelf**, en **FullShelf**.
1. Als u klaar bent, selecteert u de knop **Trein.**
1. Zodra de training is voltooid, detecteert de app of er een getrainde iteratie beschikbaar is. Het zal het proces van het exporteren van het getrainde model naar ONNX en het downloaden naar het apparaat te starten.

## <a name="use-the-trained-model"></a>Het getrainde model gebruiken

Zodra de app het getrainde model downloadt, schakelt deze over naar de **scorestatus** en begint het in een doorlopende lus met het scoren van afbeeldingen van de camera.

Voor elke vastgelegde afbeelding geeft de app de bovenste tag op het scherm weer. Als de visuele status niet wordt herkend, worden **er geen overeenkomsten weergegeven.** De app stuurt deze berichten ook naar de IoT Hub, en als er een klasse wordt gedetecteerd, bevat `detectedClassAlert`het bericht het label, de betrouwbaarheidsscore en een eigenschap die wordt genoemd , die kan worden gebruikt door IoT Hub-clients die geïnteresseerd zijn in het doen van snelle berichtroutering op basis van eigenschappen.

Bovendien maakt het voorbeeld gebruik van een [Sense HAT-bibliotheek](https://github.com/emmellsoft/RPi.SenseHat) om te detecteren wanneer deze wordt uitgevoerd op een Raspberry Pi met een Sense HAT-eenheid, zodat het kan worden gebruikt als een uitvoerweergave door alle lampjes op rood in te stellen wanneer het een klasse detecteert en leeg wanneer het niets detecteert.

## <a name="reuse-the-app"></a>De app opnieuw gebruiken

Als u de app terug wilt zetten naar de oorspronkelijke staat, u dit doen door op de knop rechtsboven `DeleteCurrentModel` in de gebruikersinterface te klikken of door de methode aan te roepen via de IoT Hub.

Op elk moment u de stap herhalen van het uploaden van trainingsafbeeldingen `EnterLearningMode` door op de ui-knop rechtsboven te klikken of de methode opnieuw aan te roepen.

Als u de app op een apparaat uitvoert en het IP-adres opnieuw moet ophalen (om bijvoorbeeld een externe verbinding via de [Windows IoT Remote Client](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)tot stand te brengen), u de `GetIpAddress` methode bellen via IoT Hub.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder uw Custom Vision-project als u het project niet meer wilt onderhouden. Ga op de [website van Custom Vision](https://customvision.ai)naar **Projecten** en selecteer de prullenbak onder uw nieuwe project.

![Schermafbeelding van een deelvenster met het label Mijn nieuwe project met een prullenbakpictogram](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie stelt u een toepassing in en die visuele statusgegevens op een IoT-apparaat detecteert en de resultaten naar de IoT-hub verzendt. Verken vervolgens de broncode verder of maak een van de voorgestelde wijzigingen hieronder.

> [!div class="nextstepaction"]
> [Voorbeeld van IoTVisualAlerts (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Voeg een IoT Hub-methode toe om de app rechtstreeks over te schakelen naar de status **Wachten op getraind model.** Op deze manier u het model trainen met afbeeldingen die niet door het apparaat zelf zijn vastgelegd en vervolgens het nieuwe model op commando naar het apparaat duwen.
* Volg de zelfstudie [Voor realtime sensorgegevens visualiseren](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) om een Power BI-dashboard te maken om de IoT Hub-waarschuwingen te visualiseren die door het voorbeeld worden verzonden.
* Volg de [zelfstudie voor het monitoren van IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) op afstand om een Logic-app te maken die reageert op de IoT Hub-waarschuwingen wanneer visuele statussen worden gedetecteerd.

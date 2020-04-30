---
title: 'Zelf studie: voor beeld van IoT Visual Alerts'
titleSuffix: Azure Cognitive Services
description: In deze zelf studie gebruikt u Custom Vision met een IoT-apparaat om visuele statussen van de video-feed van een camera te herkennen en te rapporteren.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: ac7609d49631fb2ed16fa129f8dc4099cc166247
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81769876"
---
# <a name="tutorial-use-custom-vision-with-an-iot-device-to-report-visual-states"></a>Zelf studie: Custom Vision gebruiken met een IoT-apparaat om de visuele statussen te melden

In deze voor beeld-app ziet u hoe u Custom Vision kunt gebruiken om een apparaat met een camera te trainen om visuele statussen te detecteren. U kunt dit detectie scenario uitvoeren op een IoT-apparaat met behulp van een geëxporteerd ONNX-model.

Een visuele status beschrijft de inhoud van een afbeelding: een lege ruimte of een kamer met mensen, een lege driveway of een driveway met een truck, enzovoort. In de onderstaande afbeelding ziet u dat de app detecteert wanneer een bananen of een Apple vóór de camera wordt geplaatst.

![Animatie van een gebruikers interface die aan de voor zijde van de camera is gelabeld](./media/iot-visual-alerts-tutorial/scoring.gif)

In deze zelfstudie leert u hoe u het volgende kunt doen:
> [!div class="checklist"]
> * Configureer de voor beeld-app om uw eigen Custom Vision en IoT Hub resources te gebruiken.
> * Gebruik de app voor het trainen van uw Custom Vision-project.
> * Gebruik de app om nieuwe afbeeldingen in realtime te scoren en de resultaten naar Azure te verzenden.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint. 

## <a name="prerequisites"></a>Vereisten

* [!INCLUDE [create-resources](includes/create-resources.md)]
    > [!IMPORTANT]
    > Dit project moet een **compact** installatie kopie-project zijn, omdat het model later naar ONNX wordt geëxporteerd.
* U moet ook [een IOT hub resource maken](https://ms.portal.azure.com/#create/Microsoft.IotHub) in Azure.
* [Visual Studio 2015 of hoger](https://www.visualstudio.com/downloads/)
* Optioneel een IoT-apparaat met Windows 10 IoT core versie 17763 of hoger. U kunt de app ook rechtstreeks vanaf uw PC uitvoeren.
   * Voor Raspberry pi 2 en 3 kunt u Windows 10 rechtstreeks vanuit de IoT-dashboard toepassing instellen. Voor andere apparaten, zoals DrangonBoard, moet u deze flashen met behulp van de [methode eMMC](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup#flashing-with-emmc-for-dragonboard-410c-other-qualcomm-devices). Als u hulp nodig hebt bij het instellen van een nieuw apparaat, raadpleegt u [het instellen van uw apparaat](https://docs.microsoft.com/windows/iot-core/tutorials/quickstarter/devicesetup) in de Windows IOT-documentatie.

## <a name="about-the-visual-alerts-app"></a>Over de app Visual Alerts

De app IoT Visual Alerts wordt uitgevoerd in een doorlopende lus en zo nodig overschakelen tussen vier verschillende statussen:

* **Geen model**: er is geen status. De app slaapt voortdurend één seconde en de camera wordt gecontroleerd.
* **Trainings afbeeldingen vastleggen**: in deze status legt de app een afbeelding vast en uploadt deze als een trainings afbeelding naar het doel Custom Vision project. De app wordt vervolgens in slaap stand gezet voor 500 MS en de bewerking wordt herhaald totdat het ingestelde doel aantal installatie kopieën wordt vastgelegd. Vervolgens wordt de training van het Custom Vision model geactiveerd.
* **Wachten op getraind model**: in deze staat roept de app de Custom Vision-API elke seconde aan om te controleren of het doel project een getrainde iteratie bevat. Wanneer er een wordt gevonden, wordt het bijbehorende ONNX-model gedownload naar een lokaal bestand en wordt overgeschakeld naar de **Score** status.
* **Score**: in deze status gebruikt de app Windows ml om één frame van de camera te evalueren op basis van het lokale ONNX-model. De resulterende afbeeldings classificatie wordt weer gegeven op het scherm en verzonden als een bericht naar de IoT Hub. De app wordt vervolgens één seconde geslapend voordat een nieuwe afbeelding wordt gewaarderingt.

## <a name="understand-the-code-structure"></a>De code structuur begrijpen

De volgende bestanden verwerken de belangrijkste functionaliteit van de app.

| File | Beschrijving |
|-------------|-------------|
| [MainPage. xaml](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml) | Dit bestand definieert de XAML-gebruikers interface. Het hosten het besturings element webcamera en bevat de labels die worden gebruikt voor status updates.|
| [MainPage.xaml.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/MainPage.xaml.cs) | Deze code bepaalt het gedrag van de XAML-gebruikers interface. Het bevat de verwerkings code van de status machine.|
| [CustomVision\CustomVisionServiceWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionServiceWrapper.cs) | Deze klasse is een wrapper waarmee de integratie met de Custom Vision Service wordt afgehandeld.|
| [CustomVision\CustomVisionONNXModel.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/CustomVision/CustomVisionONNXModel.cs) | Deze klasse is een wrapper die de integratie afhandelt met Windows ML voor het laden van het ONNX-model en het scoren van afbeeldingen.|
| [IoTHub\IotHubWrapper.cs](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/blob/master/IoTVisualAlerts/IoTHub/IotHubWrapper.cs) | Deze klasse is een wrapper waarmee de integratie met IoT Hub voor het uploaden van Score resultaten naar Azure wordt verwerkt.|

## <a name="set-up-the-visual-alerts-app"></a>De app Visual Alerts instellen

Volg deze stappen om de app IoT Visual Alerts op uw PC of IoT-apparaat op te halen.

1. Kloon of down load het IoTVisualAlerts-voor [beeld](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts) op github.
1. Open de oplossing _IoTVisualAlerts. SLN_ in Visual Studio
1. Uw Custom Vision project integreren:
    1. Werk in het script _CustomVision\CustomVisionServiceWrapper.cs_ de `ApiKey` variabele bij met uw trainings sleutel.
    1. Werk vervolgens de `Endpoint` variabele bij met de eind punt-URL die aan uw sleutel is gekoppeld.
    1. Werk de `targetCVSProjectGuid` variabele bij met de corresponderende id van het Custom Vision project dat u wilt gebruiken. 
1. Stel de IoT Hub resource in:
    1. Werk in het script _IoTHub\IotHubWrapper.cs_ de `s_connectionString` variabele met de juiste Connection String voor uw apparaat. 
    1. Op de Azure Portal, laadt u uw IoT Hub-exemplaar, klikt u op **IOT-apparaten** onder **Explorers**, selecteert u op uw doel apparaat (of maakt u een indien nodig) en zoekt u de Connection String onder **primaire verbindings reeks**. De teken reeks bevat uw IoT Hub naam, apparaat-ID en gedeelde toegangs sleutel. het heeft de volgende indeling: `{your iot hub name}.azure-devices.net;DeviceId={your device id};SharedAccessKey={your access key}`.

## <a name="run-the-app"></a>De app uitvoeren

Als u de app op uw PC uitvoert, selecteert u **lokale computer** voor het doel apparaat in Visual Studio en selecteert u **x64** of **x86** voor het doel platform. Druk vervolgens op F5 om het programma uit te voeren. De app moet worden gestart en de live-feed van de camera en een status bericht worden weer gegeven.

Als u implementeert op een IoT-apparaat met een ARM-processor, moet u **arm** als het doel platform en de **externe computer** als doel apparaat selecteren. Geef het IP-adres van uw apparaat op wanneer u hierom wordt gevraagd (het moet zich op hetzelfde netwerk bevinden als uw PC). U kunt het IP-adres ophalen uit de Windows IoT-standaard-app nadat u het apparaat hebt opgestart en verbinding hebt gemaakt met het netwerk. Druk op F5 om het programma uit te voeren.

Wanneer u de app voor de eerste keer uitvoert, heeft deze geen kennis van de visuele status. Er wordt een status bericht weer gegeven dat er geen model beschikbaar is. 

## <a name="capture-training-images"></a>Trainings afbeeldingen vastleggen

Als u een model wilt instellen, moet u de app in de status van de **trainings afbeeldingen vastleggen** opnemen. Voer een van de volgende stappen uit:
* Als u de app op de PC uitvoert, gebruikt u de knop in de rechter bovenhoek van de gebruikers interface.
* Als u de app op een IoT-apparaat uitvoert, roept u `EnterLearningMode` de methode op het apparaat aan via de IOT hub. U kunt deze aanroepen via de vermelding apparaat in het menu IoT Hub op het Azure Portal of met een hulp programma zoals [IoT Hub Device Explorer](https://github.com/Azure/azure-iot-sdk-csharp/tree/master/tools/DeviceExplorer).
 
Wanneer de app de status van de **trainings afbeeldingen voor vastleggen** opgeeft, worden de twee installatie kopieën elke seconde vastgelegd totdat het doel aantal installatie kopieën is bereikt. Standaard is het doel 30 afbeeldingen, maar u kunt deze para meter instellen door het gewenste getal als argument door te geven aan de `EnterLearningMode` IOT hub methode. 

Terwijl de app afbeeldingen vastlegt, moet u de camera zichtbaar maken voor de typen visuele statussen die u wilt detecteren (bijvoorbeeld een lege ruimte, een kamer met mensen, een lege Desk, een bureau met een speelgoed truck, enzovoort).

## <a name="train-the-custom-vision-model"></a>Het Custom Vision model trainen

Zodra de app klaar is met het vastleggen van installatie kopieën, worden deze geüpload en wordt vervolgens overgeschakeld naar de status **wachten op opgeleid model** . Op dit moment moet u naar de [Custom Vision-Portal](https://www.customvision.ai/) gaan en een model bouwen op basis van de nieuwe trainings installatie kopieën. In de volgende animatie ziet u een voor beeld van dit proces.

![Animatie: meerdere afbeeldingen van bananen coderen](./media/iot-visual-alerts-tutorial/labeling.gif)

Als u dit proces wilt herhalen met uw eigen scenario:

1. Meld u aan bij de [Custom Vision Portal](http://customvision.ai).
1. Zoek uw doel project, dat nu alle trainings afbeeldingen moet hebben die de app heeft geüpload.
1. Selecteer de juiste installatie kopieën voor elke visuele status die u wilt identificeren en pas de tag hand matig toe.
    * Als uw doel bijvoorbeeld onderscheid moet maken tussen een lege ruimte en een ruimte met mensen daarin, raden we u aan om vijf of meer afbeeldingen te voorzien van een nieuwe klasse, **personen**en het labelen van vijf of meer afbeeldingen zonder mensen als de **negatieve** tag. Dit helpt het model onderscheid te maken tussen de twee statussen.
    * Een ander voor beeld: als het doel is om te benaderen hoe vol een plank is, kunt u Tags gebruiken zoals **EmptyShelf**, **PartiallyFullShelf**en **FullShelf**.
1. Wanneer u klaar bent, selecteert u de **trein** knop.
1. Zodra de training is voltooid, detecteert de app dat er een getrainde iteratie beschikbaar is. Het proces voor het exporteren van het getrainde model wordt gestart naar ONNX en gedownload naar het apparaat.

## <a name="use-the-trained-model"></a>Het getrainde model gebruiken

Zodra de app het getrainde model downloadt, wordt er overgeschakeld naar de **Score** status en wordt het Score beeld van de camera in een doorlopende lus gestart.

Voor elke vastgelegde installatie kopie wordt de bovenste tag weer gegeven op het scherm. Als de visuele status niet wordt herkend, worden **er geen overeenkomsten**weer gegeven. De app stuurt deze berichten ook naar de IoT Hub, en als er een klasse wordt gedetecteerd, bevat het bericht het label, de betrouwbaarheids Score en een eigenschap `detectedClassAlert`die kan worden gebruikt door IOT hub-clients die zijn geïnteresseerd in een snelle bericht routering op basis van eigenschappen.

Daarnaast gebruikt het voor beeld een [Sense-Hat-bibliotheek](https://github.com/emmellsoft/RPi.SenseHat) om te detecteren wanneer deze wordt uitgevoerd op een Raspberry Pi met een Sense Hat-eenheid, zodat deze kan worden gebruikt als een uitvoer weergave door alle weergave lichten op rood in te stellen wanneer er een klasse wordt gedetecteerd en deze leeg is wanneer er niets wordt gedetecteerd.

## <a name="reuse-the-app"></a>De app opnieuw gebruiken

Als u de oorspronkelijke staat van de app opnieuw wilt instellen, kunt u dit doen door te klikken op de knop in de rechter bovenhoek van de gebruikers interface of door de methode `DeleteCurrentModel` aan te roepen via de IOT hub.

U kunt op elk gewenst moment de stap voor het uploaden van trainings afbeeldingen herhalen door te klikken op de knop rechtsboven op de `EnterLearningMode` gebruikers interface of de methode opnieuw aan te roepen.

Als u de app op een apparaat uitvoert en het IP-adres opnieuw moet ophalen (bijvoorbeeld om een externe verbinding te maken via de [Windows IOT externe client](https://www.microsoft.com/p/windows-iot-remote-client/9nblggh5mnxz#activetab=pivot:overviewtab)), kunt u de `GetIpAddress` methode aanroepen via IOT hub.

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder het Custom Vision-project als u het niet meer wilt behouden. Navigeer op de [website Custom Vision](https://customvision.ai)naar **projecten** en selecteer de prullen mand onder het nieuwe project.

![Scherm afbeelding van een paneel met het label mijn nieuwe project met een prullenbak pictogram](./media/csharp-tutorial/delete_project.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie kunt u een toepassing instellen en uitvoeren die informatie over de visuele status op een IoT-apparaat detecteert en de resultaten naar de IoT Hub verzendt. Bekijk vervolgens de bron code verder of maak een van de voorgestelde wijzigingen hieronder.

> [!div class="nextstepaction"]
> [IoTVisualAlerts-voor beeld (GitHub)](https://github.com/Azure-Samples/Cognitive-Services-Vision-Solution-Templates/tree/master/IoTVisualAlerts)

* Voeg een IoT Hub methode toe om de app direct over te scha kelen naar de status **van getrainde modellen** . Op deze manier kunt u het model trainen met installatie kopieën die niet worden vastgelegd door het apparaat zelf en vervolgens het nieuwe model naar het apparaat pushen.
* Volg de zelf studie voor het [visualiseren van de real-time sensor gegevens](https://docs.microsoft.com/azure/iot-hub/iot-hub-live-data-visualization-in-power-bi) om een Power bi dash board te maken voor het visualiseren van de IOT hub waarschuwingen die door het voor beeld worden verzonden.
* Volg de zelf studie voor [IOT-externe controle](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitoring-notifications-with-azure-logic-apps) voor het maken van een logische app die reageert op de IOT hub-waarschuwingen wanneer visuele statussen worden gedetecteerd.

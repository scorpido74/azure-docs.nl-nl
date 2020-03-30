---
title: Cloud-to-device-berichten met Azure IoT Hub (iOS) | Microsoft Documenten
description: Cloud-naar-device-berichten verzenden naar een apparaat vanaf een Azure IoT-hub met behulp van de Azure IoT-SDK's voor iOS.
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/19/2018
ms.author: kgremban
ms.openlocfilehash: 544b1108a3c79da50969f7fdd33cfb9af373d854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77110862"
---
# <a name="send-cloud-to-device-messages-with-iot-hub-ios"></a>Cloud-naar-device berichten verzenden met IoT Hub (iOS)

[!INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

Azure IoT Hub is een volledig beheerde service die betrouwbare en veilige bidirectionele communicatie tussen miljoenen apparaten en een back-end van een oplossing mogelijk maakt. Met [Quickstart telemetrie verzenden van een apparaat naar een IoT-hub](quickstart-send-telemetry-ios.md) ziet u hoe u een IoT-hub maakt, een apparaatidentiteit indient en een gesimuleerde apparaat-app codet die apparaat-naar-cloudberichten verzendt.

In deze handleiding ontdekt u hoe u:

* Stuur vanuit uw back-end van oplossing cloud-naar-device-berichten naar één apparaat via IoT Hub.

* Ontvang berichten van cloud naar apparaat op een apparaat.

* Vraag vanaf uw oplossing een bevestiging van de levering *(feedback)* aan voor berichten die vanuit IoT Hub naar een apparaat worden verzonden.

Meer informatie over berichten van cloud tot apparaat vindt u in het [berichtengedeelte van de IoT Hub-ontwikkelaarshandleiding.](iot-hub-devguide-messaging.md)

Aan het einde van dit artikel voer je twee Swift iOS-projecten uit:

* **voorbeeldapparaat**, dezelfde app die is gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub,](quickstart-send-telemetry-ios.md)die verbinding maakt met uw IoT-hub en berichten van cloud naar apparaat ontvangt.

* **voorbeeldservice**, die een cloud-to-device-bericht naar de gesimuleerde apparaat-app stuurt via IoT Hub en vervolgens de leveringsbevestiging ontvangt.

> [!NOTE]
> IoT Hub heeft SDK-ondersteuning voor veel apparaatplatforms en -talen (waaronder C, Java, Python en Javascript) via Azure IoT-apparaat SDK's. Zie het [Azure IoT Developer Center](https://www.azure.com/develop/iot)voor stapsgewijze instructies over het verbinden van uw apparaat met de code van deze zelfstudie en in het algemeen met Azure IoT Hub.

## <a name="prerequisites"></a>Vereisten

* Een actief Azure-account. (Als u geen account hebt, kunt u binnen een paar minuten een [gratis account](https://azure.microsoft.com/pricing/free-trial/) maken.)

* Een actieve IoT-hub in Azure.

* Het codevoorbeeld van [Azure-voorbeelden](https://github.com/Azure-Samples/azure-iot-samples-ios/archive/master.zip).

* De nieuwste versie van [XCode](https://developer.apple.com/xcode/), met de nieuwste versie van de iOS-SDK. Deze snelstart is getest met XCode 9.3 en iOS 11.3.

* De nieuwste versie van [CocoaPods](https://guides.cocoapods.org/using/getting-started.html).

* Zorg ervoor dat poort 8883 is geopend in uw firewall. Het apparaatvoorbeeld in dit artikel maakt gebruik van het MQTT-protocol, dat communiceert via poort 8883. Deze poort kan worden geblokkeerd in sommige bedrijfs- en educatieve netwerkomgevingen. Zie [Verbinding maken met IoT Hub (MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)voor meer informatie en manieren om dit probleem te omzeilen.

## <a name="simulate-an-iot-device"></a>Een IoT-apparaat simuleren

In deze sectie simuleert u een iOS-apparaat waarop een Swift-toepassing wordt uitgevoerd om cloud-to-device-berichten van de IoT-hub te ontvangen. 

Dit is het voorbeeldapparaat dat u maakt in het artikel [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-ios.md) Als je dat al hebt uitgevoerd, kun je deze sectie overslaan.

### <a name="install-cocoapods"></a>CocoaPods installeren

CocoaPods beheren afhankelijkheden voor iOS-projecten die gebruikmaken van bibliotheken van derden.

Navigeer in een terminalvenster naar de Azure-IoT-Samples-iOS-map die u hebt gedownload bij de vereisten. Navigeer vervolgens naar het voorbeeldproject:

```sh
cd quickstart/sample-device
```

Zorg ervoor dat XCode is gesloten en voer vervolgens de volgende opdracht uit om de CocoaPods te installeren die worden gedeclareerd in het **podfile**-bestand:

```sh
pod install
```

Naast het installeren van de pods die nodig zijn voor uw project, heeft de installatieopdracht ook een XCode-werkruimtebestand gemaakt dat al geconfigureerd is om de pods te gebruiken voor afhankelijkheden.

### <a name="run-the-sample-device-application"></a>De toepassing van het voorbeeldapparaat uitvoeren

1. Haal de verbindingstekenreeks voor uw apparaat op. U deze tekenreeks kopiëren vanuit de [Azure-portal](https://portal.azure.com) in het blade met apparaatdetails of deze ophalen met de volgende opdracht CLI:

    ```azurecli-interactive
    az iot hub device-identity show-connection-string --hub-name {YourIoTHubName} --device-id {YourDeviceID} --output table
    ```

1. Open de voorbeeldwerkruimte in XCode.

   ```sh
   open "MQTT Client Sample.xcworkspace"
   ```

2. Vouw het **MQTT-clientvoorbeeldproject** uit en vervolgens de map met dezelfde naam.  

3. Open **ViewController.swift** om het te bewerken in XCode. 

4. Zoek naar de **variabele connectionString** en werk de waarde bij met de tekenreeks voor apparaatverbinding die u in de eerste stap hebt gekopieerd.

5. Sla uw wijzigingen op. 

6. Voer het project in de apparaatemulator uit met de knop **Build and run** of de toetscombinatie **command+r**.

   ![Het project uitvoeren](media/iot-hub-ios-swift-c2d/run-sample.png)

## <a name="get-the-iot-hub-connection-string"></a>De verbindingstekenreeks voor IoT-hub

In dit artikel maakt u een backendservice om cloud-naar-device-berichten te verzenden via de IoT-hub die u hebt gemaakt in [Telemetrie verzenden van een apparaat naar een IoT-hub.](quickstart-send-telemetry-ios.md) Als u berichten van cloud naar apparaat wilt verzenden, heeft uw service de service-machtiging nodig om verbinding **te maken.** Standaard wordt elke IoT-hub gemaakt met een service met gedeelde toegangsbeleid met de naam **service** die deze toestemming verleent.

[!INCLUDE [iot-hub-include-find-service-connection-string](../../includes/iot-hub-include-find-service-connection-string.md)]

## <a name="simulate-a-service-device"></a>Een serviceapparaat simuleren

In deze sectie simuleert u een tweede iOS-apparaat met een Swift-app die cloud-naar-apparaatberichten via de IoT-hub verzendt. Deze configuratie is handig voor IoT-scenario's waarbij er één iPhone of iPad functioneert als controller voor andere iOS-apparaten die zijn aangesloten op een IoT-hub.

### <a name="install-cocoapods"></a>CocoaPods installeren

CocoaPods beheren afhankelijkheden voor iOS-projecten die gebruikmaken van bibliotheken van derden.

Navigeer naar de map Azure IoT iOS Samples die u in de vereiste voorwaarden hebt gedownload. Navigeer vervolgens naar het voorbeeldserviceproject:

```sh
cd quickstart/sample-service
```

Zorg ervoor dat XCode is gesloten en voer vervolgens de volgende opdracht uit om de CocoaPods te installeren die worden gedeclareerd in het **podfile**-bestand:

```sh
pod install
```

Naast het installeren van de pods die nodig zijn voor uw project, heeft de installatieopdracht ook een XCode-werkruimtebestand gemaakt dat al geconfigureerd is om de pods te gebruiken voor afhankelijkheden.

### <a name="run-the-sample-service-application"></a>De voorbeeldservicetoepassing uitvoeren

1. Open de voorbeeldwerkruimte in XCode.

   ```sh
   open AzureIoTServiceSample.xcworkspace
   ```

2. Vouw het **AzureIoIoTServiceSample-project** uit en vouw vervolgens de map met dezelfde naam uit.  

3. Open **ViewController.swift** om het te bewerken in XCode. 

4. Zoek naar de **variabele connectionString** en werk de waarde bij met de tekenreeks voor serviceverbinding die u eerder hebt gekopieerd in [De verbindingstekenreeks van de IoT-hub .](#get-the-iot-hub-connection-string)

5. Sla uw wijzigingen op.

6. Wijzig in Xcode de emulator-instellingen naar een ander iOS-apparaat dan u gewend bent om het IoT-apparaat uit te voeren. XCode kan niet meerdere emulators van hetzelfde type uitvoeren.

   ![Het emulator-apparaat wijzigen](media/iot-hub-ios-swift-c2d/change-device.png)

7. Voer het project uit in de apparaatemulator met de knop **Bouwen en uitvoeren** of de toetscombinatie Command + **r**.

   ![Het project uitvoeren](media/iot-hub-ios-swift-c2d/run-app.png)

## <a name="send-a-cloud-to-device-message"></a>Een cloud-naar-apparaatbericht verzenden

U bent nu klaar om de twee toepassingen te gebruiken voor het verzenden en ontvangen van cloud-to-device berichten.

1. Klik in de **iOS App Sample-app** die wordt uitgevoerd op het gesimuleerde IoT-apparaat op **Start**. De applicatie begint met het verzenden van device-to-cloud berichten, maar begint ook te luisteren naar cloud-to-device berichten.

   ![Voorbeeld van iot-apparaat-app weergeven](media/iot-hub-ios-swift-c2d/view-d2c.png)

2. Voer in de **IoTHub Service Client Sample-app** die wordt uitgevoerd op het gesimuleerde serviceapparaat de ID in voor het IoT-apparaat waarnaar u een bericht wilt verzenden. 

3. Schrijf een plaintext-bericht en klik op **Verzenden**.

    Verschillende acties gebeuren zodra u op verzenden klikt. Het servicevoorbeeld stuurt het bericht naar uw IoT-hub, waartoe de app toegang heeft vanwege de tekenreeks serviceverbinding die u hebt opgegeven. Uw IoT-hub controleert de apparaat-id, stuurt het bericht naar het doelapparaat en stuurt een bevestigingsbevestiging naar het bronapparaat. De app die wordt uitgevoerd op uw gesimuleerde IoT-apparaat controleert op berichten van IoT Hub en drukt de tekst af van de meest recente op het scherm.

    Uw uitvoer moet er als volgt uitzien:

   ![Berichten van cloud-to-device weergeven](media/iot-hub-ios-swift-c2d/view-c2d.png)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u berichten van cloud naar apparaat verzenden en ontvangen.

Zie de documentatie azure [IoT Solution Accelerators](https://azure.microsoft.com/documentation/suites/iot-suite/) voor voorbeelden van complete end-to-end-oplossingen die IoT Hub gebruiken.

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)voor meer informatie over het ontwikkelen van oplossingen met IoT Hub.

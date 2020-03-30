---
title: MxChip IoT DevKit verbinden met Azure IoT Hub Remote Monitoring
description: In deze zelfstudie leert u hoe u de status van sensoren op IoT DevKit AZ3166 verzendt naar de Azure IoT Remote Monitoring-oplossingsversneller.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/02/2018
ms.author: liydu
ms.openlocfilehash: 9eab035d494892671a2451866311ca06599ec030
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73953724"
---
# <a name="connect-mxchip-iot-devkit-to-azure-iot-remote-monitoring-solution-accelerator"></a>Verbind MXChip IoT DevKit met Azure IoT Remote Monitoring solution accelerator

In deze zelfstudie leert u hoe u een voorbeeld-app op uw DevKit uitvoert om sensorgegevens naar uw Azure IoT Remote Monitoring-oplossingsversneller te verzenden.

De [MXChip IoT DevKit](https://aka.ms/iot-devkit) is een alles-in-één Arduino-compatibel bord met rijke randapparatuur en sensoren. U ontwikkelen voor het met behulp van [Visual Studio Code extensie voor Arduino](https://aka.ms/arduino). En het wordt geleverd met een groeiende [projectcatalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) om u te begeleiden bij het prototype van Internet of Things -oplossingen (IoT) die profiteren van Microsoft Azure-services.

## <a name="what-you-need"></a>Wat u nodig hebt

Voltooi de [handleiding aan de slag](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) voor:

* Uw DevKit hebben aangesloten op Wi-Fi
* De ontwikkelomgeving voorbereiden

Een actief Azure-abonnement. Als u er geen hebt, u zich registreren via een van deze twee methoden:

* Een [gratis Microsoft Azure-account van 30 dagen activeren](https://azure.microsoft.com/free/)

* Uw [Azure-tegoed claimen](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) als u MSDN- of Visual Studio-abonnee bent

## <a name="create-an-azure-iot-remote-monitoring-solution-accelerator"></a>Een Azure IoT Remote Monitoring-oplossingsversneller maken

1. Ga naar [azure IoT-oplossingversnellers site](https://www.azureiotsolutions.com/) en klik **op Een nieuwe oplossing maken.**

   ![Het type Azure IoT-oplossingsversneller selecteren](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-solution-types.png)

   > [!WARNING]
   > Standaard maakt dit voorbeeld een S2 IoT-hub nadat het één IoT Remote Monitoring-oplossingsversneller heeft gemaakt. Als deze IoT-hub niet wordt gebruikt bij een enorm aantal apparaten, raden we u ten zeerste aan deze te downgraden van S2 naar S1 en de IoT Remote Monitoring-oplossingsversneller te verwijderen, zodat de bijbehorende IoT Hub ook kan worden verwijderd wanneer u deze niet meer nodig hebt. 

2. Selecteer **Bewaking op afstand**.

3. Voer een oplossingsnaam in, selecteer een abonnement en een regio en klik op **Oplossing maken**. Het kan even duren voordat de oplossing is ingericht.
  
   ![Oplossing maken](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution.png)

4. Nadat de inrichting is voltooid, klikt u op **Starten**. Sommige gesimuleerde apparaten worden gemaakt voor de oplossing tijdens het inrichtingsproces. Klik **op APPARATEN** om ze te bekijken.

   ![Dashboard](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-solution-created.png)
  
   ![Console](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-console.png)

5. Klik **op Een apparaat TOEVOEGEN**.

6. Klik **op Nieuw toevoegen** voor aangepast **apparaat**.
  
   ![Een nieuw apparaat toevoegen](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-add-new-device.png)

7. Klik **op Laat me mijn eigen apparaat-id definiëren,** voer in `AZ3166`en klik op **Maken**.
  
   ![Apparaat maken met id](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/azure-iot-suite-new-device-configuration.png)

8. Noteer de hostnaam van **IoT Hub**en klik op **Gereed**.

## <a name="open-the-remotemonitoring-sample"></a>Het voorbeeld RemoteMonitoring openen

1. Koppel de DevKit los van uw computer als deze is aangesloten.

2. Start VS Code.

3. Sluit de DevKit aan op uw computer. VS Code detecteert automatisch uw DevKit en opent de volgende pagina's:

   * De DevKit introductiepagina.
   * Arduino Voorbeelden: Hands-on samples om aan de slag te gaan met DevKit.

4. Vouw de sectie **ARDUINO-voorbeelden** aan de linkerkant uit, blader naar **Voorbeelden voor MXCHIP AZ3166 > AzureIoT**en selecteer **RemoteMonitoring**. Het opent een nieuw VS Code-venster met een projectmap erin.

   > [!NOTE]
   > Als u het deelvenster toevallig sluit, u het opnieuw openen. Gebruik `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) om het opdrachtpalet te openen, **Arduino**te typen en vervolgens Arduino te zoeken en te **selecteren: Voorbeelden**.

## <a name="provision-required-azure-services"></a>Vereiste Azure-services voor voorziening

Voer in het oplossingsvenster `Ctrl+P` uw taak `Cmd+P`door `task cloud-provision` (macOS: ) door het opgegeven tekstvak in te voeren.

In de VS Code-terminal begeleidt een interactieve opdrachtregel u bij het inrichten van de vereiste Azure-services.

![Azure-bronnen inrichten](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/provision.png)

## <a name="build-and-upload-the-device-code"></a>De apparaatcode bouwen en uploaden

1. Gebruik `Ctrl+P` (macOS: `Cmd + P`) en typ **taakconfig-device-verbinding**.

2. De terminal vraagt of u een verbindingstekenreeks wilt `task cloud-provision` gebruiken die deze uit de stap ophaalt. U ook uw eigen apparaatverbindingstekenreeks invoeren door op 'Nieuw maken...' te klikken

3. De terminal vraagt u om de configuratiemodus in te voeren. Houd hiervoor knop A ingedrukt en druk op de resetknop en laat deze los. Het scherm toont de DevKit ID en 'Configuration'.

   ![Tekenreeksverbindingstekenreeks](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/config-device-connection.png)

4. Nadat `task config-device-connection` u klaar `F1` bent, klikt u `Arduino: Upload`om VS-codeopdrachten te laden en selecteert u . VS Code begint met het verifiëren en uploaden van de Arduino-schets.
  
   ![Verificatie en upload van de Arduino schets](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/arduino-upload.png)

De DevKit start opnieuw en begint met het uitvoeren van de code.

## <a name="test-the-project"></a>Test het project

Wanneer de voorbeeld-app wordt uitgevoerd, stuurt DevKit sensorgegevens via WiFi naar uw Azure IoT Remote Monitoring-oplossingsversneller. Voer de volgende stappen uit om het resultaat te zien:

1. Ga naar uw Azure IoT Remote Monitoring solution accelerator en klik op **DASHBOARD**.

2. Op de console met remote monitoring-oplossing ziet u de status van uw DevKit-sensor.

   ![Sensorgegevens in Azure IoT Remote Monitoring-oplossingsversneller](media/iot-hub-arduino-iot-devkit-az3166-devkit-remote-monitoring/sensor-status.png)

## <a name="change-device-id"></a>Apparaat-id wijzigen

Als u de hardcoded **AZ3166** wilt wijzigen in een aangepaste apparaat-id in de code, wijzigt u de coderegel die wordt weergegeven in het voorbeeld van [externe bewaking](https://github.com/Microsoft/devkit-sdk/blob/master/AZ3166/src/libraries/AzureIoT/examples/RemoteMonitoring/RemoteMonitoring.ino#L23).

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de veelgestelde vragen van [de IoT-ontwikkelaarskit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neem contact met ons op via de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat aansluiten op uw Azure IoT Remote Monitoring-oplossingsversneller en de sensorgegevens visualiseren, volgen hier de voorgestelde volgende stappen:

* [Overzicht van Azure IoT-oplossingsversnellers](https://docs.microsoft.com/azure/iot-suite/)

* [Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing](/azure/iot-central/core/howto-connect-devkit)

* [IoT-ontwikkelaarskit](https://microsoft.github.io/azure-iot-developer-kit/) 

---
title: Berichten verzenden naar een MQTT-server met Azure MQTT-clientbibliotheek
description: Meer informatie over het gebruik van de MQTT-clientbibliotheek om berichten naar een MQTT-broker te verzenden. Lees ook hoe u uw mXChip IoT DevKit configureren als MQTT-client.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/02/2018
ms.author: liydu
ms.custom: mqtt
ms.openlocfilehash: 600e64ef5bc3329f0116359066bdcdaf42c13e2e
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81733493"
---
# <a name="send-messages-to-an-mqtt-server"></a>Berichten verzenden naar een MQTT-server

Internet of Things (IoT)-systemen hebben vaak te maken met intermitterende, slechte kwaliteit of trage internetverbindingen. MQTT is een machine-to-machine (M2M) connectiviteitsprotocol, dat is ontwikkeld met dergelijke uitdagingen in het achterhoofd. 

De MQTT client bibliotheek die hier wordt gebruikt is onderdeel van de [Eclipse Paho](https://www.eclipse.org/paho/) project, die API's biedt voor het gebruik van MQTT over meerdere transportmiddelen.

## <a name="what-you-learn"></a>Wat u leert

In dit project leert u:
- De MQTT-clientbibliotheek gebruiken om berichten naar een MQTT-broker te sturen.
- Zo configureer je je MXChip Iot DevKit als MQTT-client.

## <a name="what-you-need"></a>Wat u nodig hebt

Voltooi de [handleiding aan de slag](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started) voor:

* Uw DevKit hebben aangesloten op Wi-Fi
* De ontwikkelomgeving voorbereiden

## <a name="open-the-project-folder"></a>De projectmap openen

1. Als de DevKit al verbinding heeft met uw computer, koppelt u deze los.

2. Start VS Code.

3. Sluit de DevKit aan op uw computer.

## <a name="open-the-mqttclient-sample"></a>Het MQTTClient-voorbeeld openen

Vouw de **sectie ARDUINO-voorbeelden** aan de linkerkant uit, blader naar **Voorbeelden voor MXCHIP AZ3166 > MQTT**en selecteer **MQTTClient**. Er wordt een nieuw VS-codevenster geopend met een projectmap erin.

> [!NOTE]
> U ook voorbeeld openen vanuit het opdrachtpalet. Gebruik `Ctrl+Shift+P` (macOS: `Cmd+Shift+P`) om het opdrachtpalet te openen, **Arduino**te typen en vervolgens Arduino te zoeken en te **selecteren: Voorbeelden**.

## <a name="build-and-upload-the-arduino-sketch-to-the-devkit"></a>Bouw en upload de Arduino-schets naar de DevKit

Typ `Ctrl+P` (macOS: `Cmd+P`) `task device-upload`om uit te voeren . Zodra de upload is voltooid, wordt DevKit opnieuw opgestart en wordt de schets uitgevoerd.

![apparaatupload](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/device-upload.jpg)

> [!NOTE]
> U ontvangt mogelijk een foutmelding "Fout: AZ3166: Onbekend pakket". Deze fout treedt op wanneer de index van het bordpakket niet correct wordt vernieuwd. Als u deze fout wilt oplossen, raadpleegt u het [ontwikkelgedeelte van de Veelgestelde vragen over IoT-DevKit.](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/#development)

## <a name="test-the-project"></a>Test het project

Volg in VS-code deze procedure om de seriële monitor te openen en in te stellen:

1. Klik `COM[X]` op het woord op de statusbalk `STMicroelectronics` ![om de juiste COM-poort in te stellen met : set-com-port](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-com-port.jpg)

2. Klik op het pictogram van de stekkerop ![de statusbalk om de seriële monitor te openen: seriële monitor](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-monitor.jpg)
  
3. Klik op de statusbalk op het getal dat het `115200` ![Baud-tarief vertegenwoordigt en stel deze in op : set-baud-rate](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/set-baud-rate.jpg)

In de seriële monitor worden alle berichten weergegeven die door de voorbeeldschets worden verzonden. De schets verbindt de DevKit met Wi-Fi. Zodra de Wi-Fi-verbinding succesvol is, stuurt de schets een bericht naar de MQTT-broker. Daarna stuurt het voorbeeld herhaaldelijk twee "iot.eclipse.org" berichten met respectievelijk QoS 0 en QoS 1.

![seriële uitvoer](media/iot-hub-arduino-iot-devkit-az3166-mqtt-helloworld/serial-output.jpg)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de Veelgestelde vragen over [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of maakt u verbinding via de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="see-also"></a>Zie ook

* [IoT DevKit AZ3166 verbinden met Azure IoT Hub in de cloud](iot-hub-arduino-iot-devkit-az3166-get-started.md)
* [Schudden, schudden voor een Tweet](iot-hub-arduino-iot-devkit-az3166-retrieve-twitter-message.md)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u uw MXChip Iot DevKit configureren als MQTT-client en de MQTT-clientbibliotheek gebruiken om berichten naar een MQTT-broker te verzenden, volgen hier de voorgestelde volgende stappen:

* [Overzicht van azure IoT Remote Monitoring-oplossingsversneller](https://docs.microsoft.com/azure/iot-suite/)
* [Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing](/azure/iot-central/core/howto-connect-devkit)

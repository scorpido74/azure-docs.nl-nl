---
title: Azure-apparaattweelingen gebruiken om de LED van MXChip IoT DevKit-gebruikers te beheren | Microsoft Documenten
description: In deze zelfstudie leert u hoe u DevKit-statussen controleren en de gebruikers-LED beheren met Azure IoT Hub-apparaattweelingen.
author: liydu
manager: jeffya
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/04/2018
ms.author: liydu
ms.openlocfilehash: deb1ea8c7b41ad48bddebfbed1b15c667ee0071a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73483952"
---
# <a name="mxchip-iot-devkit"></a>MXChip IoT DevKit

U dit voorbeeld gebruiken om de MXChip IoT DevKit WiFi-informatie en sensortoestanden te controleren en om de kleur van de gebruikers-LED te regelen met Azure IoT Hub-apparaattweelingen.

## <a name="what-you-learn"></a>Wat u leert

- Hoe de MXChip IoT DevKit-sensor toestanden te controleren.

- Azure-apparaattweeling en -toepassing gebruiken om de kleur van de RGB LED van de DevKit te beheren.

## <a name="what-you-need"></a>Wat u nodig hebt

- Stel uw ontwikkelomgeving in door de [handleiding Aan de slag te volgen.](https://docs.microsoft.com/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)

- Typ in uw GitBash-terminalvenster (of een andere Git-opdrachtregelinterface de volgende opdrachten:

   ```bash
   git clone https://github.com/DevKitExamples/DevKitState.git
   cd DevKitState
   code .
   ```

## <a name="provision-azure-services"></a>Azure-services inrichten

1. Klik op de vervolgkeuzelijst **Taken** in Visual Studio Code en selecteer **Taak uitvoeren...**  -  **cloudvoorziening**.

2. Uw voortgang wordt weergegeven onder het tabblad **TERMINAL** van het deelvenster **Welkom.**

3. Selecteer een abonnement wanneer u wordt gevraagd welk *abonnement u wilt kiezen.*

4. Selecteer of kies een resourcegroep. 
 
   > [!NOTE]
   > Als je al een gratis IoT Hub hebt, kun je deze stap overslaan.

5. Wanneer u wordt gevraagd met het bericht *Welke IoT-hub wilt u kiezen,* selecteert of maakt u een IoT-hub.

6. Iets wat lijkt op *functie app: functie app naam: xxx*, wordt weergegeven. Noteer de naam van de functie-app; het zal in een latere stap worden gebruikt.

7. Wacht tot de implementatie van de Azure Resource Manager-sjabloon is voltooid, wat wordt aangegeven wanneer de implementatie van de sjabloon *Message Resource Manager: Gereed* wordt weergegeven.

## <a name="deploy-function-app"></a>Functie-app implementeren

1. Klik op de vervolgkeuzelijst **Taken** in Visual Studio Code en selecteer **Taak uitvoeren...**  -  **cloud-deploy**.

2. Wacht tot het uploadproces van functie-app-codes is voltooid; de *app berichtfunctie wordt geïmplementeerd: Gereed* wordt weergegeven.

## <a name="configure-iot-hub-device-connection-string-in-devkit"></a>Verbindingstekenreeks voor IoT-hub-apparaten configureren in DevKit

1. Sluit uw MXChip IoT DevKit aan op uw computer.

2. Klik op de vervolgkeuzelijst **Taken** in Visual Studio Code en selecteer **Taak uitvoeren...**  -  **config-device-aansluiting**

3. Druk op de MXChip IoT DevKit op knop **A**ingedrukt, druk op de **resetknop** en laat vervolgens knop **A** los om de DekKit de configuratiemodus in te laten gaan.

4. Wacht tot het configuratieproces van de verbindingstekenreeks is voltooid.

## <a name="upload-arduino-code-to-devkit"></a>Arduino-code uploaden naar DevKit

Met uw MXChip IoT DevKit aangesloten op uw computer:

1. Klik op de vervolgkeuzelijst **Taken** in Visual Studio Code en selecteer **Taak uitvoeren...** De Arduino schets wordt gecompileerd en geüpload naar de DevKit.

2. Wanneer de schets is geüpload, wordt een *Build & Upload Sketch: succesbericht* weergegeven.

## <a name="monitor-devkit-state-in-browser"></a>DevKit-status in browser controleren

1. Open in een webbrowser `DevKitState\web\index.html` het bestand dat is gemaakt tijdens de stap Wat u nodig hebt.

2. De volgende webpagina wordt weergegeven:![Geef de naam van de functie-app op.](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state-function-app-name.png)

3. Voer de naam van de functie-app in die u eerder hebt opgeschreven.

4. Klik **op** de knop Verbinding maken

5. Binnen enkele seconden vernieuwt en toont de pagina de WiFi-verbindingstatus van de DevKit en de status van elk van de sensoren aan boord.

## <a name="control-the-devkits-user-led"></a>Bedien de gebruikers-led van de DevKit

1. Klik op de gebruikers-LED-afbeelding in de afbeelding van de webpagina.

2. Binnen enkele seconden wordt het scherm vernieuwd en wordt de huidige kleurstatus van de gebruikers-LED weergegeven.

3. Probeer de kleurwaarde van de RGB LED te wijzigen door op verschillende locaties op de schuifregelaars voor RGB te klikken.

## <a name="example-operation"></a>Voorbeeldbewerking

![Voorbeeldtestprocedure](media/iot-hub-arduino-iot-devkit-az3166-devkit-state/devkit-state.gif)

> [!NOTE]
> U ruwe gegevens van apparaattweeling zien in\> Azure-portal: IoT Hub - IoT-apparaten -\> * \<uw apparaat\> *  - \> Device Twin.

## <a name="next-steps"></a>Volgende stappen

Je hebt geleerd hoe je:
- Sluit een MXChip IoT DevKit-apparaat aan op uw Azure IoT Remote Monitoring-oplossingsversneller.
- Gebruik de Azure IoT-apparaattweelingfunctie om de kleur van de RGB LED van de DevKit te voelen en te beheren.

Hier volgen de voorgestelde volgende stappen:

* [Overzicht van azure IoT Remote Monitoring-oplossingsversneller](https://docs.microsoft.com/azure/iot-suite/)
* [Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing](/azure/iot-central/core/howto-connect-devkit)

---
title: IoT DevKit verbinden met oplossing voor bewaking op afstand - Azure | Microsoft Documenten
description: In deze handleiding leert u hoe u telemetrie van de sensoren op IoT DevKit AZ3166-apparaat de versneller van de Afstandsbediening-oplossing voor bewaking en visualisatie verzenden.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73888871"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Sluit een IoT DevKit-apparaat aan op de remote monitoring oplossingsversneller

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

Deze handleiding laat je zien hoe je een voorbeeldtoepassing uitvoert op je IoT DevKit-apparaat. De voorbeeldcode stuurt telemetrie van de sensoren op het DevKit-apparaat naar uw oplossingsversneller.

De [MXChip IoT DevKit](https://aka.ms/iot-devkit) is een alles-in-één Arduino-compatibel bord met rijke randapparatuur en sensoren. U hiervoor ontwikkelen met [Azure IoT Device Workbench](https://aka.ms/iot-workbench) of [Azure IoT Tools](https://aka.ms/azure-iot-tools) extension pack in Visual Studio Code. De [projectcatalogus](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) bevat voorbeeldtoepassingen om u te helpen iot-oplossingen te prototypen.

## <a name="before-you-begin"></a>Voordat u begint

Voer eerst de volgende taken uit om de stappen in deze zelfstudie uit te voeren:

* Bereid uw DevKit voor door de stappen te volgen in [Connect IoT DevKit AZ3166 in azure IoT Hub in de cloud.](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started)

## <a name="open-sample-project"></a>Voorbeeldproject openen

Ga als volgt te werk om het voorbeeld voor externe bewaking in VS-code te openen:

1. Zorg ervoor dat uw IoT DevKit niet op uw computer staat. Start VS Code eerst en sluit de DevKit aan op uw computer.

1. Klik `F1` hierom het opdrachtpalet te openen, de **Werkbank voor Azure IoT-apparaat te**typen en te selecteren: Voorbeelden openen... . Selecteer vervolgens **IoT DevKit** als bord.

1. Zoek **externe bewaking** en klik op Voorbeeld **openen**. Er wordt een nieuw VS-codevenster geopend met de projectmap:

   ![IoT-werkbank, selecteer voorbeeld van externe bewaking](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Het apparaat configureren

Ga als eerste voor een configureren van de verbindingstekenreeks van iot-hub-apparaten op uw DevKit-apparaat:

1. Schakel de IoT DevKit over naar **de configuratiemodus:**

    * Ingedrukt houden knop **A**.
    * Druk op de knop **Opnieuw instellen** en laat deze los.

1. Het scherm toont de DevKit ID en `Configuration`.

    ![IoT-configuratiemodus voor DevKit](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Druk op **F1** om het opdrachtpalet te openen, typ en selecteer **Azure IoT Device Workbench: Device Settings configureren... > Config Device Connection String**.

1. Plak de verbindingstekenreeks die u eerder hebt gekopieerd en druk op **Enter** om het apparaat te configureren.

## <a name="build-the-code"></a>De code bouwen

Ga als het gaat om het bouwen en uploaden van de apparaatcode:

1. Druk `F1` om het opdrachtpalet te openen, typ en selecteer **Azure IoT Device Workbench: Device Code uploaden:**

1. VS Code compileert en uploadt de code naar uw DevKit-apparaat:

    ![IoT Workbench: Apparaat - > geüpload](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Het DevKit-apparaat herstart en voert de code uit die u hebt geüpload.

## <a name="test-the-sample"></a>Test het monster

Voer de volgende stappen uit om te controleren of de voorbeeldtoepassing die u naar het DevKit-apparaat hebt geüpload, werkt:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>De telemetrie weergeven die naar de oplossing voor externe bewaking wordt verzonden

Wanneer de voorbeeld-app wordt uitgevoerd, stuurt het DevKit-apparaat telemetrie van de sensoren gegevens via Wi-Fi naar uw oplossingsversneller. Ga als gebruiker naar de telemetrie:

1. Ga naar het dashboard van uw oplossing en klik op **Device Explorer**.

1. Klik op de apparaatnaam van uw DevKit-apparaat. op het tabblad rechts u de telemetrie van de DevKit in realtime zien:

    ![Sensorgegevens in Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Het DevKit-apparaat bedienen

Met de Remote Monitoring-oplossingsversneller u uw apparaat op afstand bedienen. De voorbeeldcode implementeert drie methoden die u zien in de sectie **Methode** wanneer u het apparaat selecteert op de pagina **Device Explorer:**

![IoT DevKit-methoden](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Als u de kleur van een van de DevKit-LED's wilt wijzigen, gebruikt u de **LedColor-methode:**

1. Selecteer de apparaatnaam in de lijst met apparaten en klik op de **taken:**

    ![Een taak maken](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Configureer de taken met de volgende waarden en klik op **Toepassen:**

   * Taak: **methode uitvoeren selecteren**
   * Methodenaam: **LedColor**
   * Functienaam: **ChangeLedColor**

     ![Taakinstellingen](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Na een paar seconden verandert de kleur van de RGB LED (onder de knop A) op je DevKit:

    ![IoT DevKit rood geleid](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te gaan met de zelfstudies, laat u de verbetering voor de externe bewakingsoplossing geïmplementeerd.

Als u de oplossingsversneller niet meer nodig hebt, verwijdert u deze van de pagina Ingerichte oplossingen door deze te selecteren en vervolgens op Oplossing verwijderen:

![Oplossing verwijderen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleegt u de Veelgestelde vragen van [IoT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neem contact met ons op via de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat aansluiten op uw versneller met externe bewakingsoplossingen, volgen hier enkele voorgestelde volgende stappen:

* [Overzicht van Azure IoT-oplossingsversnellers](https://docs.microsoft.com/azure/iot-accelerators/)
* [De gebruikersinterface aanpassen](iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit verbinden met uw Azure IoT Central-toepassing](../iot-central/core/howto-connect-devkit.md)
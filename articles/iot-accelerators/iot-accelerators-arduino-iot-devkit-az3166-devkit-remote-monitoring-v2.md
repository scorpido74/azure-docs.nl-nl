---
title: Externe bewakings oplossing voor IoT DevKit koppelen-Azure | Microsoft Docs
description: In deze hand leiding vindt u informatie over het verzenden van telemetrie van de Sens oren op IoT DevKit AZ3166-apparaat de naar de oplossings versneller voor externe controle voor bewaking en visualisatie.
author: isabelcabezasm
manager: ''
ms.service: iot-accelerators
services: iot-accelerators
ms.devlang: c
ms.topic: conceptual
ms.date: 11/29/2018
ms.author: isacabe
ms.openlocfilehash: 6e9f9c89cf2e5e40d37a1532e688490aae294181
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888871"
---
# <a name="connect-an-iot-devkit-device-to-the-remote-monitoring-solution-accelerator"></a>Een IoT DevKit-apparaat verbinden met de oplossings versneller voor externe controle

[!INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

In deze hand leiding vindt u informatie over het uitvoeren van een voorbeeld toepassing op uw IoT DevKit-apparaat. De voorbeeld code verzendt telemetrie van de Sens oren op het DevKit-apparaat naar uw oplossings versneller.

[MXChip IOT DevKit](https://aka.ms/iot-devkit) is een alles-in-een Arduino-compatibel bord met rijke rand apparatuur en Sens oren. U kunt dit ontwikkelen met behulp van [Azure IOT Device Workbench](https://aka.ms/iot-workbench) of [Azure IOT tools](https://aka.ms/azure-iot-tools) Extension Pack in Visual Studio code. De [catalogus projecten](https://microsoft.github.io/azure-iot-developer-kit/docs/projects/) bevat voorbeeld toepassingen die u helpen bij het prototypen van IOT-oplossingen.

## <a name="before-you-begin"></a>Voordat u begint

Voer eerst de volgende taken uit om de stappen in deze zelf studie uit te voeren:

* Bereid uw DevKit voor door de stappen in [Connect IOT DEVKIT AZ3166 te volgen op Azure IOT hub in de Cloud](/azure/iot-hub/iot-hub-arduino-iot-devkit-az3166-get-started).

## <a name="open-sample-project"></a>Voorbeeld project openen

Ga als volgt te werk om het voor beeld voor externe controle in VS code te openen:

1. Zorg ervoor dat uw IoT-DevKit niet naar uw computer. Start VS code eerst en sluit de DevKit aan op uw computer.

1. Klik op `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: voor beelden openen...** . Selecteer vervolgens **IOT DevKit** as Board.

1. Zoek **externe controle** en klik op voor **Beeld openen**. Er wordt een nieuw versus code venster geopend met daarin de projectmap:

   ![IoT Workbench, Selecteer voor beeld van externe controle](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-example.png)

## <a name="configure-the-device"></a>Het apparaat configureren

IoT Hub apparaat connection string op uw DevKit-apparaat configureren:

1. Schakel de IoT-DevKit over naar de **configuratie modus**:

    * Houd **de**knop ingedrukt.
    * Push de **Reset** -knop en laat deze los.

1. In het scherm worden de DevKit-ID en de `Configuration`weer gegeven.

    ![IoT DevKit-configuratie modus](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/devkit-configuration-mode.png)

1. Druk op **F1** om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: Apparaatinstellingen configureren... Verbindings reeks voor configuratie apparaat >** .

1. Plak de connection string die u eerder hebt gekopieerd en druk op **Enter** om het apparaat te configureren.

## <a name="build-the-code"></a>De code bouwen

De toestel code bouwen en uploaden:

1. Druk op `F1` om het opdracht palet te openen, typ en selecteer **Azure IOT Device Workbench: Upload de apparaatcode**:

1. VS code compileert en uploadt de code naar uw DevKit-apparaat:

    ![IoT Workbench: apparaat-> geüpload](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-workbench-device-uploaded.png)

1. Het DevKit-apparaat wordt opnieuw opgestart en de code die u hebt geüpload, wordt uitgevoerd.

## <a name="test-the-sample"></a>Het voor beeld testen

Voer de volgende stappen uit om te controleren of de voorbeeld toepassing die u hebt geüpload naar het DevKit-apparaat werkt:

### <a name="view-the-telemetry-sent-to-remote-monitoring-solution"></a>De telemetrie weer geven die is verzonden naar de oplossing voor externe controle

Wanneer de voor beeld-app wordt uitgevoerd, verzendt het DevKit-apparaat telemetrie van de sensor gegevens via Wi-Fi naar uw oplossings versneller. Voor een overzicht van de telemetrie:

1. Ga naar het dash board van de oplossing en klik op **device Explorer**.

1. Klik op de apparaatnaam van uw DevKit-apparaat. op het tabblad aan de rechter kant kunt u de telemetrie van de DevKit in realtime bekijken:

    ![Sensor gegevens in Azure IoT Suite](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-dashboard.png)

### <a name="control-the-devkit-device"></a>Het DevKit-apparaat beheren

Met de oplossings versneller voor externe controle kunt u uw apparaat op afstand beheren. De voorbeeld code implementeert drie methoden die u kunt zien in het gedeelte **methode** wanneer u het apparaat op de pagina **device Explorer** selecteert:

![IoT DevKit-methoden](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-methods.png)

Als u de kleur van een van de DevKit-Led's wilt wijzigen, gebruikt u de methode **LedColor** :

1. Selecteer de apparaatnaam in de lijst met apparaten en klik op de **taken**:

    ![Een taak maken](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-job.png)

1. Configureer de taken met behulp van de volgende waarden en klik op **Toep assen**:

   * Taak selecteren: **methode Run**
   * Naam methode: **LedColor**
   * Taak naam: **ChangeLedColor**

     ![Taakinstellingen](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/iot-suite-change-color.png)

1. Na een paar seconden wordt de kleur van de RGB-LED (onder de knop A) op uw DevKit gewijzigd:

    ![IoT DevKit Red-LED](media/iot-accelerators-arduino-iot-devkit-az3166-devkit-remote-monitoringv2/azure-iot-suite-devkit-led.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u van plan bent verder te gaan met de zelfstudies, laat u de verbetering voor de externe bewakingsoplossing geïmplementeerd.

Als u de oplossings versneller niet meer nodig hebt, verwijdert u deze van de pagina ingerichte oplossingen door deze te selecteren en vervolgens op oplossing verwijderen te klikken:

![Oplossing verwijderen](media/quickstart-remote-monitoring-deploy/deletesolution.png)

## <a name="problems-and-feedback"></a>Problemen en feedback

Als u problemen ondervindt, raadpleeg dan [de veelgestelde vragen over IOT DevKit](https://microsoft.github.io/azure-iot-developer-kit/docs/faq/) of neem contact op met de volgende kanalen:

* [Gitter.im](https://gitter.im/Microsoft/azure-iot-developer-kit)
* [Stack Overflow](https://stackoverflow.com/questions/tagged/iot-devkit)

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat verbindt met uw oplossings versneller voor externe controle, ziet u de volgende stappen:

* [Overzicht van de Azure IoT-oplossings Accelerators](https://docs.microsoft.com/azure/iot-accelerators/)
* [De gebruikersinterface aanpassen](iot-accelerators-remote-monitoring-customize.md)
* [IoT DevKit verbinden met uw Azure IoT Central-toepassing](../iot-central/core/howto-connect-devkit.md)
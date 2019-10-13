---
title: Een DevKit-apparaat verbinden met uw Azure IoT Central-toepassing | Microsoft Docs
description: Als ontwikkelaar van een apparaat leert u hoe u met IoT Plug en Play een MXChip IoT DevKit-apparaat verbindt met uw Azure IoT Central-toepassing.
author: liydu
ms.author: liydu
ms.date: 08/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: b7d2e1b08653cb8023ef6a5190ab53ecc3d568a6
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72297042"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing

In dit artikel wordt beschreven hoe u een MXChip IoT DevKit (DevKit)-apparaat verbindt met een Azure IoT Central-toepassing. Het apparaat gebruikt het gecertificeerde IoT Plug en Play model voor het DevKit-apparaat om de verbinding met IoT Central te configureren.

In dit procedure-artikel:

- Haal de verbindings gegevens op uit uw IoT Central-toepassing.
- Bereid het apparaat voor en sluit het aan op uw IoT Central-toepassing.
- Bekijk de telemetrie en eigenschappen van het apparaat in IoT Central.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

1. Een [DevKit-apparaat](https://aka.ms/iot-devkit-purchase).
1. Een IoT Central toepassing die is gemaakt op basis van de sjabloon **voor de voorbeeld toepassing** . U kunt de stappen in [een IoT Plug en Play-toepassing maken](./quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)volgen.

## <a name="get-device-connection-details"></a>Verbindings Details van apparaat ophalen

Selecteer in uw Azure IoT Central-toepassing het tabblad **beheer** en selecteer **apparaat-verbinding**. Noteer het **id-bereik** en de **primaire sleutel**.

![Verbindings Details van de apparaatgroep](media/howto-connect-devkit-pnp/device-group-connection-details.png)

## <a name="prepare-the-device"></a>Het apparaat voorbereiden

1. Down load de nieuwste [, vooraf gebouwde Azure-IoT Central Plug en Play firmware](https://github.com/MXCHIP/IoTDevKit/raw/master/pnp/iotc_devkit/bin/iotc_devkit.bin) voor het DevKit-apparaat van github.

1. Verbind het DevKit-apparaat met de ontwikkel computer via een USB-kabel. In Windows wordt een bestand Verkenner-venster geopend op een station dat is toegewezen aan de opslag op het DevKit-apparaat. Het station kan bijvoorbeeld **AZ3166 (D:)** worden genoemd.

1. Sleep het **iotc_devkit. bin** -bestand naar het station-venster. Wanneer het kopiëren is voltooid, wordt het apparaat opnieuw opgestart met de nieuwe firmware.

    > [!NOTE]
    > Als er fouten op het scherm worden weer gegeven, zoals **geen Wi-Fi**, is dit omdat de DevKit nog niet is verbonden met WiFi.

1. Houd op de DevKit **knop b**, druk op de knop **Reset** en laat deze los en laat vervolgens de **knop b**los. Het apparaat bevindt zich nu in de modus toegangs punt. Als u wilt bevestigen, worden in het scherm ' IoT DevKit-AP ' en het IP-adres van de configuratie portal weer gegeven.

1. Op uw computer of Tablet maakt u verbinding met de Wi-Fi-netwerk naam die op het scherm van het apparaat wordt weer gegeven. Het WiFi-netwerk wordt gestart met **AZ,** gevolgd door het MAC-adres. Wanneer u verbinding met dit netwerk maakt, hebt u geen toegang tot internet. Deze status wordt verwacht en u kunt gedurende korte tijd alleen verbinding maken met dit netwerk tijdens het configureren van het apparaat.

1. Open uw webbrowser en ga naar [http://192.168.0.1/](http://192.168.0.1/). De volgende webpagina wordt weer gegeven:

    ![Gebruikers interface configureren](media/howto-connect-devkit-pnp/config-ui.png)

    Voer op de webpagina het volgende in:

    - De naam van uw WiFi-netwerk (SSID).
    - Uw WiFi-netwerk wachtwoord.
    - De verbindings Details: de **apparaat-id** die u zelf kunt kiezen en het **id-bereik** en de **groeps-SAS-primaire sleutel** die u eerder hebt genoteerd.

    > [!NOTE]
    > Momenteel kan IoT DevKit alleen verbinding maken met 2,4 GHz Wi-Fi. 5 GHz wordt niet ondersteund als gevolg van hardwarebeperkingen.

1. Kies **apparaat configureren**, het DevKit-apparaat wordt opnieuw opgestart en de toepassing wordt uitgevoerd:

    ![Gebruikers interface opnieuw opstarten](media/howto-connect-devkit-pnp/reboot-ui.png)

    In het scherm DevKit wordt een bevestiging weer gegeven dat de toepassing wordt uitgevoerd:

    ![DevKit wordt uitgevoerd](media/howto-connect-devkit-pnp/devkit-running.png)

De DevKit registreert eerst een nieuw apparaat in IoT Central toepassing en begint vervolgens met het verzenden van gegevens.

## <a name="view-the-telemetry"></a>De telemetrie weer geven

In deze stap bekijkt u de telemetrie in uw Azure IoT Central-toepassing.

Selecteer op het tabblad **apparaten** In uw IOT Central-toepassing het apparaat dat u hebt toegevoegd. Op het tabblad **overzicht** kunt u de telemetrie van het DevKit-apparaat zien:

   ![Overzicht van IoT Central apparaat](media/howto-connect-devkit-pnp/mxchip-overview-page.png)

## <a name="review-the-code"></a>De code bekijken

Ga naar de [code voorbeelden](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/)om de code te bekijken of te wijzigen en te compileren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een DevKit-apparaat verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie over het [instellen van een sjabloon voor aangepaste apparaten](./howto-set-up-template-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json) voor uw eigen IOT-apparaat.

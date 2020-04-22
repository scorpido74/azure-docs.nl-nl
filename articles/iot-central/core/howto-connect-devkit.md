---
title: Een DevKit-apparaat aansluiten op uw Azure IoT Central-toepassing | Microsoft Documenten
description: Als apparaatontwikkelaar leert u hoe u een MXChip IoT DevKit-apparaat aansluit op uw Azure IoT Central-toepassing met behulp van IoT Plug and Play (preview).
author: liydu
ms.author: liydu
ms.date: 12/03/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: jeffya
ms.openlocfilehash: bcf1dd2f89cf049d7da5b56170b2c13874c83ba4
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81756803"
---
# <a name="connect-an-mxchip-iot-devkit-device-to-your-azure-iot-central-application"></a>Een MXChip IoT DevKit-apparaat verbinden met uw Azure IoT Central-toepassing

*Dit artikel is van toepassing op apparaatontwikkelaars.*

In dit artikel ziet u hoe u een MXChip IoT DevKit-apparaat (DevKit) verbinden met een Azure IoT Central-toepassing. Het apparaat maakt gebruik van het gecertificeerde IoT Plug and Play -model (preview) voor het DevKit-apparaat om de verbinding met IoT Central te configureren.

In dit how-to artikel, u:

- Haal de verbindingsgegevens op van uw IoT Central-toepassing.
- Bereid het apparaat voor en sluit het aan op uw IoT Central-toepassing.
- Bekijk de telemetrie en eigenschappen van het apparaat in IoT Central.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

- Een [DevKit-apparaat](https://aka.ms/iot-devkit-purchase).
- Een IoT Central-toepassing. U de stappen volgen in [Een IoT Central-toepassing maken.](./quick-deploy-iot-central.md)

## <a name="get-device-connection-details"></a>Gegevens over apparaatverbinding opvragen

1. Selecteer in uw Azure IoT Central-toepassing het tabblad **Apparaatsjablonen** en selecteer **+ Nieuw**. Selecteer **MXChip IoT DevKit**in de sectie **Gebruik een vooraf geconfigureerde apparaatsjabloon**.

    ![Apparaatsjabloon voor MXChip IoT DevKit](media/howto-connect-devkit/device-template.png)

1. Selecteer **Volgende: Aanpassen** en vervolgens **Maken**.

1. Selecteer het tabblad **Apparaten.** Selecteer **mxchip IoT DevKit** in de lijst met apparaten en selecteer **+ Nieuw** om een nieuw apparaat te maken op de sjabloon.

    ![Nieuw apparaat](media/howto-connect-devkit/new-device.png)

1. Voer in het pop-upvenster de `SampleDevKit` **apparaat-id** als en **apparaatnaam** in als `MXChip IoT DevKit - Sample`. Controleer of de **optie Gesimuleerd** is uitgeschakeld. Selecteer vervolgens **Maken**.

    ![Apparaat-ID en -naam](media/howto-connect-devkit/device-id-name.png)

1. Selecteer het apparaat dat u hebt gemaakt en selecteer **Verbinding maken**. Noteer de **id-scope,** **apparaat-id**en **primaire sleutel**. U moet deze waarden later in dit how-to artikel.

    ![Gegevens over apparaatverbinding](media/howto-connect-devkit/device-connection-info.png)

## <a name="prepare-the-device"></a>Het apparaat voorbereiden

1. Download de nieuwste [vooraf gebouwde Azure IoT Central Plug and Play (preview) firmware](https://github.com/Azure-Samples/mxchip-iot-devkit-pnp/raw/master/bin/iotc_devkit.bin) voor het DevKit-apparaat van GitHub.

1. Sluit het DevKit-apparaat aan op uw ontwikkelingsmachine via een USB-kabel. In Windows wordt een venster met verkennergeopend op een station dat is toegewezen aan de opslag op het DevKit-apparaat. De schijf kan bijvoorbeeld **AZ3166 (D:)** worden genoemd.

1. Sleep het **bestand iotc_devkit.bin** naar het stationsvenster. Wanneer het kopiëren is voltooid, wordt het apparaat opnieuw opgestart met de nieuwe firmware.

    > [!NOTE]
    > Als u fouten op het scherm ziet, zoals **Geen Wi-Fi,** is dit omdat de DevKit nog niet is verbonden met WiFi.

1. Houd op de DevKit **knop B**ingedrukt, druk op de **knop Opnieuw instellen** en laat vervolgens knop **B**los. Het apparaat bevindt zich nu in de toegangspuntmodus. Om dit te bevestigen, wordt op het scherm "IoT DevKit - AP" en het IP-adres van de configuratieportal weergegeven.

1. Maak op uw computer of tablet verbinding met de wifi-netwerknaam die wordt weergegeven op het scherm van het apparaat. Het WiFi-netwerk begint met **AZ,** gevolgd door het MAC-adres. Wanneer u verbinding maakt met dit netwerk, hebt u geen toegang tot internet. Deze status wordt verwacht en u maakt slechts korte tijd verbinding met dit netwerk terwijl u het apparaat configureert.

1. Open uw webbrowser en [http://192.168.0.1/](http://192.168.0.1/)navigeer naar . Op de volgende webpagina wordt het volgende weergegeven:

    ![Config UI](media/howto-connect-devkit/config-ui.png)

    Voer op de webpagina het als:

    - De naam van uw WiFi-netwerk (SSID).
    - Uw WiFi-netwerkwachtwoord.
    - De verbindingsgegevens: voer de **apparaat-id,** **id-scope**en **SAS Primary Key** in waar u eerder een notitie van hebt gemaakt.

    > [!NOTE]
    > Momenteel kan de IoT DevKit alleen verbinding maken met 2,4 GHz Wi-Fi, 5 GHz wordt niet ondersteund vanwege hardwarebeperkingen.

1. Kies **Apparaat configureren,** het DevKit-apparaat wordt opnieuw opgestart en voert de toepassing uit:

    ![Gebruikersinterface opnieuw opstarten](media/howto-connect-devkit/reboot-ui.png)

    Het DevKit-scherm geeft een bevestiging weer dat de toepassing wordt uitgevoerd:

    ![DevKit draait](media/howto-connect-devkit/devkit-running.png)

De DevKit registreert eerst een nieuw apparaat in de IoT Central-toepassing en begint vervolgens met het verzenden van gegevens.

## <a name="view-the-telemetry"></a>Bekijk de telemetrie

In deze stap bekijkt u de telemetrie in uw Azure IoT Central-toepassing.

Selecteer in uw IoT Central-toepassing het tabblad **Apparaten** en selecteer het apparaat dat u hebt toegevoegd. Op het tabblad **Overzicht** ziet u de telemetrie van het DevKit-apparaat:

![Overzicht van IoT Central-apparaten](media/howto-connect-devkit/mxchip-overview-page.png)

## <a name="review-the-code"></a>De code bekijken

Als u de code wilt controleren of wijzigen en compileren, gaat u naar de [codevoorbeelden](https://docs.microsoft.com/samples/azure-samples/mxchip-iot-devkit-pnp/sample/).

## <a name="next-steps"></a>Volgende stappen

Als u een apparaatontwikkelaar bent, moeten enkele voorgestelde volgende stappen:

- Lees meer over [apparaatconnectiviteit in Azure IoT Central](./concepts-get-connected.md)
- Meer informatie over [het bewaken van apparaatconnectiviteit met Azure CLI](./howto-monitor-devices-azure-cli.md)

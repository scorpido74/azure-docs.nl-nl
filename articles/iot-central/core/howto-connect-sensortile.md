---
title: Een SensorTile. Box-apparaat verbinden met uw Azure IoT Central-toepassing | Microsoft Docs
description: Als ontwikkelaar van een apparaat leert u hoe u een SensorTile. Box-apparaat verbindt met uw Azure IoT Central-toepassing.
author: sarahhubbard
ms.author: sahubbar
ms.date: 08/24/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: sandeep.pujar
ms.openlocfilehash: 0969ee6dbc035ffa105dd54d34f3b4711d4915cf
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72951209"
---
# <a name="connect-sensortilebox-device-to-your-azure-iot-central-application"></a>SensorTile. Box-apparaat verbinden met uw Azure IoT Central-toepassing

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

In dit artikel wordt beschreven hoe u als een ontwikkelaar van een apparaat een SensorTile. Box-apparaat verbindt met uw Microsoft Azure IoT Central toepassing.

## <a name="before-you-begin"></a>Voordat u begint

U hebt de volgende resources nodig om de stappen in dit artikel uit te voeren:

* Een SensorTile. Box-apparaat. Zie [SensorTile. Box](https://www.st.com/content/st_com/en/products/evaluation-tools/product-evaluation-tools/mems-motion-sensor-eval-boards/steval-mksbox1v1.html)voor meer informatie.
* De ST-sensor-app die op uw Android-apparaat is geïnstalleerd, kunt u [hier downloaden](https://play.google.com/store/apps/details?id=com.st.bluems). Ga voor meer informatie naar: [ste-sensor](https://www.st.com/stblesensor)
* Een Azure IoT Central-toepassing gemaakt op basis van de toepassings sjabloon **DevKits** . Zie voor meer informatie de [snelstart over het maken van een toepassing](quick-deploy-iot-central.md).
* Voeg de sjabloon **SensorTile. Box** device toe aan uw IOT Central-toepassing door naar de pagina met **Apparaatinstellingen** te gaan en op **+ Nieuw**te klikken en de sjabloon **SensorTile. Box** te selecteren.

### <a name="get-your-device-connection-details"></a>De verbindings gegevens van uw apparaat ophalen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe uit de **SensorTile. Box** -sjabloon en noteer de verbindings Details van het apparaat: **Scope-ID**, **apparaat-id**en **primaire sleutel**:

1. Een apparaat van apparaten toevoegen. Selecteer **+ nieuw > Real** om een echt apparaat toe te voegen.

    * Voer een **apparaat-id**in kleine letters in of gebruik de voorgestelde **apparaat-id**.
    * Voer een **naam**voor het apparaat in of gebruik de voorgestelde naam

    ![Apparaat toevoegen](media/howto-connect-sensortile/real-device.png)

1. Selecteer **verbinding maken** op de pagina apparaat om de verbindings Details van het apparaat, de **scope-id**, de **apparaat-id**en de **primaire sleutel**op te halen.

    ![Verbindingsdetails](media/howto-connect-sensortile/connect-device.png)

1. Noteer de verbindings gegevens. U hebt tijdelijk geen verbinding meer met Internet wanneer u uw DevKit-apparaat voorbereidt in de volgende stap.

## <a name="set-up-the-sensortilebox-with-the-mobile-application"></a>Het SensorTile. Box instellen met de mobiele toepassing

In deze sectie leert u hoe u de firmware van de toepassing naar het apparaat kunt pushen. U verstuurt vervolgens hoe u de apparaatgegevens verzendt naar IoT Central via de mobiele app van de ST-trein sensor met behulp van een verbinding met Bluetooth-laag energie verbruik.

1. Open de ST-sensor-app en klik op de knop **een nieuwe app maken** .

    ![App maken](media/howto-connect-sensortile/create-app.png)

1. Selecteer de toepassing **barometer** .
1. Druk op de knop uploaden.

    ![Barometer uploaden](media/howto-connect-sensortile/barometer-upload.png)

1. Druk op de afspeel knop die is gekoppeld aan uw SensorTile. Box.
1. Wanneer het proces is voltooid, stromen de SensorTile. Box de Tempe ratuur, druk en lucht vochtigheid over de handel.

## <a name="connect-the-sensortilebox-to-the-cloud"></a>De SensorTile. Box verbinden met de Cloud

In deze sectie leert u hoe u de SensorTile. box verbindt met de mobiele toepassing en hoe u de mobiele toepassing verbindt met de Cloud.

1. Selecteer de knop **Cloud logboek registratie** in het linkerdeel venster.

    ![Cloud logboek registratie](media/howto-connect-sensortile/cloud-logging.png)

1. Selecteer **Azure IOT Central** als de Cloud provider.
1. Voeg de apparaat-ID en de scope-ID in die eerder zijn vermeld.

    ![Referenties](media/howto-connect-sensortile/credentials.png)

1. Selecteer het keuze rondje voor de **toepassings sleutel** .
1. Klik op **verbinden** en selecteer de telemetriegegevens die u wilt uploaden.
1. Na enkele seconden worden de gegevens weer gegeven in het dash board van de IoT Central-toepassing.

## <a name="sensortilebox-device-template-details"></a>Details van SensorTile. Box-Device-sjabloon

Een toepassing die is gemaakt op basis van de SensorTile. Box-sjabloon met de volgende kenmerken:

### <a name="telemetry"></a>Telemetrie

| Veldnaam     | Eenheden  | Minimum | Maximum | Aantal decimalen |
| -------------- | ------ | ------- | ------- | -------------- |
| vochtigheid       | %      | 30       | 90     | 1              |
| ratuur           | D20     | 0     | 40     | 1              |
| pressure       | mbar    | 900     | 1100    | 2              |
| magnetometerX  | mgauss | -1000   | 1000    | 0              |
| magnetometerY  | mgauss | -1000   | 1000    | 0              |
| magnetometerZ  | mgauss | -1000   | 1000    | 0              |
| versnellings meterx | mg     | -2000   | 2000    | 0              |
| versnellings meter | mg     | -2000   | 2000    | 0              |
| accelerometerZ | mg     | -2000   | 2000    | 0              |
| gyroscopeX     | DPS   | -3276   | 3276    | 1              |
| gyroscopeY     | DPS   | -3276   | 3276    | 1              |
| gyroscopeZ     | DPS   | -3276   | 3276    | 1              |
| FFT_X     |    |    |     |               |
| FFT_Y     |    |    |     |               |
| FFT_Z     |    |    |     |               |

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u een SensorTile. box verbindt met uw Azure IoT Central-toepassing, is de voorgestelde volgende stap informatie [over het instellen van een aangepaste apparaatprofiel](howto-set-up-template.md) voor uw eigen IOT-apparaat.

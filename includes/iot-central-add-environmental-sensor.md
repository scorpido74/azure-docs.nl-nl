---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 03/12/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9618dc1cef8d04cc5906579af0ef372694aeaaa9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80673983"
---
## <a name="create-a-device-template"></a>Een apparaatsjabloon maken

Maak een map met `environmental-sensor` de naam op uw lokale machine.

Down load het JSON-bestand van het [Environmental Capability model](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) en sla dit op in de `environmental-sensor` map.

Gebruik een tekst editor om de twee exemplaren van te `{YOUR_COMPANY_NAME_HERE}` vervangen door de naam van uw `EnvironmentalSensorInline.capabilitymodel.json` bedrijf in het bestand dat u hebt gedownload.

Maak in uw Azure IoT Central-toepassing een Device-sjabloon met de naam *omgevings sensor* door het bestand met het `EnvironmentalSensorInline.capabilitymodel.json` mogelijkheidsprofiel te importeren:

![De sjabloon apparaat met het model voor het importeren van het apparaat](./media/iot-central-add-environmental-sensor/device-template.png)

Het functionaliteits model van het apparaat bevat twee interfaces: de standaard interface voor **apparaatgegevens** en de interface voor aangepaste **omgevings sensor** . De **omgevings sensor** interface definieert de volgende mogelijkheden:

| Type | Weergavenaam | Beschrijving |
| ---- | ------------ | ----------- |
| Eigenschap | Apparaatstatus     | De status van het apparaat. Er zijn twee statussen online/offline beschikbaar. |
| Eigenschap (schrijfbaar) | Klant naam    | De naam van de klant die momenteel op het apparaat wordt uitgevoerd. |
| Eigenschap (schrijfbaar) | Helderheids niveau | Het helderheids niveau voor het licht op het apparaat. Kan worden opgegeven als 1 (hoog), 2 (gemiddeld), 3 (laag). |
| Telemetrie | Temperatuur | Huidige temperatuur gedetecteerd door het apparaat. |
| Telemetrie | Vochtigheid    | Huidige vochtigheid gedetecteerd door het apparaat. |
| Opdracht | gaat          | Begin met het Knip peren van de LED op het apparaat voor het opgegeven tijds interval. |
| Opdracht | turnon         | Schakel de LED in op het apparaat. |
| Opdracht | turnoff        | Schakel de LED op het apparaat uit. |
| Opdracht | rundiagnostics | Met deze asynchrone opdracht wordt een diagnostische uitvoering gestart op het apparaat. |

Als u wilt aanpassen hoe de eigenschap **Apparaatstatus** wordt weer gegeven in uw IOT Central-toepassing, selecteert u **aanpassen** in de sjabloon voor het apparaat. Vouw de vermelding voor de **apparaats status** uit en voer _online_ in als de **werkelijke naam** en _offline_ als de **naam onwaar**. Sla de wijzigingen vervolgens op:

![De sjabloon voor het apparaat aanpassen](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Weergaven maken

Met weer gaven kunt u communiceren met apparaten die zijn verbonden met uw IoT Central-toepassing. U kunt bijvoorbeeld weer gaven hebben waarin telemetrie wordt weer gegeven, weer gaven die eigenschappen weer geven en weer gaven waarmee u Beschrijf bare en Cloud eigenschappen kunt bewerken. Weer gaven maken deel uit van een sjabloon voor een apparaat.

Als u een aantal standaard weergaven wilt toevoegen aan de sjabloon **omgevings sensor** apparaat, gaat u naar de sjabloon voor het apparaat, selecteert u **weer gaven**en selecteert u de tegel **Standaard weergaven genereren** . Zorg ervoor dat **overzicht** en **over** zijn **ingeschakeld**en selecteer **standaard dashboard weergave (s) genereren**. U hebt nu twee standaard weergaven gedefinieerd in uw sjabloon.

De **omgevings sensor** interface bevat twee Beschrijf bare eigenschappen: **klant naam** en **helderheids niveau**. Als u een weer gave wilt maken, kunt u deze gebruiken om deze eigenschappen te bewerken:

1. Selecteer **weer gaven** en selecteer vervolgens de tegel **apparaat en Cloud gegevens bewerken** .

1. Geef _Eigenschappen_ op als formulier naam.

1. Selecteer de eigenschappen van het **niveau helderheid** en de naam van de **klant** . Selecteer vervolgens **sectie toevoegen**.

1. Sla uw wijzigingen op.

![Een weer gave toevoegen om het bewerken van eigenschappen in te scha kelen](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat kunt toevoegen dat gebruikmaakt van de sjabloon voor de **omgevings sensor** , moet u het publiceren.

Selecteer in de sjabloon voor het apparaat **publiceren**. Selecteer **publiceren**in het deel venster **sjabloon voor dit apparaat publiceren naar het toepassings** paneel.

Als u wilt controleren of de sjabloon klaar is voor gebruik, gaat u naar de pagina **apparaten** in uw IOT Central-toepassing. De sectie **apparaten** bevat een lijst van de gepubliceerde apparaten in de toepassing:

![Gepubliceerde sjablonen op de pagina apparaten](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe aan de Device-sjabloon die u in de vorige sectie hebt gemaakt:

1. Selecteer op de pagina **apparaten** de sjabloon **omgevings sensor** .

    > [!TIP]
    > Zorg ervoor dat u de sjabloon selecteert die u wilt gebruiken voordat u **+ Nieuw**selecteert, anders maakt u een niet-gekoppeld apparaat.

1. Selecteer **+ Nieuw**.

1. Zorg ervoor dat **gesimuleerd** is **uitgeschakeld**. Selecteer vervolgens **maken**.

Klik op de naam van het apparaat en selecteer vervolgens **verbinding maken**. Noteer de verbindings gegevens van het apparaat op de pagina met de verbindings- **id**van het **apparaat** , de **apparaat-id**en de **primaire sleutel**. U hebt deze waarden nodig wanneer u uw apparaatcode maakt:

![Verbindings gegevens apparaat](./media/iot-central-add-environmental-sensor/device-connection.png)

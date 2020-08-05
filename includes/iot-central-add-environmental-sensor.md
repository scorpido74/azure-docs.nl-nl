---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 07/07/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: b32465091f82fec0aeae288ee9bfd5540bfe8b9d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87002024"
---
## <a name="create-a-device-template"></a>Een apparaatsjabloon maken

Maak de map `environmental-sensor` op uw lokale machine.

Download het JSON-bestand [Environmental Sensor Capability Model](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/9004219bff1e958b7cd6ff2a52209f4b7ae19396/samples/EnvironmentalSensorInline.capabilitymodel.json) en sla het op in de map `environmental-sensor`.

Gebruik een teksteditor om de twee exemplaren van `{YOUR_COMPANY_NAME_HERE}` te vervangen door de naam van uw bedrijf in het bestand `EnvironmentalSensorInline.capabilitymodel.json` dat u hebt gedownload. Gebruik alleen de tekens a-z, A-Z, 0-9 en onderstrepingstekens.

Maak in uw Azure IoT Central-toepassing een sjabloon met de naam *Environmental sensor* door het bestand met het capaciteitsmodel `EnvironmentalSensorInline.capabilitymodel.json` te importeren:

![Het apparaatsjabloon met het geïmporteerde model voor apparaatondersteuning](./media/iot-central-add-environmental-sensor/device-template.png)

Het model voor apparaatondersteuning bevat twee interfaces: de standaard **Apparaatgegevens** en de aangepaste **Omgevingssensor**. De interface **Omgevingssensor** definieert de volgende mogelijkheden:

| Type | Weergavenaam | Beschrijving |
| ---- | ------------ | ----------- |
| Eigenschap | Apparaatstatus     | De status van het apparaat. Er zijn twee statussen beschikbaar, online/offline. |
| Eigenschap (schrijfbaar) | Naam van klant    | De naam van de klant die het apparaat momenteel gebruikt. |
| Eigenschap (schrijfbaar) | Helderheidsniveau | Het helderheidsniveau voor het licht op het apparaat. Kan worden opgegeven als 1 (hoog), 2 (gemiddeld), 3 (laag). |
| Telemetrie | Temperatuur | Huidige temperatuur gedetecteerd door het apparaat. |
| Telemetrie | Vochtigheid    | Huidige luchtvochtigheid gedetecteerd door het apparaat. |
| Opdracht | knipper          | Laat de LED op het apparaat knipperen voor de opgegeven tijdsinterval. |
| Opdracht | turnon         | Schakel de LED op het apparaat in. |
| Opdracht | turnoff        | Schakel de LED op het apparaat uit. |
| Opdracht | rundiagnostics | Met deze asynchrone opdracht wordt een Diagnostics-uitvoering gestart op het apparaat. |

Als u wilt aanpassen hoe de eigenschap **Apparaatstatus** wordt weergegeven in uw IoT Central-toepassing, selecteert u **Aanpassen** in het apparaatsjabloon. Vouw de vermelding **Apparaatstatus** uit, voer _Online_ in als de **True name** en _Offline_ als **False name**. Sla vervolgens de wijzigingen op:

![Het apparaatsjabloon aanpassen](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Weergaven maken

Met weergaven kunt u communiceren met apparaten die zijn verbonden met uw IoT Central-toepassing. U kunt bijvoorbeeld weergaven hebben waarin telemetrie wordt weergegeven, weergaven die eigenschappen weergeven en weergaven waarmee u schrijfbare- en Cloudeigenschappen kunt bewerken. Weergaven maken deel uit van een apparaatsjabloon.

Als u een aantal standaard weergaven wilt toevoegen aan de **Omgevingssensor** van uw apparaatsjabloon, gaat u naar de apparaatsjabloon, selecteert u **Weergaven** en de tegel **Standaard weergaven genereren**. Zorg ervoor dat **Overzicht** en **Over** **Aan** zijn en selecteer vervolgens **Standaard dashboardweergave(n) genereren**. U hebt nu twee standaard weergaven gedefinieerd in uw sjabloon.

De interface **Omgevingssensor** bevat twee schrijfbare eigenschappen: **Naam van de klant** en **Helderheidsniveau**. Als u een weergave wilt maken, kunt u deze gebruiken om deze eigenschappen te bewerken:

1. Selecteer **Weergaven** en vervolgens de tegel **Apparaat- en cloudgegevens bewerken**.

1. Voer _Eigenschappen_ in als de formuliernaam.

1. Selecteer de eigenschappen **Helderheidsniveau** en **Naam van de klant**. Selecteer vervolgens **Sectie toevoegen**.

1. Sla uw wijzigingen op.

![Een weergave toevoegen om het bewerken van eigenschappen in te schakelen](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat kunt toevoegen dat gebruikmaakt van de apparaatsjabloon **Omgevingssensor**, moet u het publiceren.

Selecteer **Publiceren** in de apparaatsjabloon. Selecteer **Publiceren** in het deelvenster **Dit apparaatsjabloon op de toepassing publiceren**.

Als u wilt controleren of de sjabloon gereed is voor gebruik, gaat u naar de pagina **Apparaten** in uw IoT Central-toepassing. De sectie **Apparaten** bevat een lijst van de gepubliceerde apparaten in de toepassing:

![Gepubliceerde sjablonen op de apparatenpagina](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe aan de apparaatsjabloon die u in de vorige sectie hebt gemaakt:

1. Selecteer op de pagina **Apparaten** de apparaatsjabloon **Omgevingssensor**.

1. Selecteer **+ Nieuw**.

1. Zorg ervoor dat in het dialoogvenster **Een nieuw apparaat maken** het sjabloontype **Omgevingssensor** is en dat **Dit apparaat simuleren?** is ingesteld op **Nee**.

1. Selecteer vervolgens **Maken**.

Klik op de apparaatnaam en selecteer vervolgens **Verbinding maken**. Noteer de verbindingsgegevens voor het apparaat op de pagina **Apparaatverbinding** - **Id-bereik**, **Apparaat-id** en **Primaire sleutel**. U hebt deze waarden nodig wanneer u uw apparaatcode maakt:

![Verbindingsgegevens apparaat](./media/iot-central-add-environmental-sensor/device-connection.png)

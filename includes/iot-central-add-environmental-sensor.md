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
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80673983"
---
## <a name="create-a-device-template"></a>Een apparaatsjabloon maken

Maak een `environmental-sensor` map die op uw lokale machine wordt aangeroepen.

Download het [JSON-bestand met omgevingssensormogelijkheden](https://raw.githubusercontent.com/Azure/IoTPlugandPlay/master/samples/EnvironmentalSensorInline.capabilitymodel.json) `environmental-sensor` en sla het op in de map.

Gebruik een teksteditor om de `{YOUR_COMPANY_NAME_HERE}` twee exemplaren van `EnvironmentalSensorInline.capabilitymodel.json` uw bedrijfsnaam te vervangen door uw bedrijfsnaam in het bestand dat u hebt gedownload.

Maak in uw Azure IoT Central-toepassing een apparaatsjabloon `EnvironmentalSensorInline.capabilitymodel.json` met de naam *Omgevingssensor* door het modelbestand voor apparaatmogelijkheden te importeren:

![Apparaatsjabloon met geïmporteerd apparaatcapaciteitsmodel](./media/iot-central-add-environmental-sensor/device-template.png)

Het apparaatvermogenmodel bevat twee interfaces: de standaard **interface voor apparaatinformatie** en de aangepaste **interface voor omgevingssensoren.** De **interface omgevingssensor** definieert de volgende mogelijkheden:

| Type | Weergavenaam | Beschrijving |
| ---- | ------------ | ----------- |
| Eigenschap | Apparaatstatus     | De toestand van het apparaat. Twee staten online / offline zijn beschikbaar. |
| Eigenschap (schrijfbaar) | Naam van de klant    | De naam van de klant die momenteel het apparaat gebruikt. |
| Eigenschap (schrijfbaar) | Helderheidsniveau | Het helderheidsniveau voor het licht op het apparaat. Kan worden opgegeven als 1 (hoog), 2 (medium), 3 (laag). |
| Telemetrie | Temperatuur | Huidige temperatuur gedetecteerd door het apparaat. |
| Telemetrie | Vochtigheid    | Huidige vochtigheid gedetecteerd door het apparaat. |
| Opdracht | Knipperen          | Begin met het knipperen van de LED op het apparaat voor een bepaald tijdsinterval. |
| Opdracht | turnon         | Schakel de LED op het apparaat in. |
| Opdracht | Afslag        | Schakel de LED op het apparaat uit. |
| Opdracht | rundiagnostics | Met deze asynchrone opdracht wordt een diagnostische bewerking op het apparaat gestart. |

Als u wilt aanpassen hoe de eigenschap **Apparaatstatus** wordt weergegeven in uw IoT Central-toepassing, selecteert u **Aanpassen** in de apparaatsjabloon. Vouw de **vermelding apparaatstatus** uit, voer _Online_ in als **de echte naam** en _Offline_ als de **valse naam**. Sla vervolgens de wijzigingen op:

![De apparaatsjabloon aanpassen](./media/iot-central-add-environmental-sensor/customize-template.png)

## <a name="create-views"></a>Weergaven maken

Met weergaven u communiceren met apparaten die zijn verbonden met uw IoT Central-toepassing. U bijvoorbeeld weergaven hebben die telemetrie weergeven, weergaven die eigenschappen weergeven en weergaven waarmee u schrijfbare en cloudeigenschappen bewerken. Weergaven maken deel uit van een apparaatsjabloon.

Als u een aantal standaardweergaven wilt toevoegen aan de apparaatsjabloon **voor de omgevingssensor,** navigeert u naar de apparaatsjabloon, selecteert u **Weergaven**en selecteert u de tegel **Standaardweergaven genereren.** Controleer of **Overzicht** en **Informatie** **aan**staan en selecteer **vervolgens Standaarddashboardweergave(s) genereren**. Er zijn nu twee standaardweergaven gedefinieerd in uw sjabloon.

De **interface omgevingssensor** bevat twee schrijfbare eigenschappen - **klantnaam** en **helderheidsniveau**. Als u een weergave wilt maken, u deze eigenschappen bewerken:

1. Selecteer **Weergaven** en selecteer vervolgens het bewerkingsapparaat en de **tegel cloudgegevens.**

1. Voer _Eigenschappen_ in als formuliernaam.

1. Selecteer de eigenschappen **Helderheidsniveau** en **klantnaam.** Selecteer vervolgens **Sectie Toevoegen**.

1. Sla uw wijzigingen op.

![Een weergave toevoegen om eigenschapsbewerking in te schakelen](./media/iot-central-add-environmental-sensor/properties-view.png)

## <a name="publish-the-template"></a>De sjabloon publiceren

Voordat u een apparaat toevoegen dat de sjabloon **omgevingssensorapparaat** gebruikt, moet u het apparaat publiceren.

Selecteer **Publiceren**in de apparaatsjabloon . Selecteer **Publiceren**in **de sjabloon Dit apparaat publiceren in het toepassingspaneel** .

Als u wilt controleren of de sjabloon klaar is voor gebruik, navigeert u naar de pagina **Apparaten** in uw IoT Central-toepassing. In de sectie **Apparaten** wordt een lijst weergegeven met de gepubliceerde apparaten in de toepassing:

![Gepubliceerde sjablonen op de pagina apparaten](./media/iot-central-add-environmental-sensor/published-templates.png)

## <a name="add-a-real-device"></a>Echt apparaat toevoegen

Voeg in uw Azure IoT Central-toepassing een echt apparaat toe aan de apparaatsjabloon die u in de vorige sectie hebt gemaakt:

1. Selecteer **op** de pagina Apparaten de sjabloon **omgevingssensor.**

    > [!TIP]
    > Zorg ervoor dat u de sjabloon selecteert die u wilt gebruiken voordat u **+ Nieuw**selecteert, anders maakt u een niet-gekoppeld apparaat.

1. Selecteer **+ Nieuw**.

1. Zorg ervoor dat **gesimuleerd** is **uitgeschakeld.** Selecteer vervolgens **Maken**.

Klik op de naam van het apparaat en selecteer **Verbinding maken**. Noteer de apparaatverbindingsgegevens op de pagina **Apparaatverbinding** - **ID-bereik,** **apparaat-id**en **primaire sleutel**. U hebt deze waarden nodig wanneer u uw apparaatcode maakt:

![Apparaatverbindingsgegevens](./media/iot-central-add-environmental-sensor/device-connection.png)

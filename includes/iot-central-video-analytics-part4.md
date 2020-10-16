---
title: bestand opnemen
description: bestand opnemen
services: iot-central
author: dominicbetts
ms.service: iot-central
ms.topic: include
ms.date: 10/06/2020
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 164f5803b6e9e62447423735e98f6e4c36c73f13
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876629"
---
### <a name="add-relationships"></a>Relaties toevoegen

Selecteer in de apparaatsjabloon **LVA Edge Gateway** onder **Modules/LVA Edge Gateway-module** de optie **Relaties**. Selecteer **+ Relatie toevoegen** en voeg de volgende twee relaties toe:

|Weergavenaam               |Naam          |Doel |
|-------------------------- |------------- |------ |
|LVA Edge-bewegingsdetector   |Standaardwaarde gebruiken   |LVA Edge-bewegingsdetectorapparaat |
|LVA Edge-objectdetector   |Standaardwaarde gebruiken   |LVA Edge-objectdetectorapparaat |

Selecteer vervolgens **Opslaan**.

:::image type="content" source="media/iot-central-video-analytics-part4/relationships.png" alt-text="Relaties toevoegen":::

### <a name="add-views"></a>Weergaven toevoegen

De apparaatsjabloon **LVA Edge Gateway** bevat geen weergavedefinities.

Een weergave toevoegen aan de sjabloon voor het apparaat:

1. Navigeer in de apparaatsjabloon **LVA Edge Gateway** naar **Weergaven** en selecteer de tegel **Het apparaat visualiseren**.

1. Voer *LVA Edge Gateway-apparaat* als de weergavenaam in.

1. Voeg de volgende tegels aan de weergave toe:

    * Een tegel met de **Apparaatgegevens**-eigenschappen: **Apparaatmodel**, **Fabrikant**, **Besturingssysteem**, **Processorarchitectuur**, **Softwareversie**, **Totaal geheugen** en **Totale opslagruimte**.
    * Een tegel Lijndiagram met de telemetriewaarden **Beschikbaar geheugen** en **Systeem-heartbeat**.
    * Een tegel Gebeurtenissengeschiedenis met de volgende gebeurtenissen: **Camera maken**, **Camera verwijderen**, **Module opnieuw opstarten**, **Module gestart**, **Module gestopt**.
    * Een 2x1 tegel Laatst bekende waarde met de telemetrie **IoT Central-clientstatus**.
    * Een 2x1 tegel Laatst bekende waarde met de telemetrie **Modulestatus**.
    * Een 1x1 tegel Laatst bekende waarde met de telemetrie **Systeem-heartbeat**.
    * Een 1x1 tegel Laatst bekende waarde met de telemetrie **Verbonden camera’s**.

    :::image type="content" source="media/iot-central-video-analytics-part4/gateway-dashboard.png" alt-text="Relaties toevoegen":::

1. Selecteer **Opslaan**.

### <a name="publish-the-device-template"></a>De apparaatsjabloon publiceren

Voordat u een apparaat aan de toepassing kunt toevoegen, moet u de apparaatsjabloon publiceren:

1. Selecteer in de apparaatsjabloon **LVA Edge Gateway** de optie **Publiceren**.

1. Selecteer op de pagina **Deze apparaatsjabloon publiceren naar de toepassing** de optie **Publiceren**.

**LVA Edge Gateway** is nu beschikbaar als apparaattype voor gebruik op de pagina **Apparaten** in de toepassing.

## <a name="add-a-gateway-device"></a>Een gatewayapparaat toevoegen

Een **LVA Edge Gateway**-apparaat aan de toepassing toevoegen:

1. Navigeer naar de pagina **Apparaten** en selecteer de apparaatsjabloon **LVA Edge Gateway**.

1. Selecteer **+ Nieuw**.

1. In het dialoogvenster **Een nieuw apparaat maken** wijzigt u de apparaatnaam in *LVA Gateway 001* en wijzigt u de apparaat-id in *lva-gateway-001*.

    > [!NOTE]
    > De apparaat-id moet uniek zijn in de toepassing.

1. Selecteer **Maken**.

De apparaatstatus is **Geregistreerd**.

### <a name="get-the-device-credentials"></a>De apparaatreferenties ophalen

U hebt de referenties nodig waarmee het apparaat verbinding kan maken met uw IoT Central-toepassing. De apparaatreferenties ophalen:

1. Selecteer op de pagina **Apparaten** het apparaat **lva-gateway-001** dat u hebt gemaakt.

1. Selecteer **Verbinding maken**.

1. Op de pagina **Apparaatverbinding** noteert u in het bestand *scratchpad.txt* het **Id-bereik**, de **Apparaat-id** en de **Primaire sleutel** van het apparaat. U gebruikt deze waarden later.

1. Zorg dat de verbindingsmethode is ingesteld op **Shared Access Signature**.

1. Selecteer **Sluiten**.

## <a name="next-steps"></a>Volgende stappen

U hebt nu een IoT Central-toepassing gemaakt met behulp van de toepassingssjabloon **Videoanalyse: object- en bewegingsdetectie**, een apparaatsjabloon voor het gatewayapparaat gemaakt en een gatewayapparaat aan de toepassing toegevoegd.

Als u de toepassing ‘Videoanalyse: object- en bewegingsdetectie’ wilt uitproberen met behulp van IoT Edge-modules die een cloud-VM uitvoeren met gesimuleerde videostreams:

> [!div class="nextstepaction"]
> [Een IoT Edge-exemplaar voor videoanalyse maken (Linux-VM)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-vm.md)

Als u de toepassing ‘Videoanalyse: object- en bewegingsdetectie’ wilt uitproberen met behulp van IoT Edge-modules die een echt apparaat uitvoeren met een echte **ONVIF**-camera:

> [!div class="nextstepaction"]
> [Een IoT Edge-exemplaar voor videoanalyse maken (Intel NUC)](../articles/iot-central/retail/tutorial-video-analytics-iot-edge-nuc.md)

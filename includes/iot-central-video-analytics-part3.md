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
ms.openlocfilehash: 60a62733a17d1a3dcc4ba80ed7ceb1c37c8ac5d6
ms.sourcegitcommit: fbb620e0c47f49a8cf0a568ba704edefd0e30f81
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91876628"
---
## <a name="create-the-azure-iot-edge-gateway-device"></a>Het Azure IoT Edge-gatewayapparaat maken

De toepassing ‘Videoanalyse: object- en bewegingsdetectie’ bevat een **LVA Edge Object Detector**-apparaatsjabloon en een **LVA Edge Motion Detection**-apparaatsjabloon. In deze sectie maakt u een gatewayapparaatsjabloon met behulp van het implementatiemanifest en voegt u het gatewayapparaat aan uw IoT Central-toepassing toe.

### <a name="create-a-device-template-for-the-lva-edge-gateway"></a>Een apparaatsjabloon maken voor de LVA Edge Gateway

Het implementatiemanifest importeren en de **LVA Edge Gateway**-apparaatsjabloon maken:

1. Navigeer in uw IoT Central-toepassing naar **Apparaatsjablonen** en selecteer **+ Nieuw**.

1. Selecteer op de pagina **Sjabloontype selecteren** de tegel **Azure IoT Edge**. Selecteer vervolgens**Volgende: Aanpassen**.

1. Op de pagina **Een Azure IoT Edge-implementatiemanifest uploaden** voert u *LVA Edge Gateway* als de sjabloonnaam in en schakelt u **Gatewayapparaat met downstreamapparaten** in.

    Blader nog niet naar het implementatiemanifest. Als u dat doet, verwacht de implementatiewizard een interface voor elke module, maar u hoeft alleen de interface voor de **LvaEdgeGatewayModule** beschikbaar te stellen. U uploadt het manifest in een latere stap.

    :::image type="content" source="./media/iot-central-video-analytics-part3/upload-deployment-manifest.png" alt-text="Implementatiemanifest niet uploaden":::

    Selecteer **Volgende: Review**.

1. Selecteer op de pagina **Beoordelen** de optie **Maken**.

### <a name="import-the-device-capability-model"></a>Het apparaatondersteuningsprofiel importeren

De apparaatsjabloon moet een apparaatondersteuningsprofiel bevatten. Selecteer op de pagina **LVA Edge Gateway** de tegel **Ondersteuningsprofiel importeren**. Navigeer naar de map *lva-configuration* die u eerder hebt gemaakt, en selecteer het bestand *LvaEdgeGatewayDcm.json*.

De apparaatsjabloon **LVA Edge Gateway** bevat nu de **LVA Edge Gateway-module** samen met drie interfaces: **Apparaatgegevens**, **LVA Edge Gateway-instellingen** en **LVA Edge Gateway-interface**.

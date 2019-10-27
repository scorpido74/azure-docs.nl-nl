---
title: Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing | Microsoft Docs
description: Als operator kunt u de apparaatgroepen gebruiken in uw Azure IoT Central-toepassing.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 91aae53ae81d2bc7cfda54cefa786e4b1a1aab67
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72949662"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

In dit artikel wordt beschreven hoe, als operator, u apparaatgroepen kunt gebruiken in uw Azure IoT Central-toepassing.

Een apparaatgroep is een lijst met apparaten die zijn gegroepeerd, omdat deze voldoen aan bepaalde criteria. Met apparaatgroepen kunt u apparaten op schaal beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U kunt bijvoorbeeld een apparaatgroep maken voor het weer geven van alle airconditioning apparaten in Seattle, zodat een technicus kan zoeken naar de apparaten waarvoor ze verantwoordelijk zijn. In dit artikel wordt beschreven hoe u apparaatgroepen maakt en configureert.

>  [!NOTE] 
> Voor Azure IoT Edge-apparaten moet u Azure IoT Edge sjablonen selecteren om een apparaatgroep te maken

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Een apparaatgroep maken:

1. Kies **Apparaatgroepen** in het linkerdeel venster.

1. Selecteer **+ Nieuw**.

    ![Nieuwe apparaatgroep](media/howto-use-device-groups-pnp/image1.png)

1. Geef uw apparaatgroep een unieke naam voor de hele toepassing. U kunt ook een beschrijving toevoegen. Een apparaatgroep kan alleen apparaten uit een sjabloon van één apparaat bevatten. Kies de apparaatprofiel die u voor deze groep wilt gebruiken.

1. Maak de query om de apparaten voor de apparaatgroep te identificeren door een eigenschap, een vergelijkings operator en een waarde te selecteren. U kunt meerdere query's en apparaten toevoegen die voldoen aan **alle** criteria in de apparaatgroep worden geplaatst. De apparaatgroep die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatgroep kan bekijken, wijzigen of verwijderen.

    ![Query apparaatgroep](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > De apparaatgroep is een dynamische query. Telkens wanneer u de lijst met apparaten bekijkt, zijn er mogelijk andere apparaten in de lijst. De lijst is afhankelijk van de apparaten die momenteel voldoen aan de criteria van de query.

1. Kies **Opslaan**.

## <a name="analytics"></a>Analyse

De analyse in apparaatgroepen is hetzelfde als het tabblad Main Analytics in het linkerdeel venster. Meer informatie over analyses vindt u in het artikel over het [maken van analyses](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatgroepen kunt gebruiken in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrie-regels maken](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

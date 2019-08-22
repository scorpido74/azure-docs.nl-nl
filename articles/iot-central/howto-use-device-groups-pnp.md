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
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879745"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing (preview-functies)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

In dit artikel wordt beschreven hoe, als operator, u apparaatgroepen kunt gebruiken in uw Azure IoT Central-toepassing.

Een apparaatgroep is een lijst met apparaten die zijn gegroepeerd, omdat deze voldoen aan bepaalde criteria. Met apparaatgroepen kunt u apparaten op schaal beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U kunt bijvoorbeeld een apparaatgroep maken voor het weer geven van alle airconditioning apparaten in Seattle, zodat een technicus kan zoeken naar de apparaten waarvoor ze verantwoordelijk zijn. In dit artikel wordt beschreven hoe u apparaatgroepen maakt en configureert.

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Een apparaatgroep maken:

1. Kies **Apparaatgroepen** in het navigatie menu aan de linkerkant.

1. Selecteer **+ Nieuw**.

    ![Nieuwe apparaatgroep](media/howto-use-device-groups-pnp/image1.png)

1. Geef uw apparaatgroep een unieke naam voor de hele toepassing. U kunt ook een beschrijving toevoegen. Een apparaatgroep kan alleen apparaten uit een sjabloon van één apparaat bevatten. Kies de apparaatprofiel die u voor deze groep wilt gebruiken.

1. Maak de query om de apparaten voor de apparaatgroep te identificeren door een eigenschap, een vergelijkings operator en een waarde te selecteren. U kunt meerdere query's en apparaten toevoegen die voldoen aan **alle** criteria in de apparaatgroep worden geplaatst. De apparaatgroep die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatgroep kan bekijken, wijzigen of verwijderen.

    ![Query apparaatgroep](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > De apparaatgroep is een dynamische query. Telkens wanneer u de lijst met apparaten bekijkt, zijn er mogelijk andere apparaten in de lijst. De lijst is afhankelijk van de apparaten die momenteel voldoen aan de criteria van de query.

1. Kies **Opslaan**.

## <a name="analytics"></a>Analyse

De analyse in apparaatgroepen is hetzelfde als het tabblad Main Analytics in het navigatie menu aan de linkerkant. Meer informatie over analyses vindt u in het artikel over het [maken van analyses](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatgroepen kunt gebruiken in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrie-regels maken](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)

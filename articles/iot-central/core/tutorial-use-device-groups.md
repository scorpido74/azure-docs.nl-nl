---
title: Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing | Microsoft Docs
description: Als operator vindt u informatie over het gebruik van apparaatgroepen voor het analyseren van telemetrie van apparaten in uw Azure IoT Central-toepassing.
author: dominicbetts
ms.author: dobett
ms.date: 12/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 4fd05631e7f54b6258978f70fdd5dfb9705f989b
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77026963"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Zelf studie: apparaatgroepen gebruiken om apparaat-telemetrie te analyseren



In dit artikel wordt beschreven hoe, als operator, u apparaatgroepen kunt gebruiken voor het analyseren van de telemetrie van apparaten in uw Azure IoT Central-toepassing.

Een apparaatgroep is een lijst met apparaten die zijn gegroepeerd, omdat deze voldoen aan bepaalde criteria. Met apparaatgroepen kunt u apparaten op schaal beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U kunt bijvoorbeeld een apparaatgroep maken voor het weer geven van alle airconditioning apparaten in Seattle, zodat een technicus kan zoeken naar de apparaten waarvoor ze verantwoordelijk zijn.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatgroep maken
> * Een apparaatgroep gebruiken om telemetrie van apparaten te analyseren

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de [toepassing een Azure-IOT Central maken](./quick-deploy-iot-central.md) volt ooien en [een gesimuleerd apparaat toevoegen aan de snelstartgids van uw IOT Central toepassing](./quick-create-pnp-device.md) om de sjabloon voor het **omgevings sensor** apparaat te maken waarmee u kunt werken.

## <a name="create-simulated-devices"></a>Gesimuleerde apparaten maken

Voordat u een apparaatgroep maakt, moet u ten minste vijf gesimuleerde apparaten uit de **omgevings sensor** van het apparaat sjabloon toevoegen voor gebruik in deze zelf studie:

![Vijf gesimuleerde omgevings sensor apparaten](./media/tutorial-use-device-groups/simulated-devices.png)

Voor vier van de omgevings sensor apparaten gebruikt u de weer gave **Eigenschappen van omgevings sensor** om de naam van de klant in te stellen op **Contoso**:

![Klant naam instellen op contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Een apparaatgroep maken:

1. Kies **Apparaatgroepen** in het linkerdeel venster.

1. Selecteer **+ Nieuw**.

    ![Nieuwe apparaatgroep](media/tutorial-use-device-groups/image1.png)

1. Geef de apparaatgroep een naam zoals **Contoso-apparaten**. U kunt ook een beschrijving toevoegen. Een apparaatgroep kan alleen apparaten uit een sjabloon van één apparaat bevatten. Kies het apparaat sjabloon voor **omgevings sensors** dat u voor deze groep wilt gebruiken.

1. Maak de query om de apparaten te identificeren die deel uitmaken van **Contoso** voor de apparaatgroep door de eigenschap **Customer name** , de vergelijkings operator **gelijkwaarde** en **Contoso** als de waarde te selecteren. U kunt meerdere query's en apparaten toevoegen die voldoen aan **alle** criteria in de apparaatgroep worden geplaatst. De apparaatgroep die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatgroep kan bekijken, wijzigen of verwijderen.

    ![Query apparaatgroep](media/tutorial-use-device-groups/image2.png)

    > [!NOTE]
    > De apparaatgroep is een dynamische query. Telkens wanneer u de lijst met apparaten bekijkt, zijn er mogelijk andere apparaten in de lijst. De lijst is afhankelijk van de apparaten die momenteel voldoen aan de criteria van de query.

1. Kies **Opslaan**.

> [!NOTE]
> Selecteer Azure IoT Edge sjablonen voor Azure IoT Edge apparaten om een apparaatgroep te maken.

## <a name="analytics"></a>Analytische gegevens

U kunt **Analytics** met een apparaatgroep gebruiken om de telemetrie van de apparaten in de groep te analyseren. U kunt bijvoorbeeld de gemiddelde Tempe ratuur die door alle contoso-omgevings sensoren wordt gerapporteerd, uitzetten.

De telemetrie voor een apparaatgroep analyseren:

1. Kies **Analytics** in het linkerdeel venster.

1. Selecteer de apparaatgroep **Contoso-apparaten** die u hebt gemaakt. Voeg vervolgens de telemetrie-typen **Tempe ratuur** en **vochtigheid** toe:

    ![Analyses maken](./media/tutorial-use-device-groups/create-analysis.png)

    Gebruik de tandwiel wiel pictogrammen naast de typen telemetrie om een samenvoegings type te selecteren. De standaard waarde is **gemiddelde**. Gebruik **splitsen door** om te wijzigen hoe de statistische gegevens worden weer gegeven. Als u bijvoorbeeld hebt gesplitst op apparaat-ID, ziet u een plot voor elk apparaat wanneer u **analyseren**selecteert.

1. Selecteer **analyseren** om de gemiddelde telemetrie-waarden weer te geven:

    ![Analyse weer geven](./media/tutorial-use-device-groups/view-analysis.png)

    U kunt de weer gave aanpassen, de weer gegeven tijds periode wijzigen en de gegevens exporteren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatgroepen kunt gebruiken in uw Azure IoT Central-toepassing, is dit de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrie-regels maken](tutorial-create-telemetry-rules.md)

---
title: 'Zelfstudie: Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing | Microsoft Docs'
description: 'Zelfstudie: Als operator leert u hoe u apparaatgroepen kunt gebruiken om telemetrie te analyseren vanaf apparaten in uw Azure IoT Central-toepassing.'
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 3192a9f121d4380a3e681747596fc91997662bf0
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90967944"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Zelfstudie: Apparaatgroepen gebruiken om apparaattelemetrie te analyseren

In dit artikel wordt beschreven hoe u, als operator, apparaatgroepen kunt gebruiken voor het analyseren van apparaattelemetrie in uw Azure IoT Central-toepassing.

Een apparaatgroep is een lijst met apparaten die zijn gegroepeerd omdat ze voldoen aan bepaalde criteria. Met apparaatgroepen kunt u apparaten op schaal beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U kunt bijvoorbeeld een apparaatgroep maken om alle airco's in Seattle weer te geven, zodat een technicus kan zoeken naar de apparaten waarvoor hij verantwoordelijk is.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatgroep maken
> * Een apparaatgroep gebruiken om apparaattelemetrie te analyseren

## <a name="prerequisites"></a>Vereisten

Voordat u begint, voltooit u de quickstarts [Een Azure IoT Central-toepassing maken](./quick-deploy-iot-central.md) en [Een gesimuleerd apparaat toevoegen aan uw IoT Central-toepassing](./quick-create-simulated-device.md) om de **MXChip IoT DevKit**-apparaatsjabloon te maken waarmee u kunt werken.

## <a name="create-simulated-devices"></a>Gesimuleerde apparaten maken

Voordat u een apparaatgroep kunt maken, voegt u ten minste vijf gesimuleerde apparaten toe vanuit de apparaatsjabloon **MXChip IoT DevKit** om in deze zelfstudie te gebruiken:

![Vijf gesimuleerde sensorapparaten](./media/tutorial-use-device-groups/simulated-devices.png)

Voor vier van de gesimuleerde sensorapparaten gebruikt u de weergave **Apparaat beheren** om de naam van de klant in te stellen op *Contoso*:

![Klantnaam instellen op Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Een apparaatgroep maken:

1. Kies **Apparaatgroepen** in het linkerdeelvenster.

1. Selecteer **+** :

    ![Nieuwe apparaatgroep](media/tutorial-use-device-groups/image1.png)

1. Geef uw apparaatgroep de naam *Contoso-apparaten*. U kunt ook een beschrijving toevoegen. Een apparaatgroep kan alleen apparaten uit één apparaatsjabloon bevatten. Kies de apparaatsjabloon **MXChip IoT DevKit** om te gebruiken voor deze groep.

1. Als u de apparaatgroep wilt aanpassen zodat deze alleen de apparaten bevat die horen bij **Contoso**, selecteert u **+ Filter**. Selecteer de eigenschap **Klantnaam**, de vergelijkingsoperator **Is gelijk aan** en **Contoso** als waarde. U kunt meerdere filters en apparaten toevoegen die voldoen aan **alle** filtercriteria die in de apparaatgroep zijn geplaatst. De apparaatgroep die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatgroep kan bekijken, wijzigen of verwijderen:

    ![Apparaatgroepquery](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > De apparaatgroep is een dynamische query. Telkens wanneer u de lijst met apparaten bekijkt, kunnen er andere apparaten in de lijst staan. De lijst is afhankelijk van de apparaten die op dat moment voldoen aan de criteria van de query.

1. Kies **Opslaan**.

> [!NOTE]
> Selecteer Azure IoT Edge-sjablonen voor Azure IoT Edge-apparaten om een apparaatgroep te maken.

## <a name="analytics"></a>Analyse

U kunt **Analytische gegevens** gebruiken met een apparaatgroep om de telemetrie van de apparaten in de groep te analyseren. U kunt bijvoorbeeld de gemiddelde temperatuur uitzetten die door alle Contoso-omgevingssensoren wordt gerapporteerd.

De telemetrie voor een apparaatgroep analyseren:

1. Kies **Analyse** in het linkerdeelvenster.

1. Selecteer de apparaatgroep **Contoso-apparaten** die u hebt gemaakt. Voeg vervolgens de telemetrietypen **Temperatuur** en **Vochtigheid** toe:

    ![Analyses maken](./media/tutorial-use-device-groups/create-analysis.png)

    Gebruik de tandwielpictogrammen naast de telemetrietypen om een aggregatietype te selecteren. De standaardwaarde is **Gemiddeld**. Gebruik **Splitsen op** om te wijzigen hoe de statistische gegevens worden weergegeven. Als u bijvoorbeeld hebt gesplitst op apparaat-id, ziet u een plot voor elk apparaat wanneer u **Analyse**selecteert.

1. Selecteer **Analyseren** om de gemiddelde telemetriegegevens weer te geven:

    ![Analyse weergeven](./media/tutorial-use-device-groups/view-analysis.png)

    U kunt de weergave aanpassen, de weergegeven tijdsperiode wijzigen en de gegevens exporteren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatgroepen gebruikt in uw Azure IoT Central-toepassing, volgt hier de aanbevolen volgende stap:

> [!div class="nextstepaction"]
> [Telemetrieregels maken](tutorial-create-telemetry-rules.md)

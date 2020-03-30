---
title: Apparaatgroepen gebruiken in uw Azure IoT Central-toepassing | Microsoft Documenten
description: Als operator leert u hoe u apparaatgroepen gebruikt om telemetrie van apparaten in uw Azure IoT Central-toepassing te analyseren.
author: dominicbetts
ms.author: dobett
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 758ac037fcd224d02f62239b3408b41b50390147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77167244"
---
# <a name="tutorial-use-device-groups-to-analyze-device-telemetry"></a>Zelfstudie: Apparaatgroepen gebruiken om apparaattelemetrie te analyseren

In dit artikel wordt beschreven hoe u als operator apparaatgroepen gebruiken om apparaattelemetrie in uw Azure IoT Central-toepassing te analyseren.

Een apparaatgroep is een lijst met apparaten die zijn gegroepeerd omdat ze overeenkomen met een aantal opgegeven criteria. Apparaatgroepen helpen u apparaten op schaal te beheren, visualiseren en analyseren door apparaten te groeperen in kleinere, logische groepen. U bijvoorbeeld een apparaatgroep maken om alle airconditionerapparaten in Seattle weer te geven, zodat een technicus de apparaten kan vinden waarvoor hij verantwoordelijk is.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een apparaatgroep maken
> * Een apparaatgroep gebruiken om apparaattelemetrie te analyseren

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u de [toepassing Een Azure IoT Central maken](./quick-deploy-iot-central.md) en een gesimuleerd apparaat toevoegen aan uw [IoT Central-toepassing](./quick-create-pnp-device.md) snel start om de **MXChip IoT DevKit-apparaatsjabloon** te maken om mee te werken.

## <a name="create-simulated-devices"></a>Gesimuleerde apparaten maken

Voordat u een apparaatgroep maakt, voegt u ten minste vijf gesimuleerde apparaten toe van de **MXChip IoT DevKit-apparaatsjabloon** om te gebruiken in deze zelfstudie:

![Vijf gesimuleerde sensorapparaten](./media/tutorial-use-device-groups/simulated-devices.png)

Voor vier van de gesimuleerde sensorapparaten gebruikt u de **weergave Apparaat beheren** om de naam van de klant in te stellen op *Contoso:*

![Klantnaam instellen op Contoso](./media/tutorial-use-device-groups/customer-name.png)

## <a name="create-a-device-group"></a>Een apparaatgroep maken

Een apparaatgroep maken:

1. Kies **Apparaatgroepen** in het linkerdeelvenster.

1. Selecteer **+**:

    ![Nieuwe apparaatgroep](media/tutorial-use-device-groups/image1.png)

1. Geef uw apparaatgroep de naam *Contoso-apparaten*. U ook een beschrijving toevoegen. Een apparaatgroep kan alleen apparaten bevatten van één apparaatsjabloon. Kies de **mxchip IoT DevKit-apparaatsjabloon** die voor deze groep moet worden gebruikt.

1. Als u de apparaatgroep wilt aanpassen om alleen de apparaten van **Contoso**op te nemen, selecteert u **+ Filter**. Selecteer de eigenschap **Klantnaam,** de **vergelijkingsoperator Gelijk en** **Contoso** als waarde. U meerdere filters en apparaten toevoegen die voldoen aan **alle** filtercriteria in de apparaatgroep. De apparaatgroep die u maakt, is toegankelijk voor iedereen die toegang heeft tot de toepassing, zodat iedereen de apparaatgroep kan bekijken, wijzigen of verwijderen:

    ![Query voor apparaatgroep](media/tutorial-use-device-groups/image2.png)

    > [!TIP]
    > De apparaatgroep is een dynamische query. Elke keer dat u de lijst met apparaten bekijkt, kunnen er verschillende apparaten in de lijst staan. De lijst is afhankelijk van welke apparaten momenteel voldoen aan de criteria van de query.

1. Kies **Opslaan**.

> [!NOTE]
> Selecteer Azure IoT Edge-sjablonen voor Azure IoT Edge-apparaten om een apparaatgroep te maken.

## <a name="analytics"></a>Analyse

U **Analytics** gebruiken met een apparaatgroep om de telemetrie van de apparaten in de groep te analyseren. U bijvoorbeeld de gemiddelde temperatuur in kaart brengen die door alle Contoso-omgevingssensoren wordt gerapporteerd.

Ga als lid van het werk om de telemetrie voor een apparaatgroep te analyseren:

1. Kies **Analytics** in het linkerdeelvenster.

1. Selecteer de apparaatgroep **Voor Apparaten contoso** die u hebt gemaakt. Voeg vervolgens de **telemetrietypen Temperatuur** en **Vochtigheid** toe:

    ![Analytics maken](./media/tutorial-use-device-groups/create-analysis.png)

    Gebruik de tandwielpictogrammen naast de telemetrietypen om een aggregatietype te selecteren. De standaardinstelling is **Gemiddeld**. Gebruik **Splitsen door** om de manier waarop de geaggregeerde gegevens worden weergegeven te wijzigen. Als u bijvoorbeeld splitst op apparaat-id, ziet u een plot voor elk apparaat wanneer u **Analyseren**selecteert.

1. Selecteer **Analyseren** om de gemiddelde telemetriewaarden weer te geven:

    ![Analyse weergeven](./media/tutorial-use-device-groups/view-analysis.png)

    U de weergave aanpassen, de weergegeven tijdsperiode wijzigen en de gegevens exporteren.

## <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd hoe u apparaatgroepen in uw Azure IoT Central-toepassing gebruiken, volgt de voorgestelde volgende stap:

> [!div class="nextstepaction"]
> [Telemetrieregels maken](tutorial-create-telemetry-rules.md)

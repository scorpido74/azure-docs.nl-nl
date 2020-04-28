---
title: Realtime gegevens visualisatie van gegevens frm Azure IoT Hub – Power BI
description: Gebruik Power BI om de gegevens van de Tempe ratuur en de vochtigheid te visualiseren die van de sensor worden verzameld en naar uw Azure IoT hub te verzenden.
author: robinsh
keywords: realtime gegevens visualisatie, visualisatie van Live gegevens, sensor gegevens visualisatie
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "73954628"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Real-time sensor gegevens visualiseren vanuit Azure IoT Hub met behulp van Power BI

![End-to-end-diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u real-time sensor gegevens visualiseren die uw Azure IoT hub ontvangt door gebruik te maken van Power BI. Als u de gegevens in uw IoT-hub wilt visualiseren met een web-app, raadpleegt u [een web-app gebruiken voor het visualiseren van real-time sensor gegevens uit Azure IOT hub](iot-hub-live-data-visualization-in-web-apps.md).

## <a name="what-you-do"></a>Wat u doet

* Haal uw IoT-hub gereed voor gegevens toegang door een Consumer groep toe te voegen.

* Een Stream Analytics taak maken, configureren en uitvoeren voor gegevens overdracht van uw IoT-hub naar uw Power BI-account.

* Een Power BI rapport maken en publiceren om de gegevens te visualiseren.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze artikelen hebben betrekking op de volgende vereisten:
  
  * Een actief Azure-abonnement.
  * Een Azure IoT hub onder uw abonnement.
  * Een client toepassing die berichten verzendt naar uw Azure IoT hub.

* Een Power BI-account. ([Probeer Power bi gratis](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een Stream Analytics taak maken, configureren en uitvoeren

Laten we beginnen met het maken van een Stream Analytics taak. Nadat u de taak hebt gemaakt, definieert u de invoer, uitvoer en de query die wordt gebruikt om de gegevens op te halen.

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in de [Azure Portal](https://portal.azure.com) **een resource** > maken**Internet of Things** > **Stream Analytics taak**.

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Resource groep**: gebruik dezelfde resource groep als uw IOT-hub.

   **Locatie**: gebruik dezelfde locatie als de resource groep.

   ![Een Stream Analytics-taak maken in azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selecteer **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de taak Stream Analytics.

2. Selecteer **invoer**onder **taak topologie**.

3. Selecteer in het deel venster **invoer** de optie **stroom invoer toevoegen**en selecteer vervolgens **IOT hub** in de vervolg keuzelijst. Voer in het deel venster Nieuw invoer de volgende gegevens in:

   **Invoer alias**: Voer een unieke alias in voor de invoer.

   **IOT hub van uw abonnement opgeven**: Selecteer dit keuze rondje.

   **Abonnement**: Selecteer het Azure-abonnement dat u voor deze zelf studie gebruikt.

   **IOT hub**: selecteer de IOT hub die u voor deze zelf studie gebruikt.

   **Eindpunt**: selecteer **Berichten**.

   **Naam van beleid voor gedeelde toegang**: Selecteer de naam van het gedeelde toegangs beleid dat de stream Analytics taak moet gebruiken voor uw IOT-hub. Voor deze zelf studie kunt u *service*selecteren. Het *service* beleid wordt standaard gemaakt op nieuwe IOT-hubs en verleent machtigingen voor het verzenden en ontvangen van aan de Cloud zijde beschik bare eind punten van de IOT hub. Zie [toegangs beheer en machtigingen](iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie.

   **Sleutel voor gedeeld toegangs beleid**: dit veld wordt automatisch ingevuld op basis van uw selectie voor de naam van het gedeelde toegangs beleid.

   **Consumenten groep**: Selecteer de Consumer groep die u eerder hebt gemaakt.

   Vul alle andere velden in op de standaard waarden.

   ![Een invoer toevoegen aan een Stream Analytics-taak in azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer **uitvoer**onder **taak topologie**.

2. Selecteer in het deel venster **uitvoer** de optie **toevoegen** en **Power bi**.

3. Selecteer **machtigen** in het deel venster **Power bi-nieuwe uitvoer** en volg de prompts om u aan te melden bij uw Power bi-account.

4. Nadat u zich hebt aangemeld bij Power BI, voert u de volgende gegevens in:

   **Uitvoer alias**: een unieke alias voor de uitvoer.

   **Groeps werkruimte**: Selecteer de werk ruimte van uw doel groep.

   **Naam van gegevensset**: Voer een naam in voor de gegevensset.

   **Tabel naam**: Voer een tabel naam in.

   ![Een uitvoer toevoegen aan een Stream Analytics-taak in azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak.

   ![Een query toevoegen aan een Stream Analytics-taak in azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selecteer **Opslaan**.

### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

Selecteer in de taak stream Analytics **overzicht** **en selecteer** > **nu** > **Start**starten. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![Een Stream Analytics-taak uitvoeren in azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Een Power BI rapport maken en publiceren om de gegevens te visualiseren

1. Zorg ervoor dat de voorbeeld toepassing wordt uitgevoerd op het apparaat. Als dat niet het geval is, raadpleegt u de zelf studies onder [het instellen van uw apparaat](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started).

2. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/en-us/)-account.

3. Selecteer de werk ruimte die u hebt gebruikt, **mijn werk ruimte**.

4. Selecteer **gegevens sets**.

   U ziet de gegevensset die u hebt opgegeven tijdens het maken van de uitvoer voor de Stream Analytics taak.

5. Voor de gegevensset die u hebt gemaakt, selecteert u **rapport toevoegen** (het eerste pictogram rechts van de naam van de gegevensset).

   ![Een micro soft Power BI-rapport maken](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   1. Selecteer in het deel venster **Visualisaties** van de pagina rapport maken het pictogram lijn diagram om een lijn diagram toe te voegen.

   2. Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt.

   3. Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   4. Sleep **Temperatuur** naar **Waarden**.

      Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

      ![Een lijn diagram voor de Tempe ratuur toevoegen aan een rapport van micro soft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Volg hiervoor de bovenstaande stappen en plaats **EventEnqueuedUtcTime** op de x-as en **vochtigheid** op de y-as.

   ![Een lijn diagram voor de vochtigheid toevoegen aan een rapport van micro soft Power BI](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecteer **Opslaan** om het rapport op te slaan.

9. Selecteer **rapporten** in het linkerdeel venster en selecteer vervolgens het rapport dat u zojuist hebt gemaakt.

10. Selecteer **bestand** > **publiceren op Internet**.

11. Selecteer **invoeg code maken**en selecteer vervolgens **publiceren**.

U hebt de rapport koppeling die u kunt delen met iedereen voor toegang tot rapporten en een code fragment dat u kunt gebruiken om het rapport te integreren in uw blog of website.

![Een micro soft Power BI-rapport publiceren](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Micro soft biedt u ook de [Power bi mobiele apps](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) voor het weer geven en communiceren met uw Power bi Dash boards en rapporten op uw mobiele apparaat.

## <a name="next-steps"></a>Volgende stappen

U hebt Power BI gebruikt voor het visualiseren van real-time sensor gegevens van uw Azure IoT hub.

Zie [een web-app gebruiken voor het visualiseren van gegevens van de real-time-sensor vanuit azure IOT hub](iot-hub-live-data-visualization-in-web-apps.md)voor een andere methode voor het visualiseren van data van Azure IOT hub.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

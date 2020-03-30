---
title: Real-time datavisualisatie van data frm Azure IoT Hub – Power BI
description: Gebruik Power BI om temperatuur- en vochtigheidsgegevens te visualiseren die van de sensor worden verzameld en naar uw Azure IoT-hub worden verzonden.
author: robinsh
keywords: realtime datavisualisatie, live datavisualisatie, sensordatavisualisatie
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 6/06/2019
ms.author: robinsh
ms.openlocfilehash: f0b909d10790511408e090546fd3359889ea5aca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954628"
---
# <a name="visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Real-time sensorgegevens van Azure IoT Hub visualiseren met Power BI

![End-to-end diagram](./media/iot-hub-live-data-visualization-in-power-bi/1_end-to-end-diagram.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u realtime sensorgegevens visualiseren die uw Azure IoT-hub ontvangt met Behulp van Power BI. Zie [Een web-app gebruiken om realtime sensorgegevens van Azure IoT Hub te visualiseren](iot-hub-live-data-visualization-in-web-apps.md)als u de gegevens in uw IoT-hub wilt visualiseren.

## <a name="what-you-do"></a>Wat je doet

* Maak uw IoT-hub klaar voor toegang tot gegevens door een consumentengroep toe te voegen.

* Maak, configureer en voer een Stream Analytics-taak uit voor gegevensoverdracht van uw IoT-hub naar uw Power BI-account.

* Maak en publiceer een Power BI-rapport om de gegevens te visualiseren.

## <a name="what-you-need"></a>Wat u nodig hebt

* Voltooi de [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial of een van de apparaat tutorials; bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze artikelen hebben betrekking op de volgende eisen:
  
  * Een actief Azure-abonnement.
  * Een Azure IoT-hub onder uw abonnement.
  * Een clienttoepassing die berichten verzendt naar uw Azure IoT-hub.

* Een Power BI-account. ([Probeer Power BI gratis)](https://powerbi.microsoft.com/)

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een Stream Analytics-taak maken, configureren en uitvoeren

Laten we beginnen met het maken van een Stream Analytics-taak. Nadat u de taak hebt gemaakt, definieert u de invoer, uitvoer en de query die wordt gebruikt om de gegevens op te halen.

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Selecteer in de [Azure-portal](https://portal.azure.com)de taak **Een bron** > **internet of things** > **stream analytics**maken .

2. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie als uw resourcegroep.

   ![Een Stream Analytics-taak maken in Azure](./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job-azure.png)

3. Selecteer **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de taak Stream Analytics.

2. Selecteer **Invoer onder** **Job Topology**.

3. Selecteer in het deelvenster **Invoer** de optie **Stream-invoer toevoegen**en selecteer **Vervolgens IoT Hub** in de vervolgkeuzelijst. Voer in het nieuwe invoervenster de volgende gegevens in:

   **Invoeralias**: Voer een unieke alias in voor de invoer.

   **IoT Hub leveren vanuit uw abonnement**: Selecteer deze keuzerondje.

   **Abonnement:** selecteer het Azure-abonnement dat u voor deze zelfstudie gebruikt.

   **IoT-hub:** selecteer de IoT-hub die u voor deze zelfstudie gebruikt.

   **Eindpunt**: selecteer **Berichten**.

   **Naam beleid voor gedeelde toegang:** selecteer de naam van het beleid voor gedeelde toegang dat u wilt gebruiken voor de functie Stream Analytics voor uw IoT-hub. Voor deze zelfstudie u *service*selecteren. Het *servicebeleid* is standaard gemaakt op nieuwe IoT-hubs en verleent toestemming om te verzenden en te ontvangen op eindpunten aan de cloudzijde die worden blootgesteld door de IoT-hub. Zie [Toegangsbeheer en machtigingen](iot-hub-devguide-security.md#access-control-and-permissions)voor meer informatie.

   **Beleidssleutel voor gedeelde toegang**: dit veld wordt automatisch ingevuld op basis van uw selectie voor de naam van het beleid voor gedeelde toegang.

   **Consumentengroep**: Selecteer de consumentengroep die u eerder hebt gemaakt.

   Laat alle andere velden op hun standaardinstellingen.

   ![Een invoer toevoegen aan een Stream Analytics-taak in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job-azure.png)

4. Selecteer **Opslaan**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Selecteer **Job Topology** **Uitvoer .**

2. Selecteer **Toevoegen** en **Power BI**in het deelvenster **Uitvoer** .

3. Selecteer in het **deelvenster Power BI - Nieuw uitvoer** de optie **Autoriseren** en volg de aanwijzingen om u aan te melden bij uw Power BI-account.

4. Nadat u zich hebt aangemeld bij Power BI, voert u de volgende gegevens in:

   **Uitvoeralias**: Een unieke alias voor de uitvoer.

   **Groepswerkruimte:** selecteer uw doelgroepwerkruimte.

   **Naam van gegevensset:** voer een gegevenssetnaam in.

   **Tabelnaam:** voer een tabelnaam in.

   ![Een uitvoer toevoegen aan een Stream Analytics-taak in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job-azure.png)

5. Selecteer **Opslaan**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Selecteer **Query** onder **Taaktopologie**.

2. Vervang `[YourInputAlias]` door de invoeralias van de taak.

3. Vervang `[YourOutputAlias]` door de uitvoeralias van de taak.

   ![Een query toevoegen aan een Stream Analytics-taak in Azure](./media/iot-hub-live-data-visualization-in-power-bi/add-query-stream-analytics-job-azure.png)

4. Selecteer **Opslaan**.

### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

Selecteer in de taak Stream Analytics **Overzicht**en selecteer**Nu** >  **starten** > **.** Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![Een Stream Analytics-taak uitvoeren in Azure](./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job-azure.png)

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Een Power BI-rapport maken en publiceren om de gegevens te visualiseren

1. Controleer of de voorbeeldtoepassing op uw apparaat wordt uitgevoerd. Zo niet, dan u verwijzen naar de zelfstudies onder [Uw apparaat instellen.](https://docs.microsoft.com/azure/iot-hub/iot-hub-raspberry-pi-kit-node-get-started)

2. Meld u aan bij uw [Power BI](https://powerbi.microsoft.com/en-us/)-account.

3. Selecteer de werkruimte die u hebt gebruikt, **Mijn werkruimte**.

4. Selecteer **Gegevenssets**.

   U ziet de gegevensset die u hebt opgegeven toen u de uitvoer voor de taak Stream Analytics hebt gemaakt.

5. Selecteer **Rapport toevoegen** (het eerste pictogram rechts van de gegevenssetnaam) voor de gegevenssetdie u hebt gemaakt.

   ![Een Microsoft Power BI-rapport maken](./media/iot-hub-live-data-visualization-in-power-bi/start-power-bi.png)

6. Maak een lijndiagram om in realtime de temperatuur gedurende een bepaalde periode weer te geven.

   1. Selecteer in het deelvenster **Visualisaties** van de pagina rapportcreatie het lijndiagrampictogram om een lijndiagram toe te voegen.

   2. Klap in het deelvenster **Velden** de tabel uit die u hebt opgegeven toen u de uitvoer voor de Stream Analytics-taak hebt gemaakt.

   3. Sleep **EventEnqueuedUtcTime** naar **As** in het deelvenster **Visualisaties**.

   4. Sleep **Temperatuur** naar **Waarden**.

      Er wordt een lijndiagram gemaakt. De x-as geeft de datum en tijd in UTC-tijdzone aan. De y-as geeft de temperatuur van de sensor aan.

      ![Een lijndiagram voor temperatuur toevoegen aan een Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temp.png)

7. Maak een ander lijndiagram om in realtime de vochtigheid gedurende een bepaalde periode weer te geven. Volg hiervoor dezelfde stappen boven en plaats **EventEnqueuedUtcTime** op de x-as en **vochtigheid** op de y-as.

   ![Een lijndiagram voor vochtigheid toevoegen aan een Microsoft Power BI-rapport](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-humidity.png)

8. Selecteer **Opslaan** om het rapport op te slaan.

9. Selecteer **Rapporten** in het linkerdeelvenster en selecteer vervolgens het rapport dat u zojuist hebt gemaakt.

10. Selecteer **Bestand** > **publiceren op het web**.

11. Selecteer **Insluitcode maken**en selecteer **Vervolgens Publiceren**.

U krijgt de rapportkoppeling die u met iedereen delen voor rapporttoegang en een codefragment dat u gebruiken om het rapport in uw blog of website te integreren.

![Een Microsoft Power BI-rapport publiceren](./media/iot-hub-live-data-visualization-in-power-bi/power-bi-publish.png)

Microsoft biedt ook de [mobiele Power BI-apps](https://powerbi.microsoft.com/en-us/documentation/powerbi-power-bi-apps-for-mobile-devices/) voor het bekijken en communiceren met uw Power BI-dashboards en rapporten op uw mobiele apparaat.

## <a name="next-steps"></a>Volgende stappen

U hebt Power BI met succes gebruikt om realtime sensorgegevens van uw Azure IoT-hub te visualiseren.

Zie [Een web-app gebruiken om realtime sensorgegevens van Azure IoT Hub te visualiseren](iot-hub-live-data-visualization-in-web-apps.md)voor een andere manier om gegevens van Azure IoT Hub te visualiseren.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

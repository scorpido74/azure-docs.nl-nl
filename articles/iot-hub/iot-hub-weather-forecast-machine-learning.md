---
title: Weers voorspelling met Azure Machine Learning met IoT Hub gegevens
description: Gebruik Azure Machine Learning om de kans op een regen te voors pellen op basis van de Tempe ratuur en vochtigheids gegevens die uw IoT-hub van een sensor verzamelt.
author: robinsh
manager: philmea
keywords: machine learning weer voors pellen
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: robinsh
ms.openlocfilehash: e7114febe85af4acaf94ef7fe64fe47988fd6321
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74079271"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Weers pellen met behulp van de sensor gegevens van uw IoT-hub in Azure Machine Learning

![End-to-end-diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning is een techniek van gegevens wetenschap die computers helpt bij het leren van bestaande gegevens om toekomstige gedragingen, resultaten en trends te voors pellen. Azure Machine Learning is een cloudservice voor predictive analytics waarmee u snel voorspellende modellen kunt maken en implementeren als analytics-oplossingen.

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u Azure Machine Learning kunt gebruiken voor de weers voorspelling (kans op regen) met behulp van de Tempe ratuur en vochtigheids gegevens van uw Azure IoT hub. De kans op regen is de uitvoer van een voor bereid model voor voor spellingen. Het model is gebaseerd op historische gegevens om de kans op een regen op basis van de Tempe ratuur en vochtigheid te ramen.

## <a name="what-you-do"></a>Wat u doet

- Implementeer het weers voorspelling model als een webservice.
- Haal uw IoT-hub gereed voor gegevens toegang door een Consumer groep toe te voegen.
- Maak een Stream Analytics taak en stel de taak in op:
  - Lees de gegevens over Tempe ratuur en vochtigheid van uw IoT-hub.
  - Roep de webservice aan om de regen kans te halen.
  - Sla het resultaat op in een Azure Blob-opslag.
- Gebruik Microsoft Azure Storage Explorer om de weers voorspelling weer te geven.

## <a name="what-you-need"></a>Wat u nodig hebt

- Voltooi de zelf studie [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) of een van de zelf studies van het apparaat. bijvoorbeeld [Raspberry Pi met node. js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze voldoen aan de volgende vereisten:
  - Een actief Azure-abonnement.
  - Een Azure IoT hub onder uw abonnement.
  - Een client toepassing die berichten verzendt naar uw Azure IoT hub.
- Een [Azure machine learning Studio](https://studio.azureml.net/) -account.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Het weers voorspelling model als een webservice implementeren

1. Ga naar de pagina voor het voors [pellen model](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).
1. Klik in Microsoft Azure Machine Learning Studio op **openen in Studio** .
   ![de model pagina voor de weers voorspelling in Cortana Intelligence Gallery te openen](media/iot-hub-weather-forecast-machine-learning/2_weather-prediction-model-in-cortana-intelligence-gallery.png)
1. Klik op **uitvoeren** om de stappen in het model te valideren. Deze stap kan twee minuten duren.
   ![het model voor de weers voorspelling in Azure Machine Learning Studio te openen](media/iot-hub-weather-forecast-machine-learning/3_open-weather-prediction-model-in-azure-machine-learning-studio.png)
1. Klik op **webservice instellen** > **voorspellende webservice**.
   ![het weers Voorspellings model in Azure Machine Learning Studio te implementeren](media/iot-hub-weather-forecast-machine-learning/4-deploy-weather-prediction-model-in-azure-machine-learning-studio.png)
1. In het diagram sleept u de module **webservice-invoer** ergens bij de module **score model** .
1. Verbind de module **Web Service-invoer** met de module **score model** .
   ![twee modules in Azure Machine Learning Studio te verbinden](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)
1. Klik op **uitvoeren** om de stappen in het model te valideren.
1. Klik op **Web service implementeren** om het model als een webservice te implementeren.
1. Down load in het dash board van het model de **Excel 2010 of eerder-werkmap** voor **aanvraag/antwoord**.

   > [!Note]
   > Zorg ervoor dat u de **werkmap van excel 2010 of eerder** downloadt, zelfs als u een nieuwere versie van Excel op uw computer gebruikt.

   ![Down load het Excel-eind punt voor aanvraag antwoorden](media/iot-hub-weather-forecast-machine-learning/5_download-endpoint-app-excel-for-request-response.png)

1. Open de Excel-werkmap, noteer de URL van de **webservice** en de **toegangs sleutel**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een Stream Analytics taak maken, configureren en uitvoeren

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Klik [in de Azure Portal](https://portal.azure.com/) op **Een resource maken** > **Internet of Things** > **Stream Analytics-taak**.
1. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Resource groep**: gebruik dezelfde resource groep als uw IOT-hub.

   **Locatie**: gebruik dezelfde locatie als de resource groep.

   **Vastmaken aan dashboard**: vink deze optie aan voor eenvoudige toegang tot uw IoT-hub vanuit het dashboard.

   ![Een Stream Analytics-taak maken in azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klik op **Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de taak Stream Analytics.
1. Klik onder **Taaktopologie** op **Invoer**.
1. Klik in het deel venster **invoer** op **toevoegen**en voer de volgende gegevens in:

   **Invoer alias**: de unieke alias voor de invoer.

   **Bron**: Selecteer **IOT hub**.

   **Consumenten groep**: Selecteer de Consumer groep die u hebt gemaakt.

   ![Een invoer toevoegen aan de Stream Analytics-taak in azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klik op **Maken**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Klik onder **Taaktopologie** op **Uitvoer**.
1. Klik in het deel venster **uitvoer** op **toevoegen**en voer de volgende gegevens in:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer.

   **Sink**: Selecteer **Blob Storage**.

   **Opslag account**: het opslag account voor de Blob-opslag. U kunt een opslag account maken of een bestaande gebruiken.

   **Container**: de container waarin de blob is opgeslagen. U kunt een container maken of een bestaande gebruiken.

   **Indeling van de serialisatie van gebeurtenissen**: Selecteer **CSV**.

   ![Een uitvoer toevoegen aan de Stream Analytics-taak in azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klik op **Maken**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Een functie toevoegen aan de Stream Analytics-taak om de door u geïmplementeerde webservice aan te roepen

1. Klik onder **taak topologie**op **functies** > **toevoegen**.
1. Voer de volgende informatie in:

   **Functie alias**: Voer `machinelearning`in.

   **Functie type**: Selecteer **Azure ml**.

   **Import optie**: Selecteer **importeren uit een ander abonnement**.

   **URL**: Voer de URL in van de webservice die u hebt genoteerd uit de Excel-werkmap.

   **Sleutel**: Voer de toegangs sleutel in die u hebt genoteerd uit de Excel-werkmap.

   ![Een functie toevoegen aan de Stream Analytics-taak in azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klik op **Maken**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>De query van de Stream Analytics-taak configureren

1. Klik onder **Taaktopologie** op **Query**.
1. Vervang de bestaande code door de volgende code:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[Scored Probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Vervang `[YourInputAlias]` door de invoeralias van de taak.

   Vervang `[YourOutputAlias]` door de uitvoeralias van de taak.

1. Klik op **Opslaan**.

### <a name="run-the-stream-analytics-job"></a>Voer de Stream Analytics-taak uit

Klik in de Stream Analytics-taak op **Start** > **Nu** > **Start**. Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![Voer de Stream Analytics-taak uit](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure Storage Explorer gebruiken om de weers voorspelling weer te geven

Voer de client toepassing uit om te beginnen met het verzamelen en verzenden van gegevens over de Tempe ratuur en vochtigheid naar uw IoT-hub. Voor elk bericht dat uw IoT-hub ontvangt, roept de Stream Analytics-taak de webservice voor de weers voorspelling aan om de kans op regen te produceren. Het resultaat wordt vervolgens opgeslagen in uw Azure Blob-opslag. Azure Storage Explorer is een hulp programma dat u kunt gebruiken om het resultaat weer te geven.

1. [Down load en installeer Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Open Azure Storage Explorer.
1. Meld u aan bij uw Azure-account.
1. Selecteer uw abonnement.
1. Klik op uw abonnement > **opslag Accounts** > uw opslag account > **Blob-containers** > uw container.
1. Open een CSV-bestand om het resultaat te bekijken. In de laatste kolom wordt de kans op regen vastgelegd.

   ![Resultaat van de weers voorspelling ophalen met Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/12_get-weather-forecast-result-azure-machine-learning.png)

## <a name="summary"></a>Samenvatting

U hebt Azure Machine Learning gebruikt voor het produceren van de kans op een regen op basis van de Tempe ratuur en de vochtigheids gegevens die uw IoT-hub ontvangt.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
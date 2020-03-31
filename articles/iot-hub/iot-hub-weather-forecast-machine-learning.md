---
title: Weersvoorspelling met Azure Machine Learning met IoT Hub-gegevens
description: Gebruik Azure Machine Learning om de kans op regen te voorspellen op basis van de temperatuur- en vochtigheidsgegevens die uw IoT-hub verzamelt van een sensor.
author: robinsh
manager: philmea
keywords: weersvoorspelling machine learning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 02/10/2020
ms.author: robinsh
ms.openlocfilehash: b71b86c14c55c312ef420a4d8517140fdded4072
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122188"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning"></a>Weersverwachting met behulp van de sensorgegevens van uw IoT-hub in Azure Machine Learning

![End-to-end diagram](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine learning is een techniek van data science die computers helpt te leren van bestaande gegevens om toekomstig gedrag, resultaten en trends te voorspellen. Azure Machine Learning is een cloudservice voor predictive analytics waarmee u snel voorspellende modellen kunt maken en implementeren als analytics-oplossingen.

## <a name="what-you-learn"></a>Wat u leert

U leert hoe u Azure Machine Learning gebruiken om weersvoorspelling (kans op regen) uit te brengen met behulp van de temperatuur- en vochtigheidsgegevens van uw Azure IoT-hub. De kans op regen is de output van een voorbereid weersvoorspellingsmodel. Het model is gebouwd op historische gegevens om de kans op regen te voorspellen op basis van temperatuur en vochtigheid.

## <a name="what-you-do"></a>Wat je doet

- Implementeer het weersvoorspellingsmodel als een webservice.
- Maak uw IoT-hub klaar voor toegang tot gegevens door een consumentengroep toe te voegen.
- Maak een Stream Analytics-taak en configureer de taak op:
  - Lees temperatuur- en vochtigheidsgegevens van uw IoT-hub.
  - Bel de webservice om de kans op regen te krijgen.
  - Sla het resultaat op in een Azure blob-opslag.
- Gebruik Microsoft Azure Storage Explorer om de weersvoorspelling weer te geven.

## <a name="what-you-need"></a>Wat u nodig hebt

- Voltooi de [Raspberry Pi online simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) tutorial of een van de apparaat tutorials; bijvoorbeeld [Raspberry Pi met node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Deze hebben betrekking op de volgende eisen:
  - Een actief Azure-abonnement.
  - Een Azure IoT-hub onder uw abonnement.
  - Een clienttoepassing die berichten verzendt naar uw Azure IoT-hub.
- Een [Azure Machine Learning Studio (klassiek)](https://studio.azureml.net/) account.

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Het weersvoorspellingsmodel implementeren als webservice

In deze sectie krijgt u het weersvoorspellingsmodel van de Azure AI-bibliotheek. Vervolgens voegt u een R-script module toe aan het model om de temperatuur- en vochtigheidsgegevens schoon te maken. Ten slotte implementeert u het model als een voorspellende webservice.

### <a name="get-the-weather-prediction-model"></a>Download het weersvoorspellingsmodel

In deze sectie krijgt u het weersvoorspellingsmodel uit de Azure AI Gallery en opent u het in Azure Machine Learning Studio (klassiek).

1. Ga naar de [voorbeeldpagina voor weersvoorspelling](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![De modelpagina voor weersvoorspelling openen in Azure AI-galerie](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Klik **op Openen in Studio (klassiek)** om het model te openen in Microsoft Azure Machine Learning Studio (klassiek).

   ![Het weersvoorspellingsmodel openen in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Een R-scriptmodule toevoegen om temperatuur- en vochtigheidsgegevens op te schonen

Om het model correct te laten werken, moeten de temperatuur- en vochtigheidsgegevens worden omgezet in numerieke gegevens. In deze sectie voegt u een R-scriptmodule toe aan het weersvoorspellingsmodel waarmee rijen met gegevenswaarden voor temperatuur of vochtigheid die niet kunnen worden geconverteerd naar numerieke waarden, worden verwijderd.

1. Klik aan de linkerkant van het Azure Machine Learning Studio-venster op de pijl om het deelvenster Gereedschappen uit te vouwen. Voer 'Uitvoeren' in het zoekvak in. Selecteer de module **R-script uitvoeren.**

   ![De module R-script uitvoeren selecteren](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Sleep de module **R-script uitvoeren** in de buurt van de module **Ontbrekende gegevens opschonen** en de bestaande **Script-module uitvoeren** op het diagram. Verwijder de verbinding tussen de **Clean Missing Data** en de Execute R **Script-modules** en sluit vervolgens de ingangen en uitgangen van de nieuwe module aan zoals weergegeven.

   ![Scriptmodule Uitvoeren R-script toevoegen](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Selecteer de nieuwe **Script-module Uitvoeren** om het eigenschappenvenster te openen. Kopieer en plak de volgende code in het **vak R Script.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   Wanneer u klaar bent, moet het venster Eigenschappen er hetzelfde uitzien als het volgende:

   ![Code toevoegen aan R Script-module uitvoeren](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Voorspellende webservice implementeren

In deze sectie valideert u het model, stelt u een voorspellende webservice in op basis van het model en implementeert u de webservice.

1. Klik **op Uitvoeren** om de stappen in het model te valideren. Deze stap kan enkele minuten in beslag nemen.

   ![Het experiment uitvoeren om de stappen te valideren](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Klik **op WEBSERVICE** > **Voorspellende webservice**instellen . Het diagram voor voorspellend experiment wordt geopend.

   ![Het weersvoorspellingsmodel implementeren in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. Verwijder in het diagram met voorspellende experimenten de verbinding tussen de **invoermodule webservice** en de **weergegevensset** bovenaan. Sleep vervolgens de **invoermodule webservice** ergens in de buurt van de module **Scoremodel** en sluit deze aan zoals weergegeven:

   ![Twee modules verbinden in Azure Machine Learning Studio (klassiek)](media/iot-hub-weather-forecast-machine-learning/13_connect-modules-azure-machine-learning-studio.png)

1. Klik **op RUN** om de stappen in het model te valideren.

1. Klik **op WEB SERVICE IMPLEMENTEREN** om het model als webservice te implementeren.

1. Download op het dashboard van het model de **Excel 2010 of eerdere werkmap** voor **AANVRAAG/ANTWOORD.**

   > [!Note]
   > Zorg ervoor dat u de **Excel 2010- of eerdere werkmap** downloadt, zelfs als u een latere versie van Excel op uw computer uitvoert.

   ![Het Excel voor het eindpunt VAN het antwoord aanvragen downloaden](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Open de Excel-werkmap, noteer de URL van de **webservice** en **de TOEGANGSSLEUTEL**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Een Stream Analytics-taak maken, configureren en uitvoeren

### <a name="create-a-stream-analytics-job"></a>Een Stream Analytics-taak maken

1. Klik [in de Azure Portal](https://portal.azure.com/) op **Een resource maken** > **Internet of Things** > **Stream Analytics-taak**.
1. Voer de volgende informatie in voor de taak.

   **Taaknaam**: de naam van de taak. De naam moet wereldwijd uniek zijn.

   **Resourcegroep:** gebruik dezelfde resourcegroep die uw IoT-hub gebruikt.

   **Locatie:** gebruik dezelfde locatie als uw resourcegroep.

   **Vastmaken aan dashboard**: vink deze optie aan voor eenvoudige toegang tot uw IoT-hub vanuit het dashboard.

   ![Een Stream Analytics-taak maken in Azure](media/iot-hub-weather-forecast-machine-learning/7_create-stream-analytics-job-azure.png)

1. Klik **op Maken**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Een invoer aan de Stream Analytics-taak toevoegen

1. Open de taak Stream Analytics.
1. Klik onder **Taaktopologie** op **Invoer**.
1. Klik **in** het deelvenster Invoer op **Toevoegen**en voer de volgende gegevens in:

   **Invoeralias**: De unieke alias voor de invoer.

   **Bron**: **IoT-hub selecteren**.

   **Consumentengroep**: Selecteer de consumentengroep die u hebt gemaakt.

   ![Een invoer toevoegen aan de functie Stream Analytics in Azure](media/iot-hub-weather-forecast-machine-learning/8_add-input-stream-analytics-job-azure.png)

1. Klik **op Maken**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Een uitvoer aan de Stream Analytics-taak toevoegen

1. Klik onder **Taaktopologie** op **Uitvoer**.
1. Klik **in** het deelvenster Uitvoer op **Toevoegen**en voer de volgende gegevens in:

   **Uitvoeralias**: de alias die uniek is voor de uitvoer.

   **Gootsteen:** **Blob-opslag selecteren**.

   **Opslagaccount:** het opslagaccount voor uw blob-opslag. U een opslagaccount maken of een bestaand account gebruiken.

   **Container:** de container waarin de blob wordt opgeslagen. U een container maken of een bestaande container gebruiken.

   **Indeling voor serie-indeling:** **CSV**selecteren .

   ![Een uitvoer toevoegen aan de functie Stream Analytics in Azure](media/iot-hub-weather-forecast-machine-learning/9_add-output-stream-analytics-job-azure.png)

1. Klik **op Maken**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Een functie toevoegen aan de taak Stream Analytics om de webservice te bellen die u hebt geïmplementeerd

1. Klik **onder Taaktopologie**op **Functietoevoegen** > **Add**.
1. Voer de volgende informatie in:

   **Functiealias**: `machinelearning`Enter .

   **Functietype**: Selecteer **Azure ML**.

   **Importoptie**: Selecteer **Importeren vanuit een ander abonnement**.

   **URL:** voer de URL van de webservice in die u hebt genoteerd in de Excel-werkmap.

   **Sleutel:** voer de TOEGANGSSLEUTEL in die u hebt genoteerd in de Excel-werkmap.

   ![Een functie toevoegen aan de functie Stream Analytics in Azure](media/iot-hub-weather-forecast-machine-learning/10_add-function-stream-analytics-job-azure.png)

1. Klik **op Maken**.

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

Klik in de taak Stream Analytics op**Nu** >  **starten** > .**Start** Zodra de taak kan worden gestart, wordt de taakstatus veranderd van **Gestopt** naar **In uitvoering**.

![Voer de Stream Analytics-taak uit](media/iot-hub-weather-forecast-machine-learning/11_run-stream-analytics-job-azure.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Microsoft Azure Storage Explorer gebruiken om de weersvoorspelling weer te geven

Voer de clienttoepassing uit om temperatuur- en vochtigheidsgegevens te verzamelen en naar uw IoT-hub te verzenden. Voor elk bericht dat uw IoT-hub ontvangt, roept de functie Stream Analytics de webservice weersvoorspelling op om de kans op regen te produceren. Het resultaat wordt vervolgens opgeslagen in uw Azure blob-opslag. Azure Storage Explorer is een hulpmiddel dat u gebruiken om het resultaat weer te geven.

1. [Microsoft Azure Storage Explorer downloaden en installeren](https://storageexplorer.com/).
1. Open Azure Storage Explorer.
1. Meld u aan bij uw Azure-account.
1. Selecteer uw abonnement.
1. Klik op uw abonnement > **Opslagaccounts** > uw opslagaccount > **Blob Containers** > uw container.
1. Download een CSV-bestand om het resultaat te zien. De laatste kolom registreert de kans op regen.

   ![Resultaten van weersvoorspelling behalen met Azure Machine Learning](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Samenvatting

U hebt Azure Machine Learning met succes gebruikt om de kans op regen te produceren op basis van de temperatuur- en vochtigheidsgegevens die uw IoT-hub ontvangt.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
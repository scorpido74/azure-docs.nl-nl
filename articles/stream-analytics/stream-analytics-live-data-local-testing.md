---
title: Live-gegevens met Azure Stream Analytics voor Visual Studio test
description: Informatie over het testen van uw Azure Stream Analytics-taak lokaal via live streaminggegevens.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: cda02408f9e90cb9f19a4f7d806a34e5cff9c33c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75426043"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Testen van live gegevens lokaal met behulp van Azure Stream Analytics-hulpprogramma's voor Visual Studio (Preview)

Azure Stream Analytics-hulpprogramma's voor Visual Studio kunt u taken vanuit de IDE lokaal testen met behulp van live-gebeurtenis-stromen van Azure Event Hub, IoT-Hub en Blob-opslag. Live-gegevens lokaal testen kan niet worden vervangen de [prestaties en schaalbaarheid testen](stream-analytics-streaming-unit-consumption.md) u in de cloud kunt uitvoeren, maar u kunt tijdens het functionele testen omdat u niet hoeft te verzenden naar de cloud telkens wanneer u wilt testen van uw Stream opslaan Analytics-taak. Deze functie is beschikbaar als preview en mag niet worden gebruikt voor werkbelastingen voor productie.

## <a name="testing-options"></a>Testopties

De volgende opties voor lokale tests worden ondersteund:

|**Invoer**  |**Uitvoer**  |**Taaktype**  |
|---------|---------|---------|
|Lokale statische gegevens   |  Lokale statische gegevens   |   Cloud/Edge |
|Live invoergegevens   |  Lokale statische gegevens   |   Cloud |
|Live invoergegevens   |  Live uitvoergegevens   |   Cloud |

## <a name="local-testing-with-live-data"></a>Lokale testen met live-gegevens

1. Nadat u hebt gemaakt een [Azure Stream Analytics cloud-project in Visual Studio](stream-analytics-quick-create-vs.md)Open **script.asaql**. Het lokale testen lokale invoer- en lokale standaard gebruikt.

   ![Azure Stream Analytics Visual Studio lokale invoer- en lokale uitvoer](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Als u wilt testen live-gegevens, kies **gebruik Cloud invoer** in de vervolgkeuzelijst.

   ![Azure Stream Analytics Visual Studio cloud live-invoer](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Stel de **begintijd** om te definiëren wanneer de taak zal starten voor het verwerken van invoergegevens. De taak moet mogelijk gelezen invoergegevens tevoren om nauwkeurig resultaten te behalen. Standaard is ingesteld op 30 minuten voor de huidige tijd.

   ![Begintijd van live gegevens van Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klik op **lokaal uitvoeren**. Een consolevenster wordt weergegeven met de actieve voortgang en taak metrische gegevens. Als u wilt het proces te stoppen, kunt u dit handmatig doen. 

   ![Venster van Azure Stream Analytics Visual Studio live-gegevens verwerken](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   De resultaten van de uitvoer worden elke drie seconden met de eerste 500 uitvoer rijen in het resultatenvenster lokaal uitvoeren vernieuwd en dat de uitvoerbestanden worden geplaatst in het projectpad **ASALocalRun** map. U kunt de uitvoerbestanden ook openen door te klikken op **resultaten map openen** knop in het resultatenvenster lokaal uitvoeren.

   ![Azure Stream Analytics Visual Studio-livegegevens openen resultaten map](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Als u de resultaten naar uw cloud-uitvoerlocaties wilt, kiest u **uitvoer naar de Cloud** in de tweede vervolgkeuzelijst. Power BI en Azure Data Lake-opslag zijn niet ondersteunde uitvoereindpunten.

   ![Dynamische gegevens van Azure Stream Analytics Visual Studio uitvoer naar de cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Beperkingen

* Power BI en Azure Data Lake-opslag zijn niet ondersteunde uitvoerlocaties vanwege beperkingen van verificatie-model.

* Alleen cloud invoer opties hebben [tijd beleid](stream-analytics-out-of-order-and-late-events.md) ondersteunen, maar niet voor lokale invoer opties.

## <a name="next-steps"></a>Volgende stappen

* [Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-hulpprogramma's voor Visual Studio](stream-analytics-quick-create-vs.md)
* [Azure Stream Analytics-hulpprogramma's voor Visual Studio installeren](stream-analytics-tools-for-visual-studio-install.md)
* [Stream Analytics-query's met Visual Studio lokaal testen](stream-analytics-vs-tools-local-run.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)

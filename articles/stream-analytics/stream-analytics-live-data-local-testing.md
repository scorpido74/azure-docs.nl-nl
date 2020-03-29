---
title: Live gegevens testen met Azure Stream Analytics voor Visual Studio
description: Meer informatie over het lokaal testen van uw Azure Stream Analytics-taak met behulp van live streaminggegevens.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: f2876ea32bdcd900a454ae6b7ac58c11b8ec67c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840482"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Live gegevens lokaal testen met Azure Stream Analytics-hulpprogramma's voor Visual Studio (Preview)

Met Azure Stream Analytics-hulpprogramma's voor Visual Studio u taken lokaal testen vanuit de IDE met behulp van live gebeurtenisstreams vanuit Azure Event Hub, IoT Hub en Blob Storage. Lokale tests met live gegevens kunnen de [prestatie- en schaalbaarheidstests](stream-analytics-streaming-unit-consumption.md) die u in de cloud uitvoeren niet vervangen, maar u tijd besparen tijdens het testen van games door u niet telkens in de cloud hoeft in te dienen wanneer u uw Stream Analytics-taak wilt testen. Deze functie is in preview en mag niet worden gebruikt voor productieworkloads.

## <a name="testing-options"></a>Testopties

De volgende lokale testopties worden ondersteund:

|**Invoer**  |**Output**  |**Taaktype**  |
|---------|---------|---------|
|Lokale statische gegevens   |  Lokale statische gegevens   |   Cloud/rand |
|Live invoergegevens   |  Lokale statische gegevens   |   Cloud |
|Live invoergegevens   |  Live-uitvoergegevens   |   Cloud |

## <a name="local-testing-with-live-data"></a>Lokale tests met live gegevens

1. Nadat u een [Azure Stream Analytics-cloudproject hebt](stream-analytics-quick-create-vs.md)gemaakt in Visual Studio, opent u **script.asaql**. De lokale tests maken standaard gebruik van lokale invoer en lokale uitvoer.

   ![Lokale invoer en lokale uitvoer van Azure Stream Analytics Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Als u live gegevens wilt testen, kiest **u Cloudinvoer gebruiken** in het vervolgkeuzevak.

   ![Azure Stream Analytics Visual Studio live cloud-invoer](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)

3. Stel de **begintijd in** om te bepalen wanneer de taak wordt gestart met het verwerken van invoergegevens. De taak moet mogelijk van tevoren invoergegevens lezen om nauwkeurige resultaten te garanderen. De standaardtijd is ingesteld op 30 minuten voor de huidige tijd.

   ![Starttijd van Azure Stream Analytics Visual Studio live-gegevens](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Klik **op Lokaal uitvoeren**. Er wordt een consolevenster weergegeven met de voortgangsvoortgang en taakstatistieken. Als u het proces wilt stoppen, u dit handmatig doen. 

   ![Venster Live dataproces van Azure Stream Analytics](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   De uitvoerresultaten worden elke drie seconden vernieuwd met de eerste 500 uitvoerrijen in het lokale run-resultaatvenster en de uitvoerbestanden worden in de map **ASALocalRun** van het projectpad geplaatst. U de uitvoerbestanden ook openen door op de knop **Map met resultaten** openen te klikken in het lokale run-resultaatvenster.

   ![Azure Stream Analytics Visual Studio live data open resultaten map](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Als u de resultaten wilt uitvoeren naar uw clouduitvoersinks, kiest **u Uitvoer naar cloud** vanuit het tweede vervolgkeuzevak. Power BI en Azure Data Lake Storage worden niet ondersteund uitvoerputten.

   ![Azure Stream Analytics Visual Studio live data-uitvoer naar cloud](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Beperkingen

* Power BI en Azure Data Lake Storage worden niet ondersteund als gevolg van verificatiemodelbeperkingen.

* Alleen opties voor cloudinvoer hebben [ondersteuning voor tijdbeleid,](stream-analytics-out-of-order-and-late-events.md) terwijl lokale invoeropties dat niet doen.

## <a name="next-steps"></a>Volgende stappen

* [Een Stream Analytics-taak maken met behulp van de Azure Stream Analytics-tools voor Visual Studio](stream-analytics-quick-create-vs.md)
* [Query's van Stream Analytics lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Visual Studio gebruiken om Azure Stream Analytics-taken weer te geven](stream-analytics-vs-tools.md)

---
title: Een Azure Stream Analytics-taak testen met voorbeeldgegevens
description: In dit artikel wordt beschreven hoe u de Azure-portal gebruiken om een Azure Stream Analytics-taak, voorbeeldinvoer en het uploaden van voorbeeldgegevens te testen.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 3/6/2020
ms.custom: seodec18
ms.openlocfilehash: de2b99666dbed4eaec3db3e56800e9a7352e10e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898390"
---
# <a name="test-an-azure-stream-analytics-job-in-the-portal"></a>Een Azure Stream Analytics-taak testen in de portal

In Azure Stream Analytics u uw query testen zonder uw taak te starten of te stoppen. U query's testen op binnenkomende gegevens uit uw streamingbronnen of voorbeeldgegevens uploaden uit een lokaal bestand op Azure Portal. U query's ook lokaal testen op uw lokale voorbeeldgegevens of livegegevens in [Visual Studio](stream-analytics-live-data-local-testing.md) en Visual [Studio Code.](visual-studio-code-local-run-live-input.md)

## <a name="automatically-sample-incoming-data-from-input"></a>Automatisch inkomende gegevens uit invoer bemonsteren

Azure Stream Analytics haalt automatisch gebeurtenissen op uit uw streaming-invoer. U query's uitvoeren in het standaardvoorbeeld of een specifiek tijdsbestek voor het voorbeeld instellen.

1. Meld u aan bij Azure Portal.

2. Zoek en selecteer uw bestaande Stream Analytics-taak.

3. Selecteer op de vacaturepagina Stream Analytics onder de kop **Taaktopologie** de optie **Query** om het venster Queryeditor te openen. 

4. Als u een voorbeeldlijst met binnenkomende gebeurtenissen wilt zien, selecteert u de invoer met bestandspictogram en worden de voorbeeldgebeurtenissen automatisch weergegeven in het **voorbeeldvoorbeeld van invoer**.

   a. Het serialisatietype voor uw gegevens wordt automatisch gedetecteerd als de JSON of CSV. U het ook handmatig wijzigen in JSON, CSV, AVRO door de optie in het vervolgkeuzemenu te wijzigen.
    
   b. Gebruik de kiezer om uw gegevens weer te geven in **tabel-** of **raw-indeling.**
    
   c. Als de weergegeven gegevens niet actueel zijn, selecteert u **Vernieuwen** om de nieuwste gebeurtenissen weer te geven.

   De volgende tabel is een voorbeeld van gegevens in de **tabelnotatie:**

   ![Voorbeeldinvoer van Azure Stream Analytics in tabelindeling](./media/stream-analytics-test-query/asa-sample-table.png)

   De volgende tabel is een voorbeeld van gegevens in de **Raw-indeling:**

   ![Voorbeeldinvoer van Azure Stream Analytics in raw-indeling](./media/stream-analytics-test-query/asa-sample-raw.png)

5. Als u uw query wilt testen met binnenkomende gegevens, selecteert u **Query testen**. De resultaten worden weergegeven op het tabblad **Testresultaten.** U ook **Resultaten downloaden** selecteren om de resultaten te downloaden.

   ![Testqueryresultaten van Azure Stream Analytics-tests](./media/stream-analytics-test-query/asa-test-query.png)

6. Als u uw query wilt testen op een specifiek tijdsbereik van binnenkomende gebeurtenissen, selecteert **u Tijdbereik selecteren**.
   
   ![Azure Stream Analytics-tijdsbereik voor binnenkomende voorbeeldgebeurtenissen](./media/stream-analytics-test-query/asa-select-time-range.png)

7. Stel het tijdsbereik in van de gebeurtenissen die u wilt gebruiken om uw query te testen en **selecteer Voorbeeld**. Binnen dat tijdsbestek u maximaal 1000 gebeurtenissen of 1 MB ophalen, wat het eerst komt.

   ![Azure Stream Analytics stelt tijdsbereik in voor binnenkomende voorbeeldgebeurtenissen](./media/stream-analytics-test-query/asa-set-time-range.png)

8. Zodra de gebeurtenissen zijn bemonsterd voor een geselecteerd tijdsbereik, worden ze weergegeven op het tabblad **Voorvertoning invoer.**

   ![Azure Stream Analytics-testresultaten weergeven](./media/stream-analytics-test-query/asa-view-test-results.png)

9. Selecteer **Opnieuw instellen** om de voorbeeldlijst met binnenkomende gebeurtenissen weer te geven. Als u **Opnieuw instellen**selecteert, gaat uw selectie van tijdbereik verloren. Selecteer **Query testen** om uw query te testen en bekijk de resultaten op het tabblad **Testresultaten.**

10. Wanneer u wijzigingen aanbrengt in uw query, selecteert u **Query opslaan** om de nieuwe querylogica te testen. Hiermee u uw query iteratief wijzigen en opnieuw testen om te zien hoe de uitvoer verandert.

11. Nadat u de resultaten in de browser hebt geverifieerd, u **de** taak starten.

## <a name="upload-sample-data-from-a-local-file"></a>Voorbeeldgegevens uit een lokaal bestand uploaden

In plaats van live gegevens te gebruiken, u voorbeeldgegevens uit een lokaal bestand gebruiken om uw Azure Stream Analytics-query te testen.

1. Meld u aan bij Azure Portal.
   
2. Zoek uw bestaande Stream Analytics-taak en selecteer deze.

3. Selecteer op de vacaturepagina Stream Analytics onder de kop **Taaktopologie** de optie **Query** om het venster Queryeditor te openen.

4. Als u uw query wilt testen met een lokaal bestand, selecteert u **Voorbeeldinvoer uploaden** op het tabblad **Voorbeeld invoer.** 

   ![Voorbeeldbestand uploaden van Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-file.png)

5. Upload uw lokale bestand om de query te testen. U alleen bestanden uploaden met de JSON-, CSV- of AVRO-indelingen. Selecteer **OK**.

   ![Voorbeeldbestand uploaden van Azure Stream Analytics](./media/stream-analytics-test-query/asa-upload-sample-json-file.png)

6. Zodra u het bestand uploadt, u de bestandsinhoud ook in het formulier zien als een tabel of in de ruwe indeling. Als u **Opnieuw instellen**selecteert, worden de voorbeeldgegevens teruggezet naar de binnenkomende invoergegevens die in de vorige sectie zijn uitgelegd. U elk ander bestand uploaden om de query op elk gewenst moment te testen.

7. Selecteer **Testquery** om uw query te testen op het geüploade voorbeeldbestand.

8. De testresultaten worden weergegeven op basis van uw zoekopdracht. U uw query wijzigen en **query opslaan** selecteren om de nieuwe querylogica te testen. Hiermee u uw query iteratief wijzigen en opnieuw testen om te zien hoe de uitvoer verandert.

9. Wanneer u meerdere uitvoerin de query gebruikt, worden de resultaten weergegeven op basis van geselecteerde uitvoer. 

   ![Geselecteerde uitvoer van Azure Stream Analytics](./media/stream-analytics-test-query/asa-sample-test-selected-output.png)

10. Nadat u de resultaten in de browser hebt geverifieerd, u de taak **starten.**

## <a name="next-steps"></a>Volgende stappen
* [Bouw een IoT-oplossing met Stream Analytics:](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-build-an-iot-solution-using-stream-analytics)deze zelfstudie begeleidt u bij het bouwen van een end-to-end oplossing met een gegevensgenerator die verkeer bij een tolhuisje simuleert.

* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)

* [Voorbeelden van query's voor algemene gebruikspatronen van Stream Analytics](stream-analytics-stream-analytics-query-patterns.md)

* [Inzicht in invoer voor Azure Stream Analytics](stream-analytics-add-inputs.md)

* [Inzicht krijgen in uitvoer vanuit Azure Stream Analytics](stream-analytics-define-outputs.md)

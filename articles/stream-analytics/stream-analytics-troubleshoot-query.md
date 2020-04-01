---
title: Azure Stream Analytics-query's oplossen
description: In dit artikel worden technieken beschreven om problemen op te lossen in Azure Stream Analytics-taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: f049dc6d1261a8201cf79d1779e522b30d13c4b0
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409446"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Azure Stream Analytics-query's oplossen

In dit artikel worden veelvoorkomende problemen beschreven met het ontwikkelen van Stream Analytics-query's en hoe u deze oplossen.

In dit artikel worden veelvoorkomende problemen beschreven met het ontwikkelen van Azure Stream Analytics-query's, het oplossen van queryproblemen en het oplossen van de problemen. Voor veel stappen voor het oplossen van problemen moeten diagnostische logboeken worden ingeschakeld voor uw Stream Analytics-taak. Zie [Azure Stream Analytics oplossen met behulp van diagnostische logboeken](stream-analytics-job-diagnostic-logs.md)als u geen diagnostische logboeken hebt ingeschakeld.

## <a name="query-is-not-producing-expected-output"></a>Query produceert geen verwachte uitvoer

1.  Onderzoek fouten door lokaal te testen:

    - Selecteer op azure-portal op het tabblad **Query** de optie **Testen**. Gebruik de gedownloade voorbeeldgegevens om de query te [testen.](stream-analytics-test-query.md) Onderzoek eventuele fouten en probeer deze te corrigeren.   
    - U uw query ook lokaal testen met Azure Stream [Analytics-hulpprogramma's](stream-analytics-live-data-local-testing.md) voor Visual Studio of [Visual Studio Code.](visual-studio-code-local-run-live-input.md) 

2.  [Query's stap voor stap nauwkeurig opsporen met behulp van taakdiagram](debug-locally-using-job-diagram.md) in Azure Stream Analytics-hulpprogramma's voor Visual Studio. Het taakdiagram laat zien hoe gegevens stromen uit invoerbronnen (Gebeurtenishub, IoT Hub, enz.) via meerdere querystappen en uiteindelijk naar uitvoergootstenen. Elke querystap wordt toegewezen aan een tijdelijke resultaatset die in het script is gedefinieerd met de instructie MET. U de gegevens en statistieken bekijken in elke tussentijdse resultaatset om de bron van het probleem te vinden.

    ![Voorbeeld van het voorbeeld van het taakdiagram](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Als u [**Timestamp By gebruikt,**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)controleert u of de gebeurtenissen tijdstempels hebben die groter zijn dan de [begintijd van](stream-analytics-out-of-order-and-late-events.md)de taak.

4.  Elimineer veelvoorkomende valkuilen, zoals:
    - Een [**WHERE-component**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) in de query filterde alle gebeurtenissen uit, waardoor geen uitvoer wordt gegenereerd.
    - Een [**CAST-functie**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) mislukt, waardoor de taak mislukt. Gebruik [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) om fouten in het typecast te voorkomen.
    - Wanneer u vensterfuncties gebruikt, wacht u tot de volledige vensterduur een uitvoer van de query ziet.
    - De tijdstempel voor gebeurtenissen gaat vooraf aan de begintijd van de taak en gebeurtenissen worden verwijderd.
    - [**Join-voorwaarden**](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics) komen niet overeen. Als er geen overeenkomsten zijn, zal er nul output zijn.

5.  Zorg ervoor dat het beleid voor het bestellen van gebeurtenissen is geconfigureerd zoals verwacht. Ga naar **Instellingen** en selecteer [**Gebeurtenisbestellen**](stream-analytics-out-of-order-and-late-events.md). Het beleid wordt *niet* toegepast wanneer u de knop **Testen** gebruikt om de query te testen. Dit resultaat is een verschil tussen het testen in de browser versus het uitvoeren van de taak in productie. 

6. Foutopsporing met behulp van controle- en diagnostische logboeken:
    - Gebruik [Controlelogboeken](../azure-resource-manager/resource-group-audit.md)en filter om fouten te identificeren en te debuggen.
    - Gebruik [taakdiagnostische logboeken](stream-analytics-job-diagnostic-logs.md) om fouten te identificeren en te debuggen.

## <a name="resource-utilization-is-high"></a>Het gebruik van resources is hoog

Zorg ervoor dat u profiteert van parallellen in Azure Stream Analytics. U leren [schalen met queryparallelisatie](stream-analytics-parallelization.md) van Stream Analytics-taken door invoerpartities te configureren en de definitie van analysequery's af te stemmen.

## <a name="debug-queries-progressively"></a>Fouten progressief opsporen in query's

In realtime gegevensverwerking kan het handig zijn om te weten hoe de gegevens er midden in de query uit zien. U dit zien aan de hand van het taakdiagram in Visual Studio. Als u geen Visual Studio hebt, u aanvullende stappen ondernemen om tussentijdse gegevens uit te voeren.

Omdat invoer of stappen van een Azure Stream Analytics-taak meerdere keren kunnen worden gelezen, u extra SELECT INTO-instructies schrijven. Hierdoor worden tussenliggende gegevens naar opslag uitgevoerd en u de juistheid van de gegevens inspecteren, net zoals *horlogevariabelen* dat doen wanneer u een programma debugt.

De volgende voorbeeldquery in een Azure Stream Analytics-taak heeft één streaminvoer, twee referentiegegevensinvoer en een uitvoer naar Azure Table Storage. De query voegt gegevens van de gebeurtenishub en twee referentieblobs samen om de naam- en categoriegegevens op te halen:

![Voorbeeld van Stream Analytics SELECT INTO query](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Houd er rekening mee dat de taak wordt uitgevoerd, maar dat er geen gebeurtenissen worden geproduceerd in de uitvoer. Op de tegel **Controle,** die hier wordt weergegeven, u zien dat de invoer gegevens produceert, maar u weet niet welke stap van de **JOIN** ervoor heeft gezorgd dat alle gebeurtenissen zijn verwijderd.

![De tegel Stream Analytics Monitoring](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

In deze situatie u een paar extra SELECT INTO-instructies toevoegen om de tussentijdse JOIN-resultaten en de gegevens die uit de invoer worden gelezen, te loggen.

In dit voorbeeld hebben we twee nieuwe 'tijdelijke uitvoer' toegevoegd. Ze kunnen elke gootsteen zijn die je wilt. Hier gebruiken we Azure Storage als voorbeeld:

![Extra SELECT INTO-instructies toevoegen aan Stream Analytics-query](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

U de query vervolgens als volgt herschrijven:

![Herschreven SELECT INTO Stream Analytics-query](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Nu beginnen met de baan opnieuw, en laat het draaien voor een paar minuten. Query er vervolgens temp1 en temp2 bij Visual Studio Cloud Explorer om de volgende tabellen te produceren:

**temp1-tabel**
![SELECT IN temp1 tabel Stream Analytics-query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2-tabel**
![SELECT IN temp2-tabel Stream Analytics-query](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Zoals u zien, hebben temp1 en temp2 beide gegevens en wordt de naamkolom correct ingevuld in temp2. Echter, omdat er nog steeds geen gegevens in de output, is er iets mis:

![SELECT INTO output1-tabel zonder gegevensstroomAnalysequery](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Door de gegevens te samplen, u er bijna zeker van zijn dat het probleem bij de tweede JOIN is. U de referentiegegevens van de blob downloaden en een kijkje nemen:

![SELECT IN ref tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Zoals u zien, is de indeling van de GUID in deze referentiegegevens anders dan de indeling van de kolom [van] in temp2. Dat is de reden waarom de gegevens niet aankomen in output1 zoals verwacht.

U de gegevensindeling herstellen, uploaden naar referentieblob en het opnieuw proberen:

![SELECTEER IN temp tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Deze keer worden de gegevens in de uitvoer opgemaakt en ingevuld zoals verwacht.

![SELECTEER IN-query Stream Analytics in de laatste tabel](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Help opvragen

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

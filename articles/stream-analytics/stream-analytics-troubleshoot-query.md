---
title: Problemen met Azure Stream Analytics query's oplossen
description: In dit artikel worden technieken beschreven voor het oplossen van problemen met query's in Azure Stream Analytics-taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: bf0740bbdd4754aeba43e64f1076a1bea33cffc6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/29/2020
ms.locfileid: "76844413"
---
# <a name="troubleshoot-azure-stream-analytics-queries"></a>Problemen met Azure Stream Analytics query's oplossen

In dit artikel worden veelvoorkomende problemen beschreven met het ontwikkelen van Stream Analytics query's en het oplossen van problemen.

## <a name="query-is-not-producing-expected-output"></a>De verwachte uitvoer wordt niet geproduceerd met de query
1.  Controleer fouten door lokaal te testen:
    - Selecteer op Azure Portal op het tabblad **query** de optie **testen**. Gebruik de gedownloade voorbeeld gegevens om [de query te testen](stream-analytics-test-query.md). Controleer eventuele fouten en probeer deze te corrigeren.   
    - U kunt [uw query ook lokaal testen](stream-analytics-live-data-local-testing.md) met Azure stream Analytics-hulpprogram Ma's voor Visual Studio of [Visual Studio code](visual-studio-code-local-run-live-input.md). 

2.  [Debug-query's stap voor stap lokaal met behulp van taak diagram](debug-locally-using-job-diagram.md) in azure stream Analytics-hulpprogram Ma's voor Visual Studio. Het taak diagram is om te laten zien hoe gegevens stromen van invoer bronnen (Event hub, IoT Hub, enzovoort) via meerdere query stappen en tot slot uitvoer naar Sinks. Elke query stap wordt toegewezen aan een tijdelijke resultatenset die is gedefinieerd in het script using WITH-instructie. U kunt de gegevens weer geven en waarden in elke query stap in elke tussenliggende resultatenset om de oorzaak van het probleem te vinden.
    voorbeeld resultaat ![taak diagram](./media/debug-locally-using-job-diagram/preview-result.png)

3.  Als u [**time stamp by**](https://docs.microsoft.com/stream-analytics-query/timestamp-by-azure-stream-analytics)gebruikt, controleert u of de gebeurtenissen tijds tempels hebben die groter zijn dan de [begin tijd](stream-analytics-out-of-order-and-late-events.md)van de taak.

4.  Vermijd veelvoorkomende Valk uilen, zoals:
    - Een [**where**](https://docs.microsoft.com/stream-analytics-query/where-azure-stream-analytics) -component in de query heeft alle gebeurtenissen gefilterd, waardoor er geen uitvoer kan worden gegenereerd.
    - Een [**cast**](https://docs.microsoft.com/stream-analytics-query/cast-azure-stream-analytics) -functie mislukt, waardoor de taak mislukt. Als u geen cast-fouten wilt voor komen, gebruikt u [**TRY_CAST**](https://docs.microsoft.com/stream-analytics-query/try-cast-azure-stream-analytics) in plaats daarvan.
    - Wanneer u venster functies gebruikt, wacht u tot de volledige venster duur een uitvoer van de query ziet.
    - De tijds tempel voor gebeurtenissen gaat vooraf aan de begin tijd van de taak en daarom worden gebeurtenissen verwijderd.

5.  Zorg ervoor dat het beleid voor het best Ellen van gebeurtenissen is geconfigureerd zoals verwacht. Ga naar **instellingen** en selecteer [**gebeurtenissen ordenen**](stream-analytics-out-of-order-and-late-events.md). Het beleid wordt *niet* toegepast wanneer u de knop **testen** gebruikt om de query te testen. Dit resultaat is een verschil tussen het testen van in-browser en het uitvoeren van de taak in de productie omgeving. 

6. Fouten opsporen met behulp van controle-en Diagnostische logboeken:
    - Gebruik [controle logboeken](../azure-resource-manager/resource-group-audit.md)en filter om fouten op te sporen en op te sporen.
    - Gebruik [Diagnostische logboeken voor taken](stream-analytics-job-diagnostic-logs.md) om fouten te identificeren en op te sporen.

## <a name="job-is-consuming-too-many-streaming-units"></a>Taak verbruikt te veel streaming-eenheden
Zorg ervoor dat u in Azure Stream Analytics kunt profiteren van parallel Lise ring. U kunt de [schaal aanpassen met query parallel Lise ring](stream-analytics-parallelization.md) van stream Analytics-taken door het configureren van invoer partities en het afstemmen van de definitie van de analyse query.

## <a name="debug-queries-progressively"></a>Query's geleidelijk opsporen

In realtime gegevens verwerking kan het handig zijn om te weten hoe de gegevens in het midden van de query eruitzien. Omdat invoer of stappen van een Azure Stream Analytics taak meerdere keren kunnen worden gelezen, kunt u extra SELECT INTO-instructies schrijven. Hierbij voert u tussenliggende gegevens uit naar opslag en kunt u de juistheid van de gegevens controleren, *net als bij* het opsporen van fouten in een programma.

De volgende voorbeeld query in een Azure Stream Analytics-taak heeft één stroom invoer, twee referentie gegevens invoer en een uitvoer naar Azure Table Storage. De query voegt gegevens van de Event Hub en twee referentie-blobs toe om de naam en categorie gegevens op te halen:

![Voor beeld Stream Analytics selectie in query](./media/stream-analytics-select-into/stream-analytics-select-into-query1.png)

Houd er rekening mee dat de taak wordt uitgevoerd, maar er worden geen gebeurtenissen geproduceerd in de uitvoer. Op de **bewakings** tegel, die hier wordt weer gegeven, kunt u zien dat de invoer gegevens produceert, maar u weet niet welke stap van de **samen voeging** heeft geleid tot alle gebeurtenissen die moeten worden verwijderd.

![De tegel Stream Analytics bewaking](./media/stream-analytics-select-into/stream-analytics-select-into-monitor.png)

In dit geval kunt u een aantal extra instructies voor SELECT INTO toevoegen aan de tussenliggende samenvoegings resultaten en de gegevens die van de invoer worden gelezen.

In dit voor beeld hebben we twee nieuwe tijdelijke uitvoer bewerkingen toegevoegd. Ze kunnen een wille keurige Sink zijn. Hier gebruiken we Azure Storage als voor beeld:

![Toevoegen van extra SELECT INTO-instructies aan Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-outputs.png)

U kunt de query dan als volgt herschrijven:

![SELECTIE in Stream Analytics query opnieuw schrijven](./media/stream-analytics-select-into/stream-analytics-select-into-query2.png)

Start de taak nu opnieuw en laat deze enkele minuten uitvoeren. Zoek vervolgens temp1 en temp2 met Visual Studio Cloud Explorer om de volgende tabellen te maken:

**temp1-tabel**
![stream Analytics query selecteren in temp1-tabel](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-1.png)

**temp2-tabel**
![stream Analytics query selecteren in temp2-tabel](./media/stream-analytics-select-into/stream-analytics-select-into-temp-table-2.png)

Zoals u ziet, zijn temp1 en temp2 beide gegevens en de kolom naam correct ingevuld in temp2. Maar omdat er nog steeds geen gegevens in de uitvoer zijn, is er een fout opgetreden:

![SELECTEREN in output1-tabel zonder gegevens Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-out-table-1.png)

Door de gegevens te bemonsteren, kunt u bijna zeker zijn dat het probleem met de tweede samen voeging is. U kunt de referentie gegevens van de BLOB downloaden en een kijkje nemen:

![IN ref-tabel selecteren Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-1.png)

Zoals u kunt zien, is de indeling van de GUID in deze referentie gegevens afwijkend van de indeling van de kolom [van] in temp2. Daarom zijn de gegevens niet zoals verwacht in output1.

U kunt de gegevens indeling herstellen, uploaden naar referentie-Blob en het opnieuw proberen:

![SELECTEREN in tijdelijke tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-ref-table-2.png)

Deze keer worden de gegevens in de uitvoer opgemaakt en ingevuld zoals verwacht.

![SELECTEREN in uiteindelijke tabel Stream Analytics query](./media/stream-analytics-select-into/stream-analytics-select-into-final-table.png)

## <a name="get-help"></a>Hulp krijgen

Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

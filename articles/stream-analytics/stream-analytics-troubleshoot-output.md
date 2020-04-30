---
title: Problemen met Azure Stream Analytics uitvoer oplossen
description: In dit artikel worden technieken beschreven voor het oplossen van problemen met uw uitvoer verbindingen in Azure Stream Analytics taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 03/31/2020
ms.custom: seodec18
ms.openlocfilehash: bee40bc30a0ffbdacf8cc7bf88d1512c4fc43fa4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82147950"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Problemen met Azure Stream Analytics uitvoer oplossen

In dit artikel worden veelvoorkomende problemen met Azure Stream Analytics uitvoer verbindingen en het oplossen van uitvoer problemen beschreven. Voor een groot aantal probleemoplossings stappen zijn resource en andere diagnostische logboeken ingeschakeld voor uw Stream Analytics-taak. Als u geen resource Logboeken hebt ingeschakeld, raadpleegt u [problemen oplossen Azure stream Analytics met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md).

## <a name="the-job-doesnt-produce-output"></a>De taak produceert geen uitvoer

1. Controleer de verbinding met uitvoer met behulp van de knop **verbinding testen** voor elke uitvoer.
1. Bekijk de [metrische gegevens voor bewaking](stream-analytics-monitoring.md) op het tabblad **monitor** . Omdat de waarden worden geaggregeerd, worden de metrische gegevens een paar minuten uitgesteld.

   * Als de waarde van de **invoer gebeurtenissen** groter is dan nul, kan de taak de invoer gegevens lezen. Als de waarde van de **invoer gebeurtenissen** niet groter is dan nul, is er een probleem met de invoer van de taak. Zie [problemen met invoer verbindingen oplossen](stream-analytics-troubleshoot-input.md) voor meer informatie.
   * Als de waarde voor **gegevens conversie fouten** groter is dan nul en klimmen, raadpleegt u [Azure stream Analytics gegevens fouten](data-errors.md) voor gedetailleerde informatie over gegevens conversie fouten.
   * Als de waarde voor **runtime fouten** groter is dan nul, worden de gegevens door uw taak ontvangen, maar worden er fouten gegenereerd tijdens het verwerken van de query. Als u de fouten wilt vinden, gaat u naar de [controle logboeken](../azure-resource-manager/management/view-activity-logs.md)en filtert u op de status **mislukt** .
   * Als de waarde van de **invoer gebeurtenissen** groter is dan nul en de waarde voor **uitvoer gebeurtenissen** gelijk is aan nul, is een van de volgende-instructies waar:
      * De verwerking van de query resulteerde in uitvoer gebeurtenissen van nul.
      * Gebeurtenissen of velden kunnen een onjuiste indeling hebben, wat resulteert in een uitvoer van nul na de verwerking van de query.
      * De taak kan geen gegevens naar de uitvoer Sink pushen om connectiviteits-of verificatie redenen.

   In operations-logboek berichten worden aanvullende details uitgelegd, met inbegrip van wat er gebeurt, behalve in gevallen waarin de query logica alle gebeurtenissen filtert. Als de verwerking van meerdere gebeurtenissen fouten genereert, worden de fouten elke 10 minuten geaggregeerd.

## <a name="the-first-output-is-delayed"></a>De eerste uitvoer vertraging

Wanneer een Stream Analytics taak wordt gestart, worden de invoer gebeurtenissen gelezen. Maar er kan in bepaalde omstandigheden een vertraging optreden in de uitvoer.

Grote tijd waarden in tijdelijke query-elementen kunnen bijdragen aan de uitvoer vertraging. Om de juiste uitvoer over grote tijd Vensters te produceren, leest de streaming-taak gegevens van de laatste tijd mogelijk om het tijd venster op te vullen. De gegevens kunnen Maxi maal zeven dagen geleden zijn. Er wordt geen uitvoer gemaakt totdat de uitstaande invoer gebeurtenissen worden gelezen. Dit probleem kan zich voordoen wanneer het systeem de streaming-taken bijwerkt. Wanneer een upgrade plaatsvindt, wordt de taak opnieuw gestart. Dergelijke upgrades worden meestal elke paar maanden uitgevoerd.

Gebruik voorzichtig bij het ontwerpen van uw Stream Analytics-query. Als u een groot tijd venster gebruikt voor tijdelijke elementen in de query syntaxis van de taak, kan dit leiden tot een vertraging in de eerste uitvoer wanneer de taak wordt gestart of opnieuw wordt gestart. Meer dan een aantal uur, Maxi maal zeven dagen, wordt beschouwd als een groot tijd venster.

Een beperking voor dit type eerste uitvoer vertraging is het gebruik van query parallel Lise ring technieken, zoals het partitioneren van de gegevens. U kunt ook meer streaming-eenheden toevoegen om de door voer te verbeteren totdat de taak is opgevangen.  Zie [overwegingen bij het maken van stream Analytics Jobs](stream-analytics-concepts-checkpoint-replay.md)voor meer informatie.

Deze factoren zijn van invloed op de tijd lijn van de eerste uitvoer:

* Het gebruik van gewindoweerde aggregaties, zoals een GROUP BY-component van tumblingvenstertriggers, verspringen en verschuivings Vensters:

  * Voor tumblingvenstertriggers-of verspringen-venster aggregaties worden de resultaten gegenereerd aan het einde van de periode van het venster.
  * Voor een sliding window genereren de resultaten wanneer een gebeurtenis in de sliding window wordt ingevoerd of afgesloten.
  * Als u van plan bent een groot venster formaat te gebruiken, zoals meer dan één uur, kunt u het beste een verspringen of sliding window kiezen. Met deze venster typen kunt u de uitvoer regel matig bekijken.

* Het gebruik van tijdelijke samen voegingen, zoals samen voegen met DATEDIFF:
  * Komt overeen met het genereren zodra beide zijden van de overeenkomende gebeurtenissen arriveren.
  * Gegevens die geen overeenkomst hebben, zoals LEFT OUTER JOIN, worden aan het einde van het venster DATEDIFF gegenereerd voor elke gebeurtenis aan de linkerkant.

* Het gebruik van tijdelijke analyse functies, zoals ISFIRST, LAST en LAG met de limiet duur:
  * Voor analyse functies wordt de uitvoer voor elke gebeurtenis gegenereerd. Er is geen vertraging.

## <a name="the-output-falls-behind"></a>De uitvoer ligt achter

Tijdens de normale werking van een taak kan de uitvoer langer en langer duren. Als de uitvoer zich achter elkaar bevindt, kunt u de hoofd oorzaken bepalen door de volgende factoren te controleren:

* Hiermee wordt aangegeven of de downstream-sink wordt beperkt
* Hiermee wordt aangegeven of de upstream-bron wordt beperkt
* Of de verwerkings logica in de query reken intensief is

Als u de uitvoer details wilt zien, selecteert u de streaming-taak in de Azure Portal en selecteert u vervolgens **taak diagram**. Voor elke invoer is er een gebeurtenis metriek voor de achterstand per partitie. Als de metriek toeneemt, is het een indicatie dat de systeem bronnen beperkt zijn. De verhoging wordt mogelijk veroorzaakt door een beperking van de uitvoer-sink of een hoog CPU-gebruik. Zie voor meer informatie [fout opsporing op basis van gegevens met behulp van het taak diagram](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Waarschuwing voor sleutel schending bij Azure SQL Database uitvoer

Wanneer u een Azure SQL database als uitvoer naar een Stream Analytics taak configureert, voegt het bulksgewijs records in de doel tabel in. Over het algemeen Azure Stream Analytics een gegarandeerde [minimale levering](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) naar de uitvoer sink. U kunt nog steeds [precies één keer]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) aan een SQL-uitvoer voldoen wanneer voor een SQL-tabel een unieke beperking is gedefinieerd.

Wanneer u unieke-sleutel beperkingen instelt voor de SQL-tabel, worden dubbele records door Azure Stream Analytics verwijderd. De gegevens worden in batches gesplitst en de batches worden recursief ingevoegd totdat er één dubbele record wordt gevonden. Het proces splitsen en invoegen negeert de duplicaten een voor een. Voor een streaming-taak met veel dubbele rijen is het proces inefficiënt en tijdrovend. Als er in uw activiteiten logboek voor het vorige uur meerdere sleutel schendingen worden weer gegeven, is het waarschijnlijk dat uw SQL-uitvoer de volledige taak vertraagt.

Om dit probleem op te lossen, [configureert u de index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) die de sleutel schending veroorzaakt door de optie IGNORE_DUP_KEY in te scha kelen. Met deze optie kan SQL dubbele waarden negeren tijdens bulk toevoegingen. Azure SQL Database wordt gewoon een waarschuwing weer gegeven in plaats van een fout. Als gevolg hiervan Azure Stream Analytics geen fouten van de primaire-sleutel schending meer oplevert.

Houd rekening met de volgende waarnemingen bij het configureren van IGNORE_DUP_KEY voor verschillende typen indexen:

* U kunt geen IGNORE_DUP_KEY instellen voor een primaire sleutel of een unieke beperking die gebruikmaakt van ALTER INDEX. U moet de index verwijderen en opnieuw maken.  
* U kunt IGNORE_DUP_KEY instellen met behulp van ALTER INDEX voor een unieke index. Dit exemplaar wijkt af van een primaire sleutel/unieke beperking en wordt gemaakt met behulp van een CREATE INDEX of INDEX-definitie.  
* De optie IGNORE_DUP_KEY is niet van toepassing op column Store-indexen omdat u geen uniekheid kunt afdwingen.  

## <a name="column-names-are-lowercase-in-azure-stream-analytics-10"></a>Kolom namen zijn kleine letters in Azure Stream Analytics (1,0)

Wanneer u het oorspronkelijke compatibiliteits niveau (1,0) gebruikt, worden de namen van de kolommen Azure Stream Analytics gewijzigd in kleine letters. Dit gedrag is in latere compatibiliteits niveaus verholpen. Als u het probleem wilt behouden, gaat u naar compatibiliteits niveau 1,1 of hoger. Zie [compatibiliteits niveau voor stream Analytics taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)voor meer informatie.

## <a name="get-help"></a>Help opvragen

Probeer het [Azure stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslag informatie voor de query taal Azure Stream Analytics](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [Naslag informatie over Azure Stream Analytics beheer REST API](https://msdn.microsoft.com/library/azure/dn835031.aspx)

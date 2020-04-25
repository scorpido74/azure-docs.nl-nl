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
ms.openlocfilehash: 5652df0cf142af2ff96590368892530abcb3d667
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/24/2020
ms.locfileid: "82133219"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Problemen met Azure Stream Analytics uitvoer oplossen

In dit artikel worden veelvoorkomende problemen beschreven met Azure Stream Analytics uitvoer verbindingen, het oplossen van uitvoer problemen en het oplossen van de problemen. Voor een groot aantal probleemoplossings stappen moeten resource en andere diagnostische logboeken zijn ingeschakeld voor uw Stream Analytics-taak. Als u geen resource Logboeken hebt ingeschakeld, raadpleegt u [problemen oplossen Azure stream Analytics met behulp van resource logboeken](stream-analytics-job-diagnostic-logs.md).

## <a name="output-not-produced-by-job"></a>Uitvoer niet geproduceerd door taak

1.  Controleer de verbinding met uitvoer met behulp van de knop **verbinding testen** voor elke uitvoer.

2.  Bekijk de [**metrische gegevens voor bewaking**](stream-analytics-monitoring.md) op het tabblad **monitor** . Omdat de waarden worden geaggregeerd, worden de metrische gegevens een paar minuten uitgesteld.
   * Als de invoer gebeurtenissen groter zijn dan 0, kan de taak invoer gegevens lezen. Als de invoer gebeurtenissen niet groter zijn dan 0, is er een probleem met de invoer van de taak. Zie [problemen met invoer verbindingen oplossen](stream-analytics-troubleshoot-input.md) voor meer informatie over het oplossen van problemen met de invoer verbinding.
   * Zie [Azure stream Analytics gegevens fouten](data-errors.md) voor gedetailleerde informatie over gegevens conversie fouten als gegevens conversie fouten groter zijn dan 0 en een klimmen.
   * Als runtime fouten groter zijn dan 0, kan uw taak gegevens ontvangen, maar worden er fouten gegenereerd tijdens het verwerken van de query. Als u de fouten wilt vinden, gaat u naar de [controle logboeken](../azure-resource-manager/management/view-activity-logs.md) en filtert u op *mislukte* status.
   * Als InputEvents groter is dan 0 en OutputEvents gelijk is aan 0, is een van de volgende ' True ':
      * Het verwerken van de query heeft geen uitvoergebeurtenissen opgeleverd.
      * Gebeurtenissen of velden kunnen een onjuiste indeling hebben, wat resulteert in nul uitvoer na het verwerken van de query.
      * De taak kan geen gegevens naar de uitvoer Sink pushen om connectiviteits-of verificatie redenen.

   In alle eerder genoemde fout gevallen verklaren de berichten in het operations-logboek aanvullende informatie (inclusief wat er gebeurt), behalve in gevallen waarin de query logica alle gebeurtenissen heeft gefilterd. Als de verwerking van meerdere gebeurtenissen fouten genereert, worden de fouten elke 10 minuten geaggregeerd.

## <a name="job-output-is-delayed"></a>Taakuitvoer is vertraagd

### <a name="first-output-is-delayed"></a>Eerste uitvoer vertraging

Wanneer een Stream Analytics-taak wordt gestart, worden de invoergebeurtenissen gelezen, maar kan er in bepaalde omstandigheden een vertraging zijn in de uitvoer die wordt geproduceerd.

Grote tijd waarden in tijdelijke query-elementen kunnen bijdragen aan de uitvoer vertraging. Voor een correcte uitvoer over de grote tijd Vensters, wordt de streaming-taak gestart door gegevens te lezen van de laatste mogelijke tijd (Maxi maal zeven dagen geleden) om het tijd venster in te vullen. Gedurende die tijd wordt geen uitvoer geproduceerd totdat de uitstaande Lees bewerkingen van de openstaande invoer gebeurtenissen zijn voltooid. Dit probleem kan zich voordoen wanneer het systeem de streaming-taken bijwerkt, waardoor de taak opnieuw wordt gestart. Dergelijke upgrades worden meestal elke paar maanden uitgevoerd.

Gebruik daarom zorgvuldig bij het ontwerpen van uw Stream Analytics-query. Als u een groot tijd venster (meer dan zeven dagen) voor tijdelijke elementen in de query syntaxis van de taak gebruikt, kan dit leiden tot een vertraging bij de eerste uitvoer wanneer de taak wordt gestart of opnieuw wordt gestart.  

Een beperking voor dit type eerste uitvoer vertraging is het gebruik van query parallel Lise ring technieken (partitioneren van de gegevens) of voeg meer streaming-eenheden toe om de door voer te verbeteren totdat de taak is opgevangen.  Zie [overwegingen bij het maken van stream Analytics Jobs](stream-analytics-concepts-checkpoint-replay.md) voor meer informatie.

Deze factoren zijn van invloed op de tijd lijn van de eerste uitvoer die wordt gegenereerd:

1. Gebruik van gewindoweerde aggregaties (groeperen op basis van Tumblingvenstertriggers, verspringen en schuivende Vensters)
   - Voor tumblingvenstertriggers-of verspringen-venster aggregaties worden resultaten gegenereerd aan het einde van de periode van het venster.
   - Voor een sliding window worden de resultaten gegenereerd wanneer er een gebeurtenis in de sliding window wordt ingevoerd of afgesloten.
   - Als u van plan bent grote venster grootte (> 1 uur) te gebruiken, is het raadzaam om verspringen of sliding window te kiezen, zodat u de uitvoer vaker kunt zien.

2. Gebruik van tijdelijke samen voegingen (samen voegen met DATEDIFF)
   - Overeenkomsten worden gegenereerd zodra beide zijden van de overeenkomende gebeurtenissen arriveren.
   - Gegevens die geen overeenkomst (LEFT OUTER JOIN) hebben, worden aan het einde van het venster DATEDIFF gegenereerd ten opzichte van elke gebeurtenis aan de linkerkant.

3. Gebruik van tijdelijke analyse functies (ISFIRST, LAST en LAG met de limiet duur)
   - Voor analyse functies wordt de uitvoer voor elke gebeurtenis gegenereerd. er is geen vertraging.

### <a name="output-falls-behind"></a>Uitvoer valt achter

Als u tijdens de normale werking van de taak de uitvoer van de taak achter elkaar bevindt (langer en langer latentie), kunt u de belangrijkste oorzaken van het volgende onderzoeken:
- Hiermee wordt aangegeven of de downstream-sink wordt beperkt
- Hiermee wordt aangegeven of de upstream-bron wordt beperkt
- Of de verwerkings logica in de query reken intensief is

Als u deze details wilt zien, selecteert u in de Azure Portal de streaming-taak en selecteert u het **taak diagram**. Voor elke invoer is een gebeurtenis metriek per partitie achterstand. Als de gebeurtenis metriek van de achterstand blijft toenemen, is het een indicatie dat de systeem bronnen beperkt zijn. Dit is mogelijk het gevolg van een beperking van de uitvoer-sink of een hoge CPU. Zie voor meer informatie over het gebruik van het taak diagram [fout opsporing op basis van gegevens met behulp van het taak diagram](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Waarschuwing voor sleutel schending bij Azure SQL Database uitvoer

Wanneer u Azure SQL database als uitvoer naar een Stream Analytics-taak configureert, voegt het bulksgewijs records in de doel tabel in. Over het algemeen garandeert Azure stream Analytics [ten minste één keer per levering](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) aan de uitvoer sink, een kan nog steeds [precies één keer worden AFgeleverd]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) bij SQL-uitvoer wanneer voor SQL-tabel een unieke beperking is gedefinieerd.

Als er unieke sleutel beperkingen zijn ingesteld voor de SQL-tabel en er dubbele records worden ingevoegd in de SQL-tabel, wordt de dubbele record door Azure Stream Analytics verwijderd. De gegevens worden in batches gesplitst en de batches worden recursief ingevoegd totdat er één dubbele record wordt gevonden. Als de streaming-taak een groot aantal dubbele rijen heeft, moet dit Split-en-invoegen-proces de duplicaten een voor een negeren, wat minder efficiënt en tijdrovend is. Als er in het afgelopen uur meerdere waarschuwingen voor sleutel schendingen in uw activiteiten logboek worden weer gegeven, is het waarschijnlijk dat uw SQL-uitvoer de volledige taak vertraagt.

Om dit probleem op te lossen, moet u [de index configureren]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) die de sleutel schending veroorzaakt door de optie IGNORE_DUP_KEY in te scha kelen. Als u deze optie inschakelt, kunnen dubbele waarden door SQL worden genegeerd tijdens bulksgewijs invoegen SQL Azure en wordt er een waarschuwings bericht weer gegeven in plaats van een fout. Azure Stream Analytics produceert geen fouten van de primaire-sleutel schending meer.

Houd rekening met de volgende waarnemingen bij het configureren van IGNORE_DUP_KEY voor verschillende typen indexen:

* U kunt geen IGNORE_DUP_KEY instellen voor een primaire sleutel of een unieke beperking die gebruikmaakt van ALTER INDEX. u moet de index verwijderen en opnieuw maken.  
* U kunt de optie IGNORE_DUP_KEY instellen met behulp van ALTER INDEX voor een unieke index, die afwijkt van de PRIMARY KEY/UNIQUE-beperking en is gemaakt met CREATE INDEX of INDEX definition.  

* IGNORE_DUP_KEY is niet van toepassing op column Store-indexen omdat u geen uniek karakter van dergelijke indexen kunt afdwingen.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Kolom namen worden in kleine letters door Azure Stream Analytics
Wanneer u het oorspronkelijke compatibiliteits niveau (1,0) gebruikt, wordt Azure Stream Analytics gebruikt om kolom namen te wijzigen in kleine letters. Dit gedrag is in latere compatibiliteits niveaus verholpen. Om het probleem op te slaan, raden we klanten aan om over te stappen op compatibiliteits niveau 1,1 en hoger. U kunt meer informatie vinden over het [compatibiliteits niveau voor Azure stream Analytics taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).

## <a name="get-help"></a>Help opvragen

Probeer het [Azure stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

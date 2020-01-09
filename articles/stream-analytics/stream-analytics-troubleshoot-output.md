---
title: Problemen met Azure Stream Analytics uitvoer oplossen
description: In dit artikel worden technieken beschreven voor het oplossen van problemen met uw uitvoer verbindingen in Azure Stream Analytics taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 65d01c5c4dd852cb424c75f170ce52156f1633cc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75354108"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Problemen met Azure Stream Analytics uitvoer oplossen

Op deze pagina worden veelvoorkomende problemen met uitvoer verbindingen beschreven en wordt uitgelegd hoe u deze problemen oplost en verhelpt.

## <a name="output-not-produced-by-job"></a>Uitvoer niet geproduceerd door taak 
1.  Controleer de verbinding met uitvoer met behulp van de knop **verbinding testen** voor elke uitvoer.

2.  Bekijk de [**metrische gegevens voor bewaking**](stream-analytics-monitoring.md) op het tabblad **monitor** . Omdat de waarden worden geaggregeerd, worden de metrische gegevens een paar minuten uitgesteld.
    - Als invoer gebeurtenissen > 0, kan de taak invoer gegevens lezen. Als de invoer gebeurtenissen niet > 0, geldt het volgende:
      - Als u wilt zien of de gegevens bron geldige gegevens bevat, controleert u deze met behulp van [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Deze controle is van toepassing als Event hub als invoer wordt gebruikt voor de taak.
      - Controleer of de indeling van de gegevensserialisatie en de gegevens codering zoals verwacht worden weer gegeven.
      - Als voor de taak een event hub wordt gebruikt, controleert u of de hoofd tekst van het bericht *Null*is.
      
    - Als gegevens conversie fouten > 0 en klimmen, kan het volgende waar zijn:
      - De uitvoer gebeurtenis komt niet overeen met het schema van de doel-sink. 
      - Het gebeurtenis schema komt mogelijk niet overeen met het gedefinieerde of verwachte schema van de gebeurtenissen in de query.
      - De gegevens typen van sommige velden in de gebeurtenis komen mogelijk niet overeen met verwachtingen.
      
    - Als runtime-fouten > 0, betekent dit dat de taak de gegevens kan ontvangen, maar fouten genereert tijdens het verwerken van de query.
      - Als u de fouten wilt vinden, gaat u naar de [controle logboeken](../azure-resource-manager/resource-group-audit.md) en filtert u op *mislukte* status.
      
    - Als InputEvents > 0 en OutputEvents = 0, betekent dit dat een van de volgende voor waarden waar is:
      - Het verwerken van de query heeft geen uitvoergebeurtenissen opgeleverd.
      - Gebeurtenissen of velden kunnen een onjuiste indeling hebben, wat resulteert in nul uitvoer na het verwerken van query's.
      - De taak kan geen gegevens naar de uitvoer Sink pushen om connectiviteits-of verificatie redenen.
      
    - In alle eerder genoemde fout gevallen verklaren de berichten in het operations-logboek aanvullende informatie (inclusief wat er gebeurt), behalve in gevallen waarin de query logica alle gebeurtenissen heeft gefilterd. Als er fouten worden gegenereerd door het verwerken van meerdere gebeurtenissen, worden de eerste drie fout berichten van hetzelfde type in Stream Analytics geregistreerd binnen tien minuten aan de bewerkings Logboeken. Vervolgens worden er meer identieke fouten onderdrukt met een bericht met de melding dat fouten te snel optreden, deze worden onderdrukt.
    
## <a name="job-output-is-delayed"></a>Taak uitvoer is vertraagd

### <a name="first-output-is-delayed"></a>Eerste uitvoer is vertraagd
Wanneer een Stream Analytics-taak wordt gestart, worden de invoergebeurtenissen gelezen, maar kan er in bepaalde omstandigheden een vertraging zijn in de uitvoer die wordt geproduceerd.

Grote tijdwaarden in tijdelijke query-elementen kunnen bijdragen aan de uitvoer vertraging. Juiste uitvoer produceren via de grote tijdvensters, de streaming-taak wordt gestart met het lezen van gegevens van de meest recente tijdstip mogelijke (maximaal zeven dagen geleden) om op te vullen van het tijdvenster. Gedurende die tijd, wordt geen uitvoer geproduceerd totdat de achterstallige lezen van de openstaande invoer gebeurtenissen voltooid is. Dit probleem kan het oppervlak wanneer het systeem de streaming taken, dus de taak opnieuw starten wordt bijgewerkt. Dergelijke upgrades optreden in het algemeen eenmaal elke aantal maanden. 

Dus wees voorzichtig bij het ontwerpen van uw Stream Analytics-query. Als u een groot tijdsvenster (meer dan enkele uren, maximaal zeven dagen) voor de tijdelijke-elementen in de query-syntaxis van de taak, kan dit leiden tot een vertraging in de eerste uitvoer wanneer de taak wordt gestart of opnieuw opgestart.  

Een beperking voor dit type van de eerste uitvoer vertraging is het gebruik van query-parallellisatie technieken (partitionering van gegevens) of Voeg meer Streaming-eenheden voor de verbetering van de doorvoer totdat de taak de resultaten.  Zie voor meer informatie, [overwegingen bij het maken van Stream Analytics-taken](stream-analytics-concepts-checkpoint-replay.md)

Deze factoren van invloed zijn op de actualiteit van de eerste uitvoer die wordt gegenereerd:

1. Gebruik van statistische functies in vensters, (groep door van Tumbling, Hopping plaatsvindt, en schuiven windows)
   - Voor tumbling of hopping venster statistische functies, worden de resultaten aan het einde van het venster tijdsbestek gegenereerd. 
   - Voor een sliding window, worden de resultaten worden gegenereerd wanneer een gebeurtenis binnengaat of de sliding window van verlaat. 
   - Als u van plan bent te grote venstergrootte (> 1 uur) gebruikt, is het beste venster verspringen of sliding kiezen zodat u kunt de uitvoer vaker zien.

2. Gebruik van tijdelijke joins (lid worden met DATEDIFF)
   - Overeenkomsten worden gegenereerd als wanneer beide zijden van de overeenkomende gebeurtenissen aankomen.
   - Gegevens die beschikt niet over een overeenkomst (LEFT OUTER JOIN) is gegenereerd aan het einde van het venster DATEDIFF met betrekking tot elke gebeurtenis aan de linkerkant.

3. Gebruik van tijdelijke analytische functies (ISFIRST, LAST en LAG met LIMIT DURATION)
   - De uitvoer voor elke gebeurtenis wordt gegenereerd, is er geen vertraging voor analytische functies.

### <a name="output-falls-behind"></a>Uitvoer valt achter
Tijdens normale werking van de taak als u van de taak uitvoer valt achter (langer latentie), u kunt met deze functie de hoofdoorzaken aan de hand van deze factoren:
- Of de downstream-sink is beperkt
- Of de upstream-bron is beperkt
- Of de verwerkingslogica in de query is rekenintensieve

Als u wilt zien van de gegevens, in de Azure-portal, selecteert u de streaming-taak en selecteer de **taakdiagram**. Voor elke invoer, is er een per partitie achterstand gebeurtenis metrisch gegeven. Als de achterstand gebeurtenis metrische gegevens stijgen blijft, is dit een indicatie dat de systeemresources die zijn beperkt. Mogelijk die het gevolg is van uitvoer-sink beperking of intensief CPU. Zie voor meer informatie over het gebruik van het taakdiagram [gegevensgestuurde foutopsporing met behulp van het taakdiagram](stream-analytics-job-diagram-with-metrics.md).

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Waarschuwing voor sleutel schending bij Azure SQL Database uitvoer

Als u Azure SQL-database als uitvoer naar een Stream Analytics-taak configureert, voegt het bulksgewijs records in de doeltabel. In het algemeen, Azure stream analytics garandeert [ten minste één keer levering](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) naar het uitvoereindpunt worden verplaatst, een kunt nog steeds [precies bereiken-bezorging]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) om SQL uitvoer wanneer SQL-tabel heeft een unique-beperking gedefinieerd. 

Wanneer unique key-beperkingen zijn ingesteld op de SQL-tabel en er zijn dubbele records worden ingevoegd in de SQL-tabel, geeft Azure Stream Analytics de dubbele record wordt verwijderd. Deze splitst de gegevens in batches en recursief de batches voegen tot één dubbele record wordt gevonden. Als de streaming-taak heeft een groot aantal dubbele rijen, deze splitsen en in te voegen proces is de duplicaten één voor één, dit minder efficiënt en tijdrovend is negeren. Als u meerdere waarschuwingsberichten voor schending van de sleutel in het activiteitenlogboek is opgenomen in het afgelopen uur ziet, is het waarschijnlijk dat de SQL-uitvoer de hele taak vertragen. 

U lost dit probleem, moet u [configureren van de index]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) die de schending van de sleutel wordt veroorzaakt doordat de optie IGNORE_DUP_KEY. Als u deze optie inschakelt, kunnen dubbele waarden moeten worden genegeerd door SQL tijdens bulksgewijs invoegen en SQL Azure levert gewoon een waarschuwingsbericht wordt weergegeven in plaats van een fout. Azure Stream Analytics produceren schending van de primaire sleutel fouten niet meer.

Houd rekening met de volgende opmerkingen bij het configureren van IGNORE_DUP_KEY voor verschillende soorten indexen:

* U kunt IGNORE_DUP_KEY niet instellen op een primaire sleutel of een unique-beperking die gebruikmaakt van ALTER INDEX, moet u verwijderen en opnieuw maken van de index.  
* U kunt de optie IGNORE_DUP_KEY voor een unieke index, die verschilt van de primaire sleutel/UNIQUE-beperking en gemaakt met behulp van de definitie van CREATE INDEX of INDEX met behulp van ALTER INDEX instellen.  
* Ignore_dup_key niet van toepassing op de columnstore-indexen omdat uniekheid op dergelijke indexen kunnen niet worden afgedwongen.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Kolom namen worden in kleine letters door Azure Stream Analytics
Wanneer u het oorspronkelijke compatibiliteits niveau (1,0) gebruikt, wordt Azure Stream Analytics gebruikt om kolom namen te wijzigen in kleine letters. Dit gedrag is in latere compatibiliteits niveaus verholpen. Om het probleem op te slaan, raden we klanten aan om over te stappen op compatibiliteits niveau 1,1 en hoger. U kunt meer informatie vinden over het [compatibiliteits niveau voor Azure stream Analytics taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level).


## <a name="get-help"></a>Hulp krijgen

Voor verdere ondersteuning kunt u proberen onze [Azure Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

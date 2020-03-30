---
title: Problemen met Azure Stream Analytics-uitvoer oplossen
description: In dit artikel worden technieken beschreven om problemen op te lossen in uw uitvoerverbindingen in Azure Stream Analytics-taken.
author: sidram
ms.author: sidram
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: d40157523a074547885a14a3d92379f8e8b6f351
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254285"
---
# <a name="troubleshoot-azure-stream-analytics-outputs"></a>Problemen met Azure Stream Analytics-uitvoer oplossen

Op deze pagina worden veelvoorkomende problemen beschreven met uitvoerverbindingen en hoe u deze oplossen en aanpakken.

## <a name="output-not-produced-by-job"></a>Uitvoer die niet door taak wordt geproduceerd
1.  Controleer de verbinding met uitvoer met behulp van de knop **Verbinding testen** voor elke uitvoer.

2.  Kijk naar [**Monitoring metrics**](stream-analytics-monitoring.md) op het tabblad **Monitor.** Omdat de waarden worden samengevoegd, worden de statistieken met enkele minuten vertraagd.
    - Als invoergebeurtenissen > 0, kan de taak invoergegevens lezen. Als invoergebeurtenissen niet > 0 zijn, gaat het als nog over op:
      - Als u wilt zien of de gegevensbron geldige gegevens heeft, controleert u deze met [Service Bus Explorer](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Explorer-f2abca5a). Deze controle is van toepassing als de taak eventhub als invoer gebruikt.
      - Controleer of de indeling van de gegevensserialisatie en gegevenscodering zijn zoals verwacht.
      - Als de taak een gebeurtenishub gebruikt, controleert u of de hoofdtekst van het bericht *Null*is.

    - Als gegevensconversiefouten > 0 en klimmen, kan het volgende het volgende zijn:
      - De uitvoergebeurtenis voldoet niet aan het schema van de doelgootsteen.
      - Het gebeurtenisschema komt mogelijk niet overeen met het gedefinieerde of verwachte schema van de gebeurtenissen in de query.
      - De gegevenstypen van sommige velden in de gebeurtenis komen mogelijk niet overeen met de verwachtingen.

    - Als runtime-fouten > 0, betekent dit dat de taak de gegevens kan ontvangen, maar fouten genereert tijdens het verwerken van de query.
      - Als u de fouten wilt vinden, gaat u naar [De controlelogboeken](../azure-resource-manager/management/view-activity-logs.md) en filtert u de status *Mislukt.*

    - Als Invoergebeurtenissen > 0 en OutputEvents = 0, betekent dit dat een van de volgende gebeurtenissen waar is:
      - Het verwerken van de query heeft geen uitvoergebeurtenissen opgeleverd.
      - Gebeurtenissen of velden kunnen misvormd zijn, wat resulteert in nul uitvoer na queryverwerking.
      - De taak kon gegevens niet naar de uitvoersink pushen om redenen van connectiviteit of verificatie.

    - In alle eerder genoemde foutgevallen worden in operations log-berichten aanvullende details uitgelegd (inclusief wat er gebeurt), behalve in gevallen waarin de querylogica alle gebeurtenissen heeft gefilterd. Als de verwerking van meerdere gebeurtenissen fouten genereert, registreert Stream Analytics de eerste drie foutberichten van hetzelfde type binnen 10 minuten naar logboeken voor bewerkingen. Het onderdrukt dan extra identieke fouten met een bericht dat luidt: "Fouten gebeuren te snel, deze worden onderdrukt."

## <a name="job-output-is-delayed"></a>Taakuitvoer is vertraagd

### <a name="first-output-is-delayed"></a>Eerste output is vertraagd
Wanneer een Stream Analytics-taak wordt gestart, worden de invoergebeurtenissen gelezen, maar kan er in bepaalde omstandigheden een vertraging zijn in de uitvoer die wordt geproduceerd.

Grote tijdwaarden in tijdelijke query-elementen kunnen bijdragen aan de uitvoervertraging. Om de juiste uitvoer te produceren over de grote tijdvensters, start de streamingtaak met het lezen van gegevens van de laatst mogelijke tijd (tot zeven dagen geleden) om het tijdvenster te vullen. Gedurende die tijd wordt er geen output geproduceerd totdat de inhaalslag van de uitstekende invoergebeurtenissen is voltooid. Dit probleem kan opduiken wanneer het systeem de streamingtaken opwaardeert, waardoor de taak opnieuw wordt opgestart. Dergelijke upgrades komen over het algemeen eens in de paar maanden.

Gebruik daarom discretie bij het ontwerpen van uw Stream Analytics-query. Als u een groot tijdvenster (meer dan enkele uren, maximaal zeven dagen) gebruikt voor tijdelijke elementen in de querysyntaxis van de taak, kan dit leiden tot een vertraging van de eerste uitvoer wanneer de taak wordt gestart of opnieuw wordt gestart.  

Een beperking voor dit soort eerste uitvoervertraging is het gebruik van queryparallelisatietechnieken (het partitioneren van de gegevens) of het toevoegen van meer streamingeenheden om de doorvoer te verbeteren totdat de taak inhaalt.  Zie [Overwegingen bij het maken van Stream Analytics-taken](stream-analytics-concepts-checkpoint-replay.md) voor meer informatie

Deze factoren zijn van invloed op de tijdigheid van de eerste uitvoer die wordt gegenereerd:

1. Gebruik van gevensterde aggregaten (GROEP BY van Tumbling, Hopping en Sliding windows)
   - Voor tumbling- of hopping-vensteraggregaten worden resultaten gegenereerd aan het einde van het venstertijdsbestek.
   - Voor een schuifvenster worden de resultaten gegenereerd wanneer een gebeurtenis het schuifvenster binnenkomt of verlaat.
   - Als u van plan bent om grote venstergrootte (> 1 uur) te gebruiken, u het beste kiezen voor hopping of schuifvenster, zodat u de uitvoer vaker zien.

2. Gebruik van tijdelijke joins (JOIN met DATEDIFF)
   - Wedstrijden worden gegenereerd zodra beide zijden van de overeenkomende gebeurtenissen aankomen.
   - Gegevens die geen overeenkomst hebben (LEFT OUTER JOIN) worden gegenereerd aan het einde van het DATUMDIFF-venster met betrekking tot elke gebeurtenis aan de linkerkant.

3. Gebruik van tijdelijke analytische functies (ISFIRST, LAST en LAG met LIMIETDUUR)
   - Voor analytische functies wordt de uitvoer gegenereerd voor elke gebeurtenis, er is geen vertraging.

### <a name="output-falls-behind"></a>Output blijft achter
Tijdens de normale werking van de taak, als u merkt dat de uitvoer van de taak achterblijft (langere en langere latentie), u de onderliggende oorzaken lokaliseren door deze factoren te onderzoeken:
- Of de stroomafwaartse gootsteen wordt beperkt
- Of de upstream bron wordt beperkt
- Of de verwerkingslogica in de query rekenintensief is

Als u deze gegevens wilt zien, selecteert u in de Azure-portal de streamingtaak en selecteert u het **taakdiagram**. Voor elke invoer is er een gebeurtenisstatistiek per partitieachterstand. Als de statistiek van de gebeurtenis achterstand blijft toenemen, is dit een indicator dat de systeembronnen beperkt zijn. Potentieel dat te wijten is aan van output sink throttling, of hoge CPU. Zie Gegevensgestuurde foutopsporing op [gegevens met behulp van het taakdiagram](stream-analytics-job-diagram-with-metrics.md)voor meer informatie over het gebruik van het taakdiagram.

## <a name="key-violation-warning-with-azure-sql-database-output"></a>Waarschuwing voor schending van de sleutel met Azure SQL-database-uitvoer

Wanneer u Azure SQL-database configureert als uitvoer naar een Stream Analytics-taak, worden records in de doeltabel in de doeltabel invoegen. In het algemeen garandeert Azure stream analytics [ten minste eenmaal levering](https://docs.microsoft.com/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) aan de uitvoersink, men kan nog steeds precies één keer worden geleverd aan [SQL-uitvoer]( https://blogs.msdn.microsoft.com/streamanalytics/2017/01/13/how-to-achieve-exactly-once-delivery-for-sql-output/) wanneer sql-tabel een unieke beperking heeft gedefinieerd.

Zodra unieke sleutelbeperkingen zijn ingesteld in de SQL-tabel en er dubbele records worden ingevoegd in sql-tabel, verwijdert Azure Stream Analytics de dubbele record. Het splitst de gegevens in batches en recursief het invoegen van de partijen totdat een enkele dubbele record wordt gevonden. Als de streamingtaak een aanzienlijk aantal dubbele rijen heeft, moet dit gesplitste en invoegproces de duplicaten één voor één negeren, wat minder efficiënt en tijdrovend is. Als u binnen het afgelopen uur meerdere waarschuwingsberichten voor belangrijke schendingen in uw activiteitslogboek ziet, is het waarschijnlijk dat uw SQL-uitvoer de hele taak vertraagt.

Als u dit probleem wilt oplossen, moet u [de index configureren]( https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql) die de sleutelschending veroorzaakt door de optie IGNORE_DUP_KEY in te schakelen. Als u deze optie inschakelt, kunnen dubbele waarden worden genegeerd door SQL tijdens bulkinserts en SQL Azure produceert eenvoudig een waarschuwingsbericht in plaats van een fout. Azure Stream Analytics produceert geen primaire fouten met belangrijke schendingen meer.

Let op de volgende waarnemingen bij het configureren van IGNORE_DUP_KEY voor verschillende soorten indexen:

* U IGNORE_DUP_KEY niet instellen op een primaire sleutel of een unieke beperking die ALTER INDEX gebruikt, u moet de index laten vallen en opnieuw maken.  
* U de optie IGNORE_DUP_KEY instellen met ALTER INDEX voor een unieke index, die verschilt van de primaire sleutel/unieke beperking en die is gemaakt met de definitie VAN CREATE INDEX of INDEX.  
* IGNORE_DUP_KEY is niet van toepassing op indexen voor kolomopslag omdat u uniciteit op dergelijke indexen niet afdwingen.  

## <a name="column-names-are-lower-cased-by-azure-stream-analytics"></a>Kolomnamen worden in kleine letters gedeslopen door Azure Stream Analytics
Wanneer u het oorspronkelijke compatibiliteitsniveau (1.0) gebruikt, wijzigde Azure Stream Analytics kolomnamen in kleine letters. Dit gedrag is opgelost in latere compatibiliteitsniveaus. Om de zaak te behouden, raden we klanten aan om over te stappen naar het compatibiliteitsniveau 1.1 en hoger. Meer informatie over [compatibiliteitsniveau voor Azure Stream Analytics-taken](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-compatibility-level)vindt u meer informatie over compatibiliteitsniveau.


## <a name="get-help"></a>Help opvragen

Probeer ons Azure [Stream Analytics-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureStreamAnalytics)voor meer hulp.

## <a name="next-steps"></a>Volgende stappen

* [Inleiding tot Azure Stream Analytics](stream-analytics-introduction.md)
* [Aan de slag met Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Azure Stream Analytics-taken schalen](stream-analytics-scale-jobs.md)
* [Naslaggids voor Azure Stream Analytics Query](https://docs.microsoft.com/stream-analytics-query/stream-analytics-query-language-reference)
* [REST API-naslaggids voor Azure Stream Analytics Management](https://msdn.microsoft.com/library/azure/dn835031.aspx)

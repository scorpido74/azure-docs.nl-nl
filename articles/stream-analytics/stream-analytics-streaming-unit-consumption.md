---
title: Streaming-eenheden in Azure Stream Analytics
description: In dit artikel worden de instelling Streaming Units en andere factoren beschreven die van invloed zijn op de prestaties in Azure Stream Analytics.
author: JSeb225
ms.author: jeanb
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 397e455c8b6a1097e2a32473036e1acd2bbdf2eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267350"
---
# <a name="understand-and-adjust-streaming-units"></a>Streaming-eenheden begrijpen en aanpassen

Streaming Units (SU's) vertegenwoordigt de computerbronnen die zijn toegewezen om een Stream Analytics-taak uit te voeren. Hoe hoger het aantal streaming-eenheden, hoe meer CPU- en geheugenresources worden toegewezen voor uw taak. Met deze capaciteit u zich concentreren op de querylogica en wordt de noodzaak geabstraheerd om de hardware te beheren om uw Stream Analytics-taak tijdig uit te voeren.

Om verwerking met lage latentie te bereiken, voeren Azure Stream Analytics-taken alle verwerking in het geheugen uit. Wanneer het geheugen opraakt, mislukt de streamingtaak. Als gevolg hiervan is het voor een productietaak belangrijk om het resourcegebruik van een streamingtaak te controleren en ervoor te zorgen dat er voldoende resource is toegewezen om de taken 24/7 te laten draaien.

De su%-gebruiksstatistiek, die varieert van 0% tot 100%, beschrijft het geheugenverbruik van uw werkbelasting. Voor een streamingtaak met een minimale voetafdruk ligt deze statistiek meestal tussen de 10% en 20%. Als het gebruik van SU% laag is en invoergebeurtenissen worden geback-ingelogd, vereist uw werkbelasting waarschijnlijk meer rekenresources, waardoor u het aantal SU's moet verhogen. Het is het beste om de SU-statistiek onder de 80% te houden om rekening te houden met incidentele pieken. Microsoft raadt aan een waarschuwing in te stellen op 80% SU-gebruiksstatistiek om uitputting van resources te voorkomen. Zie [Zelfstudie: Waarschuwingen instellen voor Azure Stream Analytics-taken](stream-analytics-set-up-alerts.md)voor meer informatie.

## <a name="configure-stream-analytics-streaming-units-sus"></a>Stream Analytics-streamingeenheden (SUs) configureren
1. Aanmelden bij [Azure-portal](https://portal.azure.com/)

2. Zoek in de lijst met bronnen de streamanalytics-taak die u wilt schalen en open deze vervolgens. 

3. Selecteer Op de taakpagina onder de kop **Configureren** de optie **Schalen**. 

    ![Azure portal Stream Analytics-taakconfiguratie][img.stream.analytics.preview.portal.settings.scale]
    
4. Gebruik de schuifregelaar om de SU's voor de taak in te stellen. U bent beperkt tot specifieke SU-instellingen. 
5. U het aantal SU's wijzigen dat aan uw taak is toegewezen, zelfs wanneer deze wordt uitgevoerd. Dit is niet mogelijk als uw taak een [niet-partitieuitvoer](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#query-using-non-partitioned-output) gebruikt of [een query met meerdere stappen heeft met verschillende partitionerende door-waarden.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#multi-step-query-with-different-partition-by-values) U misschien kiezen uit een reeks SU-waarden wanneer de taak wordt uitgevoerd. 

## <a name="monitor-job-performance"></a>Taakprestaties controleren
Met de Azure-portal u de doorvoer van een taak bijhouden:

![Azure Stream Analytics-monitortaken][img.stream.analytics.monitor.job]

Bereken de verwachte doorvoer van de werkbelasting. Als de doorvoer lager is dan verwacht, stemt u de invoerpartitie af, stemt u de query af en voegt u SU's toe aan uw taak.

## <a name="how-many-sus-are-required-for-a-job"></a>Hoeveel streaming-eenheden zijn vereist voor een taak?

Het kiezen van het aantal vereiste SU's voor een bepaalde taak is afhankelijk van de partitieconfiguratie voor de ingangen en de query die binnen de taak is gedefinieerd. Met **de pagina Schalen** u het juiste aantal SU's instellen. Het is een beste praktijk om meer SU's toe te wijzen dan nodig is. De Stream Analytics-verwerkingsengine optimaliseert voor latentie en doorvoer ten koste van de toewijzing van extra geheugen.

In het algemeen is de beste praktijk om te beginnen met 6 SU's voor query's die geen **partitie gebruiken door**. Bepaal vervolgens de sweet spot met behulp van een trial and error-methode waarbij u het aantal SU's wijzigt nadat u representatieve hoeveelheden gegevens hebt doorgegeven en de statistiek SU% Gebruik hebt onderzocht. Het maximum aantal streaming-eenheden dat kan worden gebruikt door een Stream Analytics-taak is afhankelijk van het aantal stappen in de query die is gedefinieerd voor de taak en het aantal partities in elke stap. U meer informatie over de grenzen [hier](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization#calculate-the-maximum-streaming-units-of-a-job).

Zie deze pagina: [Azure Stream Analytics-taken schalen om](stream-analytics-scale-jobs.md) de doorvoer te verhogen voor meer informatie over het kiezen van het juiste aantal SU's

> [!Note]
> Het kiezen van hoeveel SU's nodig zijn voor een bepaalde taak, is afhankelijk van de partitieconfiguratie voor de ingangen en van de query die voor de taak is gedefinieerd. U maximaal uw quotum selecteren in SU's voor een taak. Standaard heeft elk Azure-abonnement een quotum van maximaal 500 SU's voor alle analysetaken in een specifieke regio. Neem contact op met [Microsoft Support](https://support.microsoft.com)om SU's voor uw abonnementen te verhogen die verder gaan dan dit quotum. Geldige waarden voor SU's per taak zijn 1, 3, 6 en omhoog in stappen van 6.

## <a name="factors-that-increase-su-utilization"></a>Factoren die het gebruikspercentage van streaming-eenheden verhogen 

Tijdelijke (tijdgeoriënteerde) query-elementen zijn de kernset van stateful operators die worden geleverd door Stream Analytics. Stream Analytics beheert de status van deze bewerkingen intern namens de gebruiker, door geheugenverbruik, controlepunten voor tolerantie en statusherstel tijdens service-upgrades te beheren. Hoewel Stream Analytics de status volledig beheert, zijn er een aantal aanbevelingen voor beste praktijken die gebruikers moeten overwegen.

Houd er rekening mee dat een taak met complexe querylogica een hoog SU%-gebruik kan hebben, zelfs als deze niet continu invoergebeurtenissen ontvangt. Dit kan gebeuren na een plotselinge piek in input- en uitvoergebeurtenissen. De taak kan de status in het geheugen behouden blijven als de query complex is.

SU% gebruik kan plotseling dalen tot 0 voor een korte periode alvorens terug te komen op de verwachte niveaus. Dit gebeurt als gevolg van tijdelijke fouten of systeem gestarte upgrades. Het toenemende aantal streaming-eenheden voor een taak vermindert mogelijk het gebruik van su% niet als uw query niet [volledig parallel is.](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-parallelization)

Terwijl u het gebruik over een bepaalde periode vergelijkt, gebruikt u [statistieken over de gebeurtenissnelheid](stream-analytics-monitoring.md). Met de statistieken InputEvents en OutputEvents wordt weergegeven hoeveel gebeurtenissen zijn gelezen en verwerkt. Er zijn statistieken die ook het aantal foutgebeurtenissen aangeven, zoals deserialisatiefouten. Wanneer het aantal gebeurtenissen per tijdseenheid toeneemt, neemt su% in de meeste gevallen toe.

## <a name="stateful-query-logicin-temporal-elements"></a>Stateful querylogica in tijdelijke elementen
Een van de unieke mogelijkheden van azure stream analytics-taak is het uitvoeren van stateful processing, zoals gevensterde aggregaten, tijdelijke joins en tijdelijke analytische functies. Elk van deze operatoren bewaart staatsinformatie.De maximale venstergrootte voor deze query-elementen is zeven dagen. 

Het tijdelijke vensterconcept wordt weergegeven in verschillende query-elementen van Stream Analytics:
1. Windowed aggregaten: GROUP BY of Tumbling, Hopping, and Sliding windows

2. Tijdelijke joins: JOIN met de functie DATEDIFF

3. Tijdelijke analytische functies: ISFIRST, LAST en LAG met LIMIETDUUR

De volgende factoren zijn van invloed op het geheugen dat wordt gebruikt (onderdeel van metrische streamingeenheden) door Stream Analytics-taken:

## <a name="windowed-aggregates"></a>Windowed aggregaten
Het verbruikte geheugen (statusgrootte) voor een vensteraggregaat is niet altijd direct evenredig met de venstergrootte. In plaats daarvan is het verbruikte geheugen evenredig met de kardinaliteit van de gegevens of het aantal groepen in elk tijdvenster.


In de volgende query is het `clusterid` getal dat is gekoppeld aan bijvoorbeeld de kardinaliteit van de query. 

   ```sql
   SELECT count(*)
   FROM input 
   GROUP BY  clusterid, tumblingwindow (minutes, 5)
   ```

Als u eventuele problemen die worden veroorzaakt door hoge kardinaliteit in de `clusterid`vorige query, u gebeurtenissen naar Gebeurtenishub verzenden en de query uitschalen door het systeem toe te staan elke invoerpartitie afzonderlijk te verwerken met BEHULP VAN **PARTITION BY,** zoals in het onderstaande voorbeeld wordt weergegeven:

   ```sql
   SELECT count(*) 
   FROM input PARTITION BY PartitionId
   GROUP BY PartitionId, clusterid, tumblingwindow (minutes, 5)
   ```

Wanneer de query is gepartitioneerd, wordt deze verspreid over verschillende knooppunten. Als gevolg hiervan wordt `clusterid` het aantal waarden dat in elk knooppunt komt, verminderd, waardoor de kardinaliteit van de groep per operator wordt verminderd. 

Gebeurtenishubpartities moeten worden verdeeld door de groeperingssleutel om te voorkomen dat een stap moet worden verwijderd. Zie [Overzicht van gebeurtenishubs](../event-hubs/event-hubs-what-is-event-hubs.md)voor meer informatie. 

## <a name="temporal-joins"></a>Tijdelijke joins
Het geheugen verbruikt (statusgrootte) van een tijdelijke join is evenredig aan het aantal gebeurtenissen in de temporele speelruimte van de join, dat is gebeurtenis invoersnelheid vermenigvuldigd met de wiggle room grootte. Met andere woorden, het geheugen dat wordt verbruikt door joins is evenredig met het tijdsbereik DateDiff vermenigvuldigd met de gemiddelde gebeurtenissnelheid.

Het aantal ongeëvenaarde gebeurtenissen in de join is van invloed op het geheugengebruik voor de query. Met de volgende query wordt gezocht naar advertentieweergaven waarmee klikken worden gegenereerd:

   ```sql
   SELECT clicks.id
   FROM clicks 
   INNER JOIN impressions ON impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10.
   ```

In dit voorbeeld is het mogelijk dat veel advertenties worden weergegeven en weinig mensen erop klikken en dat het nodig is om alle gebeurtenissen in het tijdvenster te houden. Het verbruikte geheugen is gerelateerd aan de venstergrootte en de snelheid waarmee gebeurtenissen elkaar opvolgen. 

Om dit te verwerk, stuurt u gebeurtenissen naar Gebeurtenishub die zijn verdeeld door de joinsleutels (ID in dit geval) en schaalt u de query uit door het systeem toe te staan elke invoerpartitie afzonderlijk te verwerken met behulp van **PARTITION BY** zoals weergegeven:

   ```sql
   SELECT clicks.id
   FROM clicks PARTITION BY PartitionId
   INNER JOIN impressions PARTITION BY PartitionId 
   ON impression.PartitionId = clicks.PartitionId AND impressions.id = clicks.id AND DATEDIFF(hour, impressions, clicks) between 0 AND 10 
   ```

Wanneer de query is gepartitioneerd, wordt deze verspreid over verschillende knooppunten. Als gevolg hiervan wordt het aantal gebeurtenissen dat in elk knooppunt komt verminderd, waardoor de grootte van de status die in het joinvenster wordt gehouden, wordt verminderd. 

## <a name="temporal-analytic-functions"></a>Tijdelijke analytische functies
Het verbruikte geheugen (statusgrootte) van een tijdelijke analytische functie is evenredig aan de gebeurtenissnelheid vermenigvuldigt zich met de duur.Het geheugen dat wordt verbruikt door analytische functies is niet evenredig aan de venstergrootte, maar het aantal partities in elk tijdvenster.

De herstelprocedure is vergelijkbaar met tijdelijke join. U de query schalen met **PARTITIE DOOR**. 

## <a name="out-of-order-buffer"></a>Niet op orde buffer 
De gebruiker kan de grootte van de niet-orderbuffer configureren in het configuratievenster gebeurtenisvolgorde. De buffer wordt gebruikt om ingangen vast te houden voor de duur van het venster en deze opnieuw te ordenen. De grootte van de buffer is evenredig aan de gebeurtenisinvoersnelheid vermenigvuldigt zich met de grootte van het niet-ordervenster. De standaardvenstergrootte is 0. 

Als u de overloop van de niet-orderbuffer wilt herstellen, schaalt u query uit met **PARTITIE DOOR**. Wanneer de query is gepartitioneerd, wordt deze verspreid over verschillende knooppunten. Als gevolg hiervan wordt het aantal gebeurtenissen dat in elk knooppunt komt, verminderd, waardoor het aantal gebeurtenissen in elke bestelbuffer wordt verminderd. 

## <a name="input-partition-count"></a>Aantal invoerpartities 
Elke invoerpartitie van een taakinvoer heeft een buffer. Hoe groter het aantal invoerpartities, hoe meer resource de taak verbruikt. Voor elke streamingeenheid kan Azure Stream Analytics ongeveer 1 MB/s aan invoer verwerken. Daarom u optimaliseren door het aantal Stream Analytics-streamingeenheden af te passen aan het aantal partities in uw Event Hub. 

Een taak die is geconfigureerd met één streaming-eenheid is doorgaans voldoende voor een gebeurtenishub met twee partities (wat het minimum is voor gebeurtenishub). Als de gebeurtenishub meer partities heeft, verbruikt uw Stream Analytics-taak meer resources, maar maakt deze niet noodzakelijkerwijs gebruik van de extra doorvoer die wordt geleverd door Event Hub. 

Voor een taak met 6 streaming-eenheden hebt u mogelijk 4 of 8 partities nodig van de Gebeurtenishub. Vermijd echter te veel onnodige partities, omdat dat overmatig gebruik van resources veroorzaakt. Bijvoorbeeld een Event Hub met 16 partities of groter in een Stream Analytics-taak met 1 streaming-eenheid. 

## <a name="reference-data"></a>Referentiegegevens 
Referentiegegevens in ASA worden in het geheugen geladen voor snel opzoeken. Met de huidige implementatie houdt elke join-bewerking met referentiegegevens een kopie van de referentiegegevens in het geheugen, zelfs als u meerdere keren met dezelfde referentiegegevens deelneemt. Voor query's met **PARTITION BY**heeft elke partitie een kopie van de referentiegegevens, zodat de partities volledig zijn ontkoppeld. Met het multiplicatoreffect kan het geheugengebruik snel erg hoog worden als u meerdere keren met referentiegegevens met meerdere partities deelneemt.  

### <a name="use-of-udf-functions"></a>Gebruik van UDF-functies
Wanneer u een UDF-functie toevoegt, laadt Azure Stream Analytics de JavaScript-runtime in het geheugen. Dit zal gevolgen hebben voor de SU%.

## <a name="next-steps"></a>Volgende stappen
* [Parallelle query's maken in Azure Stream Analytics](stream-analytics-parallelization.md)
* [Azure Stream Analytics-taken schalen om de doorvoer te verhogen](stream-analytics-scale-jobs.md)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

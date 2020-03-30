---
title: Query's controleren
titleSuffix: Azure Cognitive Search
description: Monitor querystatistieken voor prestaties en doorvoer. Verzamel en analyseer querytekenreeksen in diagnostische logboeken.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: a3a313ef9cd74ba901f5a6a2d82a18e3c21145dc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462515"
---
# <a name="monitor-query-requests-in-azure-cognitive-search"></a>Queryaanvragen controleren in Azure Cognitive Search

In dit artikel wordt uitgelegd hoe u queryprestaties en -volume meten aan de hand van statistieken en diagnostische logboekregistratie. Het legt ook uit hoe u de invoertermen verzamelt die in query's worden gebruikt - noodzakelijke informatie wanneer u het nut en de effectiviteit van uw zoekcorpus moet beoordelen.

Historische gegevens die worden verwerkt in statistieken, worden 30 dagen bewaard. Voor langere bewaring of voor het rapporteren over operationele gegevens en querytekenreeksen moet u een [diagnostische instelling](search-monitor-logs.md) inschakelen die een opslagoptie opgeeft voor het aanhouden van geregistreerde gebeurtenissen en metrische gegevens.

Voorwaarden die de integriteit van gegevensmeting maximaliseren, zijn onder andere:

+ Gebruik een factureerbare service (een service die is gemaakt op de basis- of standaardlaag). De gratis service wordt gedeeld door meerdere abonnees, die een zekere mate van volatiliteit introduceert als ladingen verschuiven.

+ Gebruik één replica en partitie, indien mogelijk, om een opgenomen en geïsoleerde omgeving te maken. Als u meerdere replica's gebruikt, worden querymetrische gegevens gemiddeld over meerdere knooppunten geplaatst, waardoor de nauwkeurigheid van de resultaten kan worden verlaagd. Op dezelfde manier betekenen meerdere partities dat gegevens worden verdeeld, met het potentieel dat sommige partities verschillende gegevens kunnen hebben als indexering ook aan de gang is. Bij het afstemmen van queryprestaties biedt één knooppunt en partitie een stabielere omgeving voor het testen.

> [!Tip]
> Met extra client-side code en Application Insights u ook klikdata vastleggen voor een dieper inzicht in wat de interesse van uw applicatiegebruikers aantrekt. Zie [Verkeersanalyse zoeken](search-traffic-analytics.md)voor meer informatie .

## <a name="query-volume-qps"></a>Queryvolume (QPS)

Het volume wordt gemeten als **Zoekopdrachten per seconde** (QPS), een ingebouwde statistiek die kan worden gerapporteerd als een gemiddelde, telling, minimum of maximale waarden van query's die binnen een venster van één minuut worden uitgevoerd. Intervallen van één minuut (TimeGrain = "PT1M") voor metrische gegevens worden binnen het systeem vastgesteld.

Het is gebruikelijk dat query's in milliseconden worden uitgevoerd, dus alleen query's die als seconde worden gemeten, worden weergegeven in metrische gegevens.

| Aggregatietype | Beschrijving |
|------------------|-------------|
| Average | Het gemiddelde aantal seconden binnen een minuut waarin query-uitvoering heeft plaatsgevonden.|
| Count | Het aantal statistieken dat binnen het interval van één minuut naar het logboek wordt uitgestraald. |
| Maximum | Het hoogste aantal zoekopdrachten per seconde geregistreerd gedurende een minuut. |
| Minimum | Het laagste aantal zoekopdrachten per seconde geregistreerd gedurende een minuut.  |
| Sum | De som van alle query's die binnen de minuut zijn uitgevoerd.  |

Binnen een minuut hebt u bijvoorbeeld een patroon als dit: een seconde hoge belasting die het maximum is voor SearchQueriesPerSeconde, gevolgd door 58 seconden gemiddelde belasting en ten slotte één seconde met slechts één query, wat het minimum is.

Een ander voorbeeld: als een knooppunt 100 metrische gegevens uitzendt, waarbij de waarde van elke statistiek 40 is, is 'Aantal' 100, 'Som' is 4000, 'Gemiddeld' is 40 en 'Max' 40.

## <a name="query-performance"></a>Queryprestaties

Servicebrede queryprestaties worden gemeten als zoeklatentie (hoe lang een query duurt om te voltooien) en beperkte query's die zijn verwijderd als gevolg van resource-twist.

### <a name="search-latency"></a>Zoeklatentie

| Aggregatietype | Latentie | 
|------------------|---------|
| Average | Gemiddelde queryduur in milliseconden. | 
| Count | Het aantal statistieken dat binnen het interval van één minuut naar het logboek wordt uitgestraald. |
| Maximum | Langstlopende query in het voorbeeld. | 
| Minimum | Kortste lopende query in het voorbeeld.  | 
| Totaal | Totale uitvoeringstijd van alle query's in het voorbeeld, uitvoeren binnen het interval (één minuut).  |

Overweeg het volgende voorbeeld van **zoeklatentiestatistieken:** er zijn 86 query's bemonsterd, met een gemiddelde duur van 23,26 milliseconden. Een minimum van 0 geeft aan dat sommige query's zijn verwijderd. De langstlopende query heeft 1000 milliseconden geduurd. De totale uitvoeringstijd was 2 seconden.

![Latentieaggregaties](./media/search-monitor-usage/metrics-latency.png "Latentieaggregaties")

### <a name="throttled-queries"></a>Gewurgde query's

Throttled queries verwijst naar query's die worden verwijderd in plaats van proces. In de meeste gevallen is beperking een normaal onderdeel van het uitvoeren van de service.  Het is niet per se een indicatie dat er iets mis is.

Beperking treedt op wanneer het aantal aanvragen dat momenteel wordt verwerkt de beschikbare resources overschrijdt. Mogelijk ziet u een toename van het aantal aanvragen met een beperking wanneer een replica uit de rotatie of tijdens het indexeren wordt gehaald. Zowel query- als indexeringsaanvragen worden behandeld door dezelfde set resources.

De service bepaalt of aanvragen moeten worden neergestoofd op basis van resourceverbruik. Het percentage resources dat wordt verbruikt in geheugen, CPU en schijf-IO wordt gemiddeld over een bepaalde periode. Als dit percentage een drempelwaarde overschrijdt, worden alle aanvragen voor de index beperkt totdat het aantal aanvragen is verminderd. 

Afhankelijk van uw klant kan op deze manier een aangezochte aanvraag worden aangegeven:

+ Een service retourneert een foutmelding "U verzendt te veel aanvragen. Probeer het later opnieuw. 
+ Een service retourneert een foutcode van 503 die aangeeft dat de service momenteel niet beschikbaar is. 
+ Als u de portal gebruikt (bijvoorbeeld Search Explorer), wordt de query in stilte verwijderd en moet u opnieuw op Zoeken klikken.

Als u aanbeperkende query's wilt bevestigen, gebruikt u de statistiek **Metriek ethrottled-zoekopdrachten.** U statistieken in de portal verkennen of een waarschuwingsstatistiek maken zoals beschreven in dit artikel. Voor query's die binnen het bemonsteringsinterval zijn verwijderd, gebruikt u *Total* om het percentage query's op te halen dat niet is uitgevoerd.

| Aggregatietype | Beperking |
|------------------|-----------|
| Average | Percentage query's dat binnen het interval is gedaald. |
| Count | Het aantal statistieken dat binnen het interval van één minuut naar het logboek wordt uitgestraald. |
| Maximum | Percentage query's dat binnen het interval is gedaald.|
| Minimum | Percentage query's dat binnen het interval is gedaald. |
| Totaal | Percentage query's dat binnen het interval is gedaald. |

Voor **percentage zoekopdrachten met throttled**, minimum, maximum, gemiddelde en totaal, hebben allemaal dezelfde waarde: het percentage zoekopdrachten dat is beperkt, van het totale aantal zoekopdrachten gedurende één minuut.

In de volgende schermafbeelding is het eerste getal het aantal (of het aantal statistieken dat naar het logboek wordt verzonden). Extra aggregaties, die bovenaan worden weergegeven of wanneer u boven de statistiek zweeft, omvatten gemiddeld, maximum en totaal. In dit voorbeeld zijn geen aanvragen verwijderd.

![Gewurgde aggregaties](./media/search-monitor-usage/metrics-throttle.png "Gewurgde aggregaties")

## <a name="explore-metrics-in-the-portal"></a>Statistieken verkennen in de portal

Voor een snelle blik op de huidige getallen, bevat het tabblad **Controle** op de pagina Serviceoverzicht drie statistieken **(zoeklatentie**, **zoekopdrachten per seconde (per zoekeenheid)**, **Percentage zoekquery's**met throttled over vaste intervallen gemeten in uren, dagen en weken, met de optie om het aggregatietype te wijzigen.

Voor een diepere verkenning u de verkenner met statistieken openen in het menu **Monitoring,** zodat u gegevens laaggeven, inzoomen en visualiseren om trends of afwijkingen te verkennen. Meer informatie over metrics explorer door deze [zelfstudie over het maken van een statistiekengrafiek te](https://docs.microsoft.com/azure/azure-monitor/learn/tutorial-metrics-explorer)voltooien.

1. Selecteer in de sectie Controle **de optie Statistieken** om de verkenner met het bereik te openen dat is ingesteld op uw zoekservice.

1. Kies onder Metric een in de vervolgkeuzelijst en bekijk de lijst met beschikbare aggregaties voor een voorkeurstype. De aggregatie definieert hoe de verzamelde waarden worden bemonsterd over elk tijdsinterval.

   ![Verkenner voor QPS-statistiek](./media/search-monitor-usage/metrics-explorer-qps.png "Verkenner voor QPS-statistiek")

1. Stel in de rechterbovenhoek het tijdsinterval in.

1. Kies een visualisatie. De standaardinstelling is een lijndiagram.

1. Extra aggregaties laagdoor **metrische gegevens toevoegen** te kiezen en verschillende aggregaties te selecteren.

1. Zoom in op een interessegebied in het lijndiagram. Plaats de muisaanwijzer aan het begin van het gebied, klik op de linkermuisknop en houd deze ingedrukt, sleep naar de andere kant van het gebied en laat de knop los. De grafiek zoomt in op dat tijdsbereik.

## <a name="identify-strings-used-in-queries"></a>Tekenreeksen identificeren die in query's worden gebruikt

Wanneer u diagnostische logboekregistratie inschakelt, legt het systeem queryaanvragen vast in de tabel **AzureDiagnostics.** Als voorwaarde moet u diagnostische [logboekregistratie](search-monitor-logs.md)al hebben ingeschakeld, een werkruimte voor logboekanalyse of een andere opslagoptie opgeven.

1. Selecteer **logboeken** in logopeeren in Loganalytics onder de sectie Controle om een leeg queryvenster te openen.

1. Voer de volgende expressie uit om query.search-bewerkingen uit te voeren en een tabelresulterenset terug te sturen die bestaat uit de bewerkingsnaam, de querytekenreeks, de opgevraagde index en het aantal gevonden documenten. De laatste twee instructies sluiten querytekenreeksen uit die bestaan uit een lege of niet-gespecificeerde zoekopdracht, boven een voorbeeldindex, waardoor de ruis in uw resultaten wordt verkort.

   ```
   AzureDiagnostics
   | project OperationName, Query_s, IndexName_s, Documents_d
   | where OperationName == "Query.Search"
   | where Query_s != "?api-version=2019-05-06&search=*"
   | where IndexName_s != "realestate-us-sample-index"
   ```

1. Stel destijds een kolomfilter in op *Query_s* om te zoeken naar een specifieke syntaxis of tekenreeks. U bijvoorbeeld filteren *is gelijk aan* `?api-version=2019-05-06&search=*&%24filter=HotelName`).

   ![Aangemelde querytekenreeksen](./media/search-monitor-usage/log-query-strings.png "Aangemelde querytekenreeksen")

Hoewel deze techniek werkt voor ad hoc onderzoek, u met het bouwen van een rapport de querytekenreeksen consolideren en presenteren in een lay-out die bevorderlijker is voor analyse.

## <a name="identify-long-running-queries"></a>Langlopende query's identificeren

Voeg de duurkolom toe om de getallen voor alle query's te krijgen, niet alleen de kolommen die als statistiek worden opgehaald. Het sorteren van deze gegevens laat zien welke query's het langst duren om te voltooien.

1. Selecteer in de sectie Controle de optie **Logboeken** die u wilt opvragen voor logboekgegevens.

1. Voer de volgende query uit om query's terug te sturen, gesorteerd op duur in milliseconden. De langstlopende query's staan bovenaan.

   ```
   AzureDiagnostics
   | project OperationName, resultSignature_d, DurationMs, Query_s, Documents_d, IndexName_s
   | where OperationName == "Query.Search"
   | sort by DurationMs
   ```

   ![Query's sorteren op duur](./media/search-monitor-usage/azurediagnostics-table-sortby-duration.png "Query's sorteren op duur")

## <a name="create-a-metric-alert"></a>Een metrische waarschuwing maken

Een metrische waarschuwing stelt een drempel waarde waarde op waarop u ofwel een melding ontvangt of een corrigerende actie die u vooraf definieert. 

Voor een zoekservice is het gebruikelijk om een metrische waarschuwing te maken voor zoeklatentie en gethrottled query's. Als u weet wanneer query's worden verwijderd, u zoeken naar oplossingen die de belasting verminderen of de capaciteit vergroten. Als er bijvoorbeeld query's met throttled toenemen tijdens het indexeren, u deze uitstellen totdat de queryactiviteit afneemt.

Bij het verleggen van de limieten van een bepaalde replica-partitieconfiguratie is het ook handig om waarschuwingen in te stellen voor queryvolumedrempels (QPS).

1. Selecteer onder de sectie Controle de optie **Waarschuwingen** en klik op **+ Nieuwe waarschuwingsregel**. Controleer of uw zoekservice is geselecteerd als bron.

1. Klik onder Voorwaarde op **Toevoegen**.

1. Signaallogica configureren. Kies voor signaaltype **metrische gegevens** en selecteer vervolgens het signaal.

1. Na het selecteren van het signaal, u een grafiek gebruiken om historische gegevens te visualiseren voor een weloverwogen beslissing over hoe verder te gaan met het instellen van voorwaarden.

1. Schuif vervolgens omlaag naar De logica Waarschuwen. Voor proof-of-concept u een kunstmatig lage waarde opgeven voor testdoeleinden.

   ![Waarschuwingslogica](./media/search-monitor-usage/alert-logic-qps.png "Waarschuwingslogica")

1. Geef vervolgens een actiegroep op of maak deze samen. Dit is het antwoord om zich aan te roepen wanneer de drempel wordt bereikt. Het kan een pushmelding of een geautomatiseerd antwoord zijn.

1. Geef als laatste waarschuwingsgegevens op. Geef de waarschuwing een naam en beschrijf de waarschuwing, wijs een ernstwaarde toe en geef op of u de regel wilt maken in een ingeschakelde of uitgeschakelde status.

   ![Waarschuwingsgegevens](./media/search-monitor-usage/alert-details.png "Meldingsdetails")

Als u een e-mailmelding hebt opgegeven, ontvangt u een e-mail van Microsoft Azure `<your rule name>`met een onderwerpregel van 'Azure: Geactiveerde ernst: 3'.

<!-- ## Report query data

Power BI is an analytical reporting tool useful for visualizing data, including log information. If you are collecting data in Blob storage, a Power BI template makes it easy to spot anomalies or trends. Use this link to download the template. -->

## <a name="next-steps"></a>Volgende stappen

Als u dit nog niet hebt gedaan, bekijkt u de basisprincipes van het controleren van de zoekservice om meer te weten te komen over het volledige scala aan toezichtsmogelijkheden.

> [!div class="nextstepaction"]
> [Bewerkingen en activiteiten in Azure Cognitive Search controleren](search-monitor-usage.md)
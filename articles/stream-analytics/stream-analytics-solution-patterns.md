---
title: Azure Stream Analytics-oplossingspatronen
description: Meer informatie over algemene oplossingspatronen voor Azure Stream Analytics, zoals dashboarding, gebeurtenisberichten, gegevensopslag, verrijking van referentiegegevens en monitoring.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: 3b95863c1ae53bd0642aec356f55aba1faf8ef09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79535779"
---
# <a name="azure-stream-analytics-solution-patterns"></a>Azure Stream Analytics-oplossingspatronen

Net als veel andere services in Azure wordt Stream Analytics het best gebruikt met andere services om een grotere end-to-end oplossing te maken. In dit artikel worden eenvoudige Azure Stream Analytics-oplossingen en verschillende architecturale patronen besproken. U voortbouwen op deze patronen om complexere oplossingen te ontwikkelen. De patronen beschreven in dit artikel kunnen worden gebruikt in een breed scala van scenario's. Voorbeelden van scenariospecifieke patronen zijn beschikbaar op [Azure-oplossingsarchitecturen.](https://azure.microsoft.com/solutions/architecture/?product=stream-analytics)

## <a name="create-a-stream-analytics-job-to-power-real-time-dashboarding-experience"></a>Maak een Stream Analytics-taak om realtime dashboardervaring te bieden

Met Azure Stream Analytics u snel realtime dashboards en waarschuwingen up-up. Een eenvoudige oplossing neemt gebeurtenissen op van Event Hubs of IoT Hub en [voedt het Power BI-dashboard met een streaming gegevensset.](/power-bi/service-real-time-streaming) Zie voor meer informatie de gedetailleerde zelfstudie [Telefoongespreksgegevens analyseren met Stream Analytics en de resultaten visualiseren in het Power BI-dashboard.](stream-analytics-manage-job.md)

![ASA Power BI-dashboard](media/stream-analytics-solution-patterns/pbidashboard.png)

Deze oplossing kan worden gebouwd in slechts een paar minuten van Azure portal. Er is geen uitgebreide codering betrokken, en SQL-taal wordt gebruikt om de bedrijfslogica uit te drukken.

Dit oplossingspatroon biedt de laagste latentie van de gebeurtenisbron naar het Power BI-dashboard in een browser. Azure Stream Analytics is de enige Azure-service met deze ingebouwde mogelijkheid.

## <a name="use-sql-for-dashboard"></a>SQL gebruiken voor dashboard

Het Power BI-dashboard biedt een lage latentie, maar kan niet worden gebruikt om volwaardige Power BI-rapporten te produceren. Een veelvoorkomend rapportagepatroon is om uw gegevens eerst naar een SQL-database uit te zetten. Gebruik vervolgens de SQL-connector van Power BI om SQL op te vragen voor de nieuwste gegevens.

![ASA SQL-dashboard](media/stream-analytics-solution-patterns/sqldashboard.png)

Het gebruik van SQL-database geeft u meer flexibiliteit, maar ten koste van een iets hogere latentie. Deze oplossing is optimaal voor taken met latentievereisten van meer dan een seconde. Met deze methode u de Power BI-mogelijkheden maximaliseren om de gegevens voor rapporten verder te segmenteren en te dobbelen en nog veel meer visualisatieopties. U krijgt ook de flexibiliteit om andere dashboardoplossingen te gebruiken, zoals Tableau.

SQL is geen gegevensarchief met een hoge doorvoer. De maximale doorvoer naar een SQL-database van Azure Stream Analytics bedraagt momenteel ongeveer 24 MB/s. Als de gebeurtenisbronnen in uw oplossing gegevens met een hogere snelheid produceren, moet u verwerkingslogica gebruiken in Stream Analytics om de uitvoersnelheid te verlagen tot SQL. Technieken zoals filteren, gevensterde aggregaten, patroonmatching met tijdelijke joins en analytische functies kunnen worden gebruikt. De uitvoersnelheid naar SQL kan verder worden geoptimaliseerd met behulp van technieken die worden beschreven in [Azure Stream Analytics-uitvoer naar Azure SQL Database.](stream-analytics-sql-output-perf.md)

## <a name="incorporate-real-time-insights-into-your-application-with-event-messaging"></a>Neem realtime inzichten in uw toepassing op met gebeurtenisberichten

Het tweede populairste gebruik van Stream Analytics is het genereren van realtime waarschuwingen. In dit oplossingspatroon kan bedrijfslogica in Stream Analytics worden gebruikt om [temporele en ruimtelijke patronen](stream-analytics-geospatial-functions.md) of [afwijkingen](stream-analytics-machine-learning-anomaly-detection.md)te detecteren en vervolgens waarschuwingssignalen te produceren. In tegenstelling tot de dashboardoplossing waarbij Stream Analytics Power BI als voorkeurseindpunt gebruikt, kunnen echter een aantal tussenliggende gegevensputten worden gebruikt. Deze sinks omvatten Event Hubs, Service Bus en Azure Functions. U, als de toepassingsbouwer, moet beslissen welke gegevensgootsteen het beste werkt voor uw scenario.

Downstream-logica voor gebeurtenisconsumenten moet worden geïmplementeerd om waarschuwingen te genereren in uw bestaande bedrijfsworkflow. Omdat u aangepaste logica implementeren in Azure-functies, is Azure Functions de snelste manier om deze integratie uit te voeren. Een zelfstudie voor het gebruik van Azure Function als uitvoer voor een Stream Analytics-taak is te vinden in [Azure-functies uitvoeren vanuit Azure Stream Analytics-taken.](stream-analytics-with-azure-functions.md) Azure Functions ondersteunt ook verschillende soorten meldingen, waaronder tekst en e-mail. Logic App kan ook worden gebruikt voor een dergelijke integratie, met Event Hubs tussen Stream Analytics en Logic App.

![ASA-gebeurtenisberichten-app](media/stream-analytics-solution-patterns/eventmessagingapp.png)

Event Hubs daarentegen biedt het meest flexibele integratiepunt. Veel andere services, zoals Azure Data Explorer en Time Series Insights, kunnen gebeurtenissen van gebeurtenishubs gebruiken. Services kunnen rechtstreeks worden verbonden met de gebeurtenishubs vanuit Azure Stream Analytics om de oplossing te voltooien. Event Hubs is ook de hoogste doorvoer messaging broker beschikbaar op Azure voor dergelijke integratie scenario's.

## <a name="dynamic-applications-and-websites"></a>Dynamische applicaties en websites

U aangepaste realtime visualisaties maken, zoals dashboard- of kaartvisualisatie, met Azure Stream Analytics en Azure SignalR Service. Met SignalR kunnen webclients worden bijgewerkt en dynamische inhoud in realtime weergeven.

![ASA dynamische app](media/stream-analytics-solution-patterns/dynamicapp.png)

## <a name="incorporate-real-time-insights-into-your-application-through-data-stores"></a>Real-time inzichten in uw toepassing opnemen via datastores

De meeste webservices en webtoepassingen gebruiken tegenwoordig een aanvraag-/antwoordpatroon om de presentatielaag te bedienen. Het aanvraag-/antwoordpatroon is eenvoudig te bouwen en kan eenvoudig worden geschaald met een lage responstijd met behulp van een stateloze frontend en schaalbare opslag, zoals Cosmos DB.

Een hoog gegevensvolume creëert vaak prestatieknelpunten in een CRUD-gebaseerd systeem. Het [oplossingspatroon voor het vinden van gebeurtenissen](/azure/architecture/patterns/event-sourcing) wordt gebruikt om de prestatieknelpunten aan te pakken. Temporele patronen en inzichten zijn ook moeilijk en inefficiënt te extraheren uit een traditioneel gegevensarchief. Moderne datagestuurde toepassingen met een hoog volume hanteren vaak een dataflow-gebaseerde architectuur. Azure Stream Analytics als rekenengine voor data in beweging is een spil in die architectuur.

![ASA event sourcing app](media/stream-analytics-solution-patterns/eventsourcingapp.png)

In dit oplossingspatroon worden gebeurtenissen verwerkt en samengevoegd tot gegevensopslag door Azure Stream Analytics. De toepassingslaag werkt samen met gegevensarchieven met behulp van het traditionele aanvraag-/antwoordpatroon. Vanwege de mogelijkheid van Stream Analytics om een groot aantal gebeurtenissen in realtime te verwerken, is de toepassing zeer schaalbaar zonder dat de gegevensarchieflaag hoeft te worden verruimd. De gegevensarchieflaag is in wezen een gematerialiseerde weergave in het systeem. [Azure Stream Analytics-uitvoer naar Azure Cosmos DB](stream-analytics-documentdb-output.md) beschrijft hoe Cosmos DB wordt gebruikt als Stream Analytics-uitvoer.

In echte toepassingen waar de verwerkingslogica complex is en er de noodzaak is om bepaalde delen van de logica onafhankelijk te upgraden, kunnen meerdere Stream Analytics-taken samen met Event Hubs worden samengesteld als de intermediaire gebeurtenismakelaar.

![ASA complexe app voor het werven van evenementen](media/stream-analytics-solution-patterns/eventsourcingapp2.png)

Dit patroon verbetert de veerkracht en beheersbaarheid van het systeem. Hoewel Stream Analytics precies één keer wordt verwerkt, is de kans klein dat dubbele gebeurtenissen in de intermediaire Event Hubs terecht kunnen komen. Het is belangrijk dat de downstream Stream Analytics-taak gebeurtenissen dedupemaakt met logische sleutels in een terugblikvenster. Zie Referentie [voor evenementleveringsgaranties](/stream-analytics-query/event-delivery-guarantees-azure-stream-analytics) voor meer informatie over de levering van evenementen.

## <a name="use-reference-data-for-application-customization"></a>Referentiegegevens gebruiken voor het aanpassen van toepassingen

De referentiegegevensfunctie azure stream analytics is speciaal ontworpen voor aanpassing van eindgebruikers, zoals waarschuwingsdrempel, verwerkingsregels en [geofences.](geospatial-scenarios.md) De toepassingslaag kan parameterwijzigingen accepteren en opslaan in een SQL-database. De taak Stream Analytics vraagt regelmatig om wijzigingen uit de database en maakt de aanpassingsparameters toegankelijk via een verwijzingsgegevensjoin. Zie [SQL-referentiegegevens](sql-reference-data.md) en [referentiegegevens joinvoor](/stream-analytics-query/reference-data-join-azure-stream-analytics)meer informatie over het gebruik van referentiegegevens voor het aanpassen van toepassingen.

Dit patroon kan ook worden gebruikt om een regelsengine te implementeren waarbij de drempelwaarden van de regels worden gedefinieerd aan de hand van referentiegegevens. Zie [Op configureerbare regels voor processu-in-hand in Azure Stream Analytics](stream-analytics-threshold-based-rules.md)voor meer informatie over regels.

![ASA-referentiegegevens-app](media/stream-analytics-solution-patterns/refdataapp.png)

## <a name="add-machine-learning-to-your-real-time-insights"></a>Machine Learning toevoegen aan uw realtime inzichten

Het ingebouwde [Anomaliedetectiemodel van](stream-analytics-machine-learning-anomaly-detection.md) Azure Stream Analytics is een handige manier om Machine Learning te introduceren in uw realtime toepassing. Zie Azure Stream Analytics integreert met de [scoringsservice](stream-analytics-machine-learning-integration-tutorial.md)van Azure Machine Learning voor een breder scala aan machine learning-behoeften.

Voor gevorderde gebruikers die online training en scoren willen opnemen in dezelfde Stream Analytics-pijplijn, zie dit voorbeeld van hoe je dat doet met [lineaire regressie.](stream-analytics-high-frequency-trading.md)

![ASA Machine Learning-app](media/stream-analytics-solution-patterns/mlapp.png)

## <a name="near-real-time-data-warehousing"></a>Bijna real-time data warehousing

Een ander veelvoorkomend patroon is real-time data warehousing, ook wel streaming data warehouse genoemd. Naast gebeurtenissen die vanuit uw toepassing op Event Hubs en IoT Hub binnenkomen, kan [Azure Stream Analytics die op IoT Edge wordt uitgevoerd,](stream-analytics-edge.md) worden gebruikt om te voldoen aan gegevensreiniging, gegevensreductie en gegevensopslag- en forward-behoeften. Stream Analytics die op IoT Edge wordt uitgevoerd, kan probleemmet de beperking van bandbreedte en connectiviteit in het systeem op een elegante manier afhandelen. De SQL-uitvoeradapter kan worden gebruikt om uit te outputnaar SQL Data Warehouse; De maximale doorvoer is echter beperkt tot 10 MB/s.

![ASA Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing.png)

Een manier om de doorvoer te verbeteren met enige latentie-afweging is door de gebeurtenissen te archiveren in Azure Blob-opslag en ze vervolgens [te importeren in SQL Data Warehouse met Polybase.](../synapse-analytics/sql-data-warehouse/load-data-from-azure-blob-storage-using-polybase.md) U moet handmatig de uitvoer van Stream Analytics naar blob-opslag en invoer van blob-opslag naar SQL Data Warehouse samenvoegen door [de gegevens te archiveren op tijdstempel](stream-analytics-custom-path-patterns-blob-storage-output.md) en periodiek te importeren.

In dit gebruikspatroon wordt Azure Stream Analytics gebruikt als een bijna realtime ETL-engine. Nieuw aankomende gebeurtenissen worden voortdurend getransformeerd en opgeslagen voor downstream analytics service verbruik.

![ASA high throughput Data Warehousing](media/stream-analytics-solution-patterns/datawarehousing2.png)

## <a name="archiving-real-time-data-for-analytics"></a>Archiveren van realtime gegevens voor analytics

De meeste data science en analytics activiteiten gebeuren nog steeds offline. Gegevens kunnen worden gearchiveerd door Azure Stream Analytics via Azure Data Lake Store Gen2-uitvoer- en parketuitvoerindelingen. Met deze mogelijkheid worden de frictie verwijderd om gegevens rechtstreeks in Azure Data Lake Analytics, Azure Databricks en Azure HDInsight te voeren. Azure Stream Analytics wordt gebruikt als een bijna realtime ETL-engine in deze oplossing. U gearchiveerde gegevens in Data Lake verkennen met behulp van verschillende compute engines.

![ASA offline analytics](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="use-reference-data-for-enrichment"></a>Referentiegegevens gebruiken voor verrijking

Gegevensverrijking is vaak een vereiste voor ETL-motoren. Azure Stream Analytics ondersteunt gegevensverrijking met [referentiegegevens](stream-analytics-use-reference-data.md) uit zowel SQL-database als Azure Blob-opslag. Gegevensverrijking kan worden gedaan voor het landen van gegevens in zowel Azure Data Lake als SQL Data Warehouse.

![ASA offline analytics met dataverrijking](media/stream-analytics-solution-patterns/offlineanalytics.png)

## <a name="operationalize-insights-from-archived-data"></a>Inzichten uit gearchiveerde gegevens operationaliseren

Als u het offline analysepatroon combineert met het bijna realtime toepassingspatroon, u een feedbacklus maken. Met de feedbacklus kan de toepassing automatisch worden aangepast voor het wijzigen van patronen in de gegevens. Deze feedbacklus kan net zo eenvoudig zijn als het wijzigen van de drempelwaarde voor waarschuwingen of net zo complex als het omscholen van Machine Learning-modellen. Dezelfde oplossingsarchitectuur kan worden toegepast op zowel ASA-taken die in de cloud als op IoT Edge worden uitgevoerd.

![ASA geeft inzicht in operationalisering](media/stream-analytics-solution-patterns/insightsoperationalization.png)

## <a name="how-to-monitor-asa-jobs"></a>Asa-taken controleren

Een Azure Stream Analytics-taak kan 24/7 worden uitgevoerd om binnenkomende gebeurtenissen continu in realtime te verwerken. De uptime garantie is cruciaal voor de gezondheid van de algehele toepassing. Hoewel Stream Analytics de enige streaming analytics-service in de branche is die een [beschikbaarheidsgarantie van 99,9%](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/)biedt, u nog steeds een zekere mate van downtime oplopen. In de loop der jaren heeft Stream Analytics statistieken, logboeken en taakstatussen geïntroduceerd om de status van de taken weer te geven. Ze worden allemaal weergegeven via azure monitor-service en kunnen verder worden geëxporteerd naar OMS. Zie [Taakbewaking van Stream Analytics begrijpen en hoe u query's controleren](stream-analytics-monitoring.md)voor meer informatie.

![ASA-monitoring](media/stream-analytics-solution-patterns/monitoring.png)

Er zijn twee belangrijke dingen om te controleren:

- [Status taak mislukt](job-states.md)

    Eerst en vooral, moet u ervoor zorgen dat de baan wordt uitgevoerd. Zonder de taak in de lopende status worden er geen nieuwe statistieken of logboeken gegenereerd. Taken kunnen om verschillende redenen worden gewijzigd in een mislukte status, waaronder het hebben van een hoog SU-gebruiksniveau (d.w.z. zonder resources).

- [Statistieken voor vertraging van watermerken](https://azure.microsoft.com/blog/new-metric-in-azure-stream-analytics-tracks-latency-of-your-streaming-pipeline/)

    Deze statistiek geeft aan hoe ver achter uw verwerkingspijplijn zich in de kloktijd (seconden) achterloopt. Een deel van de vertraging wordt toegeschreven aan de inherente verwerkingslogica. Als gevolg daarvan is het monitoren van de stijgende trend veel belangrijker dan het monitoren van de absolute waarde. De vertraging van de steady state moet worden verholpen door uw toepassingsontwerp, niet door te controleren of te waarschuwingen.

Na het mislukken, activiteit logs en [diagnostische logs](stream-analytics-job-diagnostic-logs.md) zijn de beste plaatsen om te beginnen met het zoeken naar fouten.

## <a name="build-resilient-and-mission-critical-applications"></a>Bouw veerkrachtige en bedrijfskritische toepassingen

Ongeacht de SLA-garantie van Azure Stream Analytics en hoe voorzichtig u uw end-to-end-toepassing uitvoert, er zijn storingen. Als uw toepassing bedrijfskritisch is, moet u voorbereid zijn op uitval om gracieus te herstellen.

Voor het waarschuwen van toepassingen, het belangrijkste is om de volgende waarschuwing te detecteren. U ervoor kiezen om de taak opnieuw op te starten vanaf de huidige tijd bij het herstellen, waarbij meldingen uit het verleden worden genegeerd. De starttijd semantiek van de taak start zijn door de eerste uitvoertijd, niet de eerste invoertijd. De invoer wordt teruggespoeld een passende hoeveelheid tijd om te garanderen dat de eerste output op het opgegeven tijdstip volledig en correct is. U krijgt geen gedeeltelijke aggregaten en activeert hierdoor onverwachtmeldingen.

U er ook voor kiezen om de uitvoer te starten vanaf een bepaalde hoeveelheid tijd in het verleden. Zowel event hubs als het bewaarbeleid van IoT Hub bevatten een redelijke hoeveelheid gegevens om verwerking uit het verleden mogelijk te maken. De afweging is hoe snel u inhalen om de huidige tijd en beginnen met het genereren van tijdige nieuwe waarschuwingen. Data verliest zijn waarde snel na verloop van tijd, dus het is belangrijk om snel in te halen om de huidige tijd. Er zijn twee manieren om snel in te halen:

- Meer resources (SU) inrichten bij een inhaalslag.
- Opnieuw starten vanaf de huidige tijd.

Opnieuw opstarten van de huidige de tijd is eenvoudig te doen, met de afweging van het verlaten van een kloof tijdens de verwerking. Het opnieuw opstarten op deze manier kan OK zijn voor het waarschuwen van scenario's, maar kan problematisch zijn voor dashboardscenario's en is een niet-starter voor archivering en data warehousing scenario's.

Het inrichten van meer resources kan het proces versnellen, maar het effect van een toename van de verwerkingssnelheid is complex.

- Test of uw taak schaalbaar is voor een groter aantal SU's. Niet alle query's zijn schaalbaar. U moet ervoor zorgen dat uw query [parallel loopt.](stream-analytics-parallelization.md)

- Zorg ervoor dat er voldoende partities zijn in de upstream-gebeurtenishubs of IoT-hub waarmee u meer doorvoereenheden (US' s) toevoegen om de invoerdoorvoer te schalen. Vergeet niet dat elke Tu-gebeurtenishubs maxes met een uitvoersnelheid van 2 MB/s.

- Zorg ervoor dat u voldoende resources in de uitvoersinks hebt ingericht (d.w.z. SQL Database, Cosmos DB), zodat ze de toename van de uitvoer niet beperken, waardoor het systeem soms kan worden vergrendeld.

Het belangrijkste is om te anticiperen op de wijziging van de verwerkingssnelheid, deze scenario's te testen voordat u in productie gaat en klaar bent om de verwerking correct te schalen tijdens de hersteltijd van de fout.

In het extreme scenario dat inkomende gebeurtenissen allemaal vertraagd zijn, is het mogelijk dat [alle vertraagde gebeurtenissen worden verwijderd](stream-analytics-time-handling.md) als u een laat aankomend venster op uw werk hebt toegepast. Het laten vallen van de gebeurtenissen kan lijken op een mysterieus gedrag aan het begin; Echter, gezien Stream Analytics is een real-time processing engine, het verwacht inkomende gebeurtenissen dicht bij de muur klok tijd. Het moet gebeurtenissen laten vallen die deze beperkingen schenden.

### <a name="lambda-architectures-or-backfill-process"></a>Lambda Architecturen of Backfill proces

Gelukkig kan het vorige gegevensarchiveringspatroon worden gebruikt om deze late gebeurtenissen gracieus te verwerken. Het idee is dat de archiveringstaak binnenkomende gebeurtenissen in aankomsttijd verwerkt en gebeurtenissen archiveert in de juiste tijdbucket in Azure Blob of Azure Data Lake Store met hun gebeurtenistijd. Het maakt niet uit hoe laat een evenement aankomt, het zal nooit worden geschrapt. Het zal altijd landen in de juiste tijd emmer. Tijdens het herstel is het mogelijk om de gearchiveerde gebeurtenissen opnieuw te verwerken en de resultaten terug te vullen naar de winkel van keuze. Dit is vergelijkbaar met hoe lambda patronen worden geïmplementeerd.

![ASA-backfill](media/stream-analytics-solution-patterns/backfill.png)

Het backfill-proces moet worden uitgevoerd met een offline batchverwerkingssysteem, dat waarschijnlijk een ander programmeermodel heeft dan Azure Stream Analytics. Dit betekent dat u de volledige verwerkingslogica opnieuw moet implementeren.

Voor het bijvullen is het nog steeds belangrijk om op zijn minst tijdelijk meer resources aan de uitvoergootstenen te voorzien om een hogere doorvoer te verwerken dan de behoeften voor verwerking van stabiele statussen.

|Scenario's  |Vanaf nu alleen opnieuw opstarten  |Opnieuw opstarten vanaf de laatste gestopte tijd |Opnieuw starten vanaf nu + vulling met gearchiveerde gebeurtenissen|
|---------|---------|---------|---------|
|**Dashboarding**   |Maakt kloof    |OK voor korte uitval    |Gebruiken voor langdurige uitval |
|**Waarschuwingen**   |Aanvaardbaar |OK voor korte uitval    |Niet nodig |
|**App voor het werven van gebeurtenissen** |Aanvaardbaar |OK voor korte uitval    |Gebruiken voor langdurige uitval |
|**Data warehousing**   |Gegevensverlies  |Aanvaardbaar |Niet nodig |
|**Offline analyses**  |Gegevensverlies  |Aanvaardbaar |Niet nodig|

## <a name="putting-it-all-together"></a>Alles samenvoegen

Het is niet moeilijk voor te stellen dat alle hierboven genoemde oplossingspatronen kunnen worden gecombineerd in een complex end-to-end systeem. Het gecombineerde systeem kan dashboards, waarschuwingen, event sourcing-toepassingen, gegevensopslag en offline analysemogelijkheden omvatten.

De sleutel is om uw systeem te ontwerpen in samenstellende patronen, zodat elk subsysteem onafhankelijk kan worden gebouwd, getest, geüpgraded en hersteld.

## <a name="next-steps"></a>Volgende stappen

U hebt nu verschillende oplossingspatronen gezien met Azure Stream Analytics. Hierna kunt u zich verder in de materie verdiepen en uw eerste Stream Analytics-taak maken:

* [Een Stream Analytics-taak maken via Azure Portal](stream-analytics-quick-create-portal.md).
* [Maak een Stream Analytics-taak met Azure PowerShell](stream-analytics-quick-create-powershell.md).
* [Create a Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md) (Een Stream Analytics-taak maken met behulp van Visual Studio).

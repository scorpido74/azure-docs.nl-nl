---
title: Preview-cases gebruiken-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over het gebruik van Azure Time Series Insights preview-cases.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/29/2020
ms.custom: seodec18
ms.openlocfilehash: 93813dfaecf40fcb5cab0856d013fb890118886c
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580675"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Use cases voor Azure Time Series Insights (preview)

Dit artikel bevat een overzicht van een aantal algemene gebruiks voorbeelden voor Azure Time Series Insights preview. De aanbevelingen in dit artikel fungeren als uitgangs punt voor het ontwikkelen van uw toepassingen en oplossingen met Time Series Insights.

In dit artikel worden met name de volgende vragen beantwoord:

* Wat zijn de algemene gebruiks cases voor Time Series Insights?
* Wat zijn de voor delen van het gebruik van Time Series Insights voor het [verkennen van gegevens en de detectie van visuele afwijkingen](#data-exploration-and-visual-anomaly-detection)?
* Wat zijn de voor delen van het gebruik van Time Series Insights voor [operationele analyse en efficiëntie van processen](#operational-analysis-and-driving-process-efficiency)?
* Wat zijn de voor delen van het gebruik van Time Series Insights voor [geavanceerde analyses](#advanced-analytics)?

Een overzicht van deze gebruiks scenario's wordt beschreven in de volgende secties.

## <a name="introduction"></a>Inleiding

Azure Time Series Insights is een end-to-end-, platform-as-a-service-aanbieding. Het wordt gebruikt voor het verzamelen, verwerken, opslaan, analyseren en opvragen van zeer contextuele, time-series geoptimaliseerde gegevens voor IoT-schaal. Time Series Insights is ideaal voor ad-hoc gegevensverkenning en voor operationele analyse. Time Series Insights is een unieke uitbreid bare, aangepaste service aanbieding die voldoet aan de uitgebreide behoeften van industriële IoT-implementaties.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Gegevens verkennen en visuele anomalie detectie

Verken en analyseer miljarden gebeurtenissen om afwijkingen op te sporen en de verborgen trends in uw gegevens te ontdekken. Time Series Insights biedt bijna realtime prestaties voor uw IoT-en DevOps-analyse werk belastingen.

[![Data Explorer](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

De meeste klanten komen overeen dat de minimale hoeveelheid tijd die nodig is om inzicht te krijgen, een van de belangrijkste-functies van Time Series Insights:

* Voor Time Series Insights is geen vooraf gegevens voorbereiding vereist.
* Het werkt snel om u te verbinden met miljarden gebeurtenissen in uw Azure IoT Hub-of Azure Event Hubs-instanties in enkele minuten.
* Zodra u verbinding hebt gemaakt, kunt u miljarden gebeurtenissen visualiseren en analyseren om afwijkingen op te sporen en verborgen trends in uw gegevens te ontdekken.

Time Series Insights is intuïtief en eenvoudig te gebruiken. U kunt met uw gegevens werken zonder ook maar één regel code te schrijven. Er is ook geen nieuwe taal die u nodig hebt om te leren, hoewel Time Series Insights een gedetailleerde Zoek taal op basis van tekst biedt voor ervaren gebruikers die bekend zijn met SQL. U kunt er ook voor kiezen om te verkennen.

Klanten kunnen profiteren van de snelheid voor het snel diagnosticeren van problemen met activa. Ze kunnen DevOps-analyses uitvoeren om de hoofd oorzaak van een bug in een IoT-oplossing te verkrijgen. Ze kunnen ook gebieden identificeren die moeten worden gemarkeerd voor nader onderzoek als onderdeel van hun data Science-initiatieven.

Er zijn drie manieren om te communiceren met gegevens die zijn opgeslagen in Time Series Insights:

* De eerste en eenvoudigste manier om aan de slag te gaan is met de Time Series Insights preview Explorer. U kunt deze gebruiken om snel al uw IoT-gegevens op één plek te visualiseren. Het bevat hulpprogram ma's zoals de heatmap waarmee u afwijkingen in uw gegevens kunt herkennen. Het biedt ook een perspectief weergave. Gebruik het om Maxi maal vier weer gaven te vergelijken van een of meer Time Series Insights omgevingen in één dash board. Het dash board geeft u een overzicht van de gegevens van uw tijd reeks op al uw locaties. Meer informatie over de [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md). Lees [Time Series Insights planning](./time-series-insights-update-plan.md)als u uw time series Insights omgeving wilt plannen.

* De tweede manier om te beginnen is met behulp van de Java script SDK om snel krachtige grafieken en grafieken in uw webtoepassing in te sluiten. Met slechts een paar regels code kunt u krachtige query's ontwerpen. Gebruik deze om lijn diagrammen, cirkel diagrammen, staaf diagrammen, heatmap, gegevens rasters en meer te vullen. Al deze elementen zijn out-of-the-box met behulp van de SDK. De SDK is ook abstracten Time Series Insights query-Api's. U kunt ze gebruiken om SQL-achtige predikaten te schrijven om de gegevens op te vragen die u op een dash board wilt weer geven. Time Series Insights biedt voor hybride oplossingen voor presentaties met para meters een URL. Ze bieden naadloze verbindings punten met de Time Series Insights preview Explorer voor diepe Dives in gegevens.

  * Lees meer informatie over de [Time Series INSIGHTS js-client bibliotheek](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) en de [Time Series Insights-client](https://github.com/Microsoft/tsiclient) documentatie voor meer informatie over de Java script SDK.

  * Meer informatie over het delen van Url's en de nieuwe gebruikers interface vindt u [in visualiseren van gegevens in de Azure time series Insights preview Explorer](time-series-insights-update-explorer.md).

* De derde manier om te beginnen is het gebruik van de krachtige Api's om query's uit te voeren op gegevens die zijn opgeslagen in Time Series Insights. Time Series Insights heeft tijdelijke Opera tors zoals `from`, `to` `first`, en `last`. `average`Het bevat aggregaties en trans formaties zoals, `min` `max` `split by` `order by`,,, en. `DateHistogram` Er zijn `has`ook filter operatoren, zoals, `in` `and` `or` `greater than`,,, en. `REGEX` Al deze opera tors bieden downstream-toepassingen de mogelijkheid om snel interessante trends en patronen in uw gegevens te vinden. Gebruik ze om Homegrown-visualisaties te vullen om afwijkingen op te sporen.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operationele analyse en efficiëntie van het proces

Gebruik Time Series Insights om de status, het gebruik en de prestaties van apparatuur op schaal te bewaken. Time Series Insights biedt een eenvoudige manier om de operationele efficiëntie te meten. Time Series Insights helpt bij het beheren van uiteenlopende en onvoorspelbare IoT-workloads zonder dat er opname-of query prestaties worden geoffert.

[![Overzicht](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming en doorlopende verwerking van gegevens die afkomstig zijn van operationele processen kunnen alle bedrijven omzetten als deze worden gecombineerd met de juiste technologie of oplossing. Vaak zijn deze oplossingen een combi natie van meerdere systemen. Ze maken het mogelijk om gegevens te verkennen en te analyseren die voortdurend worden gewijzigd, met name in de IoT-realm en een gemeen schappelijk patroon delen.

Deze patronen beginnen vaak met IoT-platformen die miljarden gebeurtenissen van apparaten en Sens oren die verschillende land instellingen omvatten. Deze systemen verwerken en analyseren streaminggegevens om real-time inzichten en acties af te leiden. Gegevens worden meestal opgeslagen in warme en koude opslag voor bijna realtime-en batch analyse.

Gegevens die worden verzameld, passeren een serie verwerkingen om deze op te schonen en waarmee voor downstream-query's en analyse scenario's. Azure biedt uitgebreide services die kunnen worden toegepast op IoT-scenario's zoals onderhoud en productie van activa. Deze services omvatten Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning en Power BI.

De oplossings architectuur kan op de volgende manier worden bereikt:

* Gegevens opnemen via IoT Hub of Event Hubs voor de beste beveiliging, door Voer en latentie.
* Gegevens verwerking en-berekeningen uitvoeren. Trechter opgenomen gegevens via Services als Stream Analytics, Logic Apps en Azure Functions. De service die u gebruikt, is afhankelijk van de specifieke behoeften voor gegevens verwerking.
* Berekende signalen van de verwerkings pijplijn worden naar Time Series Insights gepusht voor het opslaan en analyseren van gegevens.

Time Series Insights biedt een bijna realtime gegevens onderzoek en inzichten op basis van een activum via historische gegevens. Afhankelijk van de behoeften van uw bedrijf, kunnen MapReduce en Hive-taken worden uitgevoerd op gegevens die zijn opgeslagen in Time Series Insights door Time Series Insights te verbinden met Azure HDInsight. Gegevens die zijn opgeslagen in Time Series Insights, zijn beschikbaar voor Power BI en andere klant toepassingen via de Time Series Insights open bare Surface-query-Api's. Deze gegevens kunnen worden gebruikt voor uitgebreide bedrijfs-en operationele informatie scenario's.

## <a name="advanced-analytics"></a>Geavanceerde analyse

Integreer met geavanceerde analyse services zoals Machine Learning en Azure Databricks. Time Series Insights ingresses onbewerkte gegevens van miljoenen apparaten. Het voegt contextuele gegevens toe die naadloos kunnen worden gebruikt door een suite van Azure Analytics Services.

[![Analyse](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Geavanceerde analyses en machine learning grote hoeveel heden gegevens gebruiken en verwerken. Deze gegevens worden gebruikt om op gegevens gebaseerde beslissingen te nemen en voorspellende analyses uit te voeren. In IoT use cases leren geavanceerde analyse algoritmen van de gegevens die zijn verzameld van miljoenen apparaten. Met deze apparaten worden gegevens meerdere keren per seconde verzonden. De gegevens die zijn verzameld uit IoT-apparaten zijn onbewerkt. Er zijn geen contextuele informatie, zoals de locatie van het apparaat en de eenheid van het lezen van de sensor. Als gevolg hiervan zijn onbewerkte gegevens moeilijk te gebruiken voor geavanceerde analyses.

Time Series Insights is de kloof tussen IoT-gegevens en geavanceerde analyses op twee eenvoudige en rendabele manieren overbrugd:

* Eerst verzamelt Time Series Insights onbewerkte telemetriegegevens van miljoenen apparaten met behulp van IoT Hub. Het verrijkt gegevens met contextuele informatie en transformeert gegevens in een Parquet-indeling. Deze indeling kan eenvoudig worden geïntegreerd met andere geavanceerde analyse Services, zoals Machine Learning, Azure Databricks en toepassingen van derden.

    Time Series Insights kunnen fungeren als de bron van waarheid voor alle gegevens in een organisatie. Hiermee maakt u een centrale opslag plaats voor downstream Analytics-workloads die moeten worden gebruikt. Omdat Time Series Insights een nabije real-time Storage-service is, kunnen geavanceerde analyse modellen voortdurend van de inkomende IoT-telemetriegegevens worden leren. Als gevolg hiervan kunnen de modellen nauw keurige voor spellingen maken.

* Ten tweede kan de uitvoer van machine learning en Voorspellings modellen worden ingevoerd in Time Series Insights om hun resultaten te visualiseren en op te slaan. Deze procedure helpt organisaties om hun modellen te optimaliseren en te verfijnen. Time Series Insights maakt het eenvoudig om gegevensstromende telemetriegegevens op hetzelfde vlak te visualiseren als de getrainde model uitvoer. Op deze manier helpt IT bij het herkennen van problemen met gegevens wetenschappen teams en het identificeren van patronen.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Time Series Insights preview Explorer](./time-series-insights-update-explorer.md).
* Lees [Time Series Insights voorbeeld planning](./time-series-insights-update-plan.md) om uw omgeving te plannen.
* Lees de documentatie van de [Time Series Insights-client](https://github.com/Microsoft/tsiclient) .

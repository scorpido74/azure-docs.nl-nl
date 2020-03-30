---
title: Voorbeeld van use cases - Azure Time Series Insights | Microsoft Documenten
description: Meer informatie over de gebruiksvoorbeelden van voorbeelden van Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 50ac2a728750c6b01dfc57fa7e20df25c856395a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087387"
---
# <a name="azure-time-series-insights-preview-use-cases"></a>Use cases voor Azure Time Series Insights (preview)

In dit artikel worden verschillende veelvoorkomende use cases voor Azure Time Series Insights Preview samengevat. De aanbevelingen in dit artikel dienen als uitgangspunt om uw applicaties en oplossingen te ontwikkelen met Time Series Insights.

Concreet beantwoordt dit artikel de volgende vragen:

* Wat zijn de gangbare use cases voor Time Series Insights?
* Wat zijn de voordelen van het gebruik van Time Series Insights voor [data-exploratie en visuele anomaliedetectie?](#data-exploration-and-visual-anomaly-detection)
* Wat zijn de voordelen van het gebruik van Time Series Insights voor [operationele analyse en procesefficiëntie?](#operational-analysis-and-driving-process-efficiency)
* Wat zijn de voordelen van het gebruik van Time Series Insights voor [geavanceerde analyses?](#advanced-analytics)

Een overzicht van deze gebruiksscenario's wordt beschreven in de volgende secties.

## <a name="introduction"></a>Inleiding

Azure Time Series Insights is een end-to-end platform-as-a-service-aanbod. Het wordt gebruikt voor het verzamelen, verwerken, opslaan, analyseren en opvragen van sterk gecontextualiseerde, tijd-serie geoptimaliseerde IoT-schaal gegevens. Time Series Insights is ideaal voor ad-hoc gegevensverkenning en voor operationele analyse. Time Series Insights is een uniek uitbreidbaar, op maat gemaakt serviceaanbod dat voldoet aan de brede behoeften van industriële IoT-implementaties.

## <a name="data-exploration-and-visual-anomaly-detection"></a>Gegevensverkenning en detectie van visuele anomalieën

Verken en analyseer onmiddellijk miljarden gebeurtenissen om afwijkingen te herkennen en verborgen trends in uw gegevens te ontdekken. Time Series Insights levert bijna realtime prestaties voor uw IoT- en DevOps-analyseworkloads.

[![Gegevensverkenner](media/v2-update-use-cases/data-explorer.png)](media/v2-update-use-cases/data-explorer.png#lightbox)

De meeste klanten zijn het erover eens dat de minimale hoeveelheid tijd die nodig is om inzicht te krijgen een van de opvallende kenmerken van Time Series Insights is:

* Time Series Insights vereist geen voorbereiding van gegevens vooraf. 
* Het werkt snel om u binnen enkele minuten te verbinden met miljarden gebeurtenissen in uw Azure IoT Hub- of Azure Event Hubs-exemplaren. 
* Eenmaal verbonden, u miljarden gebeurtenissen visualiseren en analyseren om afwijkingen te herkennen en verborgen trends in uw gegevens te ontdekken.

Time Series Insights is intuïtief en eenvoudig te gebruiken. U communiceren met uw gegevens zonder één regel code te schrijven. Er is ook geen nieuwe taal die u moet leren, hoewel Time Series Insights een gedetailleerde op tekst gebaseerde querytaal biedt voor gevorderde gebruikers die bekend zijn met SQL. Het biedt ook select-and-click exploratie voor beginners.

Klanten kunnen profiteren van de snelheid om asset-gerelateerde problemen snel te diagnosticeren. Ze kunnen DevOps-analyse uitvoeren om bij de oorzaak van een bug in een IoT-oplossing te komen. Ze kunnen ook gebieden identificeren om te markeren voor verder onderzoek als onderdeel van hun data science-initiatieven. 

Er zijn drie primaire manieren om te communiceren met gegevens die zijn opgeslagen in Time Series Insights:

* De eerste en eenvoudigste manier om aan de slag te gaan is met de Time Series Insights Preview-verkenner. U het gebruiken om al uw IoT-gegevens snel op één plek te visualiseren. Het biedt tools zoals de heat map om u te helpen afwijkingen in uw gegevens te herkennen. Het biedt ook een perspectief. Gebruik het om maximaal vier weergaven van een of meer Time Series Insights-omgevingen in één dashboard te vergelijken. Het dashboard geeft u een overzicht van uw tijdreeksgegevens op al uw locaties. Meer informatie over de [Time Series Insights Preview explorer](./time-series-insights-update-explorer.md). Als u uw Time Series Insights-omgeving wilt plannen, leest u [Time Series Insights-planning](./time-series-insights-update-plan.md).

* De tweede manier om te beginnen is om de JavaScript SDK te gebruiken om snel krachtige grafieken en grafieken in uw webtoepassing in te sluiten. Met slechts een paar regels code u krachtige query's schrijven. Gebruik ze om lijndiagrammen, cirkeldiagrammen, staafdiagrammen, heatmaps, gegevensrasters en meer in te vullen. Al deze elementen bestaan out-of-the-box met behulp van de SDK. De SDK abstraheert ook Time Series Insights query API's. U ze gebruiken om SQL-achtige predicaten te schrijven om de gegevens op te vragen die u op een dashboard wilt weergeven. Voor hybride presentatielaagoplossingen biedt Time Series Insights geparameteriseerde URL's. Ze bieden naadloze verbindingspunten met de Time Series Insights Preview-verkenner voor diepe duiken in gegevens.

  * Lees meer over de [Time Series Insights JS-clientbibliotheek](https://github.com/microsoft/tsiclient/blob/master/docs/API.md) en de [clientdocumentatie timeseries Insights](https://github.com/Microsoft/tsiclient) voor meer informatie over de JavaScript SDK.

  * Meer informatie over het delen van URL's en de nieuwe gebruikersinterface door [visualiserende gegevens te bekijken in de Azure Time Series Insights Preview-verkenner](time-series-insights-update-explorer.md).

* De derde manier om te beginnen is door de krachtige API's te gebruiken om gegevens op te vragen die zijn opgeslagen in Time Series Insights. Time Series Insights heeft `from`tijdelijke `to` `first`operatoren `last`zoals , , en . Het heeft aggregaties `average`en `min` `max`transformaties `order by`zoals `DateHistogram`, , , `split by`, en . Het heeft ook filteroperatoren `has`zoals `or` `greater than`, `REGEX`, `in` `and`, , en . Al deze operatoren stellen downstream-toepassingen in staat om snel interessante trends en patronen in uw gegevens te vinden. Gebruik ze om visualisaties van eigen bodem te vullen om afwijkingen te detecteren.

## <a name="operational-analysis-and-driving-process-efficiency"></a>Operationele analyse en procesefficiëntie

Gebruik Time Series Insights om de gezondheid, het gebruik en de prestaties van apparatuur op schaal te controleren. Time Series Insights biedt een eenvoudige manier om operationele efficiëntie te meten. Time Series Insights helpt bij het beheren van diverse en onvoorspelbare IoT-workloads zonder inteboet aan opname- of queryprestaties.

[![Overzicht](media/v2-update-use-cases/overview.png)](media/v2-update-use-cases/overview.png#lightbox)

Streaming en continue verwerking van gegevens afkomstig van operationele processen kunnen elk bedrijf succesvol transformeren als het wordt gecombineerd met de juiste technologie of oplossing. Vaak zijn deze oplossingen een combinatie van meerdere systemen. Ze maken verkenning en analyse van gegevens mogelijk die voortdurend veranderen, vooral in het IoT-gebied, en delen een gemeenschappelijk patroon.

Deze patronen beginnen vaak met IoT-platforms die miljarden gebeurtenissen innemen van apparaten en sensoren die verschillende locaties omvatten. Deze systemen verwerken en analyseren streaming gegevens om real-time inzichten en acties af te leiden. Gegevens worden meestal gearchiveerd naar warm en koelhuis voor bijna real-time en batch-analyses.

Gegevens die worden verzameld, doorlopen een reeks verwerkingen om deze te reinigen en te contextualiseren voor downstream query-en analysescenario's. Azure biedt uitgebreide services die kunnen worden toegepast op IoT-scenario's, zoals onderhoud van activa en productie. Deze services omvatten Time Series Insights, IoT Hub, Event Hubs, Azure Stream Analytics, Azure Functions, Azure Logic Apps, Azure Databricks, Azure Machine Learning en Power BI.

Oplossingsarchitectuur kan op de volgende manier worden bereikt:

* Gegevens opnemen via IoT Hub of Event Hubs voor de beste beveiliging, doorvoer en latentie.
* Gegevensverwerking en -berekeningen uitvoeren. Trechter gegevens ingenomen via services zoals Stream Analytics, Logic Apps en Azure Functions. De service die u gebruikt, is afhankelijk van de specifieke behoeften op het niveau van gegevensverwerking.
* Berekende signalen uit de verwerkingspijplijn worden naar Time Series Insights gepusht voor opslag en analyse.

Time Series Insights biedt bijna realtime gegevensverkenning en op activa gebaseerde inzichten over historische gegevens. Afhankelijk van uw bedrijfsbehoeften kunnen MapReduce- en Hive-taken worden uitgevoerd op gegevens die zijn opgeslagen in Time Series Insights door Time Series Insights te verbinden met Azure HDInsight. Gegevens die zijn opgeslagen in Time Series Insights zijn beschikbaar voor Power BI en andere toepassingen van klanten via de TIME Series Insights public surface query API's. Deze gegevens kunnen worden gebruikt voor diepgaande business en operationele intelligentie scenario's.

## <a name="advanced-analytics"></a>Geavanceerde analyse

Integreer met geavanceerde analyseservices zoals Machine Learning en Azure Databricks. Time Series Insights geeft geen ruwe gegevens van miljoenen apparaten. Het voegt contextuele gegevens toe die naadloos kunnen worden verbruikt door een reeks Azure-analyseservices.

[![Analytics](media/v2-update-use-cases/advanced-analytics.png)](media/v2-update-use-cases/advanced-analytics.png#lightbox)

Geavanceerde analyses en machine learning verbruiken en verwerken grote hoeveelheden gegevens. Deze gegevens worden gebruikt om datagestuurde beslissingen te nemen en voorspellende analyses uit te voeren. In IoT-gebruiksvoorbeelden leren geavanceerde analysealgoritmen van de gegevens die van miljoenen apparaten worden verzameld. Deze apparaten verzenden gegevens meerdere keren per seconde. De gegevens die van IoT-apparaten worden verzameld, zijn rauw. Het ontbreekt aan contextuele informatie, zoals de locatie van het apparaat en de eenheid van de sensor lezen. Als gevolg hiervan is het moeilijk om ruwe gegevens rechtstreeks te gebruiken voor geavanceerde analyses.

Time Series Insights overbrugt de kloof tussen IoT-gegevens en geavanceerde analyses op twee eenvoudige en kosteneffectieve manieren:

* Ten eerste verzamelt Time Series Insights ruwe telemetriegegevens van miljoenen apparaten met behulp van IoT Hub. Het verrijkt gegevens met contextuele informatie en transformeert gegevens in een parketformaat. Deze indeling kan eenvoudig worden geïntegreerd met andere geavanceerde analyseservices, zoals Machine Learning, Azure Databricks en toepassingen van derden.

    Time Series Insights kan dienen als de bron van de waarheid voor alle gegevens in een organisatie. Het creëert een centrale opslagplaats voor downstream analytics-workloads om te verbruiken. Omdat Time Series Insights een bijna realtime opslagservice is, kunnen geavanceerde analysemodellen continu leren van binnenkomende IoT-telemetriegegevens. Als gevolg hiervan kunnen de modellen nauwkeurigere voorspellingen doen.

* Ten tweede kan de output van machine learning- en voorspellingsmodellen worden ingevoerd in Time Series Insights om hun resultaten te visualiseren en op te slaan. Deze procedure helpt organisaties om hun modellen te optimaliseren en aan te passen. Time Series Insights maakt het eenvoudig om streaming telemetriegegevens te visualiseren op hetzelfde vlak als de getrainde modeluitgangen. Op deze manier helpt het data science-teams afwijkingen te herkennen en patronen te identificeren.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [Time Series Insights Preview explorer](./time-series-insights-update-explorer.md).
* Lees [de planning van Time Series Insights Om](./time-series-insights-update-plan.md) uw omgeving te plannen.
* Lees de [clientdocumentatie van Time Series Insights.](https://github.com/Microsoft/tsiclient)

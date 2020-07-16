---
title: 'Overzicht: Wat is Azure Time Series Insights preview? - Azure Time Series Insights | Microsoft Docs'
description: Meer informatie over wijzigingen, verbeteringen en functies in van Azure Time Series Insights (preview).
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 5e8ea56de84f3d6b92bc23c0e3ab139d03bd68a9
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232250"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Wat is Azure Time Series Insights preview?

Azure Time Series Insights (preview) biedt een end-to-end-PaaS (Platform as a Service). U kunt dit gebruiken om gegevens te verzamelen, te verwerken, op te slaan, te analyseren en er een query op uit te voeren op de schaal van Internet of Things (IoT): gegevens die in hoge mate in context zijn geplaatst en geoptimaliseerd voor tijdreeksen. 

Time Series Insights is ontworpen voor ad-hocgegevensverkenning en operationele analyse. Het is een uitbreidbare, aangepaste service die voldoet aan de algemene vereisten voor industriële IoT-implementaties.

## <a name="video"></a>Video

Meer informatie over Azure Time Series Insights (preview).

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definitie van IoT-gegevens

Industriële IoT-gegevens in asset-intensieve organisaties ontbreekt het vaak aan structurele consistentie vanwege het gevarieerde karakter van apparaten en sensoren in een industriële setting. Gegevens die van deze stromen afkomstig zijn, worden gekenmerkt door aanzienlijke hiaten en soms beschadigde berichten en verkeerde metingen. IoT-gegevens zijn vaak zinvol in de context van de invoer van aanvullende gegevens die afkomstig zijn van eigen of externe bronnen, zoals CRM en ERP, die context toevoegen voor end-to-end werkstromen. Invoer van externe gegevensbronnen, zoals weergegevens, kunnen de telemetriestromen in een bepaalde installatie helpen verbeteren. 

Dit houdt in dat slechts een fractie van de gegevens voor operationele en zakelijke doeleinden wordt gebruikt, en dat voor de analyse meer context nodig is. Industriële gegevens worden voor een diepgaande analyse vaak over een lange tijdspanne historisch gemaakt om trends te begrijpen en te correleren. Voor het omzetten van verzamelde IoT-gegevens in bruikbare inzichten zijn de volgende zaken nodig: 

* Gegevensverwerking voor het opschonen, filteren, interpoleren, transformeren en voorbereiden van gegevens voor analyse.
* Een structuur om doorheen te navigeren en de gegevens te begrijpen (om de gegevens te normaliseren en context te geven).
* Voordelige opslag voor lange/oneindige bewaarperioden aan verwerkte (of afgeleide) gegevens, alsmede onbewerkte gegevens.

Deze gegevens bieden consistente, uitgebreide, actuele en juiste informatie voor zakelijke rapportage en analyse.

In de volgende afbeelding wordt een typische IoT-gegevensstroom getoond.

[![IoT-gegevensstroom](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights voor industriële IoT

Het IoT-landschap bestaat uit diverse klanten uit een groot aantal verschillende takken van industrie, waaronder de productie-industrie, auto-industrie, energiebedrijven, nutsbedrijven, slimme gebouwen en consulting. In dit brede scala aan industriële IoT-markten zijn cloudeigen oplossingen die uitgebreide analyses bieden voor grootschalige IoT-gegevens nog steeds in ontwikkeling. 

Azure Time Series Insights voorziet in deze marktbehoefte door een kant-en-klare, end-to-end, analytische IoT-oplossing te bieden met rijke semantische modellering voor contextualisatie van tijdreeksgegevens, op assets gebaseerde inzichten en een optimale gebruikerservaring voor detectie, trending, anomaliedetectie en operationele intelligentie. 

Dankzij een uitgebreid, operationeel analyseplatform met de mogelijkheid van interactieve gegevensverkenning stelt Time Series Insights u in staat meer waardevolle informatie af te leiden uit gegevens die verzameld zijn van IoT-assets. De preview-versie biedt ondersteuning voor: 

* Een meerlaags opslagmogelijkheid met 'warme' en 'koude' analytische ondersteuning die klanten de mogelijkheid biedt om gegevens te routeren tussen 'warm' en 'koud' voor interactieve analyse over warme gegevens en operationele intelligentie over decennia aan historische gegevens. 

    *    Een zeer interactieve methode voor 'warme' analyse om veelvuldig grote hoeveel heden query's te verrichten over kortere tijdspannen 
    *    Een data lake aan tijdreeksen in Azure Storage die schaalbaar is, uitstekend functioneert en kostenbesparend is, waardoor klanten binnen enkele seconden de trend kunnen zien uit jaren aan tijdreeksgegevens. 

* Ondersteuning van semantische modellen voor het beschrijven van het domein en de metagegevens die gekoppeld zijn aan de afgeleide en onbewerkte signalen van assets en apparaten.

* Flexibel analyseplatform voor het opslaan van historische tijdreeksgegevens in het Azure Storage-account van de klant, waardoor klanten eigenaar kunnen zijn van hun IoT-gegevens. Gegevens worden opgeslagen in een opensource-indeling van Apache Parquet die connectiviteit en samenwerking mogelijk maakt in diverse gegevensscenario's, waaronder predictive analytics, machine learning en andere aangepaste berekeningen, die worden uitgevoerd met behulp van bekende technologieën, waaronder Spark, Databricks en Jupyter.

* Uitgebreide analyse met verbeterde query-API's en gebruikerservaring, waarbij gegevensinzichten op basis van assets worden gecombineerd met uitgebreide, ad-hocgegevensanalyse met ondersteuning voor interpolatie, scalaire en statistische functies, categorische variabelen, spreidingsgrafieken en in de tijd verschuivende tijdreeksen voor uitgebreide analyse.

*    Platform voor grote ondernemingen ter ondersteuning van de behoeften aan schaalvergroting, betere prestaties, beveiliging en betrouwbaarheid van onze IoT-klanten.

* Ondersteuning van de uitbreidbaarheid en integratie voor end-to-end-analyse. Time Series Insights biedt een uitbreidbaar analyseplatform voor diverse gegevensscenario's. Met de Time Series Insights Power BI-connector kunnen klanten de query's die ze in Time Series Insights uitvoeren rechtstreeks in Power BI invoeren om een uniforme weergave van hun BI- en tijdreeksanalyses op één plek te integreren.

In dit diagram op wordt de gegevensstroom op hoog niveau getoond.

  [![Belangrijkste mogelijkheden](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights biedt een schaalbaar prijsmodel voor betalen per gebruik voor gegevensverwerking, gegevensopslag (gegevens en metagegevens) en het uitvoeren van query's op gegevens, zodat klanten hun gebruik kunnen afstemmen op hun bedrijfsbehoeften. 
 
Met de introductie van deze belangrijke, industriële IoT-functionaliteiten biedt Time Series Insights tevens de volgende belangrijke voordelen.  

| Mogelijkheid | Voordeel |
| ---| ---|
| Opslag met meerdere lagen voor tijdreeksgegevens op IoT-schaal | Met een pijplijn voor de verwerking van gedeelde gegevens voor het opnemen van gegevens kunt u gegevens opnemen in zowel warme als koude archieven. Gebruik warme opslag voor interactieve query's en koude opslag voor het opslaan van grote hoeveelheden gegevens. Zie [query's](./concepts-query-overview.md) als u wilt profiteren van hoogwaardige, op assets gebaseerde query's. |
| Time Series-model voor het contextualiseren van onbewerkte telemetriegegevens en het afleiden van inzichten op basis van assets | U kunt het tijdreeksmodel gebruiken om instanties, hiërarchieën, typen en variabelen voor uw tijdreeksgegevens te maken. Zie [Time Series-model](./concepts-model-overview.md) voor meer informatie over het Time Series-model.  |
| Probleemloze en continue integratie met andere gegevensoplossingen | Gegevens in de koude opslag van Time Series Insights worden [opgeslagen](concepts-storage.md) in opensource Apache Parquet-bestanden. Dit maakt gegevensintegratie mogelijk met andere gegevensoplossingen (intern of extern), voor scenario's met bijvoorbeeld business intelligence, geavanceerde machine learning en predictive analytics. |
| Gegevens worden bijna in realtime verkend | De [verkenner van Azure Time Series Insights (preview)](./time-series-insights-update-explorer.md) biedt gebruikers visualisatie voor alle gegevens die door de opnamepijplijn worden geleid. Nadat u verbinding hebt gemaakt met een gebeurtenisbron, kunt u gebeurtenisgegevens bekijken, verkennen en er query's op uitvoeren. Op deze manier kunt u nagaan of een apparaat gegevens naar verwachting verzendt. U kunt IoT-apparaten ook controleren op status, productiviteit en algehele effectiviteit. | 
| Uitbreidbaarheid en integratie | De integratie van Azure Time Series Insights met de Power BI-connector is via de optie **Exporteren** rechtstreeks beschikbaar in de Time Series Explorer-gebruikerservaring. Hierdoor kunnen klanten de tijdreeksquery's die ze in onze gebruikerservaring maken, rechtstreeks naar het Power BI-bureaublad exporteren en de tijdreeksgrafieken naast andere BI Analytics weergeven. Hiermee wordt een nieuwe klasse van scenario's mogelijk voor industriële IoT-ondernemingen die in Power BI hebben geïnvesteerd door in plaats van analyses afkomstig van verschillende gegevensbronnen één plek te bieden waar alles geïntegreerd is, waaronder IoT-tijdreeksen. | 
| Aangepaste toepassingen gebouwd op het Time Series Insights-platform | Time Series Insights biedt ondersteuning voor de [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). De SDK biedt uitgebreide besturingselementen en vereenvoudigde toegang tot query's. Gebruik de SDK voor het bouwen van aangepaste IoT-toepassingen op basis van Time Series Insights, die aansluiten op uw zakelijke behoeften. U kunt de [query-API's](./concepts-query-overview.md) van Time Series Insights ook rechtstreeks gebruiken om gegevens naar aangepaste IoT-toepassingen te sturen. |

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Azure Time Series Insights (preview):

> [!div class="nextstepaction"]
> [Snelstartgids](./time-series-insights-update-quickstart.md)

Informatie over use cases:

> [!div class="nextstepaction"]
> [Use cases voor Azure Time Series Insights (preview)](./time-series-insights-update-use-cases.md)
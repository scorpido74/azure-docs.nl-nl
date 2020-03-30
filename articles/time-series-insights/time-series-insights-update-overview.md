---
title: 'Overzicht: Wat is De Voorvertoning van Inzichten van Azure Time Series? - Azure Time Series Insights | Microsoft Documenten'
description: Meer informatie over wijzigingen, verbeteringen en functies in Azure Time Series Insights Preview.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 02/04/2020
ms.custom: seodec18
ms.openlocfilehash: 1e4f72300752c93659db4edd4610464dbebf2503
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77014416"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Wat is Azure Time Series Insights preview?

Azure Time Series Insights Preview is een end-to-end platform-as-a-service (PaaS) aanbod. U het gebruiken voor het verzamelen, verwerken, opslaan, analyseren en querygegevens op Internet of Things (IoT) schaal - gegevens die sterk gecontextualiseerd en geoptimaliseerd voor tijdreeksen. 

Time Series Insights is ontworpen voor ad hoc data-exploratie en operationele analyse. Het is een uitbreidbaar en op maat gemaakt serviceaanbod dat voldoet aan de brede behoeften van industriële IoT-implementaties.

## <a name="video"></a>Video

Meer informatie over Azure Time Series Insights Preview.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definitie van IoT-gegevens

Industriële IoT-gegevens in asset-intensieve organisaties missen vaak structurele consistentie vanwege het gevarieerde karakter van apparaten en sensoren in een industriële omgeving. Gegevens uit deze stromen worden gekenmerkt door aanzienlijke hiaten, en soms beschadigde berichten en valse metingen. IoT-gegevens zijn vaak zinvol in de context van aanvullende gegevensinvoer die afkomstig zijn van eerste of derde bronnen, zoals CRM of ERP die context toevoegen voor end-to-end workflows. Ingangen van gegevensbronnen van derden, zoals weergegevens, kunnen helpen bij het vergroten van telemetriestromen in een bepaalde installatie. 

Dit alles impliceert dat slechts een fractie van de gegevens wordt gebruikt voor operationele en zakelijke doeleinden, en analyse vereist contextualisatie. Industriële gegevens worden vaak gehistoriciseerd voor diepgaande analyse over langere tijdspannes om trends te begrijpen en te correleren. Voor het omzetten van verzamelde IoT-gegevens in bruikbare inzichten zijn de volgende zaken nodig: 

* Gegevensverwerking voor het opschonen, filteren, interpoleren, transformeren en voorbereiden van gegevens voor analyse.
* Een structuur om door de gegevens te navigeren en te begrijpen, dat wil zeggen om de gegevens te normaliseren en te contextualiseren.
* Kosteneffectieve opslag voor lange of oneindige bewaring van verwerkte (of afgeleide) gegevens en ruwe gegevens.

Dergelijke gegevens bieden consistente, uitgebreide, actuele en correcte informatie voor bedrijfsanalyse en -rapportage.

De volgende afbeelding toont een typische IoT-gegevensstroom.

[![IoT-gegevensstroom](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights voor industriële IoT

Het IoT-landschap is divers met klanten verspreid over verschillende industriesegmenten, waaronder productie, automotive, energie, nutsbedrijven, slimme gebouwen en consulting. In dit brede scala aan industriële IoT-markt zijn cloud-native oplossingen die uitgebreide analyses bieden die gericht zijn op grootschalige IoT-gegevens nog steeds in ontwikkeling. 

Azure Time Series Insights speelt in op deze marktbehoefte door een kant-en-klare IoT-analyseoplossing te bieden met rijke semantische modellering voor contextualisatie van tijdreeksgegevens, op activa gebaseerde inzichten en de beste gebruikerservaring voor detectie, trending, anomaliedetectie en operationele intelligentie. 

Een rijk operationeel analyseplatform in combinatie met onze interactieve mogelijkheden voor gegevensverkenning, u Time Series Insights gebruiken om meer waarde te halen uit gegevens die zijn verzameld uit IoT-assets. De preview-versie biedt ondersteuning voor: 

* Multi-layered storage oplossing met warme en koude analytics ondersteuning die klanten de mogelijkheid biedt om gegevens te routeren tussen warm en koud voor interactieve analyses via warme data en operationele intelligentie gedurende tientallen jaren historische gegevens. 

    *   Een zeer interactieve oplossing voor warme analyses om frequente en grote hoeveelheden query's uit te voeren over kortere tijdspannegegevens 
    *   Een schaalbaar, performant en kostengeoptimaliseerd e-outdatalake op basis van Azure Storage, zodat klanten in enkele seconden tijdreeksgegevens kunnen trendjaren. 

* Ondersteuning voor het semantische model dat het domein en de metagegevens beschrijft die zijn gekoppeld aan de afgeleide en ruwe signalen van assets en apparaten.

* Flexibel analyseplatform om historische tijdreeksgegevens op te slaan in het Azure Storage-account dat eigendom is van klanten, waardoor klanten eigenaar kunnen worden van hun IoT-gegevens. Gegevens worden opgeslagen in open source Apache Parket-indeling die connectiviteit en interop mogelijk maakt in een verscheidenheid van datascenario's, waaronder voorspellende analyses, machine learning en andere aangepaste berekeningen gedaan met behulp van bekende technologieën, waaronder Spark, Databricks en Jupyter.

* Uitgebreide analyses met verbeterde query-API's en gebruikerservaring die op activa gebaseerde gegevensinzichten combineert met uitgebreide, ad hoc gegevensanalyses met ondersteuning voor interpolatie, scalaire en geaggregeerde functies, categorische variabelen, spreidingsplots en tijdsverschuivende tijd seriesignalen voor diepgaande analyse.

*   Enterprise grade-platform ter ondersteuning van de behoeften op het gebied van schaal, prestaties, beveiliging en betrouwbaarheid van onze zakelijke IoT-klanten.

* Extensibility en integratie ondersteuning voor end-to-end analytics. Time Series Insights biedt een uitbreidbaar analyseplatform voor verschillende datascenario's. Met De Power BI-connector van Time Series Insights kunnen klanten de query's die ze in Time Series Insights uitvoeren, rechtstreeks naar Power BI brengen om een uniform overzicht te krijgen van hun BI- en tijdreeksanalyses in één ruit.

In het volgende diagram ziet u de gegevensstroom op hoog niveau.

  [![Belangrijkste mogelijkheden](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights biedt een schaalbaar pay-as-you-go-prijsmodel voor gegevensverwerking, opslag (gegevens en metagegevens) en query' s, zodat klanten hun gebruik kunnen afstemmen op hun bedrijfsbehoeften. 
 
Met de introductie van deze belangrijke industriële IoT-mogelijkheden biedt Time Series Insights ook de volgende belangrijke voordelen.  

| | |
| ---| ---|
| Meerlaagse opslag voor gegevens over tijdreeksen met IoT-schaal | Met een gedeelde gegevensverwerkingspijplijn voor het innemen van gegevens, u gegevens opnemen in zowel warme als koude winkels. Gebruik warme winkel voor interactieve query's en koelhuis voor het opslaan van grote hoeveelheden gegevens. Zie [query's](./time-series-insights-update-tsq.md)voor meer informatie over hoe u profiteren van hoogpresterende query's op basis van activa. |
| Time Series-model voor het contextualiseren van onbewerkte telemetriegegevens en het afleiden van inzichten op basis van assets | U het tijdreeksmodel gebruiken om instanties, hiërarchieën, typen en variabelen voor uw tijdreeksgegevens te maken. Zie Time Series Model voor meer informatie over timeseries [model.](./time-series-insights-update-tsm.md)  |
| Probleemloze en continue integratie met andere gegevensoplossingen | Data in Time Series Insights cold store wordt [opgeslagen](./time-series-insights-update-storage-ingress.md) in open-source Apache Parket bestanden. Dit maakt gegevensintegratie met andere gegevensoplossingen mogelijk, 1e of 3rd party, voor scenario's zoals business intelligence, geavanceerde machine learning en voorspellende analyses. |
| Gegevens worden bijna in realtime verkend | De [verkenner van Azure Time Series Insights (preview)](./time-series-insights-update-explorer.md) biedt gebruikers visualisatie voor alle gegevens die door de opnamepijplijn worden geleid. Nadat u een gebeurtenisbron hebt verbonden, u gebeurtenisgegevens bekijken, verkennen en opvragen. Op deze manier kunt u nagaan of een apparaat gegevens naar verwachting verzendt. U kunt IoT-apparaten ook controleren op status, productiviteit en algehele effectiviteit. | 
| Uitbreidbaarheid en integratie | De Azure Time Series Insights Power BI Connector-integratie is rechtstreeks beschikbaar in de gebruikerservaring van Time Series Explorer via de optie **Exporteren,** zodat klanten de tijdreeksquery's die ze in onze gebruikerservaring maken rechtstreeks naar het Power BI-bureaublad kunnen exporteren en hun tijdreeksgrafieken naast andere BI-analyses kunnen bekijken. Dit opent de deur naar een nieuwe klasse van scenario's voor industriële IoT-ondernemingen die hebben geïnvesteerd in Power BI door het verstrekken van een enkel glas over analytics uit verschillende gegevensbronnen, waaronder IoT-tijdreeksen. | 
| Aangepaste toepassingen gebouwd op het Time Series Insights-platform | Time Series Insights biedt ondersteuning voor de [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). De SDK biedt uitgebreide besturingselementen en vereenvoudigde toegang tot query's. Gebruik de SDK om aangepaste IoT-toepassingen te bouwen bovenop Time Series Insights die aan uw bedrijfsbehoeften voldoen. U kunt de [query-API's](./time-series-insights-update-tsq.md) van Time Series Insights ook rechtstreeks gebruiken om gegevens naar aangepaste IoT-toepassingen te sturen. |

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Quickstart](./time-series-insights-update-quickstart.md)

Informatie over use cases:

> [!div class="nextstepaction"]
> [Use cases voor Azure Time Series Insights (preview)](./time-series-insights-update-use-cases.md)
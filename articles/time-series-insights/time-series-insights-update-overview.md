---
title: 'Overzicht: Azure Time Series Insights preview | Microsoft Docs'
description: Overzicht van Azure Time Series Insights (preview).
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 05f4c64adad184e761e2b5a01b8c033c024dbc49
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73585256"
---
# <a name="what-is-azure-time-series-insights-preview"></a>Wat is Azure Time Series Insights preview?

Azure Time Series Insights preview is een end-to-end PaaS-aanbieding (platform-as-a-Service). U kunt dit gebruiken om gegevens te verzamelen, verwerken, op te slaan, te analyseren en op te vragen bij Internet of Things (IoT)-schaal: gegevens die zeer worden begrensd en geoptimaliseerd voor tijd reeksen. 

Time Series Insights is ontworpen voor het verkennen van ad hoc gegevens en operationele analyses. Het is een uitbreid bare en aangepaste service aanbieding die voldoet aan de uitgebreide behoeften van industriële IoT-implementaties.

## <a name="video"></a>Video

Meer informatie over Azure Time Series Insights preview.

> [!VIDEO https://channel9.msdn.com/Shows/Internet-of-Things-Show/Azure-Time-Series-Insights-e2e-solution-for-industrial-IoT-analytics/player]

## <a name="definition-of-iot-data"></a>Definitie van IoT-gegevens

Industriële IoT-gegevens in Asset-intensieve organisaties hebben vaak geen structurele consistentie als gevolg van de verschillende aard van apparaten en Sens oren in een industriële instelling. Gegevens uit deze stromen worden gekenmerkt door grote leemtes en soms beschadigde berichten en onwaare leesingen. IoT-gegevens zijn vaak zinvol in de context van extra invoer gegevens die afkomstig zijn van de eerste of derde bron, zoals CRM of ERP die context toevoegen aan end-to-end werk stromen. Invoer van gegevens bronnen van derden, zoals weer gegevens, kan helpen bij het uitbreiden van telemetriegegevens in een bepaalde installatie. 

Dit houdt in dat slechts een fractie van de gegevens wordt gebruikt voor operationele en zakelijke doel einden, en dat de analyse contextualization vereist. Industriële gegevens worden vaak historisch uitgedrukt op diep gaande analyses over langere tijd om trends te begrijpen en te correleren. Voor het omzetten van verzamelde IoT-gegevens in bruikbare inzichten zijn de volgende zaken nodig: 

* Gegevensverwerking voor het opschonen, filteren, interpoleren, transformeren en voorbereiden van gegevens voor analyse.
* Een structuur om te navigeren en inzicht te krijgen in de gegevens, dat wil zeggen, voor het normaliseren en waarmee van de gegevens.
* Rendabele opslag voor een lange of oneindige retentie van verwerkte (of afgeleide) gegevens en onbewerkte gegevens.

Dergelijke gegevens bieden consistente, allesomvattende, actuele en correcte informatie voor bedrijfs analyse en-rapportage.

In de volgende afbeelding ziet u een typische IoT-gegevens stroom.

[IoT-gegevens stroom ![](media/v2-update-overview/overview-one.png)](media/v2-update-overview/overview-one.png#lightbox)

## <a name="azure-time-series-insights-for-industrial-iot"></a>Azure Time Series Insights voor industriële IoT

IoT liggend is gevarieerder met klanten die een groot aantal branche segmenten omspannen, waaronder productie, automobiel, energie, NUTS bedrijven, Smart-gebouwen en Consulting. In dit brede assortiment van industriële IoT-markt kunnen Cloud-systeem eigen oplossingen die uitgebreide analyse gericht op grootschalige IoT-gegevens, nog steeds verder in ontwikkeling zijn. 

Azure Time Series Insights is gericht op deze markt, door een kant-en-klare oplossing voor IoT Analytics te bieden met een rijke semantische model lering voor contextualization van tijd reeksen, op asset gebaseerde inzichten en de beste ervaring op het niveau van de gebruiker voor detectie, trending, anomalie detectie en operationele informatie. 

Een uitgebreid operationeel analyse platform in combi natie met onze interactieve mogelijkheden voor het verkennen van gegevens kunt u Time Series Insights gebruiken om meer waarde te verkrijgen ten opzichte van de gegevens die zijn verzameld uit IoT-assets. De preview-versie biedt ondersteuning voor: 

* Multi-layered opslag oplossing met warme en koud Analytics ondersteunt klanten de mogelijkheid om gegevens te routeren tussen warme en koude voor interactieve analyses via warme gegevens en operationele informatie over tien tallen historische gegevens. 

    *   Een zeer interactieve oplossing voor warme analyse om veelvuldig en grote hoeveel heden query's te verrichten in korte tijd gegevens 
    *   Een schaal bare, krachtige en kosten geoptimaliseerde time series data Lake op basis van Azure Storage om klanten te laten werken tot trend jaren in enkele seconden. 

* Ondersteuning van semantisch model waarin het domein en de meta gegevens worden beschreven die zijn gekoppeld aan de afgeleide en onbewerkte signalen van assets en apparaten.

* Flexibel analyse platform voor het opslaan van historische tijdreeks gegevens in het Azure Storage-account van de klant, waardoor klanten eigenaar kunnen zijn van hun IoT-gegevens. Gegevens worden opgeslagen in een open source Apache Parquet-indeling die connectiviteit en Interop mogelijk maakt in diverse gegevens scenario's, waaronder predictive analytics, machine learning en andere aangepaste berekeningen die worden uitgevoerd met behulp van bekende technologieën, waaronder Spark, Databricks en Jupyter.

* Uitgebreide analyse met verbeterde query-Api's en gebruikers ervaring waarbij gegevens inzichten op basis van assets worden gecombineerd met uitgebreide, ad hoc gegevens analyse met ondersteuning voor interpolatie, scalaire en statistische functies, Categorische variabelen, spreidings grafieken en tijd verschuivings tijd reeks signalen voor uitgebreide analyse.

*   Enter prise-platform ter ondersteuning van de behoeften van de schaal, prestaties, beveiliging en betrouw baarheid van onze Enter prise IoT-klanten.

* Ondersteuning voor uitbreid baarheid en integratie voor end-to-end-analyses. Time Series Insights biedt een uitbreidbaar analyse platform voor diverse gegevens scenario's. Met Time Series Insights Power BI-connector kunnen klanten de query's die ze in Time Series Insights rechtstreeks in Power BI uitvoeren om een uniforme weer gave van hun BI-en time series-analyses in één deel venster te krijgen.

In het volgende diagram ziet u de gegevens stroom op hoog niveau.

  [Belangrijkste mogelijkheden ![](media/v2-update-overview/overview-two.png)](media/v2-update-overview/overview-two.png#lightbox)

Azure Time Series Insights biedt een schaalbaar prijs model voor betalen per gebruik voor gegevens verwerking, opslag (gegevens en meta gegevens) en query, zodat klanten hun gebruik kunnen afstemmen op hun bedrijfs behoeften. 
 
Met de introductie van deze essentiële industriële IoT-mogelijkheden biedt Time Series Insights ook de volgende belang rijke voor delen.  

| | |
| ---| ---|
| Opslag met meerdere lagen voor IoT-schaal van Time Series-gegevens | Met een pijp lijn voor gedeelde gegevens verwerking voor het opnemen van gegevens kunt u gegevens indelen in zowel warme als koude opslag. Gebruik warme Store voor interactieve query's en koude opslag voor het opslaan van grote hoeveel heden gegevens. Zie [query's](./time-series-insights-update-tsq.md)voor meer informatie over hoe u gebruik kunt maken van hoogwaardige query's op basis van een activum. |
| Time Series-model voor het waarmee van ruwe telemetrie en het afleiden van inzichten op basis van assets | U kunt het time series-model gebruiken om instanties, hiërarchieën, typen en variabelen te maken voor uw time series-gegevens. Zie [Time Series model](./time-series-insights-update-tsm.md)voor meer informatie over time series-modellen.  |
| Soepele en doorlopende integratie met andere gegevens oplossingen | Gegevens in Time Series Insights koel opslagplaats worden [opgeslagen](./time-series-insights-update-storage-ingress.md) in open-source Apache Parquet-bestanden. Dit maakt gegevens integratie mogelijk met andere gegevens oplossingen, 1e of derde partij, voor scenario's waarin business intelligence, geavanceerde machine learning en predictive analytics zijn. |
| Gegevens worden bijna in realtime verkend | De [verkenner van Azure Time Series Insights (preview)](./time-series-insights-update-explorer.md) biedt gebruikers visualisatie voor alle gegevens die door de opnamepijplijn worden geleid. Nadat u verbinding hebt gemaakt met een gebeurtenis bron, kunt u gebeurtenis gegevens weer geven, verkennen en er query's op uitvoeren. Op deze manier kunt u nagaan of een apparaat gegevens naar verwachting verzendt. U kunt IoT-apparaten ook controleren op status, productiviteit en algehele effectiviteit. | 
| Uitbreid baarheid en integratie | De integratie van de Azure Time Series Insights Power BI-connector is rechtstreeks beschikbaar in de time series Explorer-gebruikers ervaring via de optie **exporteren** , zodat klanten de tijdreeks query's die ze in onze gebruikers ervaring maken, rechtstreeks kunnen exporteren in het Power BI bureau blad en de tijd reeks grafieken naast andere BI-analyses weer te geven. Hiermee opent u de deur van een nieuwe klasse van scenario's voor industriële IoT-ondernemingen die in Power BI hebben geïnvesteerd door één deel venster van glas te bieden over analyses van verschillende gegevens bronnen, waaronder IoT-tijd reeksen. | 
| Aangepaste toepassingen die zijn gebouwd op het Time Series Insights platform | Time Series Insights biedt ondersteuning voor de [JavaScript SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). De SDK biedt uitgebreide besturingselementen en vereenvoudigde toegang tot query's. Gebruik de SDK om aangepaste IoT-toepassingen op Time Series Insights te bouwen die aansluiten op uw bedrijfs behoeften. U kunt de [query-API's](./time-series-insights-update-tsq.md) van Time Series Insights ook rechtstreeks gebruiken om gegevens naar aangepaste IoT-toepassingen te sturen. |

## <a name="next-steps"></a>Volgende stappen

Aan de slag met Azure Time Series Insights Preview:

> [!div class="nextstepaction"]
> [Snelstartgids](./time-series-insights-update-quickstart.md)

Informatie over use cases:

> [!div class="nextstepaction"]
> [Use cases voor Azure Time Series Insights (preview)](./time-series-insights-update-use-cases.md)
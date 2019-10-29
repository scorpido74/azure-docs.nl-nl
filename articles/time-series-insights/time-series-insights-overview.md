---
title: 'Overzicht: wat is Azure Time Series Insights? | Microsoft Docs'
description: Inleiding tot Azure Time Series Insights, een nieuwe service voor de analyse van tijdreeksgegevens en IoT-oplossingen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 15674d995036d11e1b4ec0ceda5aa4bbf4952906
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990031"
---
# <a name="what-is-azure-time-series-insights"></a>Wat is Azure Time Series Insights?

Azure Time Series Insights is ontworpen om grote hoeveel heden tijdreeks gegevens op te slaan, te visualiseren en op te vragen, zoals die zijn gegenereerd door IoT-apparaten. Als u tijdreeksgegevens wilt opslaan, beheren, opvragen of visualiseren in de cloud, is Time Series Insights waarschijnlijk de juiste keuze voor u. 

[Time Series Insights stroom diagram![](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Time Series Insights heeft vier belangrijke taken:

- Het is volledig geïntegreerd met Cloud gateways zoals Azure IoT Hub en Azure Event Hubs. Hierdoor kan er eenvoudig verbinding worden maakt met deze gebeurtenisbronnen en kan er JSON worden geparseerd uit berichten en structuren met gegevens in overzichtelijke rijen en kolommen. Metagegevens worden samengevoegd met telemetriegegevens en de gegevens worden geïndexeerd in een columnar store.
- Time Series Insights beheert de opslag van uw gegevens. Om ervoor te zorgen dat de gegevens altijd gemakkelijk toegankelijk zijn, worden uw gegevens in het geheugen en Ssd's tot 400 dagen bewaard. U kunt binnen enkele seconden op aanvraag miljarden gebeurtenissen opvragen.
- Time Series Insights biedt een out-of-the-box visualisatie via de Time Series Insights Explorer. 
- Time Series Insights biedt een query service, zowel in de Time Series Insights Verkenner als door gebruik te maken van Api's die eenvoudig zijn te integreren om uw tijdreeks gegevens in te sluiten in aangepaste toepassingen.

Als u een toepassing bouwt voor intern verbruik of voor gebruik door externe klanten, kunt u Time Series Insights als een back-end gebruiken. U kunt deze gebruiken om time series-gegevens te indexeren, op te slaan en samen te voegen. Als u een aangepaste visualisatie en gebruikers ervaring bovenaan wilt maken, gebruikt u de [client-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights is ook voorzien van verschillende [query-api's](how-to-shape-query-json.md) om deze aangepaste scenario's mogelijk te maken.

Tijdreeksgegevens tonen aan hoe een asset of proces verandert in de loop der tijd. Tijdreeks gegevens worden geïndexeerd door tijds tempels en tijd is de meest relevante as waarmee dergelijke gegevens zijn ingedeeld. Tijdreeks gegevens arriveren doorgaans in sequentiële volg orde, zodat deze worden behandeld als een invoeg toepassing in plaats van een update voor uw data base.

Het kan een uitdaging zijn om tijd reeks gegevens in grote volumes op te slaan, te indexeren, op te vragen, te analyseren en te visualiseren.
Azure Time Series Insights legt een nieuwe gebeurtenis vast en slaat deze op in de loop van de tijd. Als gevolg hiervan kunt u in het verleden achterwaarts zoeken naar inzichten om toekomstige wijzigingen te voors pellen.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Meer informatie over Azure Time Series Insights, het op de cloud gebaseerde IoT Analytics-platform.</br>

[VIDEO![](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primaire scenario's

- Time Series-gegevens op een schaal bare manier opslaan. 

   De basis van Time Series Insights is een database die speciaal is ontworpen voor het werken met tijdreeksgegevens. Omdat het een schaalbaar en volledig beheerd Time Series Insights, wordt het werk van het opslaan en beheren van gebeurtenissen verwerkt.

- Verken gegevens bijna in realtime. 

   Time Series Insights biedt een Explorer-Verkenner waarmee alle gegevens die in een omgeving worden gestreamd worden gevisualiseerd. Kort nadat u verbinding hebt gemaakt met een gebeurtenis bron, kunt u gebeurtenis gegevens in Time Series Insights bekijken, verkennen en er query's op uitvoeren. De gegevens helpen u om te controleren of een apparaat gegevens verzendt zoals verwacht en om een IoT-activum te bewaken voor status, productiviteit en algehele effectiviteit. 

- Voer een analyse van de hoofd oorzaak uit en detectie afwijkingen.

   Time Series Insights heeft hulpprogram ma's zoals patronen en perspectief weergaven voor het uitvoeren en opslaan van analyse van de hoofd oorzaak van meerdere stappen. Time Series Insights werkt ook met waarschuwings services zoals Azure Stream Analytics, zodat u waarschuwingen en gedetecteerde afwijkingen in bijna realtime kunt bekijken in de Time Series Insights Verkenner. 

- Krijg een globaal overzicht van de tijdreeks gegevens die stromen van verschillende locaties voor vergelijking tussen meerdere assets en sites.

   U kunt meerdere bronnen van gebeurtenissen verbinden met een Time Series Insights-omgeving. Op deze manier kunt u gegevens weer geven die in vrijwel realtime worden gestreamd vanaf meerdere, ongelijksoortige locaties. Gebruikers kunnen profiteren van deze zicht baarheid om gegevens met bedrijfs leiders te delen. Ze kunnen beter samen werken met domein experts die hun expertise kunnen helpen bij het oplossen van problemen, het Toep assen van aanbevolen procedures en het delen van informatie.

- Bouw een klant toepassing bovenop Time Series Insights. 

   Time Series Insights maakt REST query-Api's die u kunt gebruiken om toepassingen te bouwen die gebruikmaken van tijdreeks gegevens.

## <a name="capabilities"></a>Mogelijkheden

- **Snel aan de slag**: Azure time series Insights vereist geen voorafe gegevens voorbereiding, zodat u snel verbinding kunt maken met miljoenen gebeurtenissen in uw IOT-hub of event hub. Nadat u verbinding hebt gemaakt, kunt u sensor gegevens visualiseren en ermee werken om uw IoT-oplossingen snel te valideren. U kunt met uw gegevens werken zonder code te schrijven en u hoeft geen nieuwe taal te leren kennen. Time Series Insights biedt een gedetailleerd onderliggend, vrije-tekst query vlak voor geavanceerde gebruikers en wijst en klikt u op verkennen.

- **Nabije real-time inzichten**: Time Series Insights kunnen miljoenen sensor gebeurtenissen per dag opnemen, met een latentie van één minuut. Time Series Insights helpt u bij het verkrijgen van inzicht in uw sensor gegevens. Gebruik het om trends en afwijkingen op te sporen, de oorzaak van analyses uit te voeren en dure uitval tijd te voor komen. Kruis correlatie tussen realtime en historische gegevens helpt u bij het vinden van verborgen trends in de gegevens.

- **Bouw aangepaste oplossingen**: Azure time series Insights gegevens insluiten in uw bestaande toepassingen. U kunt ook nieuwe aangepaste oplossingen maken met de Time Series Insights REST Api's. U kunt bovendien persoonlijke weergaven maken die u kunt delen met anderen om zo uw inzichten breder beschikbaar te maken.

- **Schaalbaarheid**: Time Series Insights ondersteunt zowel kleine als zeer grote hoeveelheden IoT-gegevens. De service kan 1 miljoen tot 100 miljoen gebeurtenissen per dag worden opnemen, met een standaardbewaarperiode van 31 dagen. U kunt live gegevens stromen in bijna realtime visualiseren en analyseren, naast historische gegevens.

## <a name="get-started"></a>Aan de slag

Voer de volgende stappen uit om aan de slag te gaan.

1. Een Time Series Insights omgeving inrichten in de Azure Portal.
1. Verbinding maken met een gebeurtenis bron, zoals een IoT-hub of een Event Hub. 
1. Upload referentie gegevens. Dit is geen aanvullende service.
1. Bekijk de gegevens na enkele minuten met de verkenner van Time Series Insights.

## <a name="time-series-insights-explorer"></a>Time Series Insights Explorer

Dit diagram toont een voor beeld van Time Series Insights-gegevens die via de Time Series Insights Explorer worden weer gegeven.

![Time Series Insights Explorer](media/time-series-insights-explorer/explorer4.png)

## <a name="next-steps"></a>Volgende stappen

- Verken de [gratis demonstratie omgeving](./time-series-quickstart.md)van Azure time series Insights algemene Beschik baarheid.
- Meer informatie over het [plannen van uw time series Insights](time-series-insights-environment-planning.md) -omgeving.

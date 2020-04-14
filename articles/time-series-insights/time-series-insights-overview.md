---
title: 'Overzicht: Wat is Azure Time Series Insights? - Azure Time Series Insights | Microsoft Documenten'
description: Inleiding tot Azure Time Series Insights, een nieuwe service voor de analyse van tijdreeksgegevens en IoT-oplossingen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.topic: overview
ms.date: 04/13/2020
ms.custom: seodec18
ms.openlocfilehash: 59149b2ca598104d8aca9b4e5e60194a8f6398bf
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81269955"
---
# <a name="what-is-azure-time-series-insights"></a>Wat is Azure Time Series Insights?

Azure Time Series Insights is gebouwd om grote hoeveelheden tijdreeksgegevens op te slaan, visualiseren en op te vragen, zoals die gegenereerd door IoT-apparaten. Als u tijdreeksgegevens wilt opslaan, beheren, opvragen of visualiseren in de cloud, is Time Series Insights waarschijnlijk de juiste keuze voor u.

[![Stroomdiagram van Time Series Insights](media/overview/time-series-insights-flowchart.png)](media/overview/time-series-insights-flowchart.png#lightbox)

Time Series Insights heeft vier belangrijke taken:

- Het is volledig geïntegreerd met cloudgateways zoals Azure IoT Hub en Azure Event Hubs. Hierdoor kan er eenvoudig verbinding worden maakt met deze gebeurtenisbronnen en kan er JSON worden geparseerd uit berichten en structuren met gegevens in overzichtelijke rijen en kolommen. Metagegevens worden samengevoegd met telemetriegegevens en de gegevens worden geïndexeerd in een columnar store.
- Time Series Insights beheert de opslag van uw gegevens. Om ervoor te zorgen dat gegevens altijd gemakkelijk toegankelijk zijn, worden uw gegevens maximaal 400 dagen opgeslagen in geheugen en SSD's. U op interactieve wijze miljarden gebeurtenissen op vragen in seconden-op-aanvraag.
- Time Series Insights biedt out-of-the-box visualisatie via de Time Series Insights explorer.
- Time Series Insights biedt een queryservice, zowel in de Time Series Insights explorer als met API's die eenvoudig te integreren zijn om uw tijdreeksgegevens in te sluiten in aangepaste toepassingen.

Als u een toepassing bouwt voor intern verbruik of voor externe klanten om te gebruiken, u Time Series Insights gebruiken als back-end. U het gebruiken om tijdreeksgegevens te indexeren, op te slaan en te aggregeren. Als u bovenaan een aangepaste visualisatie en gebruikerservaring wilt maken, gebruikt u de [client-SDK](https://github.com/microsoft/tsiclient/blob/master/docs/API.md). Time Series Insights is ook uitgerust met verschillende [Query API's](how-to-shape-query-json.md) om deze aangepaste scenario's mogelijk te maken.

Tijdreeksgegevens tonen aan hoe een asset of proces verandert in de loop der tijd. Tijdreeksgegevens worden geïndexeerd aan de basis van tijdstempels en de tijd is de meest betekenisvolle as waarlangs dergelijke gegevens zijn georganiseerd. Tijdreeksgegevens worden meestal in opeenvolgende volgorde weergegeven, dus het wordt behandeld als een invoegtoepassing in plaats van een update van uw database.

Het kan een uitdaging zijn om tijdreeksgegevens in grote volumes op te slaan, te indexeren, op te vragen, te analyseren en te visualiseren.
Azure Time Series Insights legt elke nieuwe gebeurtenis vast en slaat deze op als een rij en wijzigingen worden efficiënt gemeten in de tijd. Als gevolg hiervan u terugkijken om inzichten uit het verleden te trekken om toekomstige veranderingen te helpen voorspellen.

## <a name="video"></a>Video

### <a name="learn-more-about-azure-time-series-insights-the-cloud-based-iot-analytics-platformbr"></a>Meer informatie over Azure Time Series Insights, het cloudgebaseerde IoT-analyseplatform.</br>

[![Video](https://img.youtube.com/vi/GaARrFfjoss/0.jpg)](https://www.youtube.com/watch?v=GaARrFfjoss)

## <a name="primary-scenarios"></a>Primaire scenario's

- Sla tijdreeksgegevens op een schaalbare manier op.

   De basis van Time Series Insights is een database die speciaal is ontworpen voor het werken met tijdreeksgegevens. Omdat het schaalbaar en volledig beheerd is, zorgt Time Series Insights voor het opslaan en beheren van gebeurtenissen.

- Ontdek gegevens in bijna realtime.

   Time Series Insights biedt een verkenner die alle gegevens visualiseert die naar een omgeving stromen. Kort nadat u verbinding hebt gemaakt met een gebeurtenisbron, u gebeurtenisgegevens bekijken, verkennen en opvragen in Inzichten in de time-serie. De gegevens helpen u om te valideren of een apparaat gegevens uitzendt zoals verwacht en om een IoT-asset te controleren op gezondheid, productiviteit en algehele effectiviteit.

- Voer de oorzaakanalyse uit en detecteer afwijkingen.

   Time Series Insights heeft tools zoals patronen en perspectiefweergaven om analyse van meerdere oorzaken uit te voeren en op te slaan. Time Series Insights werkt ook met waarschuwingsservices zoals Azure Stream Analytics, zodat u waarschuwingen en gedetecteerde afwijkingen in bijna realtime bekijken in de Time Series Insights-verkenner.

- Krijg een globaal overzicht van tijdreeksgegevens die worden gestroomd vanaf verschillende locaties voor vergelijking met meerdere activa of locaties.

   U kunt meerdere bronnen van gebeurtenissen verbinden met een Time Series Insights-omgeving. Op deze manier u gegevens die vanaf meerdere, verschillende locaties samenstromen in bijna realtime bekijken. Gebruikers kunnen profiteren van deze zichtbaarheid om gegevens te delen met bedrijfsleiders. Ze kunnen beter samenwerken met domeinexperts die hun expertise kunnen toepassen om problemen op te lossen, best practices toe te passen en learnings te delen.

- Bouw een klanttoepassing bovenop Time Series Insights.

   Time Series Insights legt REST Query API's bloot die u gebruiken om toepassingen te bouwen die tijdreeksgegevens gebruiken.

## <a name="capabilities"></a>Functionaliteit

- **Snel aan de slag:** Azure Time Series Insights vereist geen voorbereiding van gegevens vooraf, zodat u snel verbinding maken met miljoenen gebeurtenissen in uw IoT-hub of gebeurtenishub. Nadat u verbinding hebt gemaakt, u sensorgegevens visualiseren en ermee communiceren om uw IoT-oplossingen snel te valideren. U met uw gegevens communiceren zonder code te schrijven en u hoeft geen nieuwe taal te leren. Time Series Insights biedt een gedetailleerd, gratis tekstqueryoppervlak voor gevorderde gebruikers en point-and-click-verkenning.

- **Bijna realtime inzichten:** Time Series Insights kan miljoenen sensorgebeurtenissen per dag innemen, met een latentie van één minuut. Time Series Insights helpt u inzicht te krijgen in uw sensorgegevens. Gebruik het om trends en afwijkingen te herkennen, root-cause analyses uit te voeren en kostbare downtime te voorkomen. Cross-correlatie tussen real-time en historische gegevens helpt u verborgen trends in de gegevens te vinden.

- **Aangepaste oplossingen bouwen:** Integreer Azure Time Series Insights-gegevens in uw bestaande toepassingen. U ook nieuwe aangepaste oplossingen maken met de Time Series Insights REST API's. U kunt bovendien persoonlijke weergaven maken die u kunt delen met anderen om zo uw inzichten breder beschikbaar te maken.

- **Schaalbaarheid**: Time Series Insights is ontworpen om IoT op schaal te ondersteunen. De service kan 1 miljoen tot 100 miljoen gebeurtenissen per dag worden opnemen, met een standaardbewaarperiode van 31 dagen. U live gegevensstromen in bijna realtime visualiseren en analyseren, naast historische gegevens.

## <a name="get-started"></a>Aan de slag

Volg deze stappen om aan de slag te gaan.

1. Inrichten van een Time Series Insights-omgeving in de Azure-portal.
1. Maak verbinding met een gebeurtenisbron zoals een IoT-hub of een gebeurtenishub.
1. Referentiegegevens uploaden. Dit is geen extra service.
1. Bekijk en geef uw gegevens binnen enkele minuten weer met de Time Series Insights explorer.

## <a name="explorer"></a>Verkenner

Bekijk, analyseer en ontdek trends in uw gegevens met behulp van de Azure Time Series Insights explorer.

![Verkenner van Time Series Insights](media/overview/time-series-insights-explorer-panel.png)

Lees hoe u de [Azure Time Series Insights-verkenner kunt](time-series-insights-explorer.md) gebruiken en hoe u inzichten uit uw gegevens halen.

## <a name="next-steps"></a>Volgende stappen

- Ontdek de gratis [demonstratieomgeving](./time-series-quickstart.md)voor algemene beschikbaarheid van Azure Time Series Insights.

- Meer informatie over het [plannen van uw Time Series Insights-omgeving.](time-series-insights-environment-planning.md)

---
title: Inzicht in het bewaren van gegevens in uw omgeving - Azure Time Series Insight | Microsoft Documenten
description: In dit artikel worden twee instellingen beschreven die het bewaren van gegevens in uw Azure Time Series Insights-omgeving regelen.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 04/14/2020
ms.custom: seodec18
ms.openlocfilehash: fd34595d5ea942602efc920904ff326fc203c088
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380695"
---
# <a name="understand-data-retention-in-azure-time-series-insights"></a>Inzicht in gegevensretentie in Azure Time Series Insights

In dit artikel worden twee primaire instellingen beschreven die van invloed zijn op het bewaren van gegevens in uw Azure Time Series Insights-omgeving.

## <a name="video"></a>Video

### <a name="the-following-video-summarizes-time-series-insights-data-retention-and-how-to-plan-for-itbr"></a>De volgende video vat het bewaren van time series Insights-gegevens samen en hoe u dit plannen.</br>

> [!VIDEO https://www.youtube.com/embed/03x6zKDQ6DU]

Elk van uw Azure Time Series Insights-omgevingen heeft een instelling die de **bewaartijd voor gegevens**regelt. De waarde varieert van 1 tot 400 dagen. De gegevens worden verwijderd op basis van de opslagcapaciteit of bewaarduur van de omgeving, afhankelijk van wat het eerst komt.

Bovendien heeft uw Azure Time Series Insights-omgeving een **opslaglimiet die de gedragsinstelling heeft overschreden.** Het controleert het binnendringen en zuiveren van gedrag wanneer de maximale capaciteit van een omgeving wordt bereikt. Er zijn twee gedragingen om uit te kiezen bij het configureren van het:

- **Oude gegevens wissen** (standaard)  
- **Binnenlaten onderbreken**

> [!NOTE]
> Bij het maken van een nieuwe omgeving is de retentie standaard geconfigureerd om oude gegevens te **zuiveren.** Deze instelling kan indien nodig worden ingeschakeld na het maken van de tijd met behulp van de Azure-portal, op de pagina **Configureren** van de time series Insights-omgeving.
> * Lees [Bewaarbeleid configureren in Time Series Insights](time-series-insights-how-to-configure-retention.md)voor informatie over het configureren van bewaarbeleid.

Beide beleid voor het bewaren van gegevens worden hieronder nader beschreven.

## <a name="purge-old-data"></a>Oude gegevens wissen

- **Oude gegevens wissen** is de standaardinstelling voor Azure Time Series Insights-omgevingen.  
- **Het wissen van oude gegevens** heeft de voorkeur wanneer gebruikers altijd hun meest recente *gegevens* in hun Time Series Insights-omgeving willen hebben.
- Met **de instelling Oude gegevens wissen** worden gegevens *verwijderd* zodra de grenzen van de omgeving (bewaartijd, grootte of telling, wat het eerst komt) zijn bereikt. Retentie is standaard ingesteld op 30 dagen.
- De oudste ingenomen gegevens worden eerst gezuiverd (de "First In First Out"-benadering).

### <a name="example-one"></a>Voorbeeld één

Overweeg een voorbeeldomgeving met bewaargedrag **Doorgaan met binnendringen en oude gegevens zuiveren:**

**De bewaartijd van gegevens** is ingesteld op 400 dagen. **De capaciteit** is ingesteld op S1-eenheid, die 30 GB totale capaciteit bevat. Laten we aannemen dat binnenkomende gegevens zich ophopen tot gemiddeld 500 MB per dag. Deze omgeving kan slechts 60 dagen aan gegevens bewaren, gezien de snelheid van binnenkomende gegevens, omdat de maximale capaciteit wordt bereikt op 60 dagen. De binnenkomende gegevens stapelen zich op als: 500 MB per dag x 60 dagen = 30 GB.

Op de 61e dag toont de omgeving de meest verse gegevens, maar worden de oudste gegevens, ouder dan 60 dagen, gewist. De zuivering maakt ruimte voor de nieuwe gegevens streaming in, zodat nieuwe gegevens kunnen blijven worden onderzocht. Als de gebruiker gegevens langer wil bewaren, kan deze de grootte van de omgeving vergroten door extra eenheden toe te voegen of minder gegevens te pushen.  

### <a name="example-two"></a>Voorbeeld twee

Overweeg een omgeving die ook een bewaargedrag heeft **geconfigureerd, ga verder met binnendringen en oude gegevens zuiveren.** In dit voorbeeld wordt **de bewaartijd van gegevens** ingesteld op een lagere waarde van 180 dagen. **De capaciteit** is ingesteld op S1-eenheid, die 30 GB totale capaciteit bevat. Om gegevens gedurende de volledige 180 dagen op te slaan, mag de dagelijkse binnenlaten niet meer bedragen dan 0,166 GB (166 MB) per dag.  

Wanneer de dagelijkse invallenvan deze omgeving hoger is dan 0,166 GB per dag, kunnen gegevens niet gedurende 180 dagen worden opgeslagen, omdat sommige gegevens worden verwijderd. Overweeg deze zelfde omgeving tijdens een drukke periode. Stel dat de invallende snelheid van de omgeving kan stijgen tot gemiddeld 0,189 GB per dag. In dat drukke tijdsbestek worden ongeveer 158 dagen aan gegevens bewaard (30GB/0.189 = 158,73 dagen retentie). Deze tijd is minder dan de gewenste tijdsbewaartermijn voor gegevens.

## <a name="pause-ingress"></a>Binnenlaten onderbreken

- De instelling **Onderbreken binnenlaten** is ontworpen om ervoor te zorgen dat gegevens niet worden gewist als de grootte- en tellimieten vóór de bewaarperiode worden bereikt.  
- **Pauze-ingress** biedt extra tijd voor de gebruikers om de capaciteit van hun omgeving te verhogen voordat gegevens worden verwijderd als gevolg van schending van de bewaartermijn.
- Het helpt u te beschermen tegen gegevensverlies, maar kan een kans creëren voor het verlies van uw meest recente gegevens als het binnendringen wordt onderbroken na de bewaarperiode van uw gebeurtenisbron.
- Zodra de maximale capaciteit van een omgeving is bereikt, wordt de gegevens in de omgeving echter onderbroken totdat de volgende aanvullende acties optreden:

   - U verhoogt de maximale capaciteit van de omgeving om meer schaaleenheden toe te voegen zoals beschreven in [Hoe u uw Time Series Insights-omgeving schalen.](time-series-insights-how-to-scale-your-environment.md)
   - De bewaartermijn voor gegevens wordt bereikt en gegevens worden gezuiverd, waardoor de omgeving onder de maximale capaciteit komt.

### <a name="example-three"></a>Voorbeeld drie

Overweeg een omgeving met bewaargedrag dat is geconfigureerd om binnendringen te **pauzeren.** In dit voorbeeld wordt de **bewaarperiode voor gegevens** geconfigureerd tot 60 dagen. **De capaciteit** is ingesteld op drie (3) eenheden van S1. Stel dat deze omgeving elke dag een binnendringenvan 2 GB-gegevens heeft. In deze omgeving wordt het binnendringen onderbroken zodra de maximale capaciteit is bereikt.

Op dat moment toont de omgeving dezelfde gegevensset totdat binnendringen wordt hervat of totdat **blijven binnendringen** is ingeschakeld (wat oudere gegevens zou zuiveren om ruimte te maken voor nieuwe gegevens).

Bij het binnenvallen hervat:

- Gegevensstromen in de volgorde waarin deze door gebeurtenisbron is ontvangen
- De gebeurtenissen worden geïndexeerd op basis van hun tijdstempel, tenzij u het bewaarbeleid op uw gebeurtenisbron hebt overschreden. [Veelgestelde vragen](../event-hubs/event-hubs-faq.md) over gebeurtenisbronbehoud

> [!IMPORTANT]
> U moet waarschuwingen instellen om een melding te geven om te voorkomen dat binnendringen wordt onderbroken. Gegevensverlies is mogelijk omdat de standaardretentie 1 dag is voor Azure-gebeurtenisbronnen. Daarom, zodra binnendringen is onderbroken, verliest u waarschijnlijk de meest recente gegevens, tenzij extra actie wordt ondernomen. U moet de capaciteit vergroten of het gedrag omschakelen naar **oude gegevens wissen** om het potentieel voor gegevensverlies te voorkomen.

In de impacthebbende gebeurtenishubs u overwegen de eigenschap **Berichtbehoud** aan te passen om gegevensverlies te minimaliseren wanneer het onderbreken van de gebeurtenissen plaatsvindt in inzichten in de tijdreeksen.

[![Gebeurtenishubberichtbehoud.](media/time-series-insights-concepts-retention/event-hub-retention.png)](media/time-series-insights-concepts-retention/event-hub-retention.png#lightbox)

Als er geen eigenschappen zijn`timeStampPropertyName`geconfigureerd op gebeurtenisbron ( ), wordt Time Series Insights standaard ingesteld op de tijdstempel van aankomst op gebeurtenishub als de X-as. Als `timeStampPropertyName` de omgeving is geconfigureerd als iets anders, `timeStampPropertyName` zoekt de omgeving naar het geconfigureerde in het gegevenspakket wanneer gebeurtenissen worden ontleed.

Lees [hoe u uw Time Series Insights-omgeving schalen](time-series-insights-how-to-scale-your-environment.md) om uw omgeving te schalen om extra capaciteit mogelijk te maken of de retentieduur te verlengen.

## <a name="next-steps"></a>Volgende stappen

- Voor informatie over het configureren of wijzigen van instellingen voor gegevensbewaring, controleert [u Het configureren van retentie in Time Series Insights](time-series-insights-how-to-configure-retention.md).

- Meer informatie over [het beperken van latentie in Azure Time Series Insights](time-series-insights-environment-mitigate-latency.md).

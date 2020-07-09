---
title: Doorvoer beperkingen voor streaming-opname-Azure Time Series Insights | Microsoft Docs
description: Meer informatie over de doorvoer limieten voor inkomend verkeer in Azure Time Series Insights.
author: lyrana
ms.author: lyhughes
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: baa132589f5242ab480c9caa55ca793e0652ad6e
ms.sourcegitcommit: e995f770a0182a93c4e664e60c025e5ba66d6a45
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86135014"
---
# <a name="streaming-ingestion-throughput-limits"></a>Doorvoer limieten voor stroom inslikken

Azure Time Series Insights beperkingen voor het volgen van gegevens stromen worden hieronder beschreven.

> [!TIP]
> [Plan uw voorbeeld omgeving](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-update-plan#review-preview-limits) voor een uitgebreide lijst met alle preview-limieten.

## <a name="per-environment-limitations"></a>Beperkingen per omgeving

Over het algemeen worden de ingangs snelheden weer gegeven als de factor van het aantal apparaten in uw organisatie, de gebeurtenis emissie frequentie en de grootte van elke gebeurtenis:

*  **Aantal apparaten** × **gebeurtenis emissie frequentie** × **grootte van elke gebeurtenis**.

Standaard kan Time Series Insights inkomende gegevens opnemen met een snelheid van **Maxi maal 1 MB per seconde (Mbps) per time series Insights omgeving**. Er zijn extra beperkingen [per hub-partitie](concepts-streaming-throughput-limitations.md#hub-partitions-and-per-partition-limits).

> [!TIP]
>
> * Omgevings ondersteuning voor het opnemen van snelheden tot 16 MBps kan worden geboden door een aanvraag.
> * Neem contact met ons op als u een hogere door voer nodig hebt door een ondersteunings ticket in te dienen via Azure Portal.
 
* **Voor beeld 1:**

    Contoso-verzen ding heeft 100.000 apparaten die drie keer per minuut een gebeurtenis verzenden. De grootte van een gebeurtenis is 200 bytes. Ze gebruiken een IoT Hub met vier partities als de bron van de Time Series Insights gebeurtenis.

    * De opname frequentie voor de Time Series Insights omgeving zou zijn: **100.000 apparaten * 200 bytes/gebeurtenis * (3/60 gebeurtenis/sec) = 1 Mbps**.
    * De opname frequentie per partitie zou 0,25 MBps zijn.
    * De opname snelheid van Contoso-verzen ding zou binnen de schaal limiet vallen.

* **Voor beeld 2:**

    Contoso vloot Analytics heeft 60.000 apparaten die elke seconde een gebeurtenis verzenden. Ze gebruiken een event hub met een aantal partities van 4 als de bron van de Time Series Insights gebeurtenis. De grootte van een gebeurtenis is 200 bytes.

    * Het opname tempo van de omgeving is: **60.000 apparaten * 200 bytes/gebeurtenis * 1 gebeurtenis/SEC = 12 Mbps**.
    * Het per partitie aantal zou 3 MBps zijn.
    * De opname frequentie van Contoso vloot Analytics ligt boven de omgeving en de partitie limieten. Ze kunnen een aanvraag indienen bij Time Series Insights via Azure Portal om de opname frequentie voor hun omgeving te verhogen en een event hub te maken met meer partities die binnen de preview-grenzen vallen.

## <a name="hub-partitions-and-per-partition-limits"></a>Hub-partities en limieten per partitie

Bij het plannen van uw Time Series Insights-omgeving is het belang rijk dat u rekening houdt met de configuratie van de bron (nen) van de gebeurtenis die u wilt verbinden met Time Series Insights. Zowel Azure IoT Hub als Event Hubs gebruiken partities om een horizontale schaal in te scha kelen voor gebeurtenis verwerking. 

Een *partitie* is een geordende reeks gebeurtenissen die in een hub worden bewaard. Het aantal partities wordt ingesteld tijdens het maken van de hub en kan niet worden gewijzigd.

Bekijk voor Event Hubs aanbevolen procedures voor het partitioneren van [het aantal partities dat ik nodig heb?](https://docs.microsoft.com/azure/event-hubs/event-hubs-faq#how-many-partitions-do-i-need)

> [!NOTE]
> De meeste IoT-hubs die worden gebruikt met Azure Time Series Insights hebben slechts vier partities nodig.

Of u nu een nieuwe hub voor uw Time Series Insights omgeving maakt of een bestaand gebruikt, u moet de opname frequentie per partitie berekenen om te bepalen of deze binnen de limieten valt. 

Azure Time Series Insights preview heeft momenteel een **limiet van 0,5 Mbps voor algemeen per partitie**.

### <a name="iot-hub-specific-considerations"></a>IoT Hub-specifieke overwegingen

Wanneer een apparaat wordt gemaakt in IoT Hub, wordt het permanent toegewezen aan een partitie. In dat geval kan IoT Hub de volg orde van gebeurtenissen garanderen (omdat de toewijzing nooit wordt gewijzigd).

Een vaste partitie toewijzing heeft ook invloed op Time Series Insights instanties die gegevens opnemen die vanuit IoT Hub downstream worden verzonden. Wanneer berichten van meerdere apparaten worden doorgestuurd naar de hub met behulp van dezelfde gateway apparaat-ID, kunnen ze in dezelfde partitie worden ontvangen op hetzelfde moment mogelijk de schaal limieten per partitie overschrijden.

**Impact**:

* Als een enkele partitie een onderhouds frequentie voor de preview-limiet heeft, is het mogelijk dat Time Series Insights niet alle telemetrie van het apparaat synchroniseert voordat de IoT Hub gegevens Bewaar periode is overschreden. Als gevolg hiervan kunnen verzonden gegevens verloren gaan als de opname limieten consistent zijn overschreden.

Om dat te beperken, raden we u aan de volgende aanbevolen procedures uit te voeren:

* Bereken uw per omgeving en de opname snelheid per partitie voordat u de oplossing implementeert.
* Zorg ervoor dat uw IoT Hub-apparaten zoveel mogelijk gelijkmatig zijn verdeeld.

> [!IMPORTANT]
> Voor omgevingen die IoT Hub als gebeurtenis bron gebruiken, berekent u de opname snelheid met het aantal Hub-apparaten dat wordt gebruikt om ervoor te zorgen dat de snelheid lager is dan de limiet van 0,5 MBps per partitie.
>
> * Zelfs als er meerdere gebeurtenissen tegelijk arriveren, wordt de limiet voor de preview-versie niet overschreden.

  ![IoT Hub partitie diagram](media/concepts-ingress-overview/iot-hub-partiton-diagram.png)

Raadpleeg de volgende bronnen voor meer informatie over het optimaliseren van hub-door Voer en-partities:

* [IoT Hub schaal](https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling)
* [Event hub-schaal](https://docs.microsoft.com/azure/event-hubs/event-hubs-scalability#throughput-units)
* [Event hub-partities](https://docs.microsoft.com/azure/event-hubs/event-hubs-features#partitions)

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over gegevens [opslag](concepts-storage.md)
---
title: Architectuur IoT Central Digital Distribution Center | Microsoft Documenten
description: Een architectuur van de toepassingsjabloon Digital Distribution Center voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 359aaf7bbde5501716f3d8c4229b2b90918fd5de
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020978"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architectuur van de toepassingsjabloon van het Centrale Digitale Distributiecentrum



Partners en klanten kunnen de app-sjabloon gebruiken & na richtlijnen om end-to-end **digitale distributiecentrumoplossingen** te ontwikkelen.

> [!div class="mx-imgBorder"]
> ![digitaal distributiecentrum](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Set IoT-sensoren die telemetriegegevens naar een gateway-apparaat verzenden
2. Gateway-apparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Gegevens worden doorgestuurd naar de gewenste Azure-service voor manipulatie
4. Azure-services zoals ASA of Azure-functies kunnen worden gebruikt om gegevensstromen te hervormen en naar de gewenste opslagaccounts te verzenden 
5. Verwerkte gegevens worden opgeslagen in hete opslag voor near real-time acties of koude opslag voor extra inzicht verbeteringen die is gebaseerd op ML of batch analyse. 
6. Logic Apps kunnen worden gebruikt om verschillende zakelijke workflows van stroom te maken in zakelijke toepassingen voor eindgebruikers

## <a name="details"></a>Details
Volgende sectie schetst elk deel van de conceptuele architectuur

## <a name="video-cameras"></a>Videocamera's 
Videocamera's zijn de belangrijkste sensoren in dit digitaal verbonden ecosysteem op bedrijfsschaal. Vooruitgang op het gebied van machine learning en kunstmatige intelligentie die het mogelijk maken om video om te zetten in gestructureerde gegevens en deze aan de rand te verwerken voordat deze naar de cloud wordt verzonden. We kunnen IP-camera's gebruiken om beelden vast te leggen, ze te comprimeren op de camera en vervolgens de gecomprimeerde gegevens over edge compute te verzenden voor video-analysepijplijn of GigE vision-camera's te gebruiken om beelden op de sensor vast te leggen en deze beelden rechtstreeks naar de Azure IoT Edge te verzenden , die vervolgens comprimeert voordat deze wordt verwerkt in de pijplijn video-analyse. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-gateway
De "camera's-als-sensoren" en edge workloads worden lokaal beheerd door Azure IoT Edge en de camerastream wordt verwerkt door analytics pipeline. De video-analyseverwerkingspijplijn bij Azure IoT Edge biedt veel voordelen, waaronder een verminderde responstijd, een lage bandbreedte, wat resulteert in een lage latentie voor snelle gegevensverwerking. Alleen de meest essentiële metadata, inzichten of acties worden naar de cloud gestuurd voor verdere actie of onderzoek. 

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een oplossingsontwikkelingsplatform dat iot-apparaat vereenvoudigt & Azure IoT Edge-gatewayconnectiviteit, -configuratie en -beheer. Het platform vermindert de lasten en kosten van IoT-apparaatbeheer, -bewerkingen en gerelateerde ontwikkelingen aanzienlijk. Klanten & partners kunnen een end-to-end enterprise-oplossingen bouwen om een digitale feedbackloop in distributiecentra te realiseren.

## <a name="business-insights-and-actions-using-data-egress"></a>Bedrijfsinzichten en -acties met behulp van gegevensuitgang 
IoT Central-platform biedt uitgebreide uitbreidbaarheidsopties via Continuous Data Export (CDE) en API's. Bedrijfsinzichten die zijn gebaseerd op telemetriegegevensverwerking of ruwe telemetrie worden doorgaans geëxporteerd naar een voorkeurslijn-of-business-toepassing. Dit kan worden bereikt via webhook, Service Bus, event hub of blob-opslag om machine learning-modellen te bouwen, te trainen en te implementeren en inzichten verder te verrijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van [sjabloon voor digitale distributiecentrum](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Meer informatie over [IoT Central-retailsjablonen](./overview-iot-central-retail-pnp.md)
* Meer informatie over IoT Central verwijzen naar [IoT Central-overzicht](../core/overview-iot-central.md)

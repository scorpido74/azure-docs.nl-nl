---
title: Architectuur IoT Central Digital Distribution Center | Microsoft Docs
description: Een architectuur van het Digital Distribution Center-toepassings sjabloon voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 75d6591b13e21ce2914c3e407bad970522856c90
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957998"
---
# <a name="architecture-of-iot-central-digital-distribution-center-application-template"></a>Architectuur van IoT Central Digital Distribution Center-toepassings sjabloon

Partners & klant kunnen gebruikmaken van de app-sjabloon & volgende richt lijnen voor het ontwikkelen van end-to-end **Digital Distribution Center** -oplossingen.

> [!div class="mx-imgBorder"]
> ![Digital Distribution Center](./media/concept-ddc-architecture/digital-distribution-center-architecture.png)

1. Set IoT Sens oren die telemetriegegevens naar een gateway apparaat verzenden
2. Gateway apparaten die telemetrie en geaggregeerde inzichten verzenden naar IoT Central
3. Gegevens worden doorgestuurd naar de gewenste Azure-service voor manipulatie
4. Azure-Services zoals ASA of Azure Functions kunnen worden gebruikt om gegevens stromen opnieuw op te maken en te verzenden naar de gewenste opslag accounts 
5. Verwerkte gegevens worden opgeslagen in warme opslag voor bijna realtime acties of koude opslag voor aanvullende inzicht verbeteringen op basis van ML of batch analyse. 
6. Logic Apps kan worden gebruikt om verschillende zakelijke werk stromen in zakelijke toepassingen voor eind gebruikers uit te scha kelen

## <a name="details"></a>Details
De volgende sectie bevat een overzicht van elk deel van de conceptuele architectuur

## <a name="video-cameras"></a>Video camera's 
Video camera's zijn de primaire Sens oren in dit digitaal verbonden ecosysteem op ondernemings schaal. Voor schotten in machine learning en kunst matige intelligentie waarmee video kan worden omgezet in gestructureerde gegevens en deze kan verwerken voordat ze naar de cloud worden verzonden. We kunnen IP-camera's gebruiken om installatie kopieën vast te leggen, ze op de camera te comprimeren en vervolgens de gecomprimeerde gegevens via de Edge Compute voor video Analytics-pijp lijn te verzenden, of GigE Vision-camera's te gebruiken om afbeeldingen op de sensor vast te leggen en deze afbeeldingen vervolgens rechtstreeks naar de Azure IoT Edge te verzenden , die vervolgens vóór verwerking in video Analytics-pijp lijn comprimeert. 

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge gateway
De werk belastingen ' camera ' as-Sens oren ' en Edge worden lokaal beheerd door Azure IoT Edge en de camera stroom wordt verwerkt door de analyse pijplijn. De video Analytics processing-pijp lijn op Azure IoT Edge biedt talloze voor delen, zoals een afnemende reactie tijd, een laag bandbreedte verbruik dat resulteert in lage latentie voor snelle gegevens verwerking. Alleen de meest essentiële meta gegevens, inzichten of acties worden naar de Cloud verzonden voor verdere actie of onderzoek. 

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor het ontwikkelen van oplossingen waarmee IoT-apparaat & Azure IoT Edge gateway connectiviteit, configuratie en beheer worden vereenvoudigd. Het platform vermindert de belasting en de kosten van IoT-Apparaatbeheer,-bewerkingen en gerelateerde ontwikkelingen aanzienlijk. Klanten & partners kunnen een end-to-end bedrijfs oplossingen bouwen om een digitale feedback-lus in distributie centra te bereiken.

## <a name="business-insights--actions-via-data-egress"></a>Zakelijke inzichten & acties via gegevens uitvoer 
IoT Central-platform biedt uitgebreide uitbreidings opties via continue gegevens export (COB) en Api's. Zakelijke inzichten op basis van gegevens verwerking via telemetrie of ruwe telemetrie worden meestal geëxporteerd naar een gewenste LOB-toepassing. Dit kan worden bereikt via webhook, service bus, Event Hub of Blob-opslag om machine learning modellen te bouwen, te trainen en te implementeren & meer verrijkend inzicht.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van een [Digital Distribution Center-sjabloon](./tutorial-iot-central-digital-distribution-center-pnp.md)
* Meer informatie over [IOT Central Retail-sjablonen](./overview-iot-central-retail-pnp.md)
* Raadpleeg [IOT Central Overview](../core/overview-iot-central-pnp.md) voor meer informatie over IOT Central

---
title: Op de architectuur IoT verbonden logistiek | Microsoft Docs
description: Een architectuur van IoT Connected logistiek Application Temp late for IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: e462d879ea1c7756021cd3a241920fb0028a2e4f
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/29/2019
ms.locfileid: "73026389"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architectuur van IoT Central verbonden logistiek-toepassings sjabloon

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

Partners & klant kunnen gebruikmaken van de app-sjabloon & volgende richt lijnen voor het ontwikkelen van end-to-end **verbonden logistiek-oplossingen**.

> [!div class="mx-imgBorder"]
> ![verbonden logistiek-dash board](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Set met IoT-Tags voor het verzenden van telemetriegegevens naar een gateway apparaat
2. Gateway apparaten die telemetrie en geaggregeerde inzichten verzenden naar IoT Central
3. Gegevens worden doorgestuurd naar de gewenste Azure-service voor manipulatie
4. Azure-Services zoals ASA of Azure Functions kunnen worden gebruikt om gegevens stromen opnieuw op te maken en te verzenden naar de gewenste opslag accounts 
5. Verschillende zakelijke werk stromen kunnen worden aangedreven door zakelijke toepassingen voor eind gebruikers

## <a name="details"></a>Details
De volgende sectie bevat een overzicht van elk deel van de telemetrie-opname van de conceptuele architectuur van IoT-Tags & gateways

## <a name="iot-tags"></a>IoT-Tags
IoT-Tags bieden fysieke, omgevings-en omgevings sensor mogelijkheden zoals Tempe ratuur, vochtigheid, schokken, kantel & licht. IoT-Tags maken meestal verbinding met het gateway apparaat via ZigBee (802.15.4). Tags zijn goedkope Sens oren; Daarom kunnen ze aan het einde van een typische logistiek traject worden verwijderd om te voor komen dat uitdagingen met een omgekeerde logistiek worden gedaan.

## <a name="gateway"></a>Gateway
Gateways kunnen ook fungeren als IoT-Tags met hun omgevings detectie mogelijkheden. De gateway maakt Azure IoT-Cloud connectiviteit (MQTT) upstream via mobiele, Wi-Fi-kanalen.  De modi Bluetooth, NFC en 802.15.4 Wireless Sensor Network (WSN) worden gebruikt voor downstream-communicatie met IoT-Tags. Gateways bieden end-to-end veilige Cloud connectiviteit, IoT-Tags koppelen, aggregatie van sensor gegevens, gegevens retentie en de mogelijkheid om alarm drempels te configureren.

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor oplossings ontwikkeling dat de connectiviteit, configuratie en het beheer van IoT-apparaten vereenvoudigt. Het platform vermindert de belasting en de kosten van IoT-Apparaatbeheer,-bewerkingen en gerelateerde ontwikkelingen aanzienlijk. Klanten & partners kunnen een end-to-end bedrijfs oplossingen bouwen om een digitale feedback-lus in logistiek te bereiken.

## <a name="business-insights--actions-via-data-egress"></a>Zakelijke inzichten & acties via gegevens uitvoer 
IoT Central-platform biedt uitgebreide uitbreidings opties via continue gegevens export (COB) en Api's. Zakelijke inzichten op basis van gegevens verwerking via telemetrie of ruwe telemetrie worden meestal geëxporteerd naar een gewenste LOB-toepassing. Dit kan worden bereikt via webhook, service bus, Event Hub of Blob-opslag om machine learning modellen te bouwen, te trainen en te implementeren & meer verrijkend inzicht.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van een sjabloon voor een [verbonden logistiek-oplossing](./tutorial-iot-central-connected-logistics-pnp.md)
* Meer informatie over [IOT Central Retail-sjablonen](./overview-iot-central-retail-pnp.md)
* Raadpleeg [IOT Central Overview](../core/overview-iot-central-pnp.md) voor meer informatie over IOT Central

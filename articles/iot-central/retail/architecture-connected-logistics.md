---
title: Architectuur IoT Connected logistiek | Microsoft Documenten
description: Een architectuur van IoT Connected Logistics-toepassingssjabloon voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 8925b98269b67bfb8a96cb057982ee4e396f17ed
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686232"
---
# <a name="architecture-of-iot-central-connected-logistics-application-template"></a>Architectuur van IoT Central connected logistics application template



Partners & klant kunnen de app-sjabloon gebruiken & richtlijnen te volgen om end-to-end **verbonden logistieke oplossingen**te ontwikkelen.

> [!div class="mx-imgBorder"]
> ![verbonden logistiek dashboard](./media/concept-connected-logistics-architecture/connected-logistics-architecture.png)

1. Set IoT-tags die telemetriegegevens naar een gateway-apparaat verzenden
2. Gateway-apparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Gegevens worden doorgestuurd naar de gewenste Azure-service voor manipulatie
4. Azure-services zoals ASA of Azure-functies kunnen worden gebruikt om gegevensstromen te hervormen en naar de gewenste opslagaccounts te verzenden 
5. Verschillende zakelijke workflows kunnen worden aangedreven door zakelijke toepassingen voor eindgebruikers

## <a name="details"></a>Details
Volgende sectie schetst elk deel van de conceptuele architectuur Telemetrie opname van IoT Tags & Gateways

## <a name="iot-tags"></a>IoT-tags
IoT-tags bieden fysieke, omgevings- en omgevingssensormogelijkheden zoals temperatuur, vochtigheid, schok, kantelen &licht. IoT-tags maken doorgaans verbinding met het gatewayapparaat via Zigbee (802.15.4). Tags zijn minder dure sensoren; ze kunnen dus worden weggegooid aan het einde van een typische logistieke reis om uitdagingen met omgekeerde logistiek te voorkomen.

## <a name="gateway"></a>Gateway
Gateways kunnen ook fungeren als IoT-tags met hun omgevingsdetectiemogelijkheden. De gateway maakt upstream Azure IoT cloud connectivity (MQTT) mogelijk via mobiele, Wi-Fi-kanalen.  Bluetooth-, NFC- en WSN-modi (Wireless Sensor Network) worden gebruikt voor downstreamcommunicatie met IoT-tags. Gateways bieden end-to-end veilige cloudconnectiviteit, IoT-tagkoppeling, sensorgegevensaggregatie, gegevensretentie en de mogelijkheid om alarmdrempels te configureren.

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor het ontwikkelen van oplossingen dat de connectiviteit, configuratie en beheer van IoT-apparaten vereenvoudigt. Het platform vermindert de lasten en kosten van IoT-apparaatbeheer, -bewerkingen en gerelateerde ontwikkelingen aanzienlijk. Klanten & partners kunnen een end-to-end enterprise-oplossingen bouwen om een digitale feedbackloop in de logistiek te realiseren.

## <a name="business-insights-and-actions-using-data-egress"></a>Bedrijfsinzichten en -acties met behulp van gegevensuitgang 
IoT Central-platform biedt uitgebreide uitbreidbaarheidsopties via Continuous Data Export (CDE) en API's. Bedrijfsinzichten op basis van telemetriegegevensverwerking of ruwe telemetrie worden doorgaans geëxporteerd naar een voorkeurslijn-of-business-toepassing. Het kan worden bereikt met behulp van webhook, servicebus, event hub of blob-opslag om machine learning-modellen te bouwen, te trainen en te implementeren & inzichten verder te verrijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van [sjabloon verbonden logistieke oplossingen](./tutorial-iot-central-connected-logistics.md)
* Meer informatie over [IoT Central-retailsjablonen](./overview-iot-central-retail.md)
* Meer informatie over IoT Central verwijzen naar [IoT Central-overzicht](../core/overview-iot-central.md)

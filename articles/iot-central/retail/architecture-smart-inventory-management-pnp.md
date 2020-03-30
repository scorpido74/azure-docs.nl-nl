---
title: Architectuur IoT Smart Inventory Management | Microsoft Documenten
description: Een architectuur van IoT Smart Inventory Management-sjabloon voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: 756f15f6cb0d797d2a7db5fbe5c6157b2913b40b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77020859"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architectuur van iot central smart inventory management application template

Partners en klanten kunnen de app-sjabloon en de volgende richtlijnen gebruiken om end-to-end **slimme oplossingen voor voorraadbeheer** te ontwikkelen.

> [!div class="mx-imgBorder"]
> ![slim voorraadbeheer](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Set IoT-sensoren die telemetriegegevens naar een gateway-apparaat verzenden
2. Gateway-apparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
3. Gegevens worden doorgestuurd naar de gewenste Azure-service voor manipulatie
4. Azure-services zoals ASA of Azure-functies kunnen worden gebruikt om gegevensstromen te hervormen en naar de gewenste opslagaccounts te verzenden 
5. Verwerkte gegevens worden opgeslagen in hete opslag voor near real-time acties of koude opslag voor extra inzicht verbeteringen die is gebaseerd op ML of batch analyse. 
6. Logic Apps kunnen worden gebruikt om verschillende zakelijke workflows van stroom te maken in zakelijke toepassingen voor eindgebruikers

## <a name="details"></a>Details
Volgende sectie schetst elk deel van de conceptuele architectuur Telemetrie opname van Radio-frequency identification (RFID), Bluetooth low energy (BLE) tags

## <a name="rfid-tags"></a>RFID-tags
RFID-tags verzenden gegevens over een item via radiogolven. RFID-tags hebben meestal geen batterij, tenzij opgegeven. Tags ontvangen energie van de radiogolven gegenereerd door de lezer en zenden een signaal terug naar de RFID-lezer.

## <a name="ble-tags"></a>BLE-tags
Energy beacon zendt pakketten met gegevens op regelmatige tijdstippen uit. Beacon-gegevens worden gedetecteerd door BLE-lezers of geïnstalleerde services op smartphones en verzenden die vervolgens naar de cloud.

## <a name="rfid--ble-readers"></a>RFID & BLE-lezers
RFID-lezer zet de radiogolven om in een meer bruikbare vorm van gegevens. Informatie die van de tags wordt verzameld, wordt vervolgens opgeslagen in de lokale edge-server of naar de cloud verzonden met JSON-RPC 2.0 via MQTT.
BLE reader ook wel bekend als Access Points (AP) zijn vergelijkbaar met RFID-lezer. Het wordt gebruikt om Bluetooth-signalen in de buurt te detecteren en het bericht door te sturen naar lokale Azure IoT Edge of cloud met JSON-RPC 2.0 via MQTT.
Veel lezers zijn in staat om RFID en bakensignalen te lezen en extra sensormogelijkheden te bieden met betrekking tot temperatuur, vochtigheid, versnellingsmeter en gyroscoop.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge-gateway
Azure IoT Edge-server biedt een plek om die gegevens lokaal vooraf te verwerken voordat ze naar de cloud worden verzonden. We kunnen cloudworkloads ook implementeren voor kunstmatige intelligentie, Azure en services van derden, bedrijfslogica met behulp van standaardcontainers.

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor het ontwikkelen van oplossingen dat de connectiviteit, configuratie en beheer van IoT-apparaten vereenvoudigt. Het platform vermindert de lasten en kosten van IoT-apparaatbeheer, -bewerkingen en gerelateerde ontwikkelingen aanzienlijk. Klanten & partners kunnen een end-to-end enterprise-oplossingen bouwen om een digitale feedbacklus in voorraadbeheer te realiseren.

## <a name="business-insights--actions-using-data-egress"></a>Bedrijfsinzichten & acties met behulp van gegevensuitgang 
IoT Central-platform biedt uitgebreide uitbreidbaarheidsopties via Continuous Data Export (CDE) en API's. Bedrijfsinzichten op basis van telemetriegegevensverwerking of ruwe telemetrie worden doorgaans geëxporteerd naar een voorkeurslijn-of-business-toepassing. Het kan worden bereikt met behulp van webhook, servicebus, event hub of blob-opslag om machine learning-modellen te bouwen, te trainen en te implementeren & inzichten verder te verrijken.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van [een sjabloon voor slim voorraadbeheer](./tutorial-iot-central-smart-inventory-management-pnp.md)
* Meer informatie over [IoT Central-retailsjablonen](./overview-iot-central-retail-pnp.md)
* Meer informatie over IoT Central verwijzen naar [IoT Central-overzicht](../core/overview-iot-central.md)

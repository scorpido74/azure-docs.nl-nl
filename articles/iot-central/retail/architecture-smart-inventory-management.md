---
title: Architectuur IoT Smart Inventory Management | Microsoft Docs
description: Een architectuur van IoT Smart Inventory Management-sjabloon voor IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.custom: mqtt
ms.openlocfilehash: 9e0b4a3c22c1e6008bdce7fecc1d122fe317dd58
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81686207"
---
# <a name="architecture-of-iot-central-smart-inventory-management-application-template"></a>Architectuur van IoT Central sjabloon slimme inventaris beheer toepassing

Partners en klanten kunnen gebruikmaken van de app-sjabloon en de volgende richt lijnen voor het ontwikkelen van end-to-end **Smart Inventory Management** -oplossingen.

> [!div class="mx-imgBorder"]
> ![Intelligent inventaris beheer](./media/concept-smart-inventory-mgmt-architecture/smart-inventory-management-architecture.png)

1. Set IoT Sens oren die telemetriegegevens naar een gateway apparaat verzenden
2. Gateway apparaten die telemetrie en geaggregeerde inzichten verzenden naar IoT Central
3. Gegevens worden doorgestuurd naar de gewenste Azure-service voor manipulatie
4. Azure-Services zoals ASA of Azure Functions kunnen worden gebruikt om gegevens stromen opnieuw op te maken en te verzenden naar de gewenste opslag accounts 
5. Verwerkte gegevens worden opgeslagen in warme opslag voor bijna realtime acties of koude opslag voor aanvullende inzicht verbeteringen die zijn gebaseerd op ML of batch analyse. 
6. Logic Apps kan worden gebruikt om verschillende zakelijke werk stromen in zakelijke toepassingen voor eind gebruikers uit te scha kelen

## <a name="details"></a>Details
De volgende sectie bevat een overzicht van elk deel van de telemetrie-opname van de conceptuele architectuur (RFID), Bluetooth Low Energy (Tour)-Tags

## <a name="rfid-tags"></a>RFID-tags
RFID-tags verzenden gegevens over een item via radio golven. RFID-tags hebben doorgaans geen accu, tenzij ze zijn opgegeven. Tags ontvangen energie van de radio golven die zijn gegenereerd door de lezer en sturen een signaal naar de RFID-lezer.

## <a name="ble-tags"></a>Handelslabels
Energie baken verzendt met regel matige intervallen pakketten met gegevens. Beacon-gegevens worden gedetecteerd door de handel lezers of geïnstalleerde services op smartphone en verzenden deze vervolgens naar de Cloud.

## <a name="rfid--ble-readers"></a>RFID &er-lezers
RFID-lezer converteert de radio golven naar een meer bruikbare vorm van gegevens. De gegevens die worden verzameld uit de tags worden vervolgens opgeslagen in de lokale Edge-Server of naar de Cloud verzonden met behulp van JSON-RPC 2,0 via MQTT.
De uitgevende of Access points (AP) is vergelijkbaar met de RFID-lezer. Het wordt gebruikt om Bluetooth-signalen in de buurt te detecteren en het bericht door te sturen naar lokale Azure IoT Edge of Cloud met behulp van JSON-RPC 2,0 via MQTT.
Veel lezers kunnen RFID-en baken signalen lezen en extra sensor mogelijkheden bieden met betrekking tot Tempe ratuur, vochtigheid, versnellings meter en gyroscope.

## <a name="azure-iot-edge-gateway"></a>Azure IoT Edge gateway
Azure IoT Edge Server biedt een plek om die gegevens lokaal voor te verwerken voordat deze naar de cloud worden verzonden. We kunnen ook Cloud werkbelastingen kunst matige intelligentie, Azure en services van derden, bedrijfs logica, gebruiken voor het implementeren van standaard containers.

## <a name="device-management-with-iot-central"></a>Apparaatbeheer met IoT Central 
Azure IoT Central is een platform voor oplossings ontwikkeling dat de connectiviteit, configuratie en het beheer van IoT-apparaten vereenvoudigt. Het platform vermindert de belasting en de kosten van IoT-Apparaatbeheer,-bewerkingen en gerelateerde ontwikkelingen aanzienlijk. Klanten & partners kunnen een end-to-end bedrijfs oplossingen bouwen om een digitale feedback-lus in het voorraad beheer te bereiken.

## <a name="business-insights--actions-using-data-egress"></a>Zakelijke inzichten & acties die gebruikmaken van gegevens uitvoer 
IoT Central-platform biedt uitgebreide uitbreidings opties door middel van een continue gegevens export (COB) en Api's. Zakelijke inzichten op basis van gegevens verwerking via telemetrie of ruwe telemetrie worden meestal geëxporteerd naar een gewenste LOB-toepassing. Dit kan worden bereikt met behulp van webhook, service bus, Event Hub of blobopslag om machine learning modellen te bouwen, te trainen en te implementeren & meer verrijkend inzicht.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het implementeren van een [Smart Inventory Management-sjabloon](./tutorial-iot-central-smart-inventory-management.md)
* Meer informatie over [IOT Central Retail-sjablonen](./overview-iot-central-retail.md)
* Raadpleeg [IOT Central Overview](../core/overview-iot-central.md) voor meer informatie over IOT Central

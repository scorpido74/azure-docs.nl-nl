---
title: Azure IoT Hub vergelijken met Azure Event Hubs | Microsoft Documenten
description: Een vergelijking van de Azure-services van IoT Hub en Event Hubs die functionele verschillen en use cases benadrukken. De vergelijking omvat ondersteunde protocollen, apparaatbeheer, bewaking en bestandsuploads.
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: 7a589ba80b61ea5ef9ea1c941e9a0218a1653c99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60735509"
---
# <a name="connecting-iot-devices-to-azure-iot-hub-and-event-hubs"></a>IoT-apparaten verbinden met Azure: IoT Hub en Event Hubs

Azure biedt services die speciaal zijn ontwikkeld voor verschillende soorten connectiviteit en communicatie om u te helpen uw gegevens te verbinden met de kracht van de cloud. Zowel Azure IoT Hub als Azure Event Hubs zijn cloudservices die grote hoeveelheden gegevens kunnen opnemen en die gegevens kunnen verwerken of opslaan voor bedrijfsinzichten. De twee services zijn vergelijkbaar omdat ze beide ondersteuning bieden voor het innemen van gegevens met een lage latentie en hoge betrouwbaarheid, maar ze zijn ontworpen voor verschillende doeleinden. IoT Hub is ontwikkeld om tegemoet te komen aan de unieke vereisten voor het verbinden van IoT-apparaten met de Azure-cloud, terwijl Event Hubs is ontworpen voor het streamen van big data. Microsoft raadt aan Azure IoT Hub te gebruiken om IoT-apparaten te verbinden met Azure

Azure IoT Hub is de cloudgateway die IoT-apparaten verbindt om gegevens te verzamelen en bedrijfsinzichten en automatisering te stimuleren. Daarnaast bevat IoT Hub functies die de relatie tussen uw apparaten en uw backend-systemen verrijken. Bidirectionele communicatiemogelijkheden betekenen dat u, terwijl u gegevens van apparaten ontvangt, ook opdrachten en beleidsregels terug sturen naar apparaten. Gebruik bijvoorbeeld cloud-to-device messaging om eigenschappen bij te werken of apparaatbeheeracties aan te roepen. Cloud-to-device-communicatie stelt u ook in staat om cloudintelligence naar uw edge-apparaten te sturen met Azure IoT Edge. De unieke identiteit op apparaatniveau van IoT Hub helpt uw IoT-oplossing beter te beveiligen tegen mogelijke aanvallen. 

[Azure Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md) is de big data streaming service van Azure. Het is ontworpen voor scenario's voor gegevensstromen met een hoge doorvoer waarbij klanten miljarden aanvragen per dag kunnen verzenden. Event Hubs maakt gebruik van een gepartitioneerd verbruikersmodel om uw stroom uit te breiden, en is geïntegreerd in de big data- en analyseservices van Azure, met inbegrip van Databricks, Stream Analytics, ADLS en HDInsight. Met functies zoals Event Hubs Capture en Auto-Inflate, is deze service ontworpen om uw big data-apps en -oplossingen te ondersteunen. Daarnaast gebruikt IoT Hub Event Hubs voor het telemetriestroompad, zodat uw IoT-oplossing ook profiteert van de enorme kracht van Event Hubs.

Om samen te vatten, beide oplossingen zijn ontworpen voor het innemen van gegevens op grote schaal. Alleen IoT Hub biedt de uitgebreide IoT-specifieke mogelijkheden die zijn ontworpen voor u om de bedrijfswaarde van het verbinden van uw IoT-apparaten met de Azure-cloud te maximaliseren.  Als uw IoT-reis nog maar net is begonnen, zorgt u ervoor dat u vanaf het begin met IoT Hub uw scenario's voor het innemen van gegevens direct toegang hebt tot de volledige IoT-mogelijkheden zodra uw bedrijf en technische behoeften dit vereisen.

In de volgende tabel vindt u meer informatie over hoe de twee lagen IoT Hub zich verhouden tot gebeurtenishubs wanneer u ze evalueert op IoT-mogelijkheden. Zie Hoe u [de juiste IoT Hub-laag kiest](iot-hub-scaling.md)voor meer informatie over de standaard- en basislagen van IoT Hub.

| IoT-mogelijkheden | Standaardlaag IoT Hub | Basislaag voor IoT-hub | Event Hubs |
| --- | --- | --- | --- |
| Device-to-cloud berichten | ![Selecteren][checkmark] | ![Selecteren][checkmark] | ![Selecteren][checkmark] |
| Protocollen: HTTPS, AMQP, AMQP via webSockets | ![Selecteren][checkmark] | ![Selecteren][checkmark] | ![Selecteren][checkmark] |
| Protocollen: MQTT, MQTT via webSockets | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Identiteit per apparaat | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Bestandsupload vanaf apparaten | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Service voor inrichting van apparaten | ![Selecteren][checkmark] | ![Selecteren][checkmark] |  |
| Cloud-to-device messaging | ![Selecteren][checkmark] |  |  |
| Apparaatdubbel- en apparaatbeheer | ![Selecteren][checkmark] |  |  |
| Apparaatstreams (voorbeeld) | ![Selecteren][checkmark] |  |  |
| IoT Edge | ![Selecteren][checkmark] |  |  |

Zelfs als de enige use case device-to-cloud data opname is, raden we ten zeerste aan om IoT Hub te gebruiken, omdat het een service biedt die is ontworpen voor IoT-apparaatconnectiviteit. 

### <a name="next-steps"></a>Volgende stappen

Zie de [IoT Hub-ontwikkelaarshandleiding](iot-hub-devguide.md)om de mogelijkheden van IoT Hub verder te verkennen.

<!-- This one reference link is used over and over. --robinsh -->
[checkmark]: ./media/iot-hub-compare-event-hubs/ic195031.png

---
title: Azure IoT Central micro-fulfillment center | Microsoft Documenten
description: Leer een toepassing voor microafhandelingscentrumbouwen met behulp van onze micro-fulfillment center-toepassingssjabloon in IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: b0d030240ebe22886826b7a25bd5ca7b8f54e358
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "81000425"
---
# <a name="micro-fulfillment-center-architecture"></a>Architectuur van het micro-fulfilmentcenter

Met oplossingen voor microfulfilmentcenters u alle aspecten van een volledig geautomatiseerd fulfillmentcentrum digitaal verbinden, bewaken en beheren om de kosten te verlagen door downtime te elimineren en tegelijkertijd de beveiliging en algehele efficiëntie te verhogen. Deze oplossingen kunnen worden gebouwd met behulp van een van de toepassingssjablonen binnen IoT Central en de architectuur hieronder als leidraad.

![Azure IoT Central Store Analytics](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Set IoT-sensoren die telemetriegegevens naar een gateway-apparaat verzenden
- Gateway-apparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
- Continue gegevensexport naar de gewenste Azure-service voor manipulatie
- Gegevens kunnen in het gewenste formaat worden gestructureerd en naar een opslagservice worden verzonden
- Bedrijfstoepassingen kunnen gegevens opvragen en inzichten genereren die retailactiviteiten van stroom voorzetten
 
Laten we eens kijken naar belangrijke componenten die over het algemeen een rol spelen in een micro-fulfillment center oplossing.

## <a name="robotic-carriers"></a>Robotdragers

Een micro-fulfilment center oplossing zal waarschijnlijk een grote set van robotdragers genereren verschillende soorten telemetrie signalen. Deze signalen kunnen worden ingenomen door een gateway-apparaat, geaggregeerd, en vervolgens verzonden naar IoT Central, zoals weerspiegeld door de linkerkant van het architectuurdiagram.  

## <a name="condition-monitoring-sensors"></a>Sensoren voor conditiebewaking

Een IoT-oplossing begint met een reeks sensoren die zinvolle signalen vastleggen vanuit uw fulfillmentcenter. Het wordt weerspiegeld door verschillende soorten sensoren aan de linkerkant van de architectuur diagram hierboven.

## <a name="gateway-devices"></a>Gateway-apparaten

Veel IoT-sensoren kunnen ruwe signalen rechtstreeks naar de cloud of naar een gateway-apparaat in de buurt voeren. Het gatewayapparaat voert gegevensaggregatie aan de rand uit voordat het overzichtsinzichten naar een IoT Central-toepassing verzendt. De gateway-apparaten zijn ook verantwoordelijk voor het doorgeven van commando- en besturingsbewerkingen aan de sensorapparaten indien van toepassing. 

## <a name="iot-central-application"></a>IoT Central-toepassing

De Azure IoT Central-toepassing neemt gegevens op van verschillende soorten IoT-sensoren, robots en gateway-apparaten binnen de fulfillmentcenteromgeving en genereert een reeks zinvolle inzichten.

Azure IoT Central biedt de winkeloperator ook een op maat gemaakte ervaring waarmee ze de infrastructuurapparaten op afstand kunnen bewaken en beheren.

## <a name="data-transform"></a>Gegevenstransformatie
De Azure IoT Central-toepassing binnen een oplossing kan worden geconfigureerd om ruwe of geaggregeerde inzichten te exporteren naar een set Azure PaaS-services (Platform-as-a-Service) die gegevensmanipulatie kunnen uitvoeren en deze inzichten kunnen verrijken voordat ze in een bedrijfstoepassing worden geplaatst. 

## <a name="business-application"></a>Bedrijfstoepassing
De IoT-gegevens kunnen worden gebruikt om verschillende soorten bedrijfstoepassingen van stroom te maken die binnen een retailomgeving worden geïmplementeerd. Een fulfillment center manager of medewerker kan deze toepassingen gebruiken om bedrijfsinzichten te visualiseren en zinvolle acties in realtime te ondernemen. Volg de [zelfstudie](./tutorial-in-store-analytics-create-app.md)voor meer informatie over het bouwen van een realtime Power BI-dashboard voor uw retailteam.

## <a name="next-steps"></a>Volgende stappen
* Ga aan de slag met de toepassingssjabloon [Micro-fulfillment Center.](https://aka.ms/checkouttemplate) 
* Neem een kijkje op de [tutorial](https://aka.ms/mfc-tutorial) die u begeleidt hoe u een oplossing bouwen met behulp van de micro-fulfillment Center app sjabloon.

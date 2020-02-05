---
title: Azure IoT Central micro fulfillment Center | Microsoft Docs
description: Meer informatie over het bouwen van een micro fulfillment Center-toepassing met behulp van onze micro-fulfillment Center-toepassings sjabloon in IoT Central
author: avneet723
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: f752c77a6a62b9b259a8bb1869ca03ff6a19b1f5
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77020876"
---
# <a name="micro-fulfillment-center-architecture"></a>Architectuur van micro fulfill Center

Met oplossingen van micro fulfill Center kunt u alle aspecten van een volledig geautomatiseerd fulfillment-centrum digitaal verbinden, bewaken en beheren om de kosten te verlagen door uitval tijd te elimineren en de beveiliging en algemene efficiëntie te verg Roten. Deze oplossingen kunnen worden gemaakt met behulp van een van de toepassings sjablonen in IoT Central en de architectuur hieronder als richt lijn.

![Azure IoT Central Store-analyses](./media/architecture/micro-fulfillment-center-architecture-frame.png)

- Set IoT Sens oren die telemetriegegevens naar een gateway apparaat verzenden
- Gateway apparaten die telemetrie en geaggregeerde inzichten verzenden naar IoT Central
- Continue gegevens export naar de gewenste Azure-service voor manipulatie
- Gegevens kunnen worden gestructureerd in de gewenste indeling en naar een opslag service worden verzonden
- Zakelijke toepassingen kunnen gegevens opvragen en inzichten genereren die de werking van Powers
 
Laten we eens kijken naar belang rijke onderdelen die doorgaans een deel spelen in een oplossing voor micro-fulfillment Center.

## <a name="robotic-carriers"></a>Automatische vervoerders

Een micro-fulfillment Center-oplossing heeft waarschijnlijk een grote set robot-providers die verschillende soorten telemetrie-signalen genereren. Deze signalen kunnen worden opgenomen door een gateway apparaat, geaggregeerd en vervolgens naar IoT Central worden verzonden, zoals wordt weer gegeven door de linkerkant van het architectuur diagram.  

## <a name="condition-monitoring-sensors"></a>Sens oren voor voorwaarde bewaking

Een IoT-oplossing begint met een set Sens oren die zinvolle signalen in uw fulfillment Center vastleggen. Het wordt weer gegeven door verschillende Sens oren helemaal links in het architectuur diagram hierboven.

## <a name="gateway-devices"></a>Gateway-apparaten

Veel IoT Sens oren kunnen onbewerkte signalen rechtstreeks in de Cloud of op een gateway apparaat die zich in de buurt bevindt. Het gateway apparaat voert gegevens aggregatie aan de rand uit voordat Summary Insights wordt verzonden naar een IoT Central-toepassing. De gateway apparaten zijn ook verantwoordelijk voor het door sturen van opdracht-en controle bewerkingen naar de sensor apparaten, indien van toepassing. 

## <a name="iot-central-application"></a>IoT Central toepassing

De Azure IoT Central-toepassing neemt gegevens op uit verschillende soorten IoT Sens oren, robots, als gateway apparaten in de fulfillment Center-omgeving en genereert een set zinvolle inzichten.

Azure IoT Central biedt ook een aangepaste ervaring met de opslag operator, zodat deze de infrastructuur apparaten op afstand kunnen bewaken en beheren.

## <a name="data-transform"></a>Gegevens transformatie
De Azure IoT Central-toepassing binnen een oplossing kan worden geconfigureerd voor het exporteren van RAW of geaggregeerde inzichten naar een set Azure PaaS-Services (platform-as-a-Service) waarmee gegevens kunnen worden gemanipuleerd en deze inzichten worden verrijkd voordat ze in een bedrijf worden geaanvoer modules. 

## <a name="business-application"></a>Zakelijke toepassing
De IoT-gegevens kunnen worden gebruikt om verschillende soorten zakelijke toepassingen uit te scha kelen die in een bedrijfs omgeving worden geïmplementeerd. Een fulfillment Center Manager of werk nemer kan deze toepassingen gebruiken om zakelijke inzichten te visualiseren en in real-time zinvolle acties uit te voeren. Volg de [zelf studie](./tutorial-in-store-analytics-create-app-pnp.md)als u wilt weten hoe u een real-time Power bi dash board kunt maken voor uw Retail team.

## <a name="next-steps"></a>Volgende stappen
* Ga aan de slag met de toepassings sjabloon [micro-fulfillment Center](https://aka.ms/checkouttemplate) . 
* Bekijk de [zelf studie](https://aka.ms/mfc-tutorial) waarin u wordt begeleid bij het bouwen van een oplossing met behulp van de micro-fulfillment Center app-sjabloon.

---
title: Architectuur van Store Analytics
description: Meer informatie over het bouwen van een in-Store Analytics-toepassing met behulp van een uitcheck toepassings sjabloon in IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6c2514bd078cc3feee4bd2802cf314079b824311
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022117"
---
# <a name="in-store-analytics-architecture"></a>Architectuur van de in-Store-analyses



In-Store Analytics-oplossingen kunt u verschillende voor waarden in de Retail Store-omgeving bewaken. Deze oplossingen kunnen worden gemaakt met behulp van een van de toepassings sjablonen in IoT Central en de architectuur hieronder als richt lijn.


![Azure IoT Central Store-analyses](./media/architecture/store-analytics-architecture-frame.png)

- Set IoT Sens oren die telemetriegegevens naar een gateway apparaat verzenden
- Gateway apparaten die telemetrie en geaggregeerde inzichten verzenden naar IoT Central
- Continue gegevens export naar de gewenste Azure-service voor manipulatie
- Gegevens kunnen worden gestructureerd in de gewenste indeling en naar een opslag service worden verzonden
- Zakelijke toepassingen kunnen gegevens opvragen en inzichten genereren die de werking van Powers
 
Laten we eens kijken naar belang rijke onderdelen die doorgaans een deel spelen in een in-Store Analytics-oplossing.

## <a name="condition-monitoring-sensors"></a>Sens oren voor voorwaarde bewaking

Een IoT-oplossing begint met een set Sens oren die zinvolle signalen in een Retail Store-omgeving vastleggen. Het wordt weer gegeven door verschillende Sens oren helemaal links in het architectuur diagram hierboven.

## <a name="gateway-devices"></a>Gateway-apparaten

Veel IoT Sens oren kunnen onbewerkte signalen rechtstreeks in de Cloud of op een gateway apparaat die zich in de buurt bevindt. Het gateway apparaat voert gegevens aggregatie aan de rand uit voordat Summary Insights wordt verzonden naar een IoT Central-toepassing. De gateway apparaten zijn ook verantwoordelijk voor het door sturen van opdracht-en controle bewerkingen naar de sensor apparaten, indien van toepassing. 

## <a name="iot-central-application"></a>IoT Central toepassing

De Azure IoT Central-toepassing neemt gegevens uit verschillende soorten IoT-Sens oren op als gateway apparaten in de Retail Store-omgeving en genereert een set zinvolle inzichten.

Azure IoT Central biedt ook een aangepaste ervaring met de opslag operator, zodat deze de infrastructuur apparaten op afstand kunnen bewaken en beheren.

## <a name="data-transform"></a>Gegevens transformatie
De Azure IoT Central-toepassing binnen een oplossing kan worden geconfigureerd voor het exporteren van RAW of geaggregeerde inzichten naar een set Azure PaaS (platform-as-a-Service) waarmee gegevens kunnen worden gemanipuleerd en die inzichten kan worden verrijkd voordat ze in een bedrijf worden geaanvoer modules. 

## <a name="business-application"></a>Zakelijke toepassing
De IoT-gegevens kunnen worden gebruikt om verschillende soorten zakelijke toepassingen uit te scha kelen die in een bedrijfs omgeving worden geïmplementeerd. Een winkel manager of een personeelslid kan deze toepassingen gebruiken om zakelijke inzichten te visualiseren en in real-time zinvolle acties uit te voeren. Volg de [zelf studie](./tutorial-in-store-analytics-create-app-pnp.md)als u wilt weten hoe u een real-time Power bi dash board kunt maken voor uw Retail team.

## <a name="next-steps"></a>Volgende stappen
* Ga aan de slag met de [Uitchecken in-Store Analytics](https://aka.ms/checkouttemplate) en [in-Store Analytics-voor waarde bewaking](https://aka.ms/conditiontemplate) toepassings sjablonen. 
* Bekijk de [end-to-end-zelf studie](https://aka.ms/storeanalytics-tutorial) waarin wordt uitgelegd hoe u een oplossing bouwt met behulp van een van de in-Store Analytics-toepassings sjablonen.

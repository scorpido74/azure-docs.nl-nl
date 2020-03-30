---
title: Store Analytics-architectuur
description: Meer informatie over het bouwen van een in-store analytics-toepassing met de sjabloon Checkout-toepassing in IoT Central
author: avneets
ms.author: avneets
ms.date: 10/13/2019
ms.topic: overview
ms.service: iot-central
ms.subservice: iot-central-retail
services: iot-central
manager: eliotgra
ms.openlocfilehash: 6c2514bd078cc3feee4bd2802cf314079b824311
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77022117"
---
# <a name="in-store-analytics-architecture"></a>In-store analytics-architectuur



Met in-store analytics-oplossingen u verschillende omstandigheden binnen de winkelomgeving monitoren. Deze oplossingen kunnen worden gebouwd met behulp van een van de toepassingssjablonen binnen IoT Central en de architectuur hieronder als leidraad.


![Azure IoT Central Store Analytics](./media/architecture/store-analytics-architecture-frame.png)

- Set IoT-sensoren die telemetriegegevens naar een gateway-apparaat verzenden
- Gateway-apparaten die telemetrie en geaggregeerde inzichten naar IoT Central verzenden
- Continue gegevensexport naar de gewenste Azure-service voor manipulatie
- Gegevens kunnen in het gewenste formaat worden gestructureerd en naar een opslagservice worden verzonden
- Bedrijfstoepassingen kunnen gegevens opvragen en inzichten genereren die retailactiviteiten van stroom voorzetten
 
Laten we eens kijken naar belangrijke onderdelen die over het algemeen een rol spelen in een in-store analytics-oplossing.

## <a name="condition-monitoring-sensors"></a>Sensoren voor conditiebewaking

Een IoT-oplossing begint met een set sensoren die zinvolle signalen opvangen vanuit een winkelomgeving. Het wordt weerspiegeld door verschillende soorten sensoren aan de linkerkant van de architectuur diagram hierboven.

## <a name="gateway-devices"></a>Gateway-apparaten

Veel IoT-sensoren kunnen ruwe signalen rechtstreeks naar de cloud of naar een gateway-apparaat in de buurt voeren. Het gatewayapparaat voert gegevensaggregatie aan de rand uit voordat het overzichtsinzichten naar een IoT Central-toepassing verzendt. De gateway-apparaten zijn ook verantwoordelijk voor het doorgeven van commando- en besturingsbewerkingen aan de sensorapparaten indien van toepassing. 

## <a name="iot-central-application"></a>IoT Central-toepassing

De Azure IoT Central-toepassing neemt gegevens op van verschillende soorten IoT-sensoren en gateway-apparaten binnen de winkelomgeving en genereert een reeks zinvolle inzichten.

Azure IoT Central biedt de winkeloperator ook een op maat gemaakte ervaring waarmee ze de infrastructuurapparaten op afstand kunnen bewaken en beheren.

## <a name="data-transform"></a>Gegevenstransformatie
De Azure IoT Central-toepassing binnen een oplossing kan worden geconfigureerd om ruwe of geaggregeerde inzichten te exporteren naar een set Azure PaaS-services (Platform-as-a Service) die gegevensmanipulatie kunnen uitvoeren en deze inzichten kunnen verrijken voordat ze in een bedrijf worden geplaatst Toepassing. 

## <a name="business-application"></a>Bedrijfstoepassing
De IoT-gegevens kunnen worden gebruikt om verschillende soorten bedrijfstoepassingen van stroom te maken die binnen een retailomgeving worden geïmplementeerd. Een winkelmanager of medewerker kan deze toepassingen gebruiken om bedrijfsinzichten te visualiseren en zinvolle acties in realtime te ondernemen. Volg de [zelfstudie](./tutorial-in-store-analytics-create-app-pnp.md)voor meer informatie over het bouwen van een realtime Power BI-dashboard voor uw retailteam.

## <a name="next-steps"></a>Volgende stappen
* Ga aan de slag met de toepassingssjablonen [In-Store Analytics Checkout](https://aka.ms/checkouttemplate) en [In-Store Analytics Condition Monitoring.](https://aka.ms/conditiontemplate) 
* Neem een kijkje op het [einde van de tutorial](https://aka.ms/storeanalytics-tutorial) die u begeleidt hoe u een oplossing bouwen met behulp van een van de In-Store Analytics-toepassingssjablonen.

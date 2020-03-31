---
title: Wat is OPC Publisher - Azure | Microsoft Documenten
description: In dit artikel vindt u een overzicht van de functies van OPC Publisher. Hiermee u gecodeerde JSON-telemetriegegevens publiceren met behulp van een JSON-payload naar Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73824781"
---
# <a name="what-is-opc-publisher"></a>Wat is OPC Publisher?

OPC Publisher is een referentie-implementatie die laat zien hoe:

- Maak verbinding met bestaande OPC UA-servers.
- Publiceer JSON-gecodeerde telemetriegegevens van OPC UA-servers in de OPC UA Pub/Sub-indeling, met behulp van een JSON-payload, naar Azure IoT Hub.

U alle transportprotocollen gebruiken die de Azure IoT Hub-client SDK ondersteunt: HTTPS, AMQP en MQTT.

De referentie-uitvoering omvat:

- Een OPC *UA-client* voor verbinding met bestaande OPC UA-servers die u in uw netwerk hebt.
- Een OPC *UA-server* op poort 62222 die u gebruiken om te beheren wat er is gepubliceerd en biedt IoT Hub directe methoden om hetzelfde te doen.

U de [referentie-implementatie](https://github.com/Azure/iot-edge-opc-publisher) van OPC Publisher downloaden van GitHub.

De applicatie wordt geïmplementeerd met behulp van .NET Core-technologie en kan worden uitgevoerd op elk platform dat wordt ondersteund door .NET Core.

OPC Publisher implementeert logica opnieuw proberen om verbindingen te leggen met eindpunten die niet reageren op een bepaald aantal keep alive-verzoeken. Als een OPC UA-server bijvoorbeeld niet meer reageert vanwege een stroomstoring.

Voor elk afzonderlijk publicatieinterval naar een OPC UA-server maakt de toepassing een apart abonnement waarop alle knooppunten met dit publicatieinterval worden bijgewerkt.

OPC Publisher ondersteunt batching van de gegevens die naar IoT Hub worden verzonden om de netwerkbelasting te verminderen. Deze batching verzendt een pakket alleen naar IoT Hub als de geconfigureerde pakketgrootte is bereikt.

Deze applicatie gebruikt de OPC Foundation OPC UA referentiestack als NuGet pakketten. Zie [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) voor de licentievoorwaarden.

### <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd wat OPC Publisher is, is de voorgestelde volgende stap om te leren hoe [opc publisher configureren](howto-opc-publisher-configure.md).

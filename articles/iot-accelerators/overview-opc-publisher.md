---
title: Wat is OPC Publisher-Azure | Microsoft Docs
description: Dit artikel bevat een overzicht van de functies van OPC Publisher. U kunt gecodeerde JSON-telemetriegegevens met behulp van een JSON-nettolading publiceren naar Azure IoT Hub.
author: dominicbetts
ms.author: dobett
ms.date: 06/10/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 2310a81d3cfaeff203134af9968bc2d5caea3e9c
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824781"
---
# <a name="what-is-opc-publisher"></a>Wat is OPC Publisher?

OPC Publisher is een referentie-implementatie die laat zien hoe u:

- Verbinding maken met bestaande OPC UA-servers.
- Met JSON gecodeerde telemetriegegevens publiceren van OPC UA-servers in OPC UA pub/sub-indeling, met behulp van een JSON-nettolading naar Azure IoT Hub.

U kunt elk van de transport protocollen gebruiken die de Azure IoT Hub client-SDK ondersteunt: HTTPS, AMQP en MQTT.

De referentie-implementatie omvat:

- Een OPC UA- *client* voor het maken van verbinding met bestaande OPC UA-servers die u in uw netwerk hebt.
- Een OPC UA- *Server* op poort 62222 die u kunt gebruiken voor het beheren van wat wordt gepubliceerd en biedt IOT hub directe methoden om hetzelfde te doen.

U kunt de [OPC Uitgever referentie-implementatie](https://github.com/Azure/iot-edge-opc-publisher) downloaden van github.

De toepassing wordt geïmplementeerd met behulp van .NET Core-technologie en kan worden uitgevoerd op elk platform dat wordt ondersteund door .NET core.

OPC Publisher implementeert logica voor nieuwe pogingen om verbindingen tot stand te brengen met eind punten die niet reageren op een bepaald aantal Keep Alive-aanvragen. Als een OPC UA-server bijvoorbeeld niet meer reageert vanwege een stroom storing.

Voor elke afzonderlijke publicatie-interval naar een OPC UA-server, maakt de toepassing een afzonderlijk abonnement dat alle knoop punten met dit publicatie interval worden bijgewerkt.

OPC Publisher ondersteunt batch verwerking van de gegevens die worden verzonden naar IoT Hub om de netwerk belasting te verminderen. Deze batch verwerking verzendt een pakket naar IoT Hub alleen als de geconfigureerde pakket grootte is bereikt.

Deze toepassing maakt gebruik van de OPC Foundation OPC UA-referentie stack als NuGet-pakketten. Zie [https://opcfoundation.org/license/redistributables/1.3/](https://opcfoundation.org/license/redistributables/1.3/) voor de licentie voorwaarden.

### <a name="next-steps"></a>Volgende stappen

Nu u hebt geleerd wat OPC Publisher is, is de voorgestelde volgende stap te leren hoe u [OPC Publisher kunt configureren](howto-opc-publisher-configure.md).

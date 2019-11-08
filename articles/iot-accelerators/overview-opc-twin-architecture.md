---
title: OPC, dubbele architectuur-Azure | Microsoft Docs
description: Dit artikel bevat een overzicht van de OPC-dubbele architectuur. Het bevat informatie over de detectie, activering, bladeren en controle van de-server.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819857"
---
# <a name="opc-twin-architecture"></a>OPC, dubbele architectuur

De volgende diagrammen illustreren de OPC-dubbele architectuur.

## <a name="discover-and-activate"></a>Detecteren en activeren

1. De operator schakelt netwerk scan in voor de module of maakt een eenmalige detectie met behulp van een detectie-URL. De gedetecteerde eind punten en toepassings gegevens worden via telemetrie verzonden naar de onboarding-agent voor verwerking.  De taak OPC UA-agent voor het voorbereiden van het apparaat verwerkt OPC UA server-detectie gebeurtenissen die worden verzonden door de methode OPC-dubbele IoT Edge in de modus detectie of scan. De detectie gebeurtenissen resulteren in het registreren van toepassingen en updates in het OPC UA-Device Registry.

   ![Hoe OPC dubbele Works](media/overview-opc-twin-architecture/opc-twin1.png)

1. De operator inspecteert het certificaat van het gedetecteerde eind punt en activeert het geregistreerde eind punt voor toegang. 

   ![Hoe OPC dubbele Works](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bladeren en bewaken

1. Eenmaal geactiveerd, kan de operator de dubbele service REST API gebruiken om te bladeren of te controleren op het server informatie model, object variabelen lezen/schrijven en aanroep methoden.  De gebruiker gebruikt een vereenvoudigd OPC UA API, die volledig wordt weer gegeven in HTTP en JSON.

   ![Hoe OPC dubbele Works](media/overview-opc-twin-architecture/opc-twin3.png)

1. De dubbele service REST-interface kan ook worden gebruikt voor het maken van bewaakte items en abonnementen in de OPC-Uitgever. Met de OPC-Publisher kan telemetrie worden verzonden vanuit OPC UA-server systemen naar IoT Hub. Zie [What is OPC Publisher](overview-opc-publisher.md)(Engelstalig) voor meer informatie over OPC Publisher.

   ![Hoe OPC dubbele Works](media/overview-opc-twin-architecture/opc-twin4.png)

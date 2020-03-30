---
title: OPC Twin-architectuur - Azure | Microsoft Documenten
description: Dit artikel geeft een overzicht van de OPC Twin architectuur. Het beschrijft over de detectie, activering, browsen en monitoring van de server.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: b8d4424c92ff24c36650e34a5d050bdc5f0f9091
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "73819857"
---
# <a name="opc-twin-architecture"></a>OPC Twin architectuur

De volgende diagrammen illustreren de OPC Twin architectuur.

## <a name="discover-and-activate"></a>Ontdekken en activeren

1. De operator maakt netwerkscanning op de module mogelijk of doet een eenmalige detectie met behulp van een detectie-URL. De gedetecteerde eindpunten en toepassingsinformatie worden via telemetrie naar de onboarding-agent verzonden voor verwerking.  De ONBoarding-agent voor OPC UA-apparaten verwerkt OPC UA-serverdetectiegebeurtenissen die door de OPC Twin IoT Edge-module worden verzonden wanneer u in de detectie- of scanmodus bent. De detectiegebeurtenissen resulteren in toepassingsregistratie en updates in het OPC UA-apparaatregister.

   ![Hoe OPC Twin werkt](media/overview-opc-twin-architecture/opc-twin1.png)

1. De operator inspecteert het certificaat van het ontdekte eindpunt en activeert de geregistreerde endpoint twin voor toegang. 

   ![Hoe OPC Twin werkt](media/overview-opc-twin-architecture/opc-twin2.png)

## <a name="browse-and-monitor"></a>Bladeren en controleren

1. Eenmaal geactiveerd, kan de operator de Twin service REST API gebruiken om te bladeren of het serverinformatiemodel, lees/schrijfobjectvariabelen en aanroepmethoden te inspecteren.  De gebruiker maakt gebruik van een vereenvoudigde OPC UA API die volledig wordt uitgedrukt in HTTP en JSON.

   ![Hoe OPC Twin werkt](media/overview-opc-twin-architecture/opc-twin3.png)

1. De twin service REST-interface kan ook worden gebruikt om bewaakte items en abonnementen te maken in de OPC Publisher. Met de OPC Publisher kan telemetrie worden verzonden van OPC UA-serversystemen naar IoT Hub. Zie [Wat is OPC Publisher](overview-opc-publisher.md)voor meer informatie over OPC Publisher .

   ![Hoe OPC Twin werkt](media/overview-opc-twin-architecture/opc-twin4.png)

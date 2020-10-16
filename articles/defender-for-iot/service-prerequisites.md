---
title: Onderdelen & vereisten
description: Meer informatie over alles wat u nodig hebt om aan de slag te gaan met Azure Defender voor IoT-service vereisten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2020
ms.author: rkarlin
ms.custom: references_regions
ms.openlocfilehash: b0913dc48f807c26396a38e31d293877b4561b7d
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92089176"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender voor IoT-vereisten

Dit artikel bevat een uitleg van de verschillende onderdelen van de Defender voor IoT-service, wat u moet beginnen en legt de basis concepten uit om inzicht te krijgen in de service.

## <a name="minimum-requirements"></a>Minimale vereisten

- Bewaking zonder agents voor IoT-en OT-apparaten (op basis van Cyberx-technologie)
    - Netwerk switches ondersteunen verkeers bewaking via de bereik poort
    - Hardware-apparaten voor NTA-sensor, voor meer informatie, Zie [gecertificeerde hardware](https://aka.ms/AzureDefenderforIoTBareMetalAppliance)
    - Rol **Inzender** voor Azure-abonnementen (alleen vereist tijdens het voorbereiden van het definiëren van vastgelegde apparaten)
    - IoT Hub (gratis of laag) **Inzender** rol (voor verbonden met het Cloud beheer)
- Beveiliging voor beheerde IoT-apparaten die worden beheerd via Azure IoT Hub
    - De rol **Inzender** voor IOT hub (Standard-laag)
    - IoT Hub: de functie voor het in-/uitschakelen **van Azure Defender voor IOT** moet zijn ingeschakeld
    - Ondersteuning voor beveiligings modules op apparaatniveau  
        - Defender voor IoT-agents biedt ondersteuning voor een groeiende lijst met apparaten en platformen. Zie de [lijst met ondersteunde platforms](how-to-deploy-agent.md)


## <a name="supported-service-regions"></a>Ondersteunde service regio's

Zie [IOT hub ondersteunde regio's](https://azure.microsoft.com/global-infrastructure/services/?products=iot-hub) voor meer informatie. 

Defender voor IoT stuurt al het verkeer van alle Europese regio's naar het Europa-west regionale Data Center en alle overige regio's naar het centrale regionale Data Center van de Verenigde Staten.

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van Azure IOT Security
- Meer informatie over [het inschakelen van de service](quickstart-onboard-iot-hub.md)
- Lees de [Veelgestelde vragen over Defender voor IOT](resources-frequently-asked-questions.md)
- Ontdek hoe u [Defender kunt begrijpen voor IOT-waarschuwingen](concept-security-alerts.md)

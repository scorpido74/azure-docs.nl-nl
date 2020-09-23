---
title: Onderdelen & vereisten
description: Meer informatie over alles wat u nodig hebt om aan de slag te gaan met Azure Defender voor IoT-service vereisten.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2020
ms.author: mlottner
ms.custom: references_regions
ms.openlocfilehash: 19263f8db58c8d20288d3ae74c24efd85667bc33
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90935324"
---
# <a name="azure-defender-for-iot-prerequisites"></a>Azure Defender voor IoT-vereisten

Dit artikel bevat een uitleg van de verschillende onderdelen van de Defender voor IoT-service, wat u moet beginnen en legt de basis concepten uit om inzicht te krijgen in de service.

## <a name="minimum-requirements"></a>Minimale vereisten

- IoT Hub Standard-laag
  - Bevoegdheden **eigenaar** niveau van Azure Role
- [Log Analytics werk ruimte](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (aanbevolen)
  - Het gebruik van Azure Security Center is een aanbeveling en geen vereiste. Zonder Azure Security Center kunt u uw andere Azure-resources niet weer geven binnen IoT Hub.

## <a name="working-with-defender-for-iot-service"></a>Werken met Defender voor IoT-service

Defender voor IoT Insights en rapportage zijn beschikbaar via Azure IoT Hub en Azure Security Center. Als u Defender wilt inschakelen voor IoT op uw Azure-IoT Hub, is een account met bevoegdheden op **eigenaars** niveau vereist. Nadat u ASC voor IoT hebt ingeschakeld in uw IoT Hub, wordt Defender voor IoT Insights weer gegeven als de **beveiligings** functie in azure IOT hub en als  **IOT** in azure Security Center.

## <a name="supported-service-regions"></a>Ondersteunde service regio's

Defender voor IoT wordt momenteel ondersteund voor IoT-hubs in de volgende Azure-regio's:

- Central US
- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- VS Zuid-Centraal
- VS - noord-centraal
- Canada - midden
- Canada - oost
- Europa - noord
- Brazil South
- Frankrijk - centraal
- Verenigd Koninkrijk West
- Verenigd Koninkrijk Zuid
- Europa -west
- Noord-Europa
- Japan - west
- Japan East
- Australia Southeast
- Australië - oost
- Azië - oost
- Azië - zuidoost
- Korea - centraal
- Korea - zuid
- Central India
- India - zuid

Defender voor IoT stuurt al het verkeer van alle Europese regio's naar het Europa-west regionale Data Center en alle overige regio's naar het centrale regionale Data Center van de Verenigde Staten.

## <a name="wheres-my-iot-hub"></a>Waar is mijn IoT Hub?

Controleer de locatie van uw IoT Hub om de beschik baarheid van de service te controleren voordat u begint.

1. Open uw IoT Hub.
1. Klik op **Overzicht**.
1. Controleer of de weer gegeven locatie overeenkomt met een van de [ondersteunde service regio's](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Ondersteunde platforms voor agents

Defender voor IoT-agents biedt ondersteuning voor een groeiende lijst met apparaten en platformen. Zie de [lijst met ondersteunde platforms](how-to-deploy-agent.md) om uw bestaande of geplande Apparaatbeheer te controleren.

## <a name="next-steps"></a>Volgende stappen

- Lees het [overzicht](overview.md) van Azure IOT Security
- Meer informatie over [het inschakelen van de service](quickstart-onboard-iot-hub.md)
- Lees de [Veelgestelde vragen over Defender voor IOT](resources-frequently-asked-questions.md)
- Ontdek hoe u [Defender kunt begrijpen voor IOT-waarschuwingen](concept-security-alerts.md)

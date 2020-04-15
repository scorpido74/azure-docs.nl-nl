---
title: Onderdelen & vereisten
description: Details van alles wat nodig is om aan de slag te gaan met azure security center voor IoT-servicevereisten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 790cbcb7-1340-4cc1-9509-7b262e7c3181
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2019
ms.author: mlottner
ms.openlocfilehash: fb5c42ad490ed04f14ff150093a44b552434ed9e
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310585"
---
# <a name="azure-security-center-for-iot-prerequisites"></a>Azure Security Center voor IoT-vereisten

In dit artikel wordt uitgelegd wat de verschillende onderdelen van de Azure Security Center for IoT-service, wat u moet beginnen, en worden de basisconcepten uitgelegd om de service te begrijpen.

## <a name="minimum-requirements"></a>Minimale vereisten

- Standaardvoor IoT-hub
  - RBAC-bevoegdheden op het niveau **van de eigenaar**
- [Werkruimte logboekanalyse](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)
- Azure Security Center (aanbevolen)
  - Het gebruik van Azure Security Center is een aanbeveling en geen vereiste. Zonder Azure Security Center u uw andere Azure-bronnen niet bekijken in IoT Hub.

## <a name="working-with-azure-security-center-for-iot-service"></a>Werken met Azure Security Center voor IoT-service

Azure Security Center voor IoT-inzichten en -rapportage zijn beschikbaar met Azure IoT Hub en Azure Security Center. Om Azure Security Center for IoT in te schakelen op uw Azure IoT Hub, is een account met **eigenarenniveaubevoegdheden** vereist. Nadat ASC voor IoT is inschakelt in uw IoT-hub, wordt Azure Security Center for IoT-inzichten weergegeven als de **beveiligingsfunctie** in Azure IoT Hub en als **IoT** in Azure Security Center.

## <a name="supported-service-regions"></a>Ondersteunde serviceregio's

Azure Security Center for IoT wordt momenteel ondersteund voor IoT-hubs in de volgende Azure-regio's:

- VS - centraal
- VS - oost
- VS - oost 2
- VS - west-centraal
- VS - west
- VS - west 2
- Centraal Vs-Zuid
- VS - noord-centraal
- Canada - midden
- Canada - oost
- Europa - noord
- Brazilië - zuid
- Frankrijk - centraal
- Verenigd Koninkrijk West
- Verenigd Koninkrijk Zuid
- Europa -west
- Noord-Europa
- Japan - west
- Japan - oost
- Australië - zuidoost
- Australië - oost
- Azië - oost
- Azië - zuidoost
- Korea - centraal
- Korea - zuid
- India - centraal
- India - zuid

Azure Security Center for IoT leidt al het verkeer van alle Europese regio's naar het regionale datacenter in West-Europa en alle resterende regio's naar het regionale datacenter van de Centrale VS.

## <a name="wheres-my-iot-hub"></a>Waar is mijn IoT Hub?

Controleer de locatie van uw IoT-hub om de beschikbaarheid van de service te verifiëren voordat u begint.

1. Open je IoT Hub.
1. Klik op **Overzicht**.
1. Controleer of de vermelde locatie overeenkomt met een van de [ondersteunde serviceregio's](#supported-service-regions).

## <a name="supported-platforms-for-agents"></a>Ondersteunde platforms voor agenten

Azure Security Center voor IoT-agents ondersteunt een groeiende lijst met apparaten en platforms. Bekijk de [lijst met ondersteunde platforms](how-to-deploy-agent.md) om uw bestaande of geplande apparaatbibliotheek te controleren.

## <a name="next-steps"></a>Volgende stappen

- Lees het [Azure](overview.md) IoT-beveiligingsoverzicht
- Meer informatie over het [inschakelen van de service](quickstart-onboard-iot-hub.md)
- Lees de [veelgestelde vragen over azure security center voor IoT](resources-frequently-asked-questions.md)
- Ontdek hoe u [Azure Security Center voor IoT-waarschuwingen begrijpen](concept-security-alerts.md)

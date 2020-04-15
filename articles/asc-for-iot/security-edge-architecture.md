---
title: Beveiligingsmodule voor IoT Edge
description: Inzicht in de architectuur en mogelijkheden van azure security center voor IoT-beveiligingsmodule voor IoT Edge.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: e78523ae-d70a-456a-818d-f8b1b025d7cb
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 0eb68f517c4c5b04dd4f92dd1a804dcf5ad24362
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81310631"
---
# <a name="azure-iot-edge-security-module"></a>Azure IoT Edge-beveiligingsmodule

[Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/) biedt krachtige mogelijkheden voor het beheren en uitvoeren van zakelijke workflows aan de rand.
Het belangrijkste onderdeel dat IoT Edge speelt in IoT-omgevingen maken het bijzonder aantrekkelijk voor kwaadwillende actoren.

Azure Security Center for IoT-beveiligingsmodule biedt een uitgebreide beveiligingsoplossing voor uw IoT Edge-apparaten.
Azure Security Center for IoT-module verzamelt, verzamelt en analyseert ruwe beveiligingsgegevens van uw besturingssysteem en containersysteem tot bruikbare beveiligingsaanbevelingen en waarschuwingen.

Net als bij Azure Security Center voor IoT-beveiligingsagents voor IoT-apparaten is de Azure Security Center for IoT Edge-module zeer aanpasbaar via de moduletwee.
Zie [Uw agent configureren](how-to-agent-configuration.md) voor meer informatie.

Azure Security Center for IoT-beveiligingsmodule voor IoT Edge biedt de volgende functies:

- Verzamelt onbewerkte beveiligingsgebeurtenissen van het onderliggende besturingssysteem (Linux) en de IoT Edge Container-systemen.

  Zie [azure security center voor iot-agentconfiguratie](how-to-agent-configuration.md) voor meer informatie over beschikbare beveiligingsgegevensverzamelaars.

- Analyse van IoT Edge-implementatiemanifesten.

- Verzamelt onbewerkte beveiligingsgebeurtenissen in berichten die via [IoT Edge Hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-runtime#iot-edge-hub)worden verzonden.

- Verwijder de configuratie door het gebruik van de beveiligingsmodule twin.

  Zie [Een Azure Security Center configureren voor IoT-agent](how-to-agent-configuration.md) voor meer informatie.

Azure Security Center for IoT-beveiligingsmodule voor IoT Edge wordt uitgevoerd in een bevoorrechte modus onder IoT Edge.
De bevoorrechte modus is vereist om de module in staat te stellen het besturingssysteem en andere IoT Edge-modules te bewaken.

## <a name="module-supported-platforms"></a>Door de module ondersteunde platforms

Azure Security Center for IoT-beveiligingsmodule voor IoT Edge is momenteel alleen beschikbaar voor Linux.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de architectuur en mogelijkheden van azure security center voor IoT-beveiligingsmodule voor IoT Edge.

Gebruik de volgende artikelen om verder te gaan met Azure Security Center for IoT-implementatie:

- Beveiligingsmodule [implementeren voor IoT Edge](how-to-deploy-edge.md)
- Meer informatie over het [configureren van uw beveiligingsmodule](how-to-agent-configuration.md)
- De vereisten van het Azure Security Center voor [IoT-service controleren](service-prerequisites.md)
- Meer informatie over het [inschakelen van Azure Security Center voor IoT-service in uw IoT-hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service vindt u van de veelgestelde vragen over [azure security center voor IoT](resources-frequently-asked-questions.md)

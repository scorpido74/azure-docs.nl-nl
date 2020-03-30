---
title: Aan de slag met Azure Security Center voor IoT-beveiligingsagents| Microsoft Documenten
description: Ga aan de slag met het begrijpen, configureren, implementeren en gebruiken van Azure Security Center voor IoT-beveiligingsserviceagents op uw IoT-apparaten.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 55c8d3b6-3126-4246-8d07-ef88fe5ea84f
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 1ed890d9d3602de24e6f85f6f0ae7f59849f3df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74664181"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Aan de slag met Azure Security Center voor IoT-apparaatbeveiligingsagents

Azure Security Center voor IoT-beveiligingsagents bieden verbeterde beveiligingsmogelijkheden, zoals het bewaken van externe verbindingen, actieve toepassingen, inloggebeurtenissen en best practices voor het configureren van besturingssystemen. Neem de controle over uw apparaat veld bescherming van bedreigingen en veiligheid houding met een enkele service. 

Referentiearchitectuur voor Linux- en Windows-beveiligingsagents, zowel in C# als C, zijn aanwezig.

Het Azure Security Center voor IoT-beveiligingsagents verwerken onbewerkte gebeurtenisverzameling vanaf het besturingssysteem van het apparaat, gebeurtenisaggregatie om de kosten te verlagen en configuratie via een apparaatmoduletwee. Beveiligingsberichten worden verzonden via uw IoT Hub, naar Azure Security Center for IoT-analyseservices.

Gebruik de volgende werkstroom om uw Azure Security Center voor IoT-beveiligingsagents te implementeren en te testen: 

1. [Azure Security Center voor IoT-service inschakelen voor uw IoT-hub](quickstart-onboard-iot-hub.md)
1. Als uw IoT-hub geen geregistreerde apparaten heeft, [registreert u een nieuw apparaat](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Maak een azureiotsecurity-beveiligingsmodule](quickstart-create-security-twin.md) voor uw apparaten.
1. Als u de agent wilt installeren op een door Azure gesimuleerd apparaat in plaats van op een daadwerkelijk apparaat te installeren, [draait u een nieuwe Azure Virtual Machine (VM) op](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) in een beschikbare zone. 
1. [Implementeer een Azure Security Center voor IoT-beveiligingsagent](how-to-deploy-linux-cs.md) op uw IoT-apparaat of nieuwe VM.
1. Volg de instructies voor [trigger_events](https://aka.ms/iot-security-github-trigger-events) om een onschadelijke simulatie van een aanval uit te voeren.
1. Controleer Azure Security Center voor IoT-waarschuwingen als reactie op de gesimuleerde aanval in de vorige stap. Begin de verificatie vijf minuten na het uitvoeren van het script.
1. Ontdek [waarschuwingen,](concept-security-alerts.md) [aanbevelingen](concept-recommendations.md)en [diepe duiken met Behulp van Log Analytics](how-to-security-data-access.md) met Behulp van IoT Hub. 


## <a name="next-steps"></a>Volgende stappen

- Uw [oplossing configureren](quickstart-configure-your-solution.md)
- [Beveiligingsmodules maken](quickstart-create-security-twin.md)
- [Aangepaste waarschuwingen configureren](quickstart-create-custom-alerts.md)
- [Een beveiligingsagent implementeren](how-to-deploy-agent.md)

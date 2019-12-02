---
title: Aan de slag met Azure Security Center voor IoT-beveiligings agenten | Microsoft Docs
description: Ga aan de slag met het leren, configureren, implementeren en gebruiken van Azure Security Center voor IoT Security Service-agents op uw IoT-apparaten.
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
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/01/2019
ms.locfileid: "74664181"
---
# <a name="get-started-with-azure-security-center-for-iot-device-security-agents"></a>Aan de slag met Azure Security Center voor IoT-apparaten voor beveiligings agenten

Azure Security Center voor IoT-beveiligings agenten bieden verbeterde beveiligings mogelijkheden, zoals het bewaken van externe verbindingen, actieve toepassingen, aanmeldings gebeurtenissen en aanbevolen procedures voor het configureren van besturings systemen. Neem de controle over uw apparaat veld beveiliging tegen bedreigingen en beveiligings postuur met één service. 

Referentie architectuur voor Linux-en Windows-beveiligings agenten, C# zowel in als C, worden weer gegeven.

Met de Azure Security Center voor IoT-beveiligings agenten wordt de verzameling van onbewerkte gebeurtenissen van het besturings systeem van het apparaat afgehandeld, gebeurtenissen samen voegen om de kosten te verlagen en configuratie via een apparaat-module. Beveiligings berichten worden via uw IoT Hub verzonden naar Azure Security Center voor IoT Analytics-Services.

Gebruik de volgende werk stroom om uw Azure Security Center voor IoT-beveiligings agenten te implementeren en te testen: 

1. [Azure Security Center voor IoT-service inschakelen voor uw IoT Hub](quickstart-onboard-iot-hub.md)
1. Als uw IoT Hub geen geregistreerde apparaten heeft, [registreert u een nieuw apparaat](https://docs.microsoft.com/azure/iot-accelerators/quickstart-device-simulation-deploy).
1. [Maak een azureiotsecurity-beveiligings module](quickstart-create-security-twin.md) voor uw apparaten.
1. Als u de agent wilt installeren op een gesimuleerd Azure-apparaat in plaats van op een echt apparaat te installeren, kunt u [een nieuwe virtuele Azure-machine (VM)](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal) in een beschik bare zone zetten. 
1. [Implementeer een Azure Security Center voor IOT-beveiligings agent](how-to-deploy-linux-cs.md) op uw IOT-apparaat of op een nieuwe virtuele machine.
1. Volg de instructies voor [trigger_events](https://aka.ms/iot-security-github-trigger-events) om een onschadelijke simulatie van een aanval uit te voeren.
1. Controleer Azure Security Center op IoT-waarschuwingen als reactie op de gesimuleerde aanval in de vorige stap. Begin met de verificatie 5 minuten na het uitvoeren van het script.
1. Bekijk [waarschuwingen](concept-security-alerts.md), [aanbevelingen](concept-recommendations.md)en [diep gaande informatie met behulp van Log Analytics](how-to-security-data-access.md) met behulp van IOT hub. 


## <a name="next-steps"></a>Volgende stappen

- Uw [oplossing](quickstart-configure-your-solution.md) configureren
- [Beveiligings modules maken](quickstart-create-security-twin.md)
- [Aangepaste waarschuwingen](quickstart-create-custom-alerts.md) configureren
- [Een beveiligings agent implementeren](how-to-deploy-agent.md)

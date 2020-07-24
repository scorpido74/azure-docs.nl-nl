---
title: Ondersteuning voor Azure RTO'S
description: Meer informatie over ondersteuning van Azure RTO'S in de Azure Security Center voor IoT-service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87096696"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Azure Security Center voor IoT-beveiligings oplossing voor Azure RTO'S 

De beveiligings module Azure Security Center voor IoT biedt een uitgebreide beveiligings oplossing voor Azure RTO'S-apparaten. Azure RTO'S wordt geleverd met een ingebouwde beveiligings module die algemene bedreigingen op real-time besturingssysteem apparaten dekt. 

![Azure Security Center voor IoT Azure RTO'S](./media/architecture/azure-rtos-security-monitoring.png)


Azure Security Center voor IoT-beveiligings module met Azure RTO'S-ondersteuning biedt de volgende functies: 
- Detectie van schadelijke netwerk activiteiten
- Aangepaste waarschuwing op basis van het gedrag van het apparaat basis lijnen
- Beveiligings hygiëne van het apparaat verbeteren

### <a name="detection-of-malicious-network-activities"></a>Detectie van schadelijke netwerk activiteiten

De inkomende en uitgaande netwerk activiteit van elk apparaat wordt bewaakt (ondersteunde protocollen: TCP, UDP, ICMP op IPv4 en IPv6). Azure Security Center voor IoT worden elk van deze netwerk activiteiten gecontroleerd op basis van de micro soft Threat Intelligence-feed. De feed wordt in realtime bijgewerkt met miljoenen unieke bedreigings indicatoren die wereld wijd zijn verzameld. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Gedrag van het apparaat basis lijnen op basis van aangepaste waarschuwingen

Basis lijnen maakt het mogelijk om apparaten te clusteren in beveiligings groepen en het verwachte gedrag van elke groep te definiëren. Omdat IoT-apparaten doorgaans zijn ontworpen om te functioneren in goed gedefinieerde en beperkte scenario's, is het eenvoudig om een basis lijn te maken die het verwachte gedrag definieert met behulp van een set para meters. Elke afwijking van de basis lijn, een waarschuwing wordt geactiveerd. 

### <a name="improve-your-device-security-hygiene"></a>Verbeter de beveiligings hygiëne van uw apparaat

Door gebruik te maken van de aanbevolen infrastructuur Azure Security Center voor IoT, kunt u kennis en inzichten krijgen over problemen in uw omgeving die van invloed zijn op de beveiligings postuur van uw apparaten. Slechte IoT-postuur kunnen toestaan dat mogelijke aanvallen slagen als deze ongewijzigd blijven, omdat de beveiliging altijd wordt gemeten door de zwakkere koppeling binnen een organisatie. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Aan de slag met het beveiligen van Azure RTO'S-apparaten

- Azure Security Center voor IoT-beveiligings module voor Azure RTO'S is beschikbaar als gratis down load voor uw apparaten. De Azure Security Center voor IoT-Cloud service is beschikbaar met een proef versie van 30 dagen per Azure-abonnement. Down load de [Azure Security Center voor IOT Security module voor Azure rto's](https://github.com/azure-rtos/iot-security-module-preview) om aan de slag te gaan. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Security Center voor IoT Azure RTO'S-ondersteuning. Raadpleeg de volgende artikelen voor meer informatie over hoe u aan de slag kunt gaan en uw beveiligings oplossing in IoT Hub kunt inschakelen:

- [Servicevereisten](service-prerequisites.md)
- [Aan de slag](getting-started.md)
- [De oplossing configureren](quickstart-configure-your-solution.md)
- [Beveiliging in IoT Hub inschakelen](quickstart-onboard-iot-hub.md)
- [Veelgestelde vragen over Azure Security Center voor IoT](resources-frequently-asked-questions.md)
- [Beveiligingswaarschuwingen in Azure Security Center for IoT](concept-security-alerts.md)

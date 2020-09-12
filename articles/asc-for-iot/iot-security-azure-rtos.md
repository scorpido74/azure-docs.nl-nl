---
title: Overzicht van de beveiligings module voor Azure RTO'S
description: Meer informatie over de beveiligings module voor Azure RTO'S-ondersteuning en-implementatie als onderdeel van de Azure Security Center voor IoT-service.
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
ms.date: 09/07/2020
ms.author: mlottner
ms.openlocfilehash: bcf98f503b0f0c8bf76435d9884297d5f53d65e6
ms.sourcegitcommit: 7f62a228b1eeab399d5a300ddb5305f09b80ee14
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/08/2020
ms.locfileid: "89514472"
---
# <a name="overview-security-module-for-azure-rtos-preview"></a>Overzicht: beveiligings module voor Azure RTO'S (preview)

De Azure Security Center voor IoT RTO'S Security module biedt een uitgebreide beveiligings oplossing voor Azure RTO'S-apparaten. Azure RTO'S wordt nu geleverd met de ingebouwde Azure IoT-beveiligings module en biedt dekking voor veelvoorkomende bedreigingen en mogelijke schadelijke activiteiten op real-time besturings systemen. 

![Azure Security Center voor IoT Azure RTO'S](./media/architecture/azure-rtos-security-monitoring.png)


De beveiligings module voor Azure RTO'S biedt de volgende functies: 
- Detectie van schadelijke netwerk activiteiten
- Aangepaste waarschuwing op basis van het gedrag van het apparaat basis lijnen
- Beveiligings hygiëne van het apparaat verbeteren

### <a name="detection-of-malicious-network-activities"></a>Detectie van schadelijke netwerk activiteiten

De inkomende en uitgaande netwerk activiteit van elk apparaat wordt bewaakt (ondersteunde protocollen: TCP, UDP, ICMP op IPv4 en IPv6). Azure Security Center voor IoT worden elk van deze netwerk activiteiten gecontroleerd op basis van de micro soft Threat Intelligence-feed. De feed wordt in realtime bijgewerkt met miljoenen unieke bedreigings indicatoren die wereld wijd zijn verzameld. 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>Gedrag van het apparaat basis lijnen op basis van aangepaste waarschuwingen

Basis lijnen maakt het mogelijk om apparaten te clusteren in beveiligings groepen en het verwachte gedrag van elke groep te definiëren. Omdat IoT-apparaten doorgaans zijn ontworpen om te functioneren in goed gedefinieerde en beperkte scenario's, is het eenvoudig om een basis lijn te maken die het verwachte gedrag definieert met behulp van een set para meters. Elke afwijking van de basis lijn, een waarschuwing wordt geactiveerd. 

### <a name="improve-your-device-security-hygiene"></a>Verbeter de beveiligings hygiëne van uw apparaat

Door gebruik te maken van de aanbevolen infrastructuur Azure Security Center voor IoT, kunt u kennis en inzichten krijgen over problemen in uw omgeving die van invloed zijn op de beveiligings postuur van uw apparaten. Zwakke IoT Device Security-postuur kunnen toestaan dat mogelijke aanvallen slagen als deze ongewijzigd blijven, omdat de beveiliging altijd wordt gemeten door de zwakkere koppeling binnen een organisatie. 

## <a name="get-started-protecting-azure-rtos-devices"></a>Aan de slag met het beveiligen van Azure RTO'S-apparaten

De beveiligings module voor Azure RTO'S is beschikbaar als gratis down load voor uw apparaten. De Azure Security Center voor IoT-Cloud service is beschikbaar met een proef versie van 30 dagen per Azure-abonnement. Down load de [beveiligings module voor Azure rto's](https://github.com/azure-rtos/iot-security-module-preview) om aan de slag te gaan. 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de beveiligings module voor de Azure RTO'S-service. Raadpleeg de volgende artikelen voor meer informatie over de beveiligings module en aan de slag:

- [Concepten van Azure RTO'S IoT Security module](concept-rtos-security-module.md)
- [Snelstartgids: Azure RTO'S IoT Security module](quickstart-azure-rtos-security-module.md)



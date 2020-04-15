---
title: Architectuur voor de oplossing
description: Meer informatie over de informatiestroom in de Azure Security Center for IoT-service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311753"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center for IoT-architectuur

In dit artikel wordt de functionele systeemarchitectuur van de Azure Security Center for IoT-oplossing uitgelegd.

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center voor IoT-componenten

Azure Security Center for IoT bestaat uit de volgende componenten:

- IoT Hub-integratie
- Apparaatagenten (optioneel)
- Beveiligingsbericht SDK verzenden
- Analytics-pijplijn

### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center voor IoT-werkstromen

Azure Security Center for IoT werkt in een van de twee functiewerkstromen: ingebouwd en verbeterd

### <a name="built-in"></a>Ingebouwd

In **de ingebouwde** modus is Azure Security Center for IoT ingeschakeld wanneer u ervoor kiest om de **optie Beveiliging** in te schakelen in uw IoT-hub. De ingebouwde modus biedt realtime bewaking, aanbevelingen en waarschuwingen en biedt zichtbaarheid in één stap voor apparaten en ongeëvenaarde beveiliging. De ingebouwde modus vereist geen installatie van agenten op alle apparaten en maakt gebruik van geavanceerde analyses van geregistreerde activiteiten om uw veldapparaat te analyseren en te beschermen.

### <a name="enhanced"></a>Verbeterd

In **de uitgebreide** modus verzamelen, verzamelen en analyseren de agents, nadat ze de **beveiligingsoptie** in uw IoT-hub hebben ingeschakeld en Azure Security Center voor IoT-apparaatagents op uw apparaten hebben geïnstalleerd, en nadat ze onbewerkte beveiligingsgebeurtenissen van uw apparaten verzamelen, samenvoegen en analyseren. Onbewerkte beveiligingsgebeurtenissen kunnen IP-verbindingen, procescreatie, gebruikersaanmeldingen en andere beveiligingsrelevante informatie bevatten. Azure Security Center voor IoT-apparaatagents verwerken ook gebeurtenisaggregatie om een hoge netwerkdoorvoer te voorkomen. De agents zijn zeer aanpasbaar, zodat u ze gebruiken voor specifieke taken, zoals het verzenden van alleen belangrijke informatie tegen de snelste SLA, of voor het aggregeren van uitgebreide beveiligingsinformatie en context in grotere segmenten, waardoor hogere servicekosten worden vermeden.

![Azure Security Center for IoT-architectuur](./media/architecture/azure-iot-security-architecture.png)

Apparaatagents en andere toepassingen gebruiken het **Azure-beveiligingsbericht SDK** om beveiligingsgegevens naar Azure IoT Hub te verzenden. IoT Hub pikt deze informatie op en stuurt deze door naar de Azure Security Center for IoT-service.

Zodra de Azure Security Center for IoT-service is ingeschakeld, stuurt IoT Hub naast de doorgestuurde gegevens ook al zijn interne gegevens voor analyse door Azure Security Center for IoT. Deze gegevens omvatten gegevens, gegevens over de werkingslogboeken van het apparaat, apparaatidentiteiten en hubconfiguratie. Al deze informatie helpt bij het maken van de Azure Security Center for IoT-analysepijplijn.

Azure Security Center for IoT analytics pipeline ontvangt ook extra threat intelligence streams van verschillende bronnen binnen Microsoft en Microsoft partners. De volledige analysepijplijn van het Azure Security Center voor IoT werkt met elke klantconfiguratie die op de service is gemaakt (zoals aangepaste waarschuwingen en het gebruik van de SDK voor verzenden beveiligingsbericht).

Met behulp van de analysepijplijn combineert Azure Security Center for IoT alle informatiestromen om bruikbare aanbevelingen en waarschuwingen te genereren. De pijplijn bevat zowel aangepaste regels die zijn gemaakt door beveiligingsonderzoekers en -experts, als machine learning-modellen die zoeken naar afwijkingen van standaardapparaatgedrag en risicoanalyse.

Azure Security Center voor IoT-aanbevelingen en waarschuwingen (analytics pipeline output) wordt geschreven naar de Log Analytics-werkruimte van elke klant. Inclusief de ruwe gebeurtenissen in de werkruimte, evenals de waarschuwingen en aanbevelingen maakt diepe duik onderzoeken en query's met behulp van de exacte details van de verdachte activiteiten gedetecteerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over de basisarchitectuur en workflow van Azure Security Center for IoT-oplossing. Zie de volgende artikelen voor meer informatie over vereisten, hoe u aan de slag en uw beveiligingsoplossing in schakelt in IoT Hub:

- [Servicevereisten](service-prerequisites.md)
- [Aan de slag](getting-started.md)
- [De oplossing configureren](quickstart-configure-your-solution.md)
- [Beveiliging inschakelen in IoT-hub](quickstart-onboard-iot-hub.md)
- [Veelgestelde vragen over Azure Security Center for IoT](resources-frequently-asked-questions.md)
- [Beveiligingswaarschuwingen in Azure Security Center for IoT](concept-security-alerts.md)

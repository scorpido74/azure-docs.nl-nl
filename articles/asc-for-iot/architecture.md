---
title: Wat is Azure Security Center voor IoT-oplossings architectuur? Microsoft Docs
description: Meer informatie over de stroom van informatie in de Azure Security Center voor IoT-service.
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
ms.openlocfilehash: 2e55f98f8c7b6ddbc21f7ea8633467461ea5be29
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75922167"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center for IoT-architectuur

In dit artikel wordt de functionele systeem architectuur uitgelegd van de Azure Security Center voor IoT-oplossing. 

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center voor IoT-onderdelen

Azure Security Center voor IoT bestaat uit de volgende onderdelen:
- Integratie van IoT Hub
- Apparaat-agents (optioneel)
- SDK voor beveiligings berichten verzenden
- Analytics-pijp lijn
 
### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center voor IoT-werk stromen

Azure Security Center voor IoT werkt op een van de twee functie werk stromen: ingebouwd en uitgebreid  

### <a name="built-in"></a>Ingebouwd
In de **ingebouwde** modus is Azure Security Center voor IOT ingeschakeld wanneer u ervoor kiest om de optie **beveiliging** in uw IOT hub in te scha kelen. Dankzij realtime bewaking, aanbevelingen en waarschuwingen biedt de ingebouwde modus een beschermte en ongeëvenaarde beveiliging voor apparaten in één stap. Voor de build-in modus is geen agent geïnstalleerd op apparaten en wordt gebruikgemaakt van geavanceerde analyses op geregistreerde activiteiten om uw veld apparaat te analyseren en beveiligen. 

### <a name="enhanced"></a>Verbeterd 
Na het **inschakelen van de** optie **beveiliging** in uw IOT hub en het installeren van Azure Security Center voor IOT Device agents op uw apparaten, verzamelen en analyseren de agents onbewerkte beveiligings gebeurtenissen van uw apparaten. Onbewerkte beveiligings gebeurtenissen kunnen IP-verbindingen, het maken van processen, gebruikers aanmeldingen en andere informatie over beveiliging zijn. Azure Security Center voor IoT-apparaat-agents verwerken ook gebeurtenissen aggregatie om te voor komen dat hoge netwerk doorvoer wordt door voer. De agents zijn zeer aanpasbaar, zodat u ze kunt gebruiken voor specifieke taken, zoals het verzenden van belang rijke informatie op de snelste SLA, of voor het samen voegen van uitgebreide beveiligings informatie en-context in grotere segmenten, waardoor er hogere service kosten worden bespaard.

![Azure Security Center for IoT-architectuur](./media/architecture/azure-iot-security-architecture.png)
 
Apparaat-agents en andere toepassingen gebruiken de SDK voor het **verzenden van beveiligings berichten van Azure** om beveiligings gegevens naar Azure IOT hub te verzenden. IoT Hub haalt deze informatie op en stuurt deze door naar de Azure Security Center voor IoT-service.

Zodra de Azure Security Center voor IoT-service is ingeschakeld, wordt naast de doorgestuurde IoT Hub gegevens ook alle interne gegevens voor analyse verzonden door Azure Security Center voor IoT. Deze gegevens omvatten onder andere de bewerkingen voor het bewerkings logboek van de Cloud, apparaat-id's en de configuratie van de hub. Al deze informatie helpt u bij het maken van de Azure Security Center voor IoT Analytics-pijp lijn.
 
Azure Security Center voor IoT Analytics-pijp lijn ontvangt ook extra Threat Intelligence-streams van verschillende bronnen in micro soft-en micro soft-partners. Het Azure Security Center voor IoT-volledige analyse pijplijn werkt met elke klant configuratie die is gemaakt voor de service (zoals aangepaste waarschuwingen en het gebruik van de SDK voor het verzenden van beveiligings berichten).
 
Met behulp van de analytische pijp lijn combineert Azure Security Center voor IoT alle gegevens stromen om aanbevelingen en waarschuwingen te genereren die actie kunnen ondernemen. De pijp lijn bevat zowel aangepaste regels die zijn gemaakt door veiligheids onderzoekers als experts, en machine learning modellen zoekt naar afwijkingen vanuit het standaard gedrag van apparaten en risico analyse.
 
Azure Security Center voor IoT-aanbevelingen en-waarschuwingen (analyse pijplijn uitvoer) wordt geschreven naar de Log Analytics werk ruimte van elke klant. Met inbegrip van de onbewerkte gebeurtenissen in de werk ruimte en de waarschuwingen en aanbevelingen kunnen grondige onderzoeken en query's worden uitgevoerd met de exacte details van de gedetecteerde verdachte activiteiten.  

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de basis architectuur en werk stroom van Azure Security Center voor IoT-oplossing. Raadpleeg de volgende artikelen voor meer informatie over de vereisten, hoe u aan de slag kunt gaan en uw beveiligings oplossing in IoT Hub kunt inschakelen:

- [Service vereisten](service-prerequisites.md)
- [Aan de slag](getting-started.md)
- [Uw oplossing configureren](quickstart-configure-your-solution.md)
- [Beveiliging in IoT Hub inschakelen](quickstart-onboard-iot-hub.md)
- [Veelgestelde vragen over Azure Security Center voor IoT](resources-frequently-asked-questions.md)
- [Beveiligingswaarschuwingen in Azure Security Center for IoT](concept-security-alerts.md)

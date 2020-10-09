---
title: Architectuur voor de oplossing
description: Meer informatie over de stroom van gegevens in de Azure Defender voor IoT-service.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: elazark
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/08/2020
ms.author: v-ekrieg
ms.openlocfilehash: 3fc695770350e5a60ae3da9ab1796da5cac99370
ms.sourcegitcommit: b87c7796c66ded500df42f707bdccf468519943c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2020
ms.locfileid: "91843410"
---
# <a name="azure-defender-for-iot-architecture"></a>Azure Defender voor IoT-architectuur

In dit artikel wordt de functionele systeem architectuur van de Defender voor IoT-oplossing uitgelegd.

## <a name="defender-for-iot-components"></a>Defender voor IoT-onderdelen

Defender voor IoT bestaat uit de volgende onderdelen:

- Integratie van IoT Hub
- Apparaat-agents (optioneel)
- SDK voor beveiligings berichten verzenden
- Analytics-pijp lijn

### <a name="defender-for-iot-workflows"></a>Defender voor IoT-werk stromen

Defender voor IoT werkt op een van de twee functie werk stromen: ingebouwd en uitgebreid

### <a name="built-in"></a>Ingebouwd

In de **ingebouwde** modus is Defender voor IOT ingeschakeld wanneer u ervoor kiest om de optie **beveiliging** in uw IOT hub in te scha kelen. Dankzij realtime bewaking, aanbevelingen en waarschuwingen biedt de ingebouwde modus een beschermte en ongeëvenaarde beveiliging voor apparaten in één stap. Voor de build-in modus is geen agent geïnstalleerd op apparaten en wordt gebruikgemaakt van geavanceerde analyses op geregistreerde activiteiten om uw veld apparaat te analyseren en beveiligen.

### <a name="enhanced"></a>Verbeterd

Na het inschakelen van de optie **beveiliging** in uw IOT hub en het installeren van Defender voor IOT Device agents op uw apparaten, worden in de **uitgebreide** modus onbewerkte beveiligings gebeurtenissen van uw apparaten verzameld, geaggregeerd en geanalyseerd. Onbewerkte beveiligings gebeurtenissen kunnen IP-verbindingen, het maken van processen, gebruikers aanmeldingen en andere informatie over beveiliging zijn. Verdedigings medewerkers voor IoT-apparaten verwerken ook gebeurtenissen aggregatie om een hoge netwerk doorvoer te voor komen. De agents zijn zeer aanpasbaar, zodat u ze kunt gebruiken voor specifieke taken, zoals het verzenden van belang rijke informatie op de snelste SLA, of voor het samen voegen van uitgebreide beveiligings informatie en-context in grotere segmenten, waardoor er hogere service kosten worden bespaard.

![Defender voor IoT-architectuur](./media/architecture/azure-iot-security-architecture.png)

Apparaat-agents en andere toepassingen gebruiken de SDK voor het **verzenden van beveiligings berichten van Azure** om beveiligings gegevens naar Azure IOT hub te verzenden. IoT Hub haalt deze informatie op en stuurt deze door naar de Defender voor IoT-service.

Als de Defender voor IoT-service is ingeschakeld, wordt naast de doorgestuurde IoT Hub gegevens ook alle interne gegevens verzonden voor analyse door Defender voor IoT. Deze gegevens omvatten onder andere de bewerkingen voor het bewerkings logboek van de Cloud, apparaat-id's en de configuratie van de hub. Al deze informatie helpt u bij het maken van de Defender voor IoT Analytics-pijp lijn.

Defender voor IoT Analytics-pijp lijn ontvangt ook extra Threat Intelligence-streams van verschillende bronnen in micro soft-en micro soft-partners. De volledige analyse pijplijn van Defender voor IoT werkt met elke klant configuratie die is gemaakt voor de service (zoals aangepaste waarschuwingen en het gebruik van de SDK voor het verzenden van beveiligings berichten).

Met behulp van de analytische pijp lijn combineert Defender voor IoT alle gegevens stromen om aanbevelingen en waarschuwingen te genereren die actie kunnen ondernemen. De pijp lijn bevat zowel aangepaste regels die zijn gemaakt door veiligheids onderzoekers als experts, en machine learning modellen zoekt naar afwijkingen vanuit het standaard gedrag van apparaten en risico analyse.

Defender voor IoT-aanbevelingen en-waarschuwingen (analyse pijplijn uitvoer) worden geschreven naar de Log Analytics werk ruimte van elke klant. Met inbegrip van de onbewerkte gebeurtenissen in de werk ruimte en de waarschuwingen en aanbevelingen kunnen grondige onderzoeken en query's worden uitgevoerd met de exacte details van de gedetecteerde verdachte activiteiten.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de basis architectuur en werk stroom van Defender voor IoT-oplossing. Raadpleeg de volgende artikelen voor meer informatie over de vereisten, hoe u aan de slag kunt gaan en uw beveiligings oplossing in IoT Hub kunt inschakelen:

- [Servicevereisten](service-prerequisites.md)
- [Aan de slag](getting-started.md)
- [De oplossing configureren](quickstart-configure-your-solution.md)
- [Beveiliging in IoT Hub inschakelen](quickstart-onboard-iot-hub.md)
- [Veelgestelde vragen over Defender voor IoT](resources-frequently-asked-questions.md)
- [Defender voor IoT-beveiligings waarschuwingen](concept-security-alerts.md)

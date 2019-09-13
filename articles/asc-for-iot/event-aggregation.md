---
title: Meer informatie over Azure Security Center voor de samen voeging van IoT-oplossings gebeurtenissen | Microsoft Docs
description: Meer informatie over hoe gebeurtenissen worden geaggregeerd in Azure Security Center voor IoT-service.
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2019
ms.author: mlottner
ms.openlocfilehash: b8100fc78eced9aa26fe185a8d68244d8f665ff2
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933906"
---
# <a name="security-agent-event-aggregation"></a>Aggregatie van beveiligings agent gebeurtenissen

Azure Security Center voor IoT-beveiligings agenten verzamelen gegevens en systeem gebeurtenissen van uw lokale apparaat en verzenden deze gegevens naar de Azure-Cloud voor verwerking en analyse. De beveiligings agent verzamelt veel soorten apparaatfuncties, waaronder nieuw proces en nieuwe verbindings gebeurtenissen. Het nieuwe proces en de nieuwe verbindings gebeurtenissen kunnen zich binnen een tweede keer regel matig voordoen op een apparaat, en als belang rijk voor de robuuste en uitgebreide beveiliging, kan de hoeveelheid berichten waarmee de beveiligings agenten worden verzonden, snel worden bereikt of groter worden dan uw IoT Hub quota en kosten limieten. Deze gebeurtenissen bevatten echter zeer waardevolle beveiligings informatie die essentieel is voor het beveiligen van uw apparaat.

Azure Security Center voor IoT-agents deze typen gebeurtenissen samen voegen om het extra quotum en de kosten te verminderen terwijl uw apparaten beveiligd blijven.

Gebeurtenis aggregatie is standaard **ingeschakeld** en hoewel dit niet wordt aanbevolen, kan op elk gewenst moment hand matig **worden uitgeschakeld.**

Aggregatie is momenteel beschikbaar voor de volgende typen gebeurtenissen:
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (alleen Windows)

## <a name="how-does-event-aggregation-work"></a>Hoe werkt gebeurtenis aggregatie?
Wanneer gebeurtenis aggregatie is ingeschakeld, worden **in**Azure Security Center voor IOT-agents gebeurtenissen verzameld voor de interval periode of het tijd venster.
Zodra de interval periode is verstreken, verzendt de agent de geaggregeerde gebeurtenissen naar de Azure-Cloud voor verdere analyse.
De geaggregeerde gebeurtenissen worden in het geheugen opgeslagen totdat ze naar de Azure-Cloud worden verzonden.

De agent verhoogt het aantal treffers van deze specifieke gebeurtenis, wanneer de agent een identieke gebeurtenis verzamelt die al in het geheugen wordt bewaard, om het geheugen gebruik van de agent te verminderen. Wanneer het aggregatie tijd venster wordt door gegeven, verzendt de agent het aantal treffers van elk specifiek type gebeurtenis dat heeft plaatsgevonden. Gebeurtenis aggregatie is simpelweg de aggregatie van de treffer aantallen van elk verzameld type gebeurtenis.

Gebeurtenissen worden alleen als identiek beschouwd wanneer aan de volgende voor waarden wordt voldaan: 

* ProcessCreate-gebeurtenissen: wanneer **commandline**, **uitvoer bare bestand**, **gebruikers naam** en gebruikers- **id** identiek zijn
* ConnectionCreate-gebeurtenissen: **Wanneer commandline**, **GebruikersID**, **Direction**, **lokaal adres**, **extern adres**, **protocol** en **doel poort** identiek zijn
* ProcessTerminate-gebeurtenissen: wanneer het **uitvoer bare bestand** en de status van het **Afsluiten** identiek zijn

### <a name="working-with-aggregated-events"></a>Werken met geaggregeerde gebeurtenissen

Tijdens aggregatie worden gebeurtenis eigenschappen die niet worden geaggregeerd, verwijderd en weer gegeven in log Analytics met de waarde 0. 
* ProcessCreate-gebeurtenissen- **processId**en **parentProcessId** zijn ingesteld op 0
* ConnectionCreate-gebeurtenissen- **processId**en **bron poort** zijn ingesteld op 0

## <a name="event-aggregation-based-alerts"></a>Waarschuwingen op basis van gebeurtenis aggregatie 
Na analyse maakt Azure Security Center voor IoT beveiligings waarschuwingen voor verdachte geaggregeerde gebeurtenissen. Waarschuwingen die zijn gemaakt op basis van geaggregeerde gebeurtenissen, worden slechts één keer voor elke geaggregeerde gebeurtenis weer gegeven.

Begin tijd van de aggregatie, de eind tijd en het aantal treffers voor elke gebeurtenis worden vastgelegd in het veld gebeurtenis **ExtraDetails** binnen log Analytics voor gebruik tijdens het onderzoek. 

Elke geaggregeerde gebeurtenis vertegenwoordigt een periode van 24 uur aan verzamelde waarschuwingen. Met het menu gebeurtenis opties in de linkerbovenhoek van elke gebeurtenis kunt u elke afzonderlijke statistische gebeurtenis **sluiten** .    

## <a name="event-aggregation-twin-configuration"></a>Dubbele configuratie gebeurtenis aggregatie
Breng wijzigingen aan in de configuratie van Azure Security Center voor IoT-gebeurtenis aggregatie in het object van de [agent configuratie](how-to-agent-configuration.md) van de module dubbele identiteit van de module **azureiotsecurity** .

| Configuratie naam | Mogelijke waarden | Details | Opmerkingen |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | Gebeurtenis aggregatie voor het maken van proces gebeurtenissen in-of uitschakelen |
| aggregationIntervalProcessCreate | ISO8601 time span-teken reeks | Aggregatie-interval voor het maken van proces gebeurtenissen |
| aggregationEnabledConnectionCreate | boolean| Aggregatie van gebeurtenissen in-of uitschakelen voor het maken van verbinding |
| aggregationIntervalConnectionCreate | ISO8601 time span-teken reeks | Aggregatie-interval voor het maken van verbindings gebeurtenissen |
| aggregationEnabledProcessTerminate | boolean | Gebeurtenis aggregatie voor proces beëindigings gebeurtenissen in-/uitschakelen | Alleen Windows|
| aggregationIntervalProcessTerminate | ISO8601 time span-teken reeks | Aggregatie-interval voor gebeurtenissen voor proces beëindigen | Alleen Windows|
|

## <a name="default-configurations-settings"></a>Standaard configuratie-instellingen

| Configuratie naam | Standaardwaarden |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | true |
| aggregationIntervalProcessCreate | PT1H|
| aggregationEnabledConnectionCreate | true |
| aggregationIntervalConnectionCreate | PT1H|
| aggregationEnabledProcessTerminate | true |
| aggregationIntervalProcessTerminate | PT1H|
|

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over Azure Security Center voor de samen voeging van IoT-beveiligings agent en de beschik bare opties voor gebeurtenis configuratie.

Als u aan de slag wilt gaan met Azure Security Center voor IoT-implementatie, gebruikt u de volgende artikelen:

- Meer informatie over [verificatie methoden voor beveiligings agenten](concept-security-agent-authentication-methods.md)
- Een [beveiligings agent](how-to-deploy-agent.md) selecteren en implementeren
- Azure Security Center controleren op vereisten voor IoT- [Services](service-prerequisites.md)
- Meer informatie over het [inschakelen van Azure Security Center voor IOT-service in uw IOT hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service vindt u in de [Veelgestelde vragen over Azure Security Center voor IOT](resources-frequently-asked-questions.md)

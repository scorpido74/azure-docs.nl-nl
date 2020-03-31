---
title: Azure Security Center for IoT-gebeurtenisaggregatie| Microsoft Documenten
description: Meer informatie over azure security center voor IoT-gebeurtenisaggregatie.
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
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73928958"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>Azure Security Center for IoT-gebeurtenisaggregatie

Azure Security Center voor IoT-beveiligingsagents verzamelt gegevens en systeemgebeurtenissen van uw lokale apparaat en verzendt deze gegevens naar de Azure-cloud voor verwerking en analyse. De beveiligingsagent verzamelt vele soorten apparaatgebeurtenissen, waaronder nieuwe proces- en nieuwe verbindingsgebeurtenissen. Zowel nieuwe proces- als nieuwe verbindingsgebeurtenissen kunnen binnen een seconde regelmatig op een apparaat voorkomen en hoewel het belangrijk is voor robuuste en uitgebreide beveiliging, kan het aantal berichten dat beveiligingsmedewerkers moeten verzenden, uw IoT Hub snel bereiken of overschrijden quota en kostenlimieten. Deze gebeurtenissen bevatten echter zeer waardevolle beveiligingsinformatie die van cruciaal belang is voor de bescherming van uw apparaat.

Azure Security Center for IoT Agents verzamelt dit soort gebeurtenissen om de extra quota en kosten te verlagen en tegelijkertijd uw apparaten te beschermen.

Gebeurtenisaggregatie is standaard **ingeschakeld** en kan op elk gewenst moment handmatig worden **uitgeschakeld.**

Aggregatie is momenteel beschikbaar voor de volgende typen gebeurtenissen:
* ProcesMaken
* Verbinding Maken
* ProcessTerminate (alleen Windows)

## <a name="how-does-event-aggregation-work"></a>Hoe werkt gebeurtenisaggregatie?
Wanneer gebeurtenisaggregatie **wordt**ingeschakeld , verzamelt Azure Security Center voor IoT-agents gebeurtenissen voor de intervalperiode of het tijdvenster.
Zodra de intervalperiode is verstreken, stuurt de agent de geaggregeerde gebeurtenissen naar de Azure-cloud voor verdere analyse.
De geaggregeerde gebeurtenissen worden opgeslagen in het geheugen totdat ze naar de Azure-cloud worden verzonden.

Om de geheugenvoetafdruk van de agent te verminderen, verhoogt de agent de hittelling van deze specifieke gebeurtenis wanneer de agent een identieke gebeurtenis verzamelt die al in het geheugen wordt bewaard. Wanneer het aggregatietijdvenster wordt weergegeven, verzendt de agent het aantal treffers van elk specifiek type gebeurtenis dat is opgetreden. Gebeurtenisaggregatie is eenvoudig de aggregatie van de klaptellingen van elk verzameld type gebeurtenis.

Gebeurtenissen worden alleen als identiek beschouwd wanneer aan de volgende voorwaarden is voldaan: 

* ProcessCreate gebeurtenissen - wanneer **commandLine,** **uitvoerbaar,** **gebruikersnaam**en **userid** identiek zijn
* ConnectionCreate events - wanneer **commandLine,** **userId,** **direction,** **local address**, **remote address**, **protocol en **destination port** identiek zijn
* ProcessTerminate gebeurtenissen - wanneer **de uitvoerbare** en **exit status** identiek zijn

### <a name="working-with-aggregated-events"></a>Werken met geaggregeerde gebeurtenissen

Tijdens aggregatie worden gebeurtenis-eigenschappen die niet zijn samengevoegd, verwijderd en worden ze weergegeven in logboekanalyses met een waarde van 0. 
* ProcessCreate-gebeurtenissen - **processId**en **parentProcessId** zijn ingesteld op 0
* ConnectionCreate-gebeurtenissen - **processId**en **bronpoort** zijn ingesteld op 0

## <a name="event-aggregation-based-alerts"></a>Waarschuwingen op basis van gebeurtenisaggregatie 
Na analyse maakt Azure Security Center for IoT beveiligingswaarschuwingen voor verdachte geaggregeerde gebeurtenissen. Waarschuwingen die zijn gemaakt van geaggregeerde gebeurtenissen, worden slechts één keer weergegeven voor elke geaggregeerde gebeurtenis.

De begintijd, eindtijd en het aantal treffers voor elke gebeurtenis worden geregistreerd in het veld **ExtraDetails** in Log Analytics voor gebruik tijdens onderzoeken. 

Elke geaggregeerde gebeurtenis vertegenwoordigt een periode van 24 uur van verzamelde waarschuwingen. Met het menu gebeurtenisopties linksboven van elke gebeurtenis u elke afzonderlijke samengevoegde gebeurtenis **verwijderen.**    

## <a name="event-aggregation-twin-configuration"></a>Dubbele configuratie van gebeurtenisaggregatie
Breng wijzigingen aan in de configuratie van Azure Security Center for IoT-gebeurtenisaggregatie in het [agentconfiguratieobject](how-to-agent-configuration.md) van de module dubbele identiteit van de **azureiotsecurity-module.**

| Configuratienaam | Mogelijke waarden | Details | Opmerkingen |
|:-----------|:---------------|:--------|:--------|
| aggregatieIngeschakeldProcesmaken | booleaans | Gebeurtenisaggregatie in- of uitschakelen voor procesgebeurtenisgebeurtenissen |
| aggregatieIntervalProcessCreate | ISO8601 Tijdspanne | Aggregatie-interval voor procesgebeurtenisgebeurtenissen |
| aggregatieIngeschakeldVerbindingMaken | booleaans| Gebeurtenisaggregatie in- of uitschakelen voor verbindingsgebeurtenissen |
| aggregatieIntervalConnectionCreate | ISO8601 Tijdspanne | Aggregatie-interval voor verbindingsgebeurteniss |
| aggregatieEnabledProcessTerminate | booleaans | Gebeurtenisaggregatie in- of uitschakelen voor procesbeëindigingsgebeurtenissen | Alleen op Windows|
| aggregatieIntervalProcessTerminate | ISO8601 Tijdspanne | Aggregatie-interval voor procesbeëindigingsgebeurtenissen | Alleen op Windows|
|

## <a name="default-configurations-settings"></a>Standaardinstellingen voor configuraties

| Configuratienaam | Standaardwaarden |
|:-----------|:---------------|
| aggregatieIngeschakeldProcesmaken | waar |
| aggregatieIntervalProcessCreate | "PT1H"|
| aggregatieIngeschakeldVerbindingMaken | waar |
| aggregatieIntervalConnectionCreate | "PT1H"|
| aggregatieEnabledProcessTerminate | waar |
| aggregatieIntervalProcessTerminate | "PT1H"|
|

## <a name="next-steps"></a>Volgende stappen

In dit artikel leert u over azure security center voor iot-beveiligingsagentaggregatie en de beschikbare gebeurtenisconfiguratieopties.

Gebruik de volgende artikelen om verder te gaan met Azure Security Center for IoT-implementatie:

- Verificatiemethoden [voor beveiligingsagent begrijpen](concept-security-agent-authentication-methods.md)
- Een [beveiligingsagent](how-to-deploy-agent.md) selecteren en implementeren
- Vereisten voor Azure Security Center voor IoT-services [controleren](service-prerequisites.md)
- Meer informatie over het [inschakelen van Azure Security Center voor IoT-service in uw IoT-hub](quickstart-onboard-iot-hub.md)
- Meer informatie over de service vindt u van de veelgestelde vragen over [azure security center voor IoT](resources-frequently-asked-questions.md)

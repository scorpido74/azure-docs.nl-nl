---
title: Richtlijnen voor een verantwoorde inzet van synthetische spraaktechnologie
titleSuffix: Azure Cognitive Services
description: De algemene ontwerprichtlijnen van Microsoft voor het gebruik van synthetische spraaktechnologie. Deze werden ontwikkeld in studies die Microsoft uitgevoerd met stemtalent, consumenten, evenals personen met spraakstoornissen om de verantwoorde ontwikkeling van synthetische stem te begeleiden.
services: cognitive-services
author: benoah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: benoah
ms.openlocfilehash: a529ac8b7ce16d3ee4463f1b4bc2e8007e5b79b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73836769"
---
# <a name="guidelines-for-responsible-deployment-of-synthetic-voice-technology"></a>Richtlijnen voor een verantwoorde inzet van synthetische spraaktechnologie
Hier zijn de algemene ontwerprichtlijnen van Microsoft voor het gebruik van synthetische spraaktechnologie. Deze werden ontwikkeld in studies die Microsoft uitgevoerd met stemtalent, consumenten, evenals personen met spraakstoornissen om de verantwoorde ontwikkeling van synthetische stem te begeleiden.

## <a name="general-considerations"></a>Algemene overwegingen
Voor de implementatie van synthetische spraaktechnologie zijn de volgende richtlijnen van toepassing op de meeste scenario's.

### <a name="disclose-when-the-voice-is-synthetic"></a>Onthullen wanneer de stem synthetisch is
Openbaarmaking dat een stem is computer gegenereerd niet alleen minimaliseert het risico van schadelijke resultaten van misleiding, maar verhoogt ook het vertrouwen in de organisatie het leveren van de stem. Meer informatie over [hoe u het openbaar maken.](concepts-disclosure-guidelines.md)

### <a name="select-appropriate-voice-types-for-your-scenario"></a>De juiste stemtypen voor uw scenario selecteren
Zorgvuldig overwegen de context van het gebruik en de mogelijke schade in verband met het gebruik van synthetische stem. Synthetische stemmen met hoge betrouwbaarheid zijn bijvoorbeeld mogelijk niet geschikt in scenario's met een hoog risico, zoals voor persoonlijke berichten, financiële transacties of complexe situaties die menselijk aanpassingsvermogen of empathie vereisen. Gebruikers kunnen ook verschillende verwachtingen hebben voor spraaktypen. Bijvoorbeeld, bij het luisteren naar gevoelig nieuws wordt gelezen door een synthetische stem, sommige gebruikers de voorkeur aan een meer empathische en mens-achtige lezing van het nieuws, terwijl anderen de voorkeur aan een meer monotone, onpartijdige stem. Overweeg uw toepassing te testen om gebruikersvoorkeuren beter te begrijpen.

### <a name="be-transparent-about-capabilities-and-limitations"></a>Wees transparant over mogelijkheden en beperkingen
Gebruikers hebben meer kans op hogere verwachtingen bij de interactie met high-fidelity synthetische spraakmiddelen. Bijgevolg, wanneer systeemmogelijkheden niet aan deze verwachtingen voldoen, kan vertrouwen lijden en kan dit leiden tot onaangename of zelfs schadelijke ervaringen.

### <a name="provide-optional-human-support"></a>Bied optionele menselijke ondersteuning
In dubbelzinnige, transactionele scenario's (bijvoorbeeld een call support center) vertrouwen gebruikers niet altijd op een computeragent om op de juiste manier te reageren op hun verzoeken. Menselijke ondersteuning kan nodig zijn in deze situaties, ongeacht de realistische kwaliteit van de stem of het vermogen van het systeem.

## <a name="considerations-for-voice-talent"></a>Overwegingen voor stemtalent
Bij het werken met stemtalent, zoals stemacteurs, om synthetische stemmen te maken, is de onderstaande richtlijn van toepassing.

### <a name="obtain-meaningful-consent-from-voice-talent"></a>Krijg zinvolle toestemming van stemtalent
Voice talent verwachten controle te hebben over hun stem lettertype (hoe en waar het zal worden gebruikt) en worden gecompenseerd wanneer het wordt gebruikt. Systeemeigenaren moeten daarom expliciete schriftelijke toestemming krijgen van stemtalent en duidelijke contractuele specificaties hebben over use cases, gebruiksduur, compensatie, enzovoort. Sommige stem talent zijn zich niet bewust van de potentiële kwaadaardige toepassingen van de technologie en moet worden opgeleid door systeemeigenaren over de mogelijkheden van de technologie. Voor meer informatie over stemtalent en toestemming, lees onze [Disclosure for Voice Talent](https://aka.ms/disclosure-voice-talent).


## <a name="considerations-for-those-with-speech-disorders"></a>Overwegingen voor mensen met spraakstoornissen
Bij het werken met personen met spraakstoornissen, om synthetische spraaktechnologie te creëren of te implementeren, zijn de volgende richtlijnen van toepassing.

### <a name="provide-guidelines-to-establish-contracts"></a>Richtlijnen verstrekken om contracten op te stellen
Geef richtlijnen voor het opstellen van contracten met personen die synthetische stem gebruiken voor hulp bij het spreken. In het contract moet worden overwogen de partijen te specificeren die eigenaar zijn van de stem, gebruiksduur, eigendomsoverdrachtscriteria, procedures voor het verwijderen van het spraaklettertype en hoe ongeautoriseerde toegang kan worden voorkomen. Bovendien, inschakelen van de contractuele overdracht van spraaklettertype eigendom na overlijden aan familieleden als die persoon toestemming heeft gegeven.

### <a name="account-for-inconsistencies-in-speech-patterns"></a>Rekening houden met inconsistenties in spraakpatronen
Voor personen met spraakstoornissen die hun eigen stemlettertypen opnemen, kunnen inconsistenties in hun spraakpatroon (slurring of onvermogen om bepaalde woorden uit te spreken) het opnameproces bemoeilijken. In deze gevallen moeten synthetische spraaktechnologie en opnamesessies geschikt zijn voor hen (dat wil zeggen, pauzes en extra aantal opnamesessies).

### <a name="allow-modification-over-time"></a>Wijziging na verloop van tijd toestaan
Personen met spraakstoornissen verlangen om updates te maken van hun synthetische stem te weerspiegelen veroudering (bijvoorbeeld, een kind het bereiken van de puberteit). Individuen kunnen ook stilistische voorkeuren hebben die in de loop van de tijd veranderen en kunnen wijzigingen willen aanbrengen in toonhoogte, accent of andere stemkenmerken.


## <a name="reference-docs"></a>Referentiedocumenten

* [Openbaarmaking voor Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Overzicht van Gating](concepts-gating-overview.md)
* [Hoe openbaar te maken](concepts-disclosure-guidelines.md)
* [Ontwerpontwerppatronen](concepts-disclosure-patterns.md)

## <a name="next-steps"></a>Volgende stappen

* [Openbaarmaking voor Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Hoe openbaar te maken](concepts-disclosure-guidelines.md)
* [Ontwerpontwerppatronen](concepts-disclosure-patterns.md)

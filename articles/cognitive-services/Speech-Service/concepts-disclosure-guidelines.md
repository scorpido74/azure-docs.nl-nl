---
title: Richtlijnen voor openbaarmakingsontwerp
titleSuffix: Azure Cognitive Services
description: Inleiding tot richtlijnen voor het openbaarmakingsontwerp en het beoordelen van het openbaarmakingsniveau.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: angle
ms.openlocfilehash: fe38c6b7cfb1abbaf3f1079dd8bff66b51b98091
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "74776382"
---
# <a name="disclosure-design-guidelines"></a>Richtlijnen voor het ontwerpen van openbaarmakingen
Leer hoe je vertrouwen opbouwen en onderhouden bij klanten door transparant te zijn over het synthetische karakter van je spraakervaring.

## <a name="what-is-disclosure"></a>Wat is openbaarmaking?

Disclosure is een middel om mensen te laten weten dat ze&#39;interactie met of luisteren naar een stem die synthetisch wordt gegenereerd.

## <a name="why-is-disclosure-necessary"></a>Waarom is openbaarmaking noodzakelijk?

De noodzaak om de synthetische oorsprong van een computergegenereerde stem bekend te maken is relatief nieuw. In het verleden, computer-gegenereerde stemmen waren natuurlijk dat-niemand ooit zou ze verwarren met een echte persoon. Elke dag, echter, het realisme van synthetische stemmen verbetert, en ze worden steeds meer niet te onderscheiden van menselijke stemmen.

## <a name="goals"></a>Doelstellingen
Dit zijn de principes om in gedachten te houden bij het ontwerpen van synthetische spraakervaringen:

**Vergroot het vertrouwen**
<br>Ontwerp met de bedoeling om de Turing Test te mislukken zonder de ervaring te vernederen. Laat de gebruikers in op het feit dat ze interactie met een synthetische stem, terwijl ze naadloos in contact met de ervaring.

**Aanpassen aan de context van het gebruik**
<br>Begrijp wanneer, waar en hoe uw gebruikers zullen communiceren met de synthetische stem om het juiste type openbaarmaking op het juiste moment te bieden.

**Stel duidelijke verwachtingen**
<br>Laat gebruikers gemakkelijk de mogelijkheden van de agent ontdekken en begrijpen. Bied desgevraagd mogelijkheden om meer te weten te komen over synthetische spraaktechnologie.

**Omarm falen**
<br>Gebruik momenten van falen om de mogelijkheden van de agent te versterken.

## <a name="how-to-use-this-guide"></a>Het gebruik van deze handleiding

Deze gids helpt u te bepalen welke openbaarmakingspatronen het beste passen bij uw synthetische spraakervaring. Vervolgens geven we voorbeelden van hoe en wanneer ze moeten worden gebruikt. Elk van deze patronen is ontworpen om de transparantie met gebruikers over synthetische spraak te maximaliseren, terwijl trouw blijven aan mensgericht ontwerp.

Gezien de enorme hoeveelheid ontwerpbegeleiding over stemervaringen, richten we ons hier specifiek op:

1. [**Beoordeling van de openbaarmaking**](#disclosure-assessment): Een proces om het type openbaarmaking te bepalen dat wordt aanbevolen voor uw synthetische spraakervaring

2. [**Hoe te onthullen:**](concepts-disclosure-patterns.md)Voorbeelden van openbaarmakingspatronen die kunnen worden toegepast op uw synthetische spraakervaring

3. [**Wanneer bekend te maken**](concepts-disclosure-patterns.md#when-to-disclose): Optimale momenten om bekend te maken tijdens de gebruikersreis

## <a name="disclosure-assessment"></a>Beoordeling van de openbaarmaking
Houd rekening met uw gebruikers&#39; verwachtingen over een interactie en de context waarin ze de stem zullen ervaren. Als de context duidelijk maakt dat &quot;een&quot; synthetische stem spreekt, kan openbaarmaking minimaal, tijdelijk of zelfs onnodig zijn. De belangrijkste typen context die van invloed zijn op openbaarmaking zijn persona-type, scenariotype en blootstellingsniveau. Het helpt ook om te overwegen wie zou kunnen luisteren.

### <a name="understand-context"></a>Begrijpen van de context

Gebruik dit werkblad om de context van uw synthetische spraakervaring te bepalen. Je past dit toe in de volgende stap waar je je openbaarmakingsniveau bepaalt.

|                                    | Context van gebruik                                                                                                                                                                                                                                                                                                                                                       | Potentiële risico's & uitdagingen                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Persona-type**               | **Als een van de volgende van toepassing is, past uw persona onder de categorie 'Mens-achtig Persona':**<br><br><ul><li> Persona belichaamt een echte mens, of het nu een fictieve voorstelling is of niet. (bijvoorbeeld foto of een computergegenereerde weergave van een echte persoon)<br><br><li> De synthetische stem is gebaseerd op de stem van een breed herkenbare echte persoon (bijvoorbeeld, beroemdheid, politieke figuur) | Hoe meer mens-achtige voorstellingen u uw persona geven, hoe groter de kans dat een gebruiker het zal associëren met een echte persoon, of ervoor zorgt dat ze geloven dat de inhoud wordt gesproken door een echte persoon in plaats van computer-gegenereerd. </ul>                                                                                                                                                                      |
| **2. Scenariotype**            | **Als een van de volgende toepassing, uw stem ervaring past onder de 'Gevoelige' categorie:**<br><br><ul><li> Persoonlijke informatie van de gebruiker verkrijgen of weergeven <br><br> <li> Zendt tijdgevoelig nieuws/informatie uit (bijv. noodwaarschuwing)<br><br><li> Is bedoeld om echte mensen te helpen met elkaar te communiceren (bijvoorbeeld, leest persoonlijke e-mails / teksten)<br><br> <li> Biedt medische/gezondheidshulp </ul>            | Het gebruik van synthetische stem kan niet geschikt of betrouwbaar voelen voor de mensen die het gebruiken wanneer onderwerpen gerelateerd zijn aan gevoelige, persoonlijke of dringende zaken. Ze kunnen ook verwachten dat hetzelfde niveau van empathie en contextuele bewustzijn als een echte mens. |
| **3. Blootstellingsniveau** |**Uw spraakervaring past waarschijnlijk onder de categorie 'Hoog' als:** <br><br><ul><li>De gebruiker zal de synthetische stem vaak of gedurende een lange tijd horen of ermee communiceren </ul>                                                                                                                                                                             | Het belang van transparant zijn en vertrouwen opbouwen met gebruikers is nog groter bij het aangaan van langdurige relaties.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Het openbaarmakingsniveau bepalen

Gebruik het volgende diagram om te bepalen of uw synthetische spraakervaring een hoge of lage openbaarmaking vereist op basis van uw gebruikscontext.

  ![Informatiebeoordelingsdiagram](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="reference-docs"></a>Referentiedocumenten

* [Openbaarmaking voor Voice Talent](https://aka.ms/disclosure-voice-talent)
* [Richtlijnen voor verantwoorde implementatie van synthetische spraaktechnologie](concepts-guidelines-responsible-deployment-synthetic.md)
* [Gating overzicht](concepts-gating-overview.md)

## <a name="next-steps"></a>Volgende stappen

* [Ontwerppatronen van openbaarmakingen](concepts-disclosure-patterns.md)

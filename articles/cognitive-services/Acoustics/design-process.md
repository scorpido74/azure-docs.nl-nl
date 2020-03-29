---
title: Ontwerpconcepten met akoestische simulatie
titlesuffix: Azure Cognitive Services
description: Dit conceptuele overzicht legt uit hoe Project Acoustics akoestische simulatie verwerkt in het geluidsontwerpproces.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 11e1e3f45b5198ddedb6c31fcd354185adef445d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854295"
---
# <a name="project-acoustics-design-process-concepts"></a>Project Acoustics Ontwerp procesconcepten

Dit conceptuele overzicht legt uit hoe Project Acoustics fysieke akoestische simulatie verwerkt in het geluidsontwerpproces.

## <a name="sound-design-with-audio-dsp-parameters"></a>Geluidsontwerp met audio-DSP-parameters

3D interactieve titels bereiken hun specifieke geluid met behulp van audio digitale signaal verwerking (DSP) blokken gehost in een audio-engine. Deze blokken variëren in complexiteit van eenvoudig mengen, tot nagalm, echo, vertraging, egalisatie, compressie en beperking, en andere effecten. Het selecteren, rangschikken en instellen van parameters op deze effecten is de verantwoordelijkheid van de geluidsontwerper, die een audiografiek bouwt die de esthetische en gameplay-doelen van de ervaring bereikt.

Hoe passen deze parameters zich aan veranderende omstandigheden aan in een interactieve titel, terwijl de geluiden en de luisteraar door de 3D-ruimte bewegen? De geluidsontwerper zal vaak volumes in de ruimte ordenen die zijn geprogrammeerd om parameterwijzigingen te activeren om bijvoorbeeld veranderingen in nagalmeffecten te bereiken of om geluiden in de mix te ontwijken terwijl de luisteraar van het ene deel van de scène naar het andere gaat. Er zijn ook akoestieksystemen beschikbaar die sommige van deze effecten kunnen automatiseren.

3D-titels maken gebruik van verlichting en kinematische fysica systemen die fysica-gemotiveerd, maar ontwerper-aangepast om een mix van onderdompeling en gameplay doelen te bereiken. Een visuele ontwerper stelt geen individuele pixelwaarden in, maar past 3D-modellen, materialen en lichttransportsystemen aan die allemaal fysiek zijn gebaseerd om visuele esthetiek en CPU-kosten af te zetten. Wat zou het gelijkwaardige proces voor audio? Project Acoustics is een eerste stap in de verkenning van deze vraag. Eerst zullen we ingaan op wat het betekent om akoestische energie te transporteren door een ruimte.

![Schermafbeelding van altspace-scène die is bedekt met reverb-zones](media/reverb-zones-altspace.png)

## <a name="impulse-responses-acoustically-connecting-two-points-in-space"></a>Impulsreacties: Akoestisch twee punten in de ruimte verbinden

Als u bekend bent met audio-ontwerp, bent u mogelijk bekend met akoestische impulsreacties. Een akoestische impulsrespons modelleert het transport van een geluid van een bron naar een luisteraar. Daarom kan een impulsreactie elk interessant effect van ruimteakoestiek zoals occlusie en nagalm vangen. Impulsreacties hebben ook bepaalde krachtige eigenschappen waarmee audio-DSP-effecten kunnen worden geschaald. Het toevoegen van twee audiosignalen en verwerking met een impulsrespons geeft hetzelfde resultaat als het afzonderlijk toepassen van de impulsrespons op elk signaal en het toevoegen van de resultaten. Akoestische voortplanting en impulsreacties zijn ook niet afhankelijk van de audio die wordt verwerkt, alleen van de scène die wordt gemodelleerd en de bron- en listenerlocaties. Kortom, een impulsreactie destilleert het effect van de scène op de geluidsvoortplanting.

Een impulsrespons vangt elk interessant kamerakoestisch effect vast, en we kunnen het efficiënt toepassen op audio met een filter, en we kunnen impulsreacties krijgen van meting of simulatie. Maar wat als we niet willen dat de akoestiek precies bij de fysica past, maar het precies vormen om aan de emotionele eisen van een scène te voldoen? Maar net als pixelwaarden, een impulsreactie is slechts een lijst van duizenden nummers, hoe kunnen we eventueel aanpassen aan esthetische behoeften? En wat als we occlusie/ obstructie willen hebben die soepel varieert tijdens het passeren van deuropeningen of achter obstakels, hoeveel impulsreacties hebben we nodig om een soepel effect te krijgen? Wat als de bron snel beweegt? Hoe interpoleren we?

Het klinkt moeilijk om simulatie en impulsreacties te gebruiken voor sommige aspecten van akoestiek in interactieve titels. Maar we kunnen nog steeds een audiotransportsysteem bouwen dat designeraanpassingen ondersteunt als we onze impulsreacties van simulatie kunnen verbinden met onze bekende audio DSP-effectparameters.

## <a name="connecting-simulation-to-audio-dsp-with-parameters"></a>Simulatie verbinden met audio-DSP met parameters

Een impulsreactie bevat elk interessant (en elk oninteressant) akoestisch effect. Audio DSP blokken, wanneer hun parameters goed zijn ingesteld, kan interessante akoestische effect weer te geven. Het gebruik van akoestische simulatie om een audio DSP-blok te stimuleren om audiotransport in een 3D-scène te automatiseren, is slechts een kwestie van het meten van de audio-DSP-parameters vanuit een impulsrespons. Deze meting is goed begrepen voor bepaalde gemeenschappelijke en belangrijke akoestische effecten, waaronder occlusie, obstructie, portalen en nagalm.

Maar als de simulatie rechtstreeks is aangesloten op de audio-DSP parameters, waar is de ontwerper aanpassing? Wat hebben we binnen? Nou, we krijgen een aanzienlijke hoeveelheid geheugen terug door het weggooien van impulsreacties en het behoud van een paar DSP parameters. En om de ontwerper wat macht te geven over het eindresultaat, hoeven we alleen maar een manier te vinden om de ontwerper tussen de simulatie en de audio DSP in te voegen.

![Grafiek met gestileerde impulsreactie met parameters bedekt](media/acoustic-parameters.png)

## <a name="sound-design-by-transforming-audio-dsp-parameters-from-simulation"></a>Geluidsontwerp door audio-DSP-parameters te transformeren uit simulatie

Denk aan het effect dat uw zonnebril heeft op uw kijk op de wereld. Op een heldere dag kan de bril de glans reduceren tot iets comfortabelers. In een donkere kamer, zou je niet in staat zijn om iets te zien op alle. De bril stelt niet in alle situaties een bepaald helderheidsniveau in; ze maken alles donkerder.

Als we simulatie gebruiken om onze audio DSP aan te drijven met occlusie- en nagalmparameters, kunnen we na de simulator een filter toevoegen om de parameters aan te passen die de DSP 'ziet'. Het filter zou niet afdwingen een bepaald niveau van occlusie of reverb staart lengte, net als zonnebrillen maken niet elke kamer dezelfde helderheid. Het filter zou gewoon elke occluder occlude minder. Of meer occlude. Door het toevoegen en aanpassen van een 'verduisterend' occlusieparameterfilter zouden grote, open ruimtes nog steeds weinig tot geen occlusie-effect hebben, terwijl deuropeningen zouden toenemen van een medium naar een sterk occlusie-effect, met behoud van de gladheid in effectovergangen die de simulatie biedt.

In dit paradigma verandert de taak van de ontwerper van het kiezen van akoestische parameters voor elke situatie, in het selecteren en aanpassen van filters om toe te passen op de belangrijkste DSP-parameters die afkomstig zijn van simulatie. Het verheft de activiteiten van de ontwerper van de smalle zorgen van het opzetten van soepele overgangen naar de hogere zorgen van de intensiteit van occlusie en nagalm effecten en de aanwezigheid van bronnen in de mix. Natuurlijk, wanneer de situatie vraagt, een filter altijd beschikbaar is om gewoon terug te gaan naar het kiezen van de DSP parameters voor een specifieke bron in een specifieke situatie.

## <a name="sound-design-in-project-acoustics"></a>Sound design in Project Acoustics

Het Project Acoustics-pakket integreert elk van de hierboven beschreven componenten: een simulator, een encoder die parameters extraheert en de akoestiek asset, audio DSP, en een selectie van filters bouwt. Sound design met Project Acoustics houdt in dat parameters worden gekozen voor de filters die de occlusie- en nagalmparameters aanpassen die zijn afgeleid van simulatie en worden toegepast op de audio-DSP, met dynamische bedieningselementen die in de game-editor en de audio-engine worden belicht.

## <a name="next-steps"></a>Volgende stappen
* Probeer het ontwerpparadigma met behulp van de [Project Acoustics quickstart voor Unity](unity-quickstart.md) of de Project Acoustics [quickstart voor Unreal](unreal-quickstart.md)
* Ontdek de [ontwerpregelaars van Project Acoustics voor Unity](unity-workflow.md) of de [Ontwerpregelaars van Project Acoustics voor Unreal](unreal-workflow.md)


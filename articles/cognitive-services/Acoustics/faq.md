---
title: Veelgestelde vragen over project akoestische
titlesuffix: Azure Cognitive Services
description: Op deze pagina vindt u antwoorden op veelgestelde vragen over project akoestische, waaronder Download instructies en maken-proces.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: fa4b6499260219b0eb8ea4df4b4ccfd5263b57bb
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75770200"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Veelgestelde vragen over project akoestische

## <a name="what-is-project-acoustics"></a>Wat is Project Acoustics?

Het project akoestische pakket met invoeg toepassingen is een akoestisch systeem dat het gedrag van geluid tijdens het uitvoeren van de Akin in een statische belichting berekent. In de Cloud wordt de generatie van Wave-fysieke berekeningen geoogst, waardoor de CPU-kosten van runtime laag zijn.  

## <a name="where-can-i-download-the-plugin"></a>Waar kan ik de invoeg toepassing downloaden?

U kunt de invoeg [toepassing project akoestische unit-eenheid](https://www.microsoft.com/download/details.aspx?id=57346) of de [project akoestische Unreal-invoeg toepassing](https://www.microsoft.com/download/details.aspx?id=58090)downloaden.

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Ondersteunt project akoestische &lt;x&gt;-platform?

De ondersteuning voor het ontwikkelings platform van het project is gebaseerd op de behoeften van de klant. Neem contact met ons op op het [Forum project akoestische geluids problemen](https://github.com/microsoft/ProjectAcoustics/issues) voor meer informatie over ondersteuning voor extra platforms.

## <a name="is-azure-used-at-runtime"></a>Wordt Azure gebruikt tijdens runtime?

Nee, Cloud integratie wordt alleen tijdens de precompute-fase gebruikt als onderdeel van de scène-instelling.
 
## <a name="what-is-simulation-input"></a>Wat is simulatie invoer? 

De simulatie invoer is uw 3D-scène, de virtuele omgeving of het spel niveau. Projectbudgeties voert 3D volumetrische Golf simulaties uit die de fysica van het geluid nauw keurig model leren, met inbegrip van soepele bedekking en sprei ding.
 
## <a name="what-is-the-runtime-cost"></a>Wat zijn de runtime kosten?

De geluids bronnen duren ongeveer 0,01% van de CPU per bron per frame. Het RAM-gebruik is afhankelijk van de grootte van de scène en kan variëren van 10 tot 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Moet ik de niveau geometrie vereenvoudigen? Aantal besturings elementen in drie hoeken? Wilt u netten water dicht maken?

Nee. Het systeem neemt de geometrie van het gedetailleerde niveau rechtstreeks op. Dit wordt voxelized voor interne verwerking.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Wat is er in de opzoek tabel voor runtime?

Het ACE-bestand bevat een tabel met akoestische para meters tussen talloze bron-en listener-locatie paren, evenals de geometrie van de voxelized-scène die wordt gebruikt voor de interpolatie van para meters.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Kan de verwerkings verwerking van akoestische bronnen worden uitgevoerd?

Ja, in Project akoestische wordt de opzoek tabel geraadpleegd en de audio-DSP op elke tik bijgewerkt, zodat het verplaatsen van bronnen en listeners kan worden afgehandeld.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Kan dynamische geometrie van een project worden verwerkt? Deuren sluiten? Prik wanden verwijderd?

Nee. De akoestische para meters worden vooraf berekend op basis van de statische status van een spel niveau. We raden aan om de geometrie van de deur te verlaten en vervolgens extra bedekking toe te passen op basis van de status van destructible en beweeg bare spel objecten met behulp van bestaande technieken.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Maakt project akoestische gebruik van akoestische materialen?

Ja. Materialen worden gekozen uit de fysieke materiaal namen op uw niveau, absorptivity.
 
## <a name="what-do-the-probes-represent"></a>Wat zijn ' tests '?

Tests zijn een steek proef van de mogelijke locaties van spelers. Elke test vertegenwoordigt een afzonderlijke Golf simulatie van de scène die afkomstig is van de test locatie. Tijdens runtime worden akoestische para meters voor de locatie van de listener geïnterpoleerd vanaf locaties in de buurt.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Waarom besteedt u veel reken kracht in de Cloud? Wat is er aan de aanschaf?

Project akoestische biedt nauw keurige en betrouw bare akoestische para meters, zelfs voor zeer complexe virtuele omgevingen, waarbij elk architectuur aspect in rekening wordt gebracht. Het biedt vloeiende bedekking en obstakels en dynamische galm variaties zonder hand matige werking van teken volumes. Alle terwijl het lampje op de CPU resterend is tijdens runtime.

## <a name="what-exactly-happens-during-baking"></a>Wat gebeurt er precies tijdens "Baking"?

Een maken bestaat uit akoestische Golf simulaties van cuboid simulatie regio's die zijn gecentreerd op elke listener-test.

## <a name="is-my-source-content-secure"></a>Is mijn bron inhoud beveiligd?

De geometrie van de bron scène wordt niet geüpload naar de Cloud. In plaats daarvan werkt simulatie op een voxelization van uw scène, die wordt gecombineerd met test locatie gegevens en opgeslagen in een eigen indeling.     

## <a name="next-steps"></a>Volgende stappen
* Probeer de voorbeeld inhoud of [Unreal-voorbeeld inhoud](unreal-quickstart.md) van de [project akoestische-eenheid](unity-quickstart.md)


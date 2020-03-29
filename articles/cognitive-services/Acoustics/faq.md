---
title: Project Acoustics veelgestelde vragen
titlesuffix: Azure Cognitive Services
description: Deze pagina geeft antwoorden op vragen die vaak worden gesteld over Project Acoustics, inclusief downloadinstructies en bakproces.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75770200"
---
# <a name="project-acoustics-frequently-asked-questions"></a>Project Acoustics veelgestelde vragen

## <a name="what-is-project-acoustics"></a>Wat is Project Acoustics?

De Project Acoustics suite van plugins is een akoestieksysteem dat geluidgolf gedrag voor runtime berekent, verwant aan statische verlichting. De wolk doet het zware werk van golf fysica berekeningen, dus runtime CPU kosten is laag.  

## <a name="where-can-i-download-the-plugin"></a>Waar kan ik de plugin downloaden?

U de [Project Acoustics Unity plugin](https://www.microsoft.com/download/details.aspx?id=57346) of de Project Acoustics Unreal [plugin](https://www.microsoft.com/download/details.aspx?id=58090)downloaden.

## <a name="does-project-acoustics-support-ltxgt-platform"></a>Ondersteunt Project &lt;Acoustics&gt; het X-platform?

Project Acoustics platform ondersteuning evolueert op basis van de behoeften van de klant. Neem contact met ons op via het [projectacoustics-uitgifteforum](https://github.com/microsoft/ProjectAcoustics/issues) om te informeren naar ondersteuning voor extra platforms.

## <a name="is-azure-used-at-runtime"></a>Wordt Azure gebruikt tijdens runtime?

Nee, cloudintegratie wordt alleen gebruikt tijdens de precompute-fase als onderdeel van de scène-instelling.
 
## <a name="what-is-simulation-input"></a>Wat is simulatie-invoer? 

De simulatie-ingang is uw 3D-scène, virtuele omgeving of spelniveau. Project Acoustics voert 3D volumetrische golfsimulaties uit die de fysica van geluid nauwkeurig modelleren, inclusief gladde occlusie en verstrooiing.
 
## <a name="what-is-the-runtime-cost"></a>Wat zijn de runtime kosten?

Akoestiek neemt ongeveer 0,01% van de CPU per bron per frame. Het RAM-gebruik is afhankelijk van de grootte van de scène en kan variëren van 10 tot 100 MB.
 
## <a name="do-i-need-to-simplify-the-level-geometry-control-triangle-count-make-meshes-watertight"></a>Moet ik de niveaugeometrie vereenvoudigen? Controle driehoek tellen? Mazen waterdicht maken?

Nee. Het systeem zal gedetailleerde niveaugeometrie direct innemen. Het zal worden voxelized voor interne verwerking.
 
## <a name="whats-in-the-runtime-lookup-table"></a>Wat zit er in de opzoektafel voor runtime?

Het ACE-bestand bevat een tabel met akoestische parameters tussen talrijke bron- en listenerlocatieparen, evenals voxelized scènegeometrie die wordt gebruikt voor parameterinterpolatie.
 
## <a name="can-project-acoustics-handle-moving-sources"></a>Kan Project Acoustics bewegende bronnen aan?

Ja, Project Acoustics raadpleegt de opzoektafel en werkt de audio-DSP bij elke teek, zodat het bewegende bronnen en luisteraar kan verwerken.
 
## <a name="can-project-acoustics-handle-dynamic-geometry-closing-doors-walls-blown-away"></a>Kan Project Acoustics omgaan met dynamische geometrie? Deuren sluiten? Muren weggeblazen?

Nee. De akoestische parameters worden vooraf berekend op basis van de statische toestand van een spelniveau. We raden aan om deurgeometrie uit de akoestiek te laten en vervolgens extra occlusie toe te passen op basis van de staat van vernietigbare en beweegbare spelobjecten met behulp van gevestigde technieken.
 
## <a name="does-project-acoustics-use-acoustic-materials"></a>Gebruikt Project Acoustics akoestische materialen?

Ja. Materialen worden geplukt uit de fysieke materiaalnamen in uw niveau, het besturen van onthouding.
 
## <a name="what-do-the-probes-represent"></a>Wat betekenen de "sondes"?

Probes zijn een steekproef van mogelijke speler locaties. Elke sonde vertegenwoordigt een afzonderlijke golfsimulatie van de scène die op de sondeplaats voortkomt. Bij runtime worden akoestische parameters voor de listenerlocatie geïnterpoleerd vanaf locaties in de buurt van sondelocaties.
 
## <a name="why-spend-so-much-compute-in-the-cloud-what-does-it-buy-me"></a>Waarom zoveel rekenkracht uitgeven in de cloud? Wat koopt het mij?

Project Acoustics biedt nauwkeurige en betrouwbare akoestische parameters, zelfs voor ultracomplexe virtuele omgevingen, rekening houdend met elk architectonisch aspect. Het biedt een soepele occlusie en obstructie en dynamische reverb variatie zonder het handmatige werk van tekenvolumes. Dit alles terwijl het resterende licht op CPU tijdens runtime.

## <a name="what-exactly-happens-during-baking"></a>Wat gebeurt er precies tijdens het "bakken"?

Een bak bestaat uit akoestische golfsimulaties van cuboïde simulatiegebieden gecentreerd op elke luisteraarsonde.

## <a name="is-my-source-content-secure"></a>Is mijn broninhoud veilig?

Project Acoustics uploadt de geometrie van de bronscène niet naar de cloud. In plaats daarvan werkt simulatie op een voxelization van uw scène, die wordt gecombineerd met sonde locatiegegevens en opgeslagen in een eigen formaat.     

## <a name="next-steps"></a>Volgende stappen
* Probeer de [voorbeeldinhoud van Project Acoustics Unity](unity-quickstart.md) of [Unreal-voorbeeldinhoud](unreal-quickstart.md)


---
title: Overzicht van de project akoestische
titlesuffix: Azure Cognitive Services
description: Project akoestische is een akoestische engine voor 3D-interactieve ervaringen, waarbij geïntegreerde Golf fysica simulatie kan worden geïntegreerd met interactieve ontwerp besturings elementen.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 51bfcc47961e870fb7fb87b26a78aea0f1564d46
ms.sourcegitcommit: 88ae4396fec7ea56011f896a7c7c79af867c90a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70390308"
---
# <a name="what-is-project-acoustics"></a>Wat is Project Acoustics?
Project akoestische is een golf akoestische engine voor 3D-interactieve ervaringen. De IT-modellen zijn wave-effecten zoals bedekking, obstructie, portaling en reverberation-effecten in complexe scènes zonder hand matige zone markeringen of CPU-intensieve raytracing. Het omvat ook Game-Engine en integratie van de audio-middleware. De "filosofie van het project" is vergelijkbaar met de statische belichting: maken gedetailleerde fysieke fysica offline om een fysieke basis lijn te bieden en gebruik een licht gewicht runtime met duidelijke ontwerp besturings elementen om te voldoen aan uw artistieke doelen voor de akoestische van uw virtuele wereld.

![Scherm afbeelding van het vistuig van War 4 met akoestische voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Golf fysica gebruiken voor interactieve akoestische
Op Ray gebaseerde akoestische methoden kunnen controleren op bedekking met behulp van één bron-naar-Luister-Ray-cast of een galm van het lokale scène volume met een paar stralen. Deze technieken kunnen echter onbetrouwbaar zijn omdat een Pebble occludes net zo groot is als een Boulder. Stralen hebben geen rekening met de manier waarop geluid rond objecten wordt gebocht, een verschijnsel bekend als Diffraction. Met de simulatie project akoestische wordt deze effecten vastgelegd met behulp van een simulatie op basis van een golf. De akoestische informatie is voorspelbaarer, nauw keurig en naadloos.

De sleutel innovatie van een project is het koppelen van echte geluids simulatie op basis van geluid met traditionele ontwerp concepten. Het zet simulatie resultaten om in traditionele para meters van de audio-DSP voor bedekking, portaling en galm. De ontwerp functie maakt gebruik van besturings elementen voor dit vertaal proces. Ga naar de [pagina onderzoek project](https://www.microsoft.com/en-us/research/project/project-triton/)voor meer informatie over de belangrijkste technologieën achter de project akoestische.

![Animatie waarin een horizon taal 2D-segment met een golf door een scène wordt weer gegeven](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Video presentatie van GDC 2019 (~ 30 minuten)
Video over project akoestische(https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Klik hier om de video af te spelen") [ ![]

## <a name="setup"></a>Instellen
De integratie van de unit van het [project](unity-integration.md) is slepen en neerzetten en bevat een invoeg toepassing voor eenheids audio-engine. Breid de besturings elementen voor unit-audio bron toe door een onderdeel C# van een project akoestische controles toe te voegen aan elk audio object.

De [Unreal-integratie](unreal-integration.md) van het project bevat een editor en Game-invoeg toepassingen voor Unreal, en een WWise mixer-invoeg toepassing. Een aangepast audio onderdeel breidt de vertrouwde WWise-functionaliteit uit binnen Unreal met de ontwerp besturings elementen van Live akoestische ontwerpen. Ontwerp besturings elementen worden ook weer gegeven in WWise op de mixer-invoeg toepassing.

## <a name="workflow"></a>Werkstroom
* **Pre-maken:** Begin met het instellen van de maken door te selecteren welke geometrie reageert op akoestische, bijvoorbeeld door de lichte schachten te negeren. Bewerk vervolgens automatische materiaal toewijzingen en selecteer vervolgens navigatie gebieden om de steek proef van de listener te begeleiden. Er is geen hand matige opmaak voor galm/Portal/room-zones.
* **Maken** Een analyse stap wordt lokaal uitgevoerd, wat voxelization en andere geometrische analyses op de scène op basis van bovenstaande selecties. De resultaten worden gevisualiseerd in de editor om de installatie van de scène te controleren. Bij maken-verzen ding worden Voxel-gegevens naar Azure verzonden en wordt er een speld geluids activum teruggeleid.
* **Gezamenlijke** Laad de asset in uw niveau en u bent klaar om te Luis teren naar akoestische op uw niveau. Ontwerp de akoestische in editor met behulp van nauw keurige besturings elementen per bron. De besturings elementen kunnen ook worden gebaseerd op het niveau van scripts.

## <a name="runtime-platforms"></a>Runtime platformen
De invoeg toepassingen voor het uitvoeren van de uitvoering van het project kunnen momenteel worden geïmplementeerd op de volgende platforms:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Editor platforms
De invoeg toepassing voor de project akoestische editor is beschikbaar voor de volgende platforms:
* Windows
* MacOS (alleen Unit)

## <a name="download"></a>Downloaden
* [Invoeg toepassing en voor beelden van de project akoestische eenheid](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Projectbudget Unreal & WWise-invoeg toepassingen en-voor beelden](https://www.microsoft.com/download/details.aspx?id=58090)
  * Voor Xbox binaire bestanden en ondersteuning kunt u contact met ons opnemen via het onderstaande aanmeldings formulier

## <a name="contact-us"></a>Contact opnemen
* [Discussie over project akoestische en probleem rapportage](https://github.com/microsoft/ProjectAcoustics/issues)
* [Meld u aan voor het ontvangen van updates voor project akoestische](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRwMoAEhDCLJNqtVIPwQN6rpUOFRZREJRR0NIQllDOTQ1U0JMNVc4OFNFSy4u)

## <a name="next-steps"></a>Volgende stappen
* Probeer een [Snelstartgids voor project akoestische voor unititeit](unity-quickstart.md) of voor [Unreal](unreal-quickstart.md)
* Verken het [geluids ontwerp filosofie van project akoestische](design-process.md)


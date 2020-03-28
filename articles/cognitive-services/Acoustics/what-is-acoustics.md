---
title: Overzicht van Project Acoustics
titlesuffix: Azure Cognitive Services
description: Project Acoustics is een akoestiek motor voor 3D interactieve ervaringen, de integratie van gebakken golf fysica simulatie met interactieve ontwerp controles.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: overview
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 65678f08399f378b8580eed79e49197dd4d84c64
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351146"
---
# <a name="what-is-project-acoustics"></a>Wat is Project Acoustics?
Project Acoustics is een golfakoestiek motor voor 3D interactieve ervaringen. Het modelleert golfeffecten zoals occlusie, obstructie, portaling en nagalmeffecten in complexe scènes zonder handmatige zone-opmaak of CPU-intensieve raytracing. Het bevat ook game-engine en audio middleware integratie. De filosofie van Project Acoustics is vergelijkbaar met statische verlichting: bak gedetailleerde fysica offline om een fysieke basislijn te bieden en gebruik een lichtgewicht runtime met expressieve ontwerpcontroles om uw artistieke doelen voor de akoestiek van uw virtuele wereld te bereiken.

![Screenshot van Gears of War 4 met akoestiek voxels](media/gears-with-voxels.jpg)

## <a name="using-wave-physics-for-interactive-acoustics"></a>Golffysica gebruiken voor interactieve akoestiek
Ray-gebaseerde akoestiek methoden kunnen controleren op occlusie met behulp van een enkele bron-naar-luisteraar ray cast, of rijden galm door het schatten van lokale scène volume met een paar stralen. Maar deze technieken kunnen onbetrouwbaar zijn omdat een kiezelsteen net zoveel occludes als een rotsblok. Stralen houden geen rekening met de manier waarop geluid buigt rond objecten, een fenomeen dat bekend staat als diffractie. De simulatie van Project Acoustics legt deze effecten vast met behulp van een golfsimulatie. De akoestiek is voorspelbaarder, nauwkeuriger en naadloos.

De belangrijkste innovatie van Project Acoustics is om echte geluidsgolfgebaseerde akoestische simulatie te koppelen aan traditionele sound design concepten. Het vertaalt simulatieresultaten naar traditionele audio DSP parameters voor occlusie, portaling en reverb. De ontwerper gebruikt besturingselementen voor dit vertaalproces. Ga voor meer informatie over de kerntechnologieën achter Project Acoustics naar de pagina van het [onderzoeksproject.](https://www.microsoft.com/en-us/research/project/project-triton/)

![Animatie die een horizontaal 2D plak van golfpropagatie door een scène toont](media/wave-simulation.gif)

## <a name="video-presentation-from-gdc-2019-30-min"></a>Videopresentatie van GDC 2019 (~30 min)
[![Project Akoestiek Video](https://img.youtube.com/vi/uY4G-GUAQIE/0.jpg)](https://www.youtube.com/watch?v=uY4G-GUAQIE "Klik om video af te spelen")

## <a name="setup"></a>Instellen
[Project Acoustics Unity integratie](unity-integration.md) is drag-and-drop en bevat een Unity audio engine plugin. Vergroot de unity-audiobronbesturingselementen door een component van Project Acoustics C# aan elk audioobject te koppelen.

[Project Acoustics Unreal integratie](unreal-integration.md) omvat editor en game plugins voor Unreal, en een Wwise mixer plugin. Een aangepaste audiocomponent breidt de vertrouwde Wwise-functionaliteit binnen Unreal uit met live akoestiekontwerpregelaars. Ontwerpbesturingselementen worden ook in Wwise op de mixerplug-in weergegeven.

## <a name="workflow"></a>Werkstroom
* **Pre-bake:** Begin met het instellen van de bak door te selecteren welke geometrie reageert op de akoestiek, bijvoorbeeld door lichtschachten te negeren. Bewerk vervolgens automatische materiaaltoewijzingen en selecteer navigatiegebieden om listenersampling te begeleiden. Er is geen handmatige opmaak voor reverb/portal/room zones.
* **Bak:** Een analysestap wordt lokaal uitgevoerd, die voxelization en andere geometrische analyse op de scène doet die op selecties hierboven wordt gebaseerd. Resultaten worden gevisualiseerd in de editor om scène setup te verifiëren. Bij het indienen van bakken worden voxel-gegevens naar Azure verzonden en krijgt u een akoestiekspelasset terug.
* **Runtime:** Laad de asset in je level en je bent klaar om te luisteren naar de akoestiek in je level. Ontwerp de akoestiek live in editor met behulp van gedetailleerde besturingselementen per bron. De besturingselementen kunnen ook worden aangestuurd vanuit level scripting.

## <a name="runtime-platforms"></a>Runtime-platforms
De Runtime-plug-ins project akoestiek kunnen momenteel worden geïmplementeerd op de volgende platforms:
* Windows
* MacOS
* Android
* Xbox One

## <a name="editor-platforms"></a>Editor platforms
De Plug-in project akoestiek is beschikbaar voor de volgende platforms:
* Windows
* MacOS (alleen Unity)

## <a name="download"></a>Download
* [Project Acoustics Unity plugin en samples](https://www.microsoft.com/en-us/download/details.aspx?id=57346)
* [Project Acoustics Unreal & Wwise plugins en samples](https://www.microsoft.com/download/details.aspx?id=58090)
  * Voor Xbox binaries en andere ondersteuning, neem dan contact met ons op via het [forum](https://github.com/microsoft/ProjectAcoustics/issues).

## <a name="contact-us"></a>Contact opnemen
* [Project Acoustics discussie en rapportage van uitgifte](https://github.com/microsoft/ProjectAcoustics/issues)

## <a name="next-steps"></a>Volgende stappen
* Probeer een [Project Acoustics quickstart voor Unity](unity-quickstart.md) of voor [Unreal](unreal-quickstart.md)
* Ontdek de [sound design filosofie van Project Acoustics](design-process.md)


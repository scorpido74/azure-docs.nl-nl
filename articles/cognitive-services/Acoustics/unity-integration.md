---
title: Integratie en implementatie van unit-project akoestische eenheid
titlesuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u de invoeg toepassing project akoestische unit-eenheid integreert in uw Unity-project.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242997"
---
# <a name="project-acoustics-unity-integration"></a>Integratie van de eenheid project akoestische unit
In dit artikel wordt beschreven hoe u de invoeg toepassing project akoestische unit-eenheid integreert in uw Unity-project.

Software vereisten:
* [Unity 2018.2 +](https://unity3d.com) voor Windows
* [Eenheids pakket voor project akoestische](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>De invoeg toepassing importeren
1. Importeer de akoestische UnityPackage in uw project. 
 Ga in Unity naar **Assets** > **import pakket** > **aangepast pakket**.

    ![Het unit import package-menu](media/import-package.png)  

1. Kies **ProjectAcoustics. unitypackage**.

1. Selecteer de knop **importeren** om het Unity-pakket in uw project te integreren.

    ![Het dialoog venster unit import package importeren](media/import-dialog.png)  

Als u de invoeg toepassing in een bestaand project importeert, heeft het project mogelijk al een *mcs. RSP* -bestand in de hoofdmap van het project. In dit bestand worden de opties C# voor de compiler opgegeven. Voeg de inhoud van dat bestand samen met het bestand mcs. RSP dat wordt geleverd met de geluids invoeg toepassing voor het project.

## <a name="enable-the-plug-in"></a>De invoeg toepassing inschakelen
Voor het maken-gedeelte van de akoestische Toolkit is .NET 4 vereist. *x* Scripting runtime-versie. Met package import worden de instellingen van uw Unity-speler bijgewerkt. De eenheid moet opnieuw worden opgestart om deze instelling van kracht te laten worden.

![Het deel venster instellingen voor Unity Player](media/player-settings.png)

![Het deel venster instellingen voor Unity Player met .NET 4,5 geselecteerd](media/net45.png)

## <a name="set-up-audio-dsp"></a>Audio-DSP instellen
De geluids fragmenten van het project omvatten audio runtime DSP die kan worden geïntegreerd in het spatializer Framework van unit-audio-engine. Het bevat zowel HRTF als gesleepte spatialization. Als u de project akoestische DSP wilt inschakelen, gaat u naar **bewerken** > **project instellingen** > **Audio** om de instellingen voor unit-audio te openen. Selecteer **akoestische projects** als de **Spatializer-invoeg toepassing** voor uw project. Zorg ervoor dat de **DSP-buffer grootte** is ingesteld op de *beste prestaties*.

![Het menu eenheids project instellingen](media/project-settings.png)  

![Het Spatializer-instellingen paneel met het project akoestische Spatializer geselecteerd](media/choose-spatializer.png)

Open vervolgens de audio mixer (**venster** > **audio mixer**). Zorg ervoor dat u ten minste één mixer hebt, met één groep. Als u er nog geen hebt, selecteert u de **+-** knop rechts van **mixers**. Klik met de rechter muisknop op de onderkant van de kanaal strook in de sectie effecten en voeg het effect **micro soft akoestische** conkrachties toe. Er wordt slechts één project akoestische mixer tegelijk ondersteund.

![De eenheid audio mixer die als host fungeert voor de project akoestische mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Akoestische geluids bronnen inschakelen
Een audio bron maken: Schakel het selectie vakje **Spatialize** in onder in het deel venster AudioSource Inspector. Zorg ervoor dat **ruimtelijke Blend** is ingesteld op Full *3D*.  

![Het deel venster unit-audio bron](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akoestische ontwerp inschakelen
Koppel het *AcousticsAdjust* -script aan een geluids bron in uw scène om aanvullende para meters voor de bron ontwerp in te scha kelen: Selecteer **onderdeel toevoegen** en kies **scripts** > **akoestische geluids aanpassingen**.

![Het unit AcousticsAdjust-script](media/acoustics-adjust.png)

## <a name="next-steps"></a>Volgende stappen
* [Maken uw scène met project akoestische voor Unity](unity-baking.md).
* [Maak een Azure batch-account](create-azure-account.md) om uw scène in de cloud te maken.
* Verken het ontwerp proces voor het ontwikkelen van de [project akoestische eenheid](unity-workflow.md).

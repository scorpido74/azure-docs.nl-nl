---
title: Project Acoustics Unity integratie en implementatie
titlesuffix: Azure Cognitive Services
description: In dit artikel wordt beschreven hoe u de Plug-in Project Acoustics Unity integreren in uw Unity-project.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242997"
---
# <a name="project-acoustics-unity-integration"></a>Project Acoustics Unity integratie
In dit artikel wordt beschreven hoe u de Plug-in Project Acoustics Unity integreren in uw Unity-project.

Softwarevereisten:
* [Unity 2018.2+](https://unity3d.com) voor Windows
* [Project Acoustics Unity pakket](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>De plug-in importeren
1. Importeer de akoestiek UnityPackage in uw project. 
 Ga in Unity naar het aangepaste pakket **voor het** > **invoerpakket** > **voor**assets .

    ![Het menu Eenheidspakket importeren](media/import-package.png)  

1. Kies **ProjectAcoustics.unitypackage**.

1. Selecteer de knop **Importeren** om het Unity-pakket in uw project te integreren.

    ![Het dialoogvenster Pakket voor eenheid importeren](media/import-dialog.png)  

Als u de plug-in importeert in een bestaand project, heeft uw project mogelijk al een *mcs.rsp-bestand* in de projecthoofd. Met dit bestand worden opties aan de C#-compiler opgegeven. Voeg de inhoud van dat bestand samen met het mcs.rsp-bestand dat wordt geleverd met de Project Acoustics-plug-in.

## <a name="enable-the-plug-in"></a>De plug-in inschakelen
Het bakgedeelte van de akoestiektoolkit vereist de .NET 4. *x* runtime-versie scripting. Pakketimport werkt uw Unity-spelerinstellingen bij. Start Unity opnieuw om deze instelling van kracht te laten worden.

![Het deelvenster Instellingen voor eenheidsspelers](media/player-settings.png)

![Het deelvenster Instellingen voor eenheidsspelers met .NET 4.5 geselecteerd](media/net45.png)

## <a name="set-up-audio-dsp"></a>Audio-DSP instellen
Project Acoustics bevat audio runtime DSP die integreert in het Unity audio engine spatializer framework. Het omvat zowel HRTF-gebaseerde en panning spatialization. Als u de Project Acoustics DSP wilt inschakelen, gaat u naar**Projectinstellingen** > **Audio bewerken** **Edit** > om de audio-instellingen van Unity te openen. Selecteer **Project Acoustics** als de **Spatializer Plugin** voor uw project. Zorg ervoor dat **de DSP-buffergrootte** is ingesteld op *De beste prestaties*.

![Het menu Eenheidsprojectinstellingen](media/project-settings.png)  

![Het Unity Spatializer settings panel met Project Acoustics spatializer geselecteerd](media/choose-spatializer.png)

Open vervolgens de audiomixer **(Window** > **Audio Mixer).** Zorg ervoor dat u ten minste één mixer hebt, met één groep. Als u er geen hebt, **+** selecteert u de knop rechts van **Mixers**. Klik met de rechtermuisknop op de onderkant van de kanaalstrook in de sectie effecten en voeg het effect **Microsoft Acoustics Mixer** toe. Er wordt slechts één Project Acoustics mixer tegelijk ondersteund.

![De Unity Audio Mixer host de Project Acoustics mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Akoestiek inschakelen op geluidsbronnen
Een audiobron maken: schakel het selectievakje **Ruimtelijk maken** onder aan het deelvenster Controle-uitschakeling van AudioSource in. Zorg ervoor dat **de ruimtelijke overvloeiing** is ingesteld op volledige *3D.*  

![Het deelvenster Unity Audio Bron](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Akoestisch ontwerp inschakelen
Voeg het script *AcousticsAdjust* toe aan een geluidsbron in uw scène om extra parameters voor bronontwerp in te schakelen: Selecteer **Component toevoegen** en kies **Scripts** > **Akoestiek aanpassen**.

![Het script Unity AcousticsAdjust](media/acoustics-adjust.png)

## <a name="next-steps"></a>Volgende stappen
* [Bak je scène met Project Acoustics for Unity.](unity-baking.md)
* [Maak een Azure Batch-account](create-azure-account.md) om uw scène in de cloud te bakken.
* Ontdek het [Project Acoustics Unity ontwerpproces.](unity-workflow.md)

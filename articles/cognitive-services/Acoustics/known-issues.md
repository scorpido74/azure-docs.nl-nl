---
title: Project Acoustics plug-in bekende problemen
titlesuffix: Azure Cognitive Services
description: Mogelijk ervaart u de volgende bekende problemen in Project Acoustics.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243046"
---
# <a name="project-acoustics-known-issues"></a>Project Acoustics bekende problemen
In dit artikel worden problemen beschreven die u ondervinden wanneer u Project Acoustics gebruikt.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akoestische parameters gaan verloren wanneer u de naam van een scène wijzigt

Als u de naam van een scène wijzigt, worden alle akoestische parameters die tot die scène behoren, niet automatisch naar de nieuwe scène overgezet. Maar ze bestaan nog steeds in het oude activabestand. Zoek naar het bestand *[SceneName]_AcousticParameters.asset* in de *editormap* naast uw scènebestand. Wijzig de naam van het bestand naar de nieuwe scènenaam.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Deploy-to-Android bug van sommige Unity-versies

Sommige versies van Unity hebben een [bug](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) in de manier waarop ze audioplug-ins implementeren op Android. Zorg ervoor dat u geen versie gebruikt die door deze bug wordt beïnvloed.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"Kon niet vinden metadata bestand System.Security.dll" fout

Controleer of de **Runtime-versie scripting** in de **instellingen voor player** *.NET 4.x Equivalent*is en start Unity opnieuw.

## <a name="authentication-problems-when-connecting-to-azure"></a>Verificatieproblemen bij het maken van verbinding met Azure

Controleer of:
- U hebt de juiste referenties voor uw Azure-account gebruikt.
- Uw account ondersteunt het type knooppunt dat u in de bak hebt aangevraagd.
- Uw systeemklok is correct ingesteld.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Op het tabblad Bakken wordt nog steeds 'verwijderen' weergegeven nadat u hebt geannuleerd
Project Acoustics ruimt alle Azure-resources op voor een taak na succesvolle voltooiing of annulering. Dit proces kan tot 5 minuten duren.

## <a name="next-steps"></a>Volgende stappen
* Probeer de [unity-](unity-quickstart.md) of [Unreal-voorbeeldinhoud.](unreal-quickstart.md)

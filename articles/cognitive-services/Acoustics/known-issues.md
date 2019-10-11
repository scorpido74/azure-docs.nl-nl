---
title: Bekende problemen met invoeg toepassingen voor project akoestische
titlesuffix: Azure Cognitive Services
description: U kunt de volgende bekende problemen ondervinden in Project akoestische.
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
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243046"
---
# <a name="project-acoustics-known-issues"></a>Bekende problemen in Project akoestische
In dit artikel worden problemen beschreven die zich kunnen voordoen wanneer u project akoestische gebruikt.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Akoestische para meters gaan verloren wanneer u de naam van een scène wijzigt

Als u de naam van een scène wijzigt, worden alle akoestische para meters die deel uitmaken van die scène, niet automatisch overgedragen naar de nieuwe scène. Maar ze zijn nog steeds aanwezig in het oude activa bestand. Zoek naar het bestand *[scènenaam] _AcousticParameters. Asset* in de map *Editor* naast uw scène bestand. Wijzig de naam van het bestand zodat dit overeenkomt met die van de nieuwe scène.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Implementatie van een Android-fout vanuit sommige eenheids versies

Sommige versies van unit eenheid hebben een [fout](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) in de manier waarop ze audio-invoeg toepassingen implementeren op Android. Zorg ervoor dat u geen versie gebruikt die door deze bug wordt beïnvloed.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Fout bericht ' kan het bestands systeem voor meta gegevens niet vinden. Security. dll '

Zorg ervoor dat de **runtime-versie van scripting** in de instellingen van de **speler** *.net 4. x equivalent*is en start eenheid opnieuw op.

## <a name="authentication-problems-when-connecting-to-azure"></a>Verificatie problemen bij het maken van verbinding met Azure

Controleer het volgende:
- U hebt de juiste referenties voor uw Azure-account gebruikt.
- Uw account ondersteunt het type knoop punt dat u hebt aangevraagd in de maken.
- De systeem klok is ingesteld op de juiste manier.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Op het tabblad maken wordt nog steeds ' verwijderen ' weer gegeven nadat u hebt geannuleerd
Met de geluids bestanden van een project worden alle Azure-resources voor een taak opgeschoond nadat het volt ooien of annuleren is voltooid. Dit proces kan Maxi maal vijf minuten duren.

## <a name="next-steps"></a>Volgende stappen
* Probeer de voorbeeld inhoud van [Unity](unity-quickstart.md) of [Unreal](unreal-quickstart.md) .

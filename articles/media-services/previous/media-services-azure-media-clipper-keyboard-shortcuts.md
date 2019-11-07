---
title: Instellingen voor Azure media Clipper-toetsen bord configureren | Microsoft Docs
description: Stappen voor het instellen van Configureer bare sneltoetsen voor Azure media Clipper
services: media-services
keywords: clip; subclip; code ring; media
author: Juliako
manager: femila
ms.author: juliako
ms.date: 03/14/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 013ca640d30baceeb41e9cb38fab69020b3c50dd
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685069"
---
# <a name="configure-azure-media-clipper-keyboard-shortcuts"></a>Sneltoetsen voor Azure media Clipper configureren 

Azure media Clipper ondersteunt het aanpassen van de standaard snel toetsen door een optionele `keymap` JSON-para meter op te geven.

De volgende voor beeld-JSON illustreert de standaard snel toetsen. U kunt deze instellingen aanpassen door de sleutel velden te wijzigen en in de para meter door te geven bij het initialiseren van de Clipper.

```json
{
    "AZURE_MEDIA_SERVICE_SUBCLIPPER": {
        "UNDO": {
            "windows": "ctrl+z",
            "osx": "command+z"
        },
        "REDO": {
            "windows": "ctrl+shift+z",
            "osx": "command+shift+z"
        },
        "MARK_IN": {
            "windows": "ctrl+i",
            "osx": "command+i"
        },
        "MARK_OUT": {
            "windows": "ctrl+o",
            "osx": "command+o"
        },
        "MARK_IN_INPUT": {
            "windows": "ctrl+alt+i",
            "osx": "command+alt+i"
        },
        "MARK_OUT_INPUT": {
            "windows": "ctrl+alt+o",
            "osx": "command+alt+o"
        },
        "PREV_FRAME": {
            "windows": "ctrl+left",
            "osx": "command+left"
        },
        "NEXT_FRAME": {
            "windows": "ctrl+right",
            "osx": "command+right"
        },
        "SUBMIT_JOB": {
            "windows": "ctrl+s",
            "osx": "command+s"
        },
        "PLAY": {
            "windows": "ctrl+alt+p",
            "osx": "command+alt+p"
        },
        "PLAY_PREVIEW": {
            "windows": "ctrl+p",
            "osx": "command+p"
        },
        "MUTE": {
            "windows": "ctrl+m",
            "osx": "command+m"
        },
        "OUTPUT_TYPE": {
            "windows": "ctrl+y",
            "osx": "command+y"
        },
        "CLEAN_JOB": {
            "windows": "ctrl+alt+d",
            "osx": "command+alt+backspace"
        },
        "OPEN_ADVANCED_SETTINGS": {
            "windows": "ctrl+.",
            "osx": "command+."
        },
        "CLOSE_MODAL": "escape",
        "REMOVE_ASSET": {
            "windows": "ctrl+d",
            "osx": "command+backspace"
        },
        "ZOOM_LEFT_LESS": {
            "windows": "ctrl+shift+[",
            "osx": "command+shift+["
        },
        "ZOOM_LEFT_MORE": {
            "windows": "ctrl+[",
            "osx": "command+["
        },
        "ZOOM_RIGHT_LESS": {
            "windows": "ctrl+shift+]",
            "osx": "command+shift+]"
        },
        "ZOOM_RIGHT_MORE": {
           "windows": "ctrl+]",
            "osx": "command+]"
        }
    }
}
```

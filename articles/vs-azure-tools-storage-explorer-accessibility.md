---
title: Toegankelijkheid azure storage explorer (voorbeeld) | Microsoft Documenten
description: Toegankelijkheid azure Storage Explorer (voorbeeld)
services: storage
documentationcenter: na
author: MrayermannMSFT
manager: jinglouMSFT
editor: ''
ms.assetid: 1ed0f096-494d-49c4-ab71-f4164ee19ec8
ms.service: storage
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2018
ms.author: marayerm
ms.openlocfilehash: aa42d6b898a3cc474575f6c8dae65ff420fc8e30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60457247"
---
# <a name="storage-explorer-accessibility"></a>Toegankelijkheid van Storage Explorer

## <a name="screen-readers"></a>Schermlezers

Storage Explorer ondersteunt het gebruik van een schermlezer op Windows en Mac. Voor elk platform worden de volgende schermlezers aanbevolen:

Platform | Schermlezer
---------|--------------
Windows  | NVDA
Mac      | Voice-over
Linux    | (schermlezers worden niet ondersteund op Linux)

Als u een toegankelijkheidsprobleem tegenkomt wanneer u Storage Explorer gebruikt, opent u [een probleem op GitHub.](https://github.com/Microsoft/AzureStorageExplorer/issues)

## <a name="zoom"></a>Zoom

U de tekst in Storage Explorer groter maken via inzoomen. Als u wilt inzoomen, klikt u op **Inzoomen in** het Menu Help. U ook het Menu Help gebruiken om uit te zoomen en het zoomniveau terug te zetten naar het standaardniveau.

![Zoomopties in het helpmenu][0]

De zoominstelling vergroot de grootte van de meeste UI-elementen. Het wordt aanbevolen om ook grote tekst- en zoominstellingen voor uw besturingssysteem in te schakelen om ervoor te zorgen dat alle ui-elementen correct worden geschaald.

## <a name="high-contrast-themes"></a>Thema's met hoog contrast

Storage Explorer heeft twee thema's met een hoog contrast: **High Contrast Light** en High Contrast **Dark.** U uw thema wijzigen door in te kiezen in het menu Help-> thema's.

![Submenu Thema's][1]

De thema-instelling wijzigt de kleur van de meeste ui-elementen. Het wordt aanbevolen om ook het bijpassende thema met een hoog contrast van uw besturingssysteem in te schakelen om ervoor te zorgen dat alle UI-elementen goed zijn gekleurd.

## <a name="shortcut-keys"></a>Sneltoetsen

### <a name="window-commands"></a>Vensteropdrachten

Opdracht       | Sneltoets
--------------|--------------------
Nieuw venster    | **Control+Shift+N**
Editor sluiten  | **Control+F4**
Afsluiten          | **Control+Shift+W**

### <a name="navigation-commands"></a>Navigatieopdrachten

Opdracht                | Sneltoets
-----------------------|----------------------
Deelvenster Focus volgende       | **F6**
Vorig deelvenster Focus   | **Shift+F6**
Verkenner               | **Control+Shift+E**
Accountbeheer     | **Control+Shift+A**
Zijbalk inschakelen        | **Control+B**
Activiteitenlogboek           | **Control+Shift+L**
Acties en eigenschappen | **Control+Shift+P**
Huidige editor         | **Besturing+Startpagina**
Volgende editor            | **Control+Page Down**
Vorige editor        | **Control+Page Up**

### <a name="zoom-commands"></a>Zoomopdrachten

Opdracht  | Sneltoets
---------|------------------
Inzoomen  | **Control+=**
Uitzoomen | **Control+-**

### <a name="blob-and-file-share-editor-commands"></a>Editoropdrachten voor blobs en bestanden

Opdracht | Sneltoets
--------|--------------------
Terug    | **Alt+Pijl-links**
voren | **Alt+Pijl-rechts**
Omhoog      | **Alt+Pijl-omhoog**

### <a name="editor-commands"></a>Editoropdrachten

Opdracht | Sneltoets
--------|------------------
Exemplaar    | **Besturing+C**
Knippen     | **Control+X**
Plakken   | **Control+V**
Vernieuwen  | **Control+R**

### <a name="other-commands"></a>Andere opdrachten

Opdracht                | Sneltoets
-----------------------|------------------
Hulpprogramma's voor ontwikkelaars in- of uitschakelen | **F12**
Opnieuw laden                 | **Alt+Control+R**

[0]: ./media/vs-azure-tools-storage-explorer-accessibility/Zoom.png
[1]: ./media/vs-azure-tools-storage-explorer-accessibility/HighContrast.png

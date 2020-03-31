---
title: De Azure Cloud Shell-editor gebruiken | Microsoft Documenten
description: Overzicht van het gebruik van de Azure Cloud Shell-editor.
services: azure
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: damaerte
ms.openlocfilehash: 7f597bb5cba1a12bdb93325fe2b877ffc644e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60199205"
---
# <a name="using-the-azure-cloud-shell-editor"></a>De Azure Cloud Shell-editor gebruiken

Azure Cloud Shell bevat een geïntegreerde bestandseditor die is gebouwd vanuit de open-source [Monaco Editor.](https://github.com/Microsoft/monaco-editor) De Cloud Shell-editor ondersteunt functies, zoals het markeren van talen, het opdrachtpalet en een bestandsverkenner.

![Cloud Shell-editor](media/using-cloud-shell-editor/open-editor.png)

## <a name="opening-the-editor"></a>De editor openen

Als u eenvoudig bestanden wilt maken en bewerken, start u de editor door `code .` uit te voeren in de Cloud Shell-terminal. Met deze actie wordt de editor geopend met uw actieve werkmapset in de terminal.

Als u een bestand rechtstreeks wilt openen voor snelle bewerking, voert u `code <filename>` uit om de editor te openen zonder de bestandsverkenner.

Als u de editor wilt openen via de gebruikersinterfaceknop, klikt u op het editor-pictogram `{}` op de werkbalk. Hiermee opent u de editor en maakt u van de `/home/<user>`-map de standaardmap voor de bestandsverkenner.

## <a name="closing-the-editor"></a>De editor sluiten

Als u de editor `...` wilt sluiten, opent u het `Close editor`actiedeelvenster rechtsboven in de editor en selecteert u .

![Sluit editor](media/using-cloud-shell-editor/close-editor.png)

## <a name="command-palette"></a>Opdrachtpalet

Als u het opdrachtpalet `F1` wilt starten, gebruikt u de toets wanneer de focus op de editor is ingesteld. Het openen van het opdrachtpalet kan ook via het actiepaneel.

![Cmd-palet](media/using-cloud-shell-editor/cmd-palette.png)

## <a name="contributing-to-the-monaco-editor"></a>Bijdragen aan de Redacteur van Monaco

Ondersteuning voor taalhoogtepunten in de Cloud Shell-editor wordt ondersteund door upstream-functionaliteit in het gebruik van monarchsyntaxisdefinities door de Editor van [Monaco.](https://github.com/Microsoft/monaco-editor) Om te leren hoe je bijdragen te leveren, lees de [Monaco bijdrage gids](https://github.com/Microsoft/monaco-editor/blob/master/CONTRIBUTING.md).

## <a name="next-steps"></a>Volgende stappen
[Probeer de quickstart voor Bash in Cloud Shell](quickstart.md)
[Bekijk de volledige lijst met geïntegreerde Cloud Shell-tools](features.md)
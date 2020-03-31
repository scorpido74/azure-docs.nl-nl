---
title: InfoBox-element UI
description: Beschrijft het Microsoft.Common.InfoBox UI-element voor Azure-portal. Tekst of waarschuwingen toevoegen bij het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: tomfitz
ms.openlocfilehash: 6d1e4a84904ef7022d9ce85803bf10285bf0b8ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652474"
---
# <a name="microsoftcommoninfobox-ui-element"></a>Microsoft.Common.InfoBox-element UI

Een besturingselement dat een informatievak toevoegt. Het vak bevat belangrijke tekst of waarschuwingen die gebruikers inzicht geven in de waarden die ze opgeven. Het kan ook een link naar een URI voor meer informatie.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.InfoBox](./media/managed-application-elements/microsoft.common.infobox.png)


## <a name="schema"></a>Schema

```json
{
  "name": "text1",
  "type": "Microsoft.Common.InfoBox",
  "visible": true,
  "options": {
    "icon": "None",
    "text": "Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo.",
    "uri": "https://www.microsoft.com"
  }
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
"Nullam eros mi, mollis in sollicitudin non, tincidunt sed enim. Sed et felis metus, rhoncus ornare nibh. Ut at magna leo."
```

## <a name="remarks"></a>Opmerkingen

* Voor `icon`, gebruik **Geen**, **Info**, **Waarschuwing**of **Fout**.
* De `uri` accommodatie is optioneel.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .

---
title: Sectie-ui-element
description: Beschrijft het Microsoft.Common.Section UI-element voor Azure-portal. Gebruiken om elementen in de portal te groeperen voor het implementeren van beheerde toepassingen.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 999a7386ee7c5b3ef76f87280cc2d0cd45f143cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652253"
---
# <a name="microsoftcommonsection-ui-element"></a>Microsoft.Common.Section UI-element

Een besturingselement dat een of meer elementen onder een kop groepeert.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.Section](./media/managed-application-elements/microsoft.common.section.png)

## <a name="schema"></a>Schema

```json
{
  "name": "section1",
  "type": "Microsoft.Common.Section",
  "label": "Example section",
  "elements": [
    {
      "name": "text1",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 1"
    },
    {
      "name": "text2",
      "type": "Microsoft.Common.TextBox",
      "label": "Example text box 2"
    }
  ],
  "visible": true
}
```

## <a name="remarks"></a>Opmerkingen

- `elements`moet ten minste één element hebben en `Microsoft.Common.Section`alle elementtypen kunnen hebben, behalve .
- Dit element ondersteunt de `toolTip` eigenschap niet.

## <a name="sample-output"></a>Voorbeelduitvoer
Als u toegang wilt `elements`krijgen tot de uitvoerwaarden van elementen in , gebruikt u de functies [basics()](create-uidefinition-functions.md#basics) of [steps()](create-uidefinition-functions.md#steps) en puntnotatie:

```json
steps('configuration').section1.text1
```

Elementen van `Microsoft.Common.Section` het type hebben zelf geen uitvoerwaarden.

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .

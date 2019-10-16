---
title: Gebruikers interface-element van Azure sectie | Microsoft Docs
description: Hierin wordt het gebruikers interface-element micro soft. common. Section voor Azure Portal beschreven. Gebruiken om elementen in de portal te groeperen voor het implementeren van beheerde toepassingen.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: a48c89785e0a448609026aab53364f6cf704e948
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331659"
---
# <a name="microsoftcommonsection-ui-element"></a>Gebruikers interface-element van micro soft. common. Section
Een besturings element dat een of meer elementen onder een kop groepeert.

## <a name="ui-sample"></a>UI-voor beeld
![Micro soft. common. Section](./media/managed-application-elements/microsoft.common.section.png)

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
- `elements` moet ten minste één element hebben en kan alle element typen hebben, met uitzonde ring van `Microsoft.Common.Section`.
- Dit element biedt geen ondersteuning voor de eigenschap `toolTip`.

## <a name="sample-output"></a>Voorbeelduitvoer
Als u de uitvoer waarden van elementen in `elements` wilt openen, gebruikt u de functies [()](create-uidefinition-functions.md#basics) of [stappen (](create-uidefinition-functions.md#steps) ) en punt notatie:

```json
steps('configuration').section1.text1
```

Elementen van het type `Microsoft.Common.Section` hebben geen uitvoer waarden zelf.

## <a name="next-steps"></a>Volgende stappen
* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

---
title: TagsByResource-gebruikersinterface-element
description: Beschrijft het Microsoft.Common.TagsByResource-gebruikersinterfaceelement voor Azure-portal. Tags toepassen op een resource tijdens de implementatie.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652201"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource-gebruikersinterface-element

Een besturingselement voor het koppelen van [tags](../management/tag-resources.md) aan de resources in een implementatie.

## <a name="ui-sample"></a>UI-voorbeeld

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Opmerkingen

- Er moet ten `resources` minste één item in de array worden opgegeven.
- Elk element `resources` in moet een volledig gekwalificeerd resourcetype zijn. Deze elementen worden weergegeven in de **vervolgkeuzelijst Resource** en zijn door de gebruiker kan worden gebruikt.
- De uitvoer van het besturingselement is opgemaakt voor eenvoudige toewijzing van tagwaarden in een Azure Resource Manager-sjabloon. Als u de uitvoer van het besturingselement in een sjabloon wilt ontvangen, neemt u een parameter op in uw sjabloon, zoals in het volgende voorbeeld wordt weergegeven:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Wijs de eigenschap tags toe aan de parameterwaarde voor dat resourcetype voor elke resource die kan worden getagd:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Gebruik de functie [als](../templates/template-functions-logical.md#if) bij het openen van de parameter tagsByResource. Hiermee u een leeg object toewijzen wanneer er geen tags aan het opgegeven resourcetype zijn toegewezen.

## <a name="next-steps"></a>Volgende stappen

- Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
- Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .

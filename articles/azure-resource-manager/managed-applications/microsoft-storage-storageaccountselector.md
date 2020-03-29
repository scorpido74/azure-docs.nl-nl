---
title: Element StorageAccountSelector UI
description: Beschrijft het Microsoft.Storage.StorageAccountSelector-gebruikersinterface-element voor Azure-portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: 4fcaf0e5842ce8a65175d2fc1dfa2483a1203b2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75651889"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Microsoft.Storage.StorageAccountSelector-gebruikersinterface-element

Een besturingselement voor het selecteren van een nieuw of bestaand opslagaccount.

## <a name="ui-sample"></a>UI-voorbeeld

Het besturingselement toont de standaardwaarde.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Met het besturingselement kan de gebruiker een nieuw opslagaccount maken of een bestaand opslagaccount selecteren.

![Microsoft.Storage.StorageAccountSelector nieuw](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

## <a name="schema"></a>Schema

```json
{
  "name": "element1",
  "type": "Microsoft.Storage.StorageAccountSelector",
  "label": "Storage account",
  "toolTip": "",
  "defaultValue": {
    "name": "storageaccount01",
    "type": "Premium_LRS"
  },
  "constraints": {
    "allowedTypes": [],
    "excludedTypes": []
  },
  "options": {
    "hideExisting": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Voorbeelduitvoer

```json
{
  "name": "storageaccount01",
  "resourceGroup": "rg01",
  "type": "Premium_LRS",
  "newOrExisting": "new"
}
```

## <a name="remarks"></a>Opmerkingen

- Indien opgegeven, `defaultValue.name` wordt automatisch gevalideerd voor uniciteit. Als de naam van het opslagaccount niet uniek is, moet de gebruiker een andere naam opgeven of een bestaand opslagaccount kiezen.
- De standaardwaarde `defaultValue.type` voor is **Premium_LRS**.
- Elk type dat `constraints.allowedTypes` niet is opgegeven, wordt `constraints.excludedTypes` verborgen en elk type dat niet is opgegeven, wordt weergegeven. `constraints.allowedTypes`en `constraints.excludedTypes` zijn beide optioneel, maar kunnen niet tegelijkertijd worden gebruikt.
- Als `options.hideExisting` **dit waar**is, kan de gebruiker geen bestaand opslagaccount kiezen. De standaardwaarde is **onwaar**.

## <a name="next-steps"></a>Volgende stappen
* Zie Aan de slag met [CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van ui-definities.
* Zie [Elementen van CreateUiDefinition](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in ui-elementen .

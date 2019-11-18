---
title: Gebruikers interface-element van Azure StorageAccountSelector | Microsoft Docs
description: Hierin wordt het element micro soft. storage. StorageAccountSelector UI voor Azure Portal beschreven.
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
ms.date: 06/28/2018
ms.author: tomfitz
ms.openlocfilehash: e1f96b42e58bcb09cfc2836c993626a889669fc0
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151458"
---
# <a name="microsoftstoragestorageaccountselector-ui-element"></a>Micro soft. storage. StorageAccountSelector UI-element

Een besturings element voor het selecteren van een nieuw of bestaand opslag account.

## <a name="ui-sample"></a>UI-voor beeld

In het besturings element wordt de standaard waarde weer gegeven.

![Microsoft.Storage.StorageAccountSelector](./media/managed-application-elements/microsoft.storage.storageaccountselector.png)

Met het besturings element kan de gebruiker een nieuw opslag account maken of een bestaand opslag account selecteren.

![Micro soft. storage. StorageAccountSelector nieuw](./media/managed-application-elements/microsoft.storage.storageaccountselector-new.png)

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

- Indien opgegeven, wordt `defaultValue.name` automatisch gevalideerd voor uniekheid. Als de naam van het opslag account niet uniek is, moet de gebruiker een andere naam opgeven of een bestaand opslag account kiezen.
- De standaard waarde voor `defaultValue.type` is **Premium_LRS**.
- Elk type dat niet in `constraints.allowedTypes` is opgegeven, is verborgen en elk type dat niet in `constraints.excludedTypes` is opgegeven, wordt weer gegeven. `constraints.allowedTypes` en `constraints.excludedTypes` zijn beide optioneel, maar kunnen niet tegelijkertijd worden gebruikt.
- Als `options.hideExisting` is ingesteld op **True**, kan de gebruiker geen bestaand opslag account kiezen. De standaardwaarde is **false**.

## <a name="next-steps"></a>Volgende stappen
* Zie aan de slag [met CreateUiDefinition](create-uidefinition-overview.md)voor een inleiding tot het maken van UI-definities.
* Zie [CreateUiDefinition-elementen](create-uidefinition-elements.md)voor een beschrijving van algemene eigenschappen in UI-elementen.

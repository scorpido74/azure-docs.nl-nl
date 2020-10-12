---
title: De sjabloon voor een virtuele Azure-machine downloaden
description: Down load de sjabloon voor een virtuele machine met behulp van de portal of Power shell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 5b7e50ebe6f09de2555af03a47641ef6ca92e92a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288291"
---
# <a name="download-the-template-for-a-vm"></a>De sjabloon voor een VM downloaden
Wanneer u een virtuele machine in azure maakt met behulp van de portal of Power shell, wordt automatisch een resource manager-sjabloon voor u gemaakt. U kunt deze sjabloon gebruiken om snel een implementatie te dupliceren. De sjabloon bevat informatie over alle resources in een resource groep. Voor een virtuele machine betekent dit dat de sjabloon alles bevat dat is gemaakt ter ondersteuning van de VM in die resource groep, met inbegrip van de netwerk resources.

## <a name="download-the-template-using-the-portal"></a>De sjabloon downloaden met behulp van de portal
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in het linkermenu **virtual machines**.
3. Selecteer de virtuele machine in de lijst.
4. Selecteer **Sjabloon exporteren**.
5. Selecteer **downloaden** in het menu bovenaan en sla het zip-bestand op uw lokale computer op.
6. Open het zip-bestand en pak de bestanden uit naar een map. Het zip-bestand bevat:
   
   * parameters.jsop
   * template.jsop

De template.jsin het bestand is de sjabloon.

## <a name="download-the-template-using-powershell"></a>De sjabloon downloaden met behulp van Power shell
U kunt ook het JSON-sjabloon bestand downloaden met de cmdlet [export-AzResourceGroup](/powershell/module/az.resources/export-azresourcegroup) . U kunt de `-path` para meter gebruiken om de bestands naam en het pad voor het JSON-bestand op te geven. In dit voor beeld ziet u hoe u de sjabloon voor de resource groep met de naam **myResourceGroup** kunt downloaden naar de map **C:\users\public\downloads** op de lokale computer.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Volgende stappen
Zie [Resource Manager-sjabloon scenario](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md)voor meer informatie over het implementeren van resources met behulp van sjablonen.

---
title: De sjabloon voor een virtuele Azure-machine downloaden
description: Down load de sjabloon voor een virtuele machine met behulp van de portal of Power shell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085379"
---
# <a name="download-the-template-for-a-vm"></a>De sjabloon voor een VM downloaden
Wanneer u een virtuele machine in azure maakt met behulp van de portal of Power shell, wordt automatisch een resource manager-sjabloon voor u gemaakt. U kunt deze sjabloon gebruiken om snel een implementatie te dupliceren. De sjabloon bevat informatie over alle resources in een resource groep. Voor een virtuele machine betekent dit dat de sjabloon alles bevat dat is gemaakt ter ondersteuning van de VM in die resource groep, met inbegrip van de netwerk resources.

## <a name="download-the-template-using-the-portal"></a>De sjabloon downloaden met behulp van de portal
1. Meld u aan bij de [Azure Portal](https://portal.azure.com/).
2. Selecteer in het linkermenu **virtual machines**.
3. Selecteer de virtuele machine in de lijst.
4. Selecteer **sjabloon exporteren**.
5. Selecteer **downloaden** in het menu bovenaan en sla het zip-bestand op uw lokale computer op.
6. Open het zip-bestand en pak de bestanden uit naar een map. Het zip-bestand bevat:
   
   * para meters. json
   * sjabloon. json

Het bestand template. json is de sjabloon.

## <a name="download-the-template-using-powershell"></a>De sjabloon downloaden met behulp van Power shell
U kunt ook het JSON-sjabloon bestand downloaden met de cmdlet [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . U kunt de `-path` para meter gebruiken om de bestands naam en het pad voor het JSON-bestand op te geven. In dit voor beeld ziet u hoe u de sjabloon voor de resource groep met de naam **myResourceGroup** kunt downloaden naar de map **C:\users\public\downloads** op de lokale computer.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Volgende stappen
Zie [Resource Manager-sjabloon scenario](../../azure-resource-manager/resource-manager-template-walkthrough.md)voor meer informatie over het implementeren van resources met behulp van sjablonen.


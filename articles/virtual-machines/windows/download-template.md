---
title: De sjabloon voor een virtuele Azure-machine downloaden | Microsoft Docs
description: De templatefor een VM downloaden voor het automatiseren van implementaties in het Resource Manager-implementatie model
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 51ef4f51-0942-4249-afea-4a3f87ce1ff8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: 39abe054869bf68a254b9305a680ecc892359300
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72898874"
---
# <a name="download-the-template-for-a-vm"></a>De sjabloon voor een VM downloaden
Wanneer u een virtuele machine in azure maakt met behulp van de portal of Power shell, wordt automatisch een resource manager-sjabloon voor u gemaakt. U kunt deze sjabloon gebruiken om snel een implementatie te dupliceren. De sjabloon bevat informatie over alle resources in een resource groep. Voor een virtuele machine betekent dit dat de sjabloon alles bevat dat is gemaakt ter ondersteuning van de VM in die resource groep, met inbegrip van de netwerk resources.

## <a name="download-the-template-using-the-portal"></a>De sjabloon downloaden met behulp van de portal
1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Selecteer in het linkermenu **virtual machines**.
3. Selecteer de virtuele machine in de lijst.
4. Selecteer **sjabloon exporteren**.
5. Selecteer **downloaden** in het menu bovenaan en sla het zip-bestand op uw lokale computer op.
6. Open het zip-bestand en pak de bestanden uit naar een map. Het zip-bestand bevat:
   
   * para meters. json
   * sjabloon. json

Het bestand template. json is de sjabloon.

## <a name="download-the-template-using-powershell"></a>De sjabloon downloaden met behulp van Power shell
U kunt ook het JSON-sjabloon bestand downloaden met de cmdlet [export-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) . U kunt de para meter `-path` gebruiken om de bestands naam en het pad voor het JSON-bestand op te geven. In dit voor beeld ziet u hoe u de sjabloon voor de resource groep met de naam **myResourceGroup** kunt downloaden naar de map **C:\users\public\downloads** op de lokale computer.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Volgende stappen
Zie [Resource Manager-sjabloon scenario](../../azure-resource-manager/resource-manager-template-walkthrough.md)voor meer informatie over het implementeren van resources met behulp van sjablonen.


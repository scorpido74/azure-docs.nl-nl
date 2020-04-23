---
title: De sjabloon voor een Azure VM downloaden
description: Download de sjabloon voor een virtuele machine via de portal of PowerShell.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/17/2017
ms.author: cynthn
ms.openlocfilehash: af6905f0ba62a9053e44134348721312ade6b9d7
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085379"
---
# <a name="download-the-template-for-a-vm"></a>De sjabloon voor een VM downloaden
Wanneer u een VM in Azure maakt met de portal of PowerShell, wordt er automatisch een Resource Manager-sjabloon voor u gemaakt. U deze sjabloon gebruiken om een implementatie snel te dupliceren. De sjabloon bevat informatie over alle bronnen in een resourcegroep. Voor een virtuele machine betekent dit dat de sjabloon alles bevat dat is gemaakt ter ondersteuning van de VM in die resourcegroep, inclusief de netwerkbronnen.

## <a name="download-the-template-using-the-portal"></a>De sjabloon downloaden via de portal
1. Log in bij de [Azure-portal](https://portal.azure.com/).
2. Selecteer **Virtuele machines**in het linkermenu.
3. Selecteer de virtuele machine in de lijst.
4. Selecteer **Sjabloon Exporteren**.
5. Selecteer **Downloaden** in het menu bovenaan en sla het .zip-bestand op uw lokale computer op.
6. Open het .zip-bestand en haal de bestanden naar een map. Het .zip-bestand bevat:
   
   * parameters.json
   * template.json

Het bestand template.json is de sjabloon.

## <a name="download-the-template-using-powershell"></a>De sjabloon downloaden met PowerShell
U het sjabloonbestand .json ook downloaden met de cmdlet [Exporteren-AzResourceGroup.](https://docs.microsoft.com/powershell/module/az.resources/export-azresourcegroup) U `-path` de parameter gebruiken om de bestandsnaam en het pad voor het JSon-bestand op te geven. In dit voorbeeld ziet u hoe u de sjabloon voor de brongroep met de naam **myResourceGroup** downloadt naar de map **C:\users\public\downloads** op uw lokale computer.

```powershell
    Export-AzResourceGroup -ResourceGroupName "myResourceGroup" -Path "C:\users\public\downloads"
```

## <a name="next-steps"></a>Volgende stappen
Zie [Resourcemanager-sjabloondoorlopen](../../azure-resource-manager/resource-manager-template-walkthrough.md)voor meer informatie over het implementeren van resources met sjablonen.


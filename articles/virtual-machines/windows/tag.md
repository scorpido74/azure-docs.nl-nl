---
title: Een Windows VM-bron taggen in Azure
description: Meer informatie over het taggen van een virtuele Windows-machine die in Azure is gemaakt met behulp van het implementatiemodel Resource Beheer
services: virtual-machines-windows
documentationcenter: ''
author: mmccrory
manager: gwallace
tags: azure-resource-manager
ms.assetid: 56d17f45-e4a7-4d84-8022-b40334ae49d2
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/05/2016
ms.author: memccror
ms.openlocfilehash: 8f95c11f93ca2075eb2472ad5bb7360df7d69234
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456444"
---
# <a name="how-to-tag-a-windows-virtual-machine-in-azure"></a>Een virtuele Windows-machine taggen in Azure
In dit artikel worden verschillende manieren beschreven om een virtuele Windows-machine in Azure te taggen via het implementatiemodel van Resource Manager. Tags zijn door de gebruiker gedefinieerde sleutel-/waardeparen die rechtstreeks op een resource of een resourcegroep kunnen worden geplaatst. Azure ondersteunt momenteel maximaal 50 tags per resource- en resourcegroep. Tags kunnen op het moment van maken op een resource worden geplaatst of aan een bestaande resource worden toegevoegd. Houd er rekening mee dat tags alleen worden ondersteund voor resources die zijn gemaakt via het implementatiemodel van Resource Manager. Als u een virtuele Linux-machine wilt taggen, raadpleegt u [Hoe u een virtuele Linux-machine in Azure tagt.](../linux/tag.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[!INCLUDE [virtual-machines-common-tag](../../../includes/virtual-machines-common-tag.md)]

## <a name="tagging-with-powershell"></a>Tagging met PowerShell
Als u tags wilt maken, toevoegen en verwijderen via PowerShell, moet u eerst uw [PowerShell-omgeving][PowerShell environment with Azure Resource Manager]instellen met Azure Resource Manager. Zodra u de installatie hebt voltooid, u tags plaatsen op compute-, netwerk- en opslagbronnen bij het maken of nadat de bron via PowerShell is gemaakt. Dit artikel concentreert zich op het bekijken/bewerken van tags die op virtuele machines zijn geplaatst.

 

Navigeer eerst naar een virtuele `Get-AzVM` machine door de cmdlet.

        PS C:\> Get-AzVM -ResourceGroupName "MyResourceGroup" -Name "MyTestVM"

Als uw virtuele machine al tags bevat, ziet u alle tags op uw bron:

        Tags : {
                "Application": "MyApp1",
                "Created By": "MyName",
                "Department": "MyDepartment",
                "Environment": "Production"
               }

Als u tags wilt toevoegen via PowerShell, `Set-AzResource` u de opdracht gebruiken. Let op bij het bijwerken van tags via PowerShell, tags worden bijgewerkt als geheel. Dus als u één tag toevoegt aan een bron die al tags heeft, moet u alle tags opnemen die u op de bron wilt plaatsen. Hieronder vindt u een voorbeeld van het toevoegen van extra tags aan een resource via PowerShell-cmdlets.

Deze eerste cmdlet stelt alle tags geplaatst op *MyTestVM* `Get-AzResource` op `Tags` de *$tags* variabele, met behulp van de en eigenschap.

        PS C:\> $tags = (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

Met de tweede opdracht worden de tags voor de opgegeven variabele weergegeven.

```
    PS C:\> $tags
    
    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
```

De derde opdracht voegt een extra tag toe aan de *variabele $tags.* Let op het **+=** gebruik van de om de nieuwe sleutel / waarde paar toe te wijzen aan de *$tags* lijst.

        PS C:\> $tags += @{Location="MyLocation"}

Met de vierde opdracht worden alle tags ingesteld die zijn gedefinieerd in de *$tags* variabele op de opgegeven resource. In dit geval is het MyTestVM.

        PS C:\> Set-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags

Met de vijfde opdracht worden alle tags op de resource weergegeven. Zoals u zien, wordt *Locatie* nu gedefinieerd als een tag met *MyLocation* als waarde.

```
    PS C:\> (Get-AzResource -ResourceGroupName MyResourceGroup -Name MyTestVM).Tags

    Key           Value
    ----          -----
    Department    MyDepartment
    Application   MyApp1
    Created By    MyName
    Environment   Production
    Location      MyLocation
```

Ga voor meer informatie over tagging via PowerShell naar de [Azure Resource Cmdlets][Azure Resource Cmdlets].

[!INCLUDE [virtual-machines-common-tag-usage](../../../includes/virtual-machines-common-tag-usage.md)]

## <a name="next-steps"></a>Volgende stappen
* Zie Overzicht van [Azure Resource Manager][Azure Resource Manager Overview] en Het [gebruik van tags voor][Using Tags to organize your Azure Resources]meer informatie over het taggen van uw Azure-resources.
* Zie [Inzicht in uw Azure-factuur][Understanding your Azure Bill] en inzicht krijgen in [uw Microsoft Azure-bronverbruik][Gain insights into your Microsoft Azure resource consumption]als u wilt zien hoe tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources.

[PowerShell environment with Azure Resource Manager]: ../../azure-resource-manager/management/manage-resources-powershell.md
[Azure Resource Cmdlets]: https://docs.microsoft.com/powershell/module/az.resources/
[Azure Resource Manager Overview]: ../../azure-resource-manager/management/overview.md
[Using Tags to organize your Azure Resources]: ../../azure-resource-manager/management/tag-resources.md
[Understanding your Azure Bill]:../../cost-management-billing/understand/review-individual-bill.md
[Gain insights into your Microsoft Azure resource consumption]:../../cost-management-billing/manage/usage-rate-card-overview.md

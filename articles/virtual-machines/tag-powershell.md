---
title: Een virtuele machine labelen met behulp van Power shell
description: Meer informatie over het coderen van een virtuele machine met behulp van Power shell
author: cynthn
ms.service: virtual-machines
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 1efb512923caed97126bdb4ee6267c6a9b57f251
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94594938"
---
# <a name="how-to-tag-a-virtual-machine-in-azure-using-powershell"></a>Een virtuele machine in azure labelen met behulp van Power shell

In dit artikel wordt beschreven hoe u een virtuele machine in azure kunt labelen met behulp van Power shell. Tags zijn door de gebruiker gedefinieerde sleutel/waarde-paren die rechtstreeks kunnen worden geplaatst op een resource of resource groep. Azure ondersteunt momenteel Maxi maal 50 Tags per resource en resource groep. Labels kunnen worden geplaatst op een resource op het moment dat ze worden gemaakt of worden toegevoegd aan een bestaande resource. Als u een virtuele machine wilt labelen met behulp van de Azure CLI, raadpleegt u [een virtuele machine in azure labelen met behulp van de Azure cli](tag-cli.md).


Gebruik de `Get-AzVM` cmdlet om de huidige lijst met tags voor uw virtuele machine weer te geven.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName "myResourceGroup" -Name "myVM" | Format-List -Property Tags
```

Als uw virtuele machine al Tags bevat, worden alle tags weer geven in de lijst indeling.

Gebruik de opdracht om tags toe te voegen `Set-AzResource` . Wanneer labels via Power shell worden bijgewerkt, worden tags als geheel bijgewerkt. Als u één tag toevoegt aan een resource die al labels bevat, moet u alle labels opnemen die u op de resource wilt plaatsen. Hieronder ziet u een voor beeld van het toevoegen van extra tags aan een resource via Power shell-cmdlets.

Wijs alle huidige Tags voor de virtuele machine toe aan de `$tags` variabele, met behulp van de- `Get-AzResource` en- `Tags` eigenschap.

```azurepowershell-interactive
$tags = (Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags
```

Als u de huidige tags wilt zien, typt u de variabele.

```azurepowershell-interactive
$tags
```

De uitvoer kan er als volgt uitzien:

```output
Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
```

In het volgende voor beeld wordt een tag `Location` met de naam met de waarde toegevoegd `myLocation` . Gebruik `+=` om het nieuwe sleutel/waarde-paar toe te voegen aan de `$tags` lijst.

```azurepowershell-interactive
$tags += @{Location="myLocation"}
```

Gebruik dit `Set-AzResource` om alle labels in te stellen die zijn gedefinieerd in de variabele *$Tags* op de virtuele machine.

```azurepowershell-interactive
Set-AzResource -ResourceGroupName myResourceGroup -Name myVM -ResourceType "Microsoft.Compute/VirtualMachines" -Tag $tags
```

Gebruiken `Get-AzResource` om alle labels op de resource weer te geven.

```azurepowershell-interactive
(Get-AzResource -ResourceGroupName myResourceGroup -Name myVM).Tags

```

De uitvoer moet er ongeveer als volgt uitzien, dat nu de nieuwe tag bevat:

```output

Key           Value
----          -----
Department    MyDepartment
Application   MyApp1
Created By    MyName
Environment   Production
Location      MyLocation
```


**Volgende stappen**

- Zie [Azure Resource Manager Overview](../azure-resource-manager/management/overview.md) en [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md)voor meer informatie over het coderen van uw Azure-resources.
- Als u wilt zien hoe Tags u kunnen helpen bij het beheren van uw gebruik van Azure-resources, raadpleegt u [inzicht in uw Azure-factuur](../cost-management-billing/understand/review-individual-bill.md) en [krijgt u inzicht in uw Microsoft Azure Resource verbruik](../cost-management-billing/manage/usage-rate-card-overview.md).

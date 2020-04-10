---
title: Virtuele machineschaalsets beheren met Azure PowerShell
description: Algemene Azure PowerShell-cmdlets voor het beheren van virtuele machineschaalsets, zoals het starten en stoppen van een instantie of het wijzigen van de schaalsetcapaciteit.
author: ju-shim
tags: azure-resource-manager
ms.assetid: d35fa77a-de96-4ccd-a332-eb181d1f4273
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: jushiman
ms.openlocfilehash: 5794fb40b49a575c12f6855292c41014fabf3b8c
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81011596"
---
# <a name="manage-a-virtual-machine-scale-set-with-azure-powershell"></a>Een virtuele machineschaalset beheren met Azure PowerShell

Gedurende de levenscyclus van een schaalset voor virtuele machines moet u mogelijk een of meer beheertaken uitvoeren. Bovendien wilt u misschien scripts maken die verschillende levenscyclustaken automatiseren. In dit artikel worden enkele van de algemene Azure PowerShell-cmdlets beschreven waarmee u deze taken uitvoeren.

Als u een virtuele machineschaalset moet maken, u [een schaalset maken met Azure PowerShell](quick-create-powershell.md).

[!INCLUDE [updated-for-az.md](../../includes/updated-for-az.md)]

## <a name="view-information-about-a-scale-set"></a>Informatie over een schaalset weergeven
Als u de algemene informatie over een schaalset wilt bekijken, gebruikt u [Get-AzVmss](/powershell/module/az.compute/get-azvmss). In het volgende voorbeeld wordt informatie over de schaalset met de naam *myScaleSet* in de resourcegroep *myResourceGroup.* Voer als volgt uw eigen namen in:

```powershell
Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```


## <a name="view-vms-in-a-scale-set"></a>Virtuele machines weergeven in een schaalset
Als u een lijst met VM-instantie in een schaalset wilt weergeven, gebruikt u [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm). In het volgende voorbeeld worden alle VM-exemplaren weergegeven in de schaalset met de naam *myScaleSet* en in de resourcegroep *myResourceGroup.* Geef uw eigen waarden op voor deze namen:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"
```

Als u aanvullende informatie over een `-InstanceId` specifieke VM-instantie wilt weergeven, voegt u de parameter toe aan [Get-AzVmssVM](/powershell/module/az.compute/get-azvmssvm) en geeft u een instantie op die u wilt weergeven. In het volgende voorbeeld wordt informatie over VM-instantie *0* weergegeven in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Voer als volgt uw eigen namen in:

```powershell
Get-AzVmssVM -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="change-the-capacity-of-a-scale-set"></a>De capaciteit van een schaalset wijzigen
In de voorgaande opdrachten werden informatie weergegeven over uw schaalset en de VM-exemplaren. Als u het aantal exemplaren in de schaalset wilt verhogen of verkleinen, u de capaciteit wijzigen. De schaalset maakt of verwijdert automatisch het vereiste aantal VM's en configureert vervolgens de VM's om toepassingsverkeer te ontvangen.

Maak eerst een schaalsetobject met [Get-AzVmss](/powershell/module/az.compute/get-azvmss) en geef vervolgens een nieuwe waarde op voor `sku.capacity`. Gebruik [Update-AzVmss](/powershell/module/az.compute/update-azvmss) om de capaciteitswijziging toe te passen. In het volgende voorbeeld wordt *myScaleSet* in de brongroep *myResourceGroup* bijgewerkt naar een capaciteit van *5* exemplaren. Geef uw eigen waarden als volgt op:

```powershell
# Get current scale set
$vmss = Get-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet"

# Set and update the capacity of your scale set
$vmss.sku.capacity = 5
Update-AzVmss -ResourceGroupName "myResourceGroup" -Name "myScaleSet" -VirtualMachineScaleSet $vmss
```

Het duurt een paar minuten om de capaciteit van de schaalset bij te werken. Als u de capaciteit van een schaalset verlaagt, worden de VM's met de hoogste instantie-id's eerst verwijderd.


## <a name="stop-and-start-vms-in-a-scale-set"></a>VM’s in een schaalset stoppen en opstarten
Als u een of meer VM's in een schaalset wilt stoppen, gebruikt u [Stop-AzVmss](/powershell/module/az.compute/stop-azvmss). Met de parameter `-InstanceId` kunt u een of meer VM's opgeven om te stoppen. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestopt. Als u meerdere VM's wilt stoppen, scheidt u elke instantie-id met een komma.

In het volgende voorbeeld wordt instantie *0* gestopt in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```powershell
Stop-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```

De standaardinstelling is dat de toewijzing van gestopte VM's ongedaan wordt gemaakt en er dus geen compute-kosten meer in rekening worden gebracht. Als u wilt dat de VM de ingerichte status blijft houden nadat deze is gestopt, voeg u de parameter `-StayProvisioned` toe aan de voorgaande opdracht. Voor gestopte VM's die ingericht blijven, worden periodieke compute-kosten in rekening gebracht.


### <a name="start-vms-in-a-scale-set"></a>VM's starten in een schaalset
Als u een of meer VM's in een schaalset wilt starten, gebruikt u [Start-AzVmss](/powershell/module/az.compute/start-azvmss). Met de parameter `-InstanceId` kunt u een of meer VM's opgeven om te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset gestart. Als u meerdere VM's wilt starten, scheidt u elke instantie-id met een komma.

In het volgende voorbeeld wordt instantie *0* gestart in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```powershell
Start-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="restart-vms-in-a-scale-set"></a>VM’s in een schaalset opnieuw opstarten
Als u een of meer VM's opnieuw wilt starten in een schaalset, gebruikt u [Opnieuw starten azvmss](/powershell/module/az.compute/restart-azvmss). Met de parameter `-InstanceId` kunt u een of meer VM's opgeven om opnieuw op te starten. Als u geen exemplaar-id opgeeft, worden alle VM's in de schaalset opnieuw opgestart. Als u meerdere VM's opnieuw wilt starten, scheidt u elke instantie-id met een komma.

In het volgende voorbeeld wordt instantie *0* opnieuw gestart in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```powershell
Restart-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="remove-vms-from-a-scale-set"></a>VM's uit een schaalset verwijderen
Als u een of meer VM's in een schaalset wilt verwijderen, gebruikt u [Remove-AzVmss](/powershell/module/az.compute/remove-azvmss). Met `-InstanceId` de parameter u een of meer VM's opgeven om te verwijderen. Als u geen instantie-id opgeeft, worden alle VM's in de schaalset verwijderd. Als u meerdere VM's wilt verwijderen, scheidt u elke instantie-id met een komma.

In het volgende voorbeeld wordt instantie *0* verwijderd in de schaalset met de naam *myScaleSet* en de resourcegroep *myResourceGroup.* Geef uw eigen waarden als volgt op:

```powershell
Remove-AzVmss -ResourceGroupName "myResourceGroup" -VMScaleSetName "myScaleSet" -InstanceId "0"
```


## <a name="next-steps"></a>Volgende stappen
Andere veelvoorkomende taken voor schaalsets zijn het [implementeren van een toepassing](virtual-machine-scale-sets-deploy-app.md)en het upgraden van [VM-exemplaren](virtual-machine-scale-sets-upgrade-scale-set.md). U Azure PowerShell ook gebruiken om regels voor automatische schaal te [configureren.](virtual-machine-scale-sets-autoscale-overview.md)

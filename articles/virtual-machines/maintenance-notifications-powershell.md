---
title: Onderhoudsmeldingen voor Azure VM's ontvangen met PowerShell
description: Bekijk onderhoudsmeldingen voor virtuele machines die in Azure worden uitgevoerd en start selfserviceonderhoud met PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916079"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Gepland onderhoud verwerken met PowerShell

**Dit artikel is van toepassing op virtuele machines met zowel Linux als Windows.**

U Azure PowerShell gebruiken om te zien wanneer VM's zijn gepland voor [onderhoud.](maintenance-notifications.md) Geplande onderhoudsinformatie is beschikbaar via de [Get-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) wanneer u de `-status` parameter gebruikt.
  
Onderhoudsinformatie wordt alleen geretourneerd als er onderhoud is gepland. Als er geen onderhoud is gepland dat van invloed is op de VM, geeft de cmdlet geen onderhoudsinformatie terug. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

De volgende eigenschappen worden geretourneerd onder MaintenanceRedeployStatus: 

| Waarde | Beschrijving   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceA toegestaan | Geeft aan of u op dit moment met onderhoud aan de VM beginnen |
| Begintijd voor onderhoud         | Het begin van het onderhouds-selfservicevenster wanneer u onderhoud aan uw VM starten |
| PreMaintenanceWindowEndTime           | Het einde van het onderhouds-selfservicevenster wanneer u onderhoud aan uw VM starten |
| Begintijd van onderhoudsvenster            | Het begin van het geplande onderhoud waarin Azure onderhoud op uw VM initieert |
| MaintenanceWindowEndTime              | Het einde van het geplande onderhoudsvenster waarin Azure onderhoud op uw VM initieert |
| LastOperationResultCode               | Het resultaat van de laatste poging om onderhoud aan de VM te starten |



U ook de onderhoudsstatus voor alle VM's in een resourcegroep krijgen door [Get-AzVM te](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) gebruiken en geen VM op te geven.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

In het volgende PowerShell-voorbeeld wordt uw abonnements-ID opgenomen en wordt een lijst met VM's geretourneerd die zijn gepland voor onderhoud.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Onderhoud aan uw vm starten met PowerShell

Met behulp van informatie uit de functie in de vorige sectie wordt het onderhoud van een vm gestart als **isCustomerInitiatedMaintenanceA toegestaan** ingesteld op true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Klassieke implementaties

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Als u nog steeds verouderde VM's hebt die zijn geïmplementeerd met behulp van het klassieke implementatiemodel, u PowerShell gebruiken om vm's op te vragen en onderhoud te starten.

Typ:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Als u wilt beginnen met het onderhoud van uw klassieke VM, typt u het:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Volgende stappen

U ook gepland onderhoud afhandelen via de [Azure CLI](maintenance-notifications-cli.md) of [portal.](maintenance-notifications-portal.md)

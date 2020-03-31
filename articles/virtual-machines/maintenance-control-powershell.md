---
title: Onderhoudsbeheer voor Azure virtuele machines met PowerShell
description: Meer informatie over hoe u bepalen wanneer onderhoud wordt toegepast op uw Azure VM's met behulp van onderhoudsbeheer en PowerShell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: dc47afe9cb6eca1b10f8caca7b85087023c5eadf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060139"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Preview: Updates beheren met onderhoudsbeheer en Azure PowerShell

Beheer platformupdates, waarvoor geen herstart nodig is, met behulp van onderhoudscontrole. Azure werkt zijn infrastructuur regelmatig bij om de betrouwbaarheid, prestaties, beveiliging of lancering van nieuwe functies te verbeteren. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige workloads, zoals gaming, mediastreaming en financiële transacties, kunnen zelfs enkele seconden van een VM-bevriezing of loskoppeling voor onderhoud niet verdragen. Onderhoudscontrole geeft u de mogelijkheid om te wachten op platformupdates en deze toe te passen binnen een rollend venster van 35 dagen. 

Met onderhoudscontrole u bepalen wanneer u updates wilt toepassen op uw geïsoleerde VM's.

Met onderhoudscontrole u:
- Batch-updates in één updatepakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Automatiseer platformupdates voor uw onderhoudsvenster met Azure Functions.
- Onderhoudsconfiguraties werken voor abonnementen en resourcegroepen. 

> [!IMPORTANT]
> Maintenance Control is momenteel in openbare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.
> 

## <a name="limitations"></a>Beperkingen

- VM's moeten zich op een [speciale host](./linux/dedicated-hosts.md)bevindt of worden gemaakt met behulp van een [geïsoleerde VM-grootte](./linux/isolation.md).
- Na 35 dagen wordt automatisch een update toegepast.
- De gebruiker moet toegang tot **resourceinzender** hebben.


## <a name="enable-the-powershell-module"></a>De PowerShell-module inschakelen

Zorg `PowerShellGet` ervoor dat het up-to-date is.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

De Az.Maintenance PowerShell-cmdlets zijn in preview, dus `AllowPrerelease` u moet de module installeren met de parameter in Cloud Shell of uw lokale PowerShell-installatie.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Als u lokaal installeert, moet u ervoor zorgen dat u uw PowerShell-prompt opent als beheerder.

U ook worden gevraagd om te bevestigen dat u wilt installeren vanuit een *niet-vertrouwde opslagplaats.* Typ `Y` of selecteer **Ja tot alles** om de module te installeren.



## <a name="create-a-maintenance-configuration"></a>Een onderhoudsconfiguratie maken

Maak een resourcegroep als container voor uw configuratie. In dit voorbeeld wordt een resourcegroep met de naam *myMaintenanceRG* gemaakt in *eastus*. Als u al een resourcegroep hebt die u wilt gebruiken, u dit onderdeel overslaan en de naam van de resourcegroep vervangen door de naam van u in de rest van de voorbeelden.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Gebruik [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) om een onderhoudsconfiguratie te maken. In dit voorbeeld wordt een onderhoudsconfiguratie met de naam *myConfig* gemaakt die is uitgevoerd op de host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Met `-MaintenanceScope host` behulp van zorgt ervoor dat de onderhoudsconfiguratie wordt gebruikt voor het beheren van updates voor de host.

Als u een configuratie met dezelfde naam probeert te maken, maar op een andere locatie, krijgt u een foutmelding. Configuratienamen moeten uniek zijn voor uw abonnement.

U query's voor beschikbare onderhoudsconfiguraties met [behulp van Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>De configuratie toewijzen

Gebruik [Nieuw-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) om de configuratie toe te wijzen aan uw geïsoleerde VM of Azure Dedicated Host.

### <a name="isolated-vm"></a>Geïsoleerde VM

Pas de configuratie toe op een vm met de id van de configuratie. Geef `-ResourceType VirtualMachines` de naam van de `-ResourceName`vm voor en de `-ResourceGroupName`resourcegroep van de vm voor op en geef deze op. 

```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

### <a name="dedicated-host"></a>Toegewezen host

Als u een configuratie wilt toepassen op `-ResourceType hosts`een `-ResourceParentName` speciale host, moet u `-ResourceParentType hostGroups`ook de naam van de hostgroep opnemen en . 


```azurepowershell-interactive
New-AzConfigurationAssignment `
   -ResourceGroupName myResourceGroup `
   -Location eastus `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ConfigurationAssignmentName $config.Name `
   -MaintenanceConfigurationId $config.Id
```

## <a name="check-for-pending-updates"></a>Controleren op updates in behandeling

Gebruik [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) om te zien of er nog updates in behandeling zijn. Hiermee `-subscription` u het Azure-abonnement van de VM opgeven als het anders is dan het abonnement waarop u bent aangemeld.

Als er geen updates worden weergegeven, geeft deze opdracht niets terug. Anders wordt een PSApplyUpdate-object teruggegeven:

```json
{
   "maintenanceScope": "Host",
   "impactType": "Freeze",
   "status": "Pending",
   "impactDurationInSec": 9,
   "notBefore": "2020-02-21T16:47:44.8728029Z",
   "properties": {
      "resourceId": "/subscriptions/39c6cced-4d6c-4dd5-af86-57499cd3f846/resourcegroups/Ignite2019/providers/Microsoft.Compute/virtualMachines/MCDemo3"
} 
```

### <a name="isolated-vm"></a>Geïsoleerde VM

Controleer op updates in behandeling voor een geïsoleerde virtuele machine. In dit voorbeeld wordt de uitvoer opgemaakt als een tabel voor leesbaarheid.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Toegewezen host

Controleren op updates in behandeling voor een speciale host. In dit voorbeeld wordt de uitvoer opgemaakt als een tabel voor leesbaarheid. Vervang de waarden voor de resources door die van u.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute | Format-Table
```


## <a name="apply-updates"></a>Updates toepassen

Gebruik [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) om in behandeling zijnde updates toe te passen.

### <a name="isolated-vm"></a>Geïsoleerde VM

Maak een verzoek om updates toe te passen op een geïsoleerde virtuele machine.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Bij succes geeft deze `PSApplyUpdate` opdracht een object terug. U het kenmerk `Get-AzApplyUpdate` Naam in de opdracht gebruiken om de updatestatus te controleren. Zie [Updatestatus controleren](#check-update-status).

### <a name="dedicated-host"></a>Toegewezen host

Pas updates toe op een speciale host.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Updatestatus controleren
Gebruik [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) om de status van een update te controleren. De onderstaande opdrachten tonen de status van `default` de `-ApplyUpdateName` laatste update door te gebruiken voor de parameter. U de naam van de update vervangen (geretourneerd door de opdracht [Nieuw-AzApplyUpdate)](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) om de status van een specifieke update te krijgen.

```text
Status         : Completed
ResourceId     : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso
LastUpdateTime : 1/1/2020 12:00:00 AM
Id             : /subscriptions/12ae7457-4a34-465c-94c1-17c058c2bd25/resourcegroups/TestShantS/providers/Microsoft.Comp
ute/virtualMachines/DXT-test-04-iso/providers/Microsoft.Maintenance/applyUpdates/default
Name           : default
Type           : Microsoft.Maintenance/applyUpdates
```
LastUpdateTime is het moment waarop de update is voltooid, ofwel geïnitieerd door u of door het platform in het geval het zelfonderhoudsvenster niet werd gebruikt. Als er nog nooit een update is toegepast via onderhoudsbeheer, wordt de standaardwaarde weergegeven.

### <a name="isolated-vm"></a>Geïsoleerde VM

Controleer op updates van een specifieke virtuele machine.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Toegewezen host

Controleer op updates voor een speciale host.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName myUpdateName
```

## <a name="remove-a-maintenance-configuration"></a>Een onderhoudsconfiguratie verwijderen

Gebruik [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) om een onderhoudsconfiguratie te verwijderen.

```azurepowershell-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Volgende stappen
Zie [Onderhoud en updates](maintenance-and-updates.md)voor meer informatie.

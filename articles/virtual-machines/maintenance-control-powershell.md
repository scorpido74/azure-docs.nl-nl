---
title: Onderhouds beheer voor virtuele Azure-machines met Power shell
description: Meer informatie over hoe u kunt bepalen wanneer onderhoud wordt toegepast op uw Azure-Vm's met behulp van onderhouds beheer en Power shell.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/31/2020
ms.author: cynthn
ms.openlocfilehash: 7e4586a5fba91fbc7432aa352b9608be728e8654
ms.sourcegitcommit: 79cbd20a86cd6f516acc3912d973aef7bf8c66e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2020
ms.locfileid: "77251937"
---
# <a name="preview-control-updates-with-maintenance-control-and-azure-powershell"></a>Voor beeld: updates beheren met onderhouds beheer en Azure PowerShell

Platform updates beheren, die niet opnieuw moeten worden opgestart, met behulp van onderhouds beheer. Azure werkt de infra structuur regel matig bij om de betrouw baarheid, prestaties en beveiliging te verbeteren of nieuwe functies te starten. De meeste updates zijn transparant voor gebruikers. Sommige gevoelige werk belastingen, zoals games, mediastreaming en financiële trans acties, kunnen niet worden toegestaan tot zelfs enkele seconden van een VM-bevriezing of de verbinding met het beheer voor onderhoud. Onderhouds beheer biedt u de mogelijkheid te wachten op platform updates en deze toe te passen binnen een 35-daagse venster. 

Met de onderhouds controle kunt u bepalen wanneer u updates wilt Toep assen op uw geïsoleerde Vm's.

Met onderhouds controle kunt u het volgende doen:
- Batch updates in één update pakket.
- Wacht tot 35 dagen om updates toe te passen. 
- Platform updates voor uw onderhouds venster automatiseren met Azure Functions.
- Onderhouds configuraties werken in abonnementen en resource groepen. 

> [!IMPORTANT]
> Onderhouds beheer is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.
> 

## <a name="limitations"></a>Beperkingen

- Vm's moeten op een [specifieke host](./linux/dedicated-hosts.md)staan of worden gemaakt met behulp van een [geïsoleerde VM-grootte](./linux/isolation.md).
- Na 35 dagen wordt een update automatisch toegepast.
- De gebruiker moet toegang hebben tot de **resource bijdrager** .


## <a name="enable-the-powershell-module"></a>De Power shell-module inschakelen

Zorg ervoor dat `PowerShellGet` up-to-date is.

```azurepowershell-interactive
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```

De Power shell-cmdlets AZ. Maintenance zijn beschikbaar als preview-versie, dus u moet de module installeren met de para meter `AllowPrerelease` in Cloud Shell of uw lokale Power shell-installatie.   

```azurepowershell-interactive
Install-Module -Name Az.Maintenance -AllowPrerelease
```

Als u lokaal installeert, moet u ervoor zorgen dat u de Power shell-prompt opent als beheerder.

U wordt mogelijk ook gevraagd om te bevestigen dat u wilt installeren vanuit een *niet-vertrouwde opslag plaats*. Typ `Y` of selecteer **Ja om** de module te installeren.



## <a name="create-a-maintenance-configuration"></a>Een onderhouds configuratie maken

Maak een resource groep als een container voor uw configuratie. In dit voor beeld wordt een resource groep met de naam *myMaintenanceRG* gemaakt in *Oost-Timor*. Als u al een resource groep hebt die u wilt gebruiken, kunt u dit deel overs Laan en de naam van de resource groep vervangen door uw eigen in de rest van de voor beelden.

```azurepowershell-interactive
New-AzResourceGroup `
   -Location eastus `
   -Name myMaintenanceRG
```

Gebruik [New-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/new-azmaintenanceconfiguration) om een onderhouds configuratie te maken. In dit voor beeld wordt een onderhouds configuratie gemaakt met de naam *myConfig* scoped to the host. 

```azurepowershell-interactive
$config = New-AzMaintenanceConfiguration `
   -ResourceGroup myMaintenanceRG `
   -Name myConfig `
   -MaintenanceScope host `
   -Location  eastus
```

Met `-MaintenanceScope host` zorgt u ervoor dat de onderhouds configuratie wordt gebruikt voor het beheren van updates voor de host.

Als u probeert een configuratie met dezelfde naam te maken, maar op een andere locatie, krijgt u een fout melding. Configuratie namen moeten uniek zijn voor uw abonnement.

U kunt een query uitvoeren voor beschik bare onderhouds configuraties met [Get-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceconfiguration).

```azurepowershell-interactive
Get-AzMaintenanceConfiguration | Format-Table -Property Name,Id
```

## <a name="assign-the-configuration"></a>De configuratie toewijzen

Gebruik [New-AzConfigurationAssignment](https://docs.microsoft.com/powershell/module/az.maintenance/new-azconfigurationassignment) om de configuratie toe te wijzen aan uw geïsoleerde virtuele machine of uw toegewezen Azure-host.

### <a name="isolated-vm"></a>Geïsoleerde VM

Pas de configuratie toe op een virtuele machine met behulp van de ID van de configuratie. Geef `-ResourceType VirtualMachines` op en voer de naam in van de virtuele machine voor `-ResourceName`en de resource groep van de virtuele machine voor `-ResourceGroupName`. 

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

Als u een configuratie wilt Toep assen op een specifieke host, moet u ook `-ResourceType hosts`, `-ResourceParentName` toevoegen met de naam van de hostgroep en `-ResourceParentType hostGroups`. 


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

## <a name="check-for-pending-updates"></a>Controleren op updates die nog niet zijn uitgevoerd

Gebruik [Get-AzMaintenanceUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azmaintenanceupdate) om te zien of er updates in behandeling zijn. Gebruik `-subscription` om het Azure-abonnement van de virtuele machine op te geven als deze verschilt van de VM die u bent aangemeld.

Als er geen updates zijn om weer te geven, retourneert deze opdracht niets. Anders wordt een PSApplyUpdate-object geretourneerd:

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

Controleren op updates die in behandeling zijn voor een geïsoleerde virtuele machine. In dit voor beeld wordt de uitvoer opgemaakt als een tabel voor de Lees baarheid.

```azurepowershell-interactive
Get-AzMaintenanceUpdate `
  -ResourceGroupName myResourceGroup `
  -ResourceName myVM `
  -ResourceType VirtualMachines `
  -ProviderName Microsoft.Compute | Format-Table
```


### <a name="dedicated-host"></a>Toegewezen host

Controleren op updates die in behandeling zijn voor een specifieke host. In dit voor beeld wordt de uitvoer opgemaakt als een tabel voor de Lees baarheid. Vervang de waarden voor de resources door uw eigen gegevens.

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

Een aanvraag maken om updates op een geïsoleerde virtuele machine toe te passen.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute
```

Als de opdracht is voltooid, wordt een `PSApplyUpdate`-object geretourneerd. U kunt het naam kenmerk in de `Get-AzApplyUpdate` opdracht gebruiken om de update status te controleren. Zie [update status controleren](#check-update-status).

### <a name="dedicated-host"></a>Toegewezen host

Updates Toep assen op een specifieke host.

```azurepowershell-interactive
New-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myHost `
   -ResourceType hosts `
   -ResourceParentName myHostGroup `
   -ResourceParentType hostGroups `
   -ProviderName Microsoft.Compute
```

## <a name="check-update-status"></a>Update status controleren
Gebruik [Get-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/get-azapplyupdate) om de status van een update te controleren. Met de onderstaande opdrachten wordt de status van de meest recente update weer gegeven met behulp van `default` voor de para meter `-ApplyUpdateName`. U kunt de naam van de update vervangen (geretourneerd door de opdracht [New-AzApplyUpdate](https://docs.microsoft.com/powershell/module/az.maintenance/new-azapplyupdate) ) om de status van een specifieke update op te halen.

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
LastUpdateTime is de tijd waarop de update is voltooid, ofwel door u of door het platform is gestart in het geval dat het zelf onderhoud niet is gebruikt. Als er nog nooit een update is toegepast via de onderhouds controle, wordt de standaard waarde weer gegeven.

### <a name="isolated-vm"></a>Geïsoleerde VM

Controleren op updates voor een specifieke virtuele machine.

```azurepowershell-interactive
Get-AzApplyUpdate `
   -ResourceGroupName myResourceGroup `
   -ResourceName myVM `
   -ResourceType VirtualMachines `
   -ProviderName Microsoft.Compute `
   -ApplyUpdateName default
```

### <a name="dedicated-host"></a>Toegewezen host

Controleren op updates voor een specifieke host.

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

## <a name="remove-a-maintenance-configuration"></a>Een onderhouds configuratie verwijderen

Gebruik [Remove-AzMaintenanceConfiguration](https://docs.microsoft.com/powershell/module/az.maintenance/remove-azmaintenanceconfiguration) om een onderhouds configuratie te verwijderen.

```azurecli-interactive
Remove-AzMaintenanceConfiguration `
   -ResourceGroupName myResourceGroup `
   -Name $config.Name
```

## <a name="next-steps"></a>Volgende stappen
Zie [onderhoud en updates](maintenance-and-updates.md)voor meer informatie.

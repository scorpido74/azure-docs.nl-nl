---
title: Azure Hybrid Benefit voor Windows Server
description: Meer informatie over hoe u uw Windows Software Assurance-voordelen maximaliseren om on-premises licenties naar Azure te brengen
author: xujing-ms
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 4/22/2018
ms.author: xujing
ms.openlocfilehash: f84d4fcd85f1e718f414e63bbe76fd29fa32427d
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81869570"
---
# <a name="azure-hybrid-benefit-for-windows-server"></a>Azure Hybrid Benefit voor Windows Server
Voor klanten met Software Assurance u met Azure Hybrid Benefit voor Windows Server uw on-premises Windows Server-licenties gebruiken en virtuele Windows-machines op Azure uitvoeren tegen lagere kosten. U Azure Hybrid Benefit voor Windows Server gebruiken om nieuwe virtuele machines met Windows OS te implementeren. In dit artikel worden de stappen doorgenomen over het implementeren van nieuwe VM's met Azure Hybrid Benefit voor Windows Server en hoe u bestaande draaiende VM's bijwerken. Zie de [licentiepagina azure hybrid benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)voor Windows Server-licenties en kostenbesparingen voor meer informatie over Azure Hybrid Benefit for Windows Server.

Elke licentie met 2 processors of elke set licenties met 16 cores heeft recht op twee exemplaren van maximaal 8 cores, of één exemplaar van maximaal 16 cores. Het Azure Hybrid Benefit for Standard Edition-licenties kunnen slechts eenmaal on-premises of in Azure worden gebruikt. Datacenter Edition-voordelen maken gelijktijdig gebruik mogelijk, zowel on-premises als in Azure.

Het gebruik van Azure Hybrid Benefit voor Windows Server met vm's met Windows Server OS wordt nu in alle regio's ondersteund, inclusief VM's met extra software zoals SQL Server of marketplace-software van derden. 


## <a name="classic-vms"></a>Klassieke VM's

Voor klassieke VM's wordt alleen het implementeren van nieuwe VM's van on-premises aangepaste afbeeldingen ondersteund. Als u wilt profiteren van de mogelijkheden die in dit artikel worden ondersteund, moet u eerst klassieke VM's migreren naar resourcebeheermodel.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]
 

## <a name="ways-to-use-azure-hybrid-benefit-for-windows-server"></a>Manieren om Azure Hybrid Benefit voor Windows Server te gebruiken
Er zijn weinig manieren om virtuele Windows-machines te gebruiken met het Azure Hybrid Benefit:

1. U VM's implementeren van een van de meegeleverde Windows Server-afbeeldingen op de Azure Marketplace
2. U een aangepaste vm uploaden en implementeren met een Resource Manager-sjabloon of Azure PowerShell
3. U bestaande VM schakelen en converteren tussen uitvoeren met Azure Hybrid Benefit of on-demand kosten betalen voor Windows Server
4. U azure hybrid benefit voor Windows Server ook toepassen op de virtuele machineschaalset


## <a name="create-a-vm-with-azure-hybrid-benefit-for-windows-server"></a>Een VM maken met Azure Hybrid Benefit voor Windows Server
Alle op Windows Server OS gebaseerde afbeeldingen worden ondersteund voor Azure Hybrid Benefit voor Windows Server. U Azure-ondersteuningsafbeeldingen voor platforms gebruiken of uw eigen aangepaste Windows Server-afbeeldingen uploaden. 

### <a name="portal"></a>Portal
Als u een VM wilt maken met Azure Hybrid Benefit voor Windows Server, gebruikt u de schakelfunctie onder de sectie 'Geld besparen'.

### <a name="powershell"></a>PowerShell


```powershell
New-AzVm `
    -ResourceGroupName "myResourceGroup" `
    -Name "myVM" `
    -Location "East US" `
    -ImageName "Win2016Datacenter" `
    -LicenseType "Windows_Server"
```

### <a name="cli"></a>CLI
```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --location eastus \
    --license-type Windows_Server
```

### <a name="template"></a>Template
Binnen uw Resource Manager-sjablonen `licenseType` moet een extra parameter worden opgegeven. U meer lezen over [het maken van Azure Resource Manager-sjablonen](../../resource-group-authoring-templates.md)
```json
"properties": {
    "licenseType": "Windows_Server",
    "hardwareProfile": {
        "vmSize": "[variables('vmSize')]"
    }
```

## <a name="convert-an-existing-vm-using-azure-hybrid-benefit-for-windows-server"></a>Een bestaande vm converteren met Azure Hybrid Benefit voor Windows Server
Als u een bestaande VM hebt die u wilt converteren om te profiteren van Azure Hybrid Benefit voor Windows Server, u het licentietype van uw VM bijwerken door de onderstaande instructies te volgen.

> [!NOTE]
> Als u het licentietype op de VM wijzigt, wordt het systeem niet opnieuw opgestart of veroorzaakt het een service-interuption.  Het is gewoon een update van een metadata vlag.
> 

### <a name="portal"></a>Portal
Vanuit het portal VM-blad u de VM bijwerken om Azure Hybrid Benefit te gebruiken door de optie 'Configuratie' te selecteren en de optie 'Hybride voordeel van Azure' in te schakelen

### <a name="powershell"></a>PowerShell
- Bestaande Windows Server VM's converteren naar Azure Hybrid Benefit voor Windows Server

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "Windows_Server"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
- Windows Server VM's converteren met voordeel terug naar betalen per gebruik

    ```powershell
    $vm = Get-AzVM -ResourceGroup "rg-name" -Name "vm-name"
    $vm.LicenseType = "None"
    Update-AzVM -ResourceGroupName rg-name -VM $vm
    ```
    
### <a name="cli"></a>CLI
- Bestaande Windows Server VM's converteren naar Azure Hybrid Benefit voor Windows Server

    ```azurecli
    az vm update --resource-group myResourceGroup --name myVM --set licenseType=Windows_Server
    ```

### <a name="how-to-verify-your-vm-is-utilizing-the-licensing-benefit"></a>Hoe u controleren of uw VM gebruikmaakt van het licentievoordeel
Nadat u uw VM hebt geïmplementeerd via powershell, resourcemanager-sjabloon of portal, u de instelling in de volgende methoden verifiëren.

### <a name="portal"></a>Portal
Vanuit het vm-blad van portal u de optie voor Azure Hybrid Benefit voor Windows Server bekijken door het tabblad 'Configuratie' te selecteren.

### <a name="powershell"></a>PowerShell
In het volgende voorbeeld wordt het licentietype voor één virtuele virtuele machine weergegeven
```powershell
Get-AzVM -ResourceGroup "myResourceGroup" -Name "myVM"
```

Uitvoer:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Server
```

Deze uitvoer staat in contrast met de volgende VM die is geïmplementeerd zonder Azure Hybrid Benefit voor Windows Server-licenties:
```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

### <a name="cli"></a>CLI
```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVM --query "[?licenseType=='Windows_Server']" -o table
```

> [!NOTE]
> Als u het licentietype op de VM wijzigt, wordt het systeem niet opnieuw opgestart of veroorzaakt het een service-interuption. Het is alleen een licentievlag voor metagegevens.
>

## <a name="list-all-vms-with-azure-hybrid-benefit-for-windows-server-in-a-subscription"></a>Alle VM's met Azure Hybrid Benefit voor Windows Server in een abonnement weergeven
Als u alle virtuele machines wilt bekijken en tellen die zijn geïmplementeerd met Azure Hybrid Benefit voor Windows Server, u de volgende opdracht uitvoeren vanuit uw abonnement:

### <a name="portal"></a>Portal
Vanuit het resourceblad voor virtuele machines of virtuele machines u een lijst met al uw VM's en licentietypen bekijken door de tabelkolom te configureren om 'Azure Hybrid Benefit' op te nemen. De VM-instelling kan de status 'Ingeschakeld', 'Niet ingeschakeld' of 'Niet ondersteund' zijn.

### <a name="powershell"></a>PowerShell
```powershell
$vms = Get-AzVM
$vms | ?{$_.LicenseType -like "Windows_Server"} | select ResourceGroupName, Name, LicenseType
```

### <a name="cli"></a>CLI
```azurecli
az vm list --query "[?licenseType=='Windows_Server']" -o table
```

## <a name="deploy-a-virtual-machine-scale-set-with-azure-hybrid-benefit-for-windows-server"></a>Een virtuele machineschaalset implementeren met Azure Hybrid Benefit voor Windows Server
Binnen uw resourcebeheersjablonen voor virtuele machineschaalmoet een extra parameter `licenseType` worden opgegeven binnen de eigenschap VirtualMachineProfile. U dit doen tijdens het maken of bijwerken van uw schaalset via ARM-sjabloon, PowerShell, Azure CLI of REST.

In het volgende voorbeeld wordt de ARM-sjabloon gebruikt met een Afbeelding van het Datacenter van Windows Server 2016:
```json
"virtualMachineProfile": {
    "storageProfile": {
        "osDisk": {
            "createOption": "FromImage"
        },
        "imageReference": {
            "publisher": "MicrosoftWindowsServer",
            "offer": "WindowsServer",
            "sku": "2016-Datacenter",
            "version": "latest"
        }
    },
    "licenseType": "Windows_Server",
    "osProfile": {
            "computerNamePrefix": "[parameters('vmssName')]",
            "adminUsername": "[parameters('adminUsername')]",
            "adminPassword": "[parameters('adminPassword')]"
    }
```
U ook meer informatie vinden over het [wijzigen van een virtuele machineschaalset](../../virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set.md) voor meer manieren om uw schaalset bij te werken.

## <a name="next-steps"></a>Volgende stappen
- Lees meer over [Hoe u geld besparen met het Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/)
- Lees meer over [veelgestelde vragen voor Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-use-benefit/faq/)
- Meer informatie over gedetailleerde richtlijnen voor [Azure Hybrid Benefit voor Windows Server-licenties](https://docs.microsoft.com/windows-server/get-started/azure-hybrid-benefit)
- Meer informatie over [Azure Hybrid Benefit voor Windows Server en Azure Site Recovery maken het migreren van toepassingen naar Azure nog rendabeler](https://azure.microsoft.com/blog/hybrid-use-benefit-migration-with-asr/)
- Meer informatie over [Windows 10 op Azure met Multitenant Hosting Right](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)
- Meer informatie over [Het gebruik van Resource Manager-sjablonen](../../azure-resource-manager/management/overview.md)

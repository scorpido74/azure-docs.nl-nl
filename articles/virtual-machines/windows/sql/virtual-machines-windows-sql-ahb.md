---
title: Het licentie model voor een SQL Server virtuele machine in azure wijzigen
description: Meer informatie over het scha kelen tussen licenties voor een SQL Server virtuele machine in azure van betalen per gebruik naar uw eigen licentie met behulp van de Azure Hybrid Benefit.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/05/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: aac20034fb4a528e48d5b383f39205a952878539
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/24/2019
ms.locfileid: "72900693"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Het licentie model voor een SQL Server virtuele machine in azure wijzigen
In dit artikel wordt beschreven hoe u het licentie model voor een SQL Server virtuele machine (VM) in azure wijzigt met behulp van de nieuwe SQL VM-resource provider **micro soft. SqlVirtualMachine**.

Er zijn twee licentie modellen voor een virtuele machine die als host fungeert voor SQL Server: betalen per gebruik en Azure Hybrid Benefit. U kunt het licentie model van uw SQL Server-VM wijzigen met behulp van de Azure Portal, de Azure CLI of Power shell. 

Het betalen naar gebruik-model betekent dat de kosten per seconde van het uitvoeren van de Azure-VM de kosten van de SQL Server licentie bevatten.
Met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) kunt u uw eigen SQL Server licentie gebruiken met een VM waarop SQL Server wordt uitgevoerd. 

Azure Hybrid Benefit staat het gebruik toe van SQL Server licenties met Software Assurance (' gekwalificeerde licentie ') op virtuele machines van Azure. Met Azure Hybrid Benefit worden klanten niet in rekening gebracht voor het gebruik van een SQL Server licentie op een virtuele machine. Ze moeten echter wel betalen voor de kosten van de onderliggende Cloud Compute (dat wil zeggen, het basis tarief), opslag en back-ups. Ze moeten ook betalen voor I/O die is gekoppeld aan het gebruik van de Services (indien van toepassing).

Volgens de product termen van micro soft: ' klanten moeten aangeven dat ze Azure SQL Database (beheerde instantie, Elastische pool en Individuele database), Azure Data Factory, SQL Server Integration Services of SQL Server Virtual Machines onder Azure Hybride voor deel voor SQL Server bij het configureren van werk belastingen op Azure. "

Om het gebruik van Azure Hybrid Benefit voor SQL Server op een virtuele Azure-machine aan te geven en compatibel te zijn, hebt u drie opties:

- Richt een virtuele machine in met behulp van een SQL Server installatie kopie van uw eigen licentie vanuit Azure Marketplace. Deze optie is alleen beschikbaar voor klanten die een Enterprise Agreement hebben.
- Richt een virtuele machine in met behulp van een betalen naar gebruik-SQL Server installatie kopie van Azure Marketplace en activeer Azure Hybrid Benefit.
- Installeer SQL Server zelf op een virtuele machine van Azure, [Registreer de SQL Server-VM](virtual-machines-windows-sql-register-with-resource-provider.md)hand matig en activeer Azure Hybrid Benefit.

Het licentie type van SQL Server wordt ingesteld wanneer de virtuele machine wordt ingericht. U kunt deze later op elk gewenst moment wijzigen. Als u overschakelt tussen licentie modellen, wordt geen downtime gestart, wordt de VM niet opnieuw opgestart, worden er geen extra kosten in rekening gebracht en direct van kracht. Het activeren van Azure Hybrid Benefit *verlaagt* de kosten.

## <a name="prerequisites"></a>Vereisten

Voor het gebruik van de resource provider van de SQL-VM is de SQL Server IaaS-extensie vereist. Als zodanig hebt u het volgende nodig:
- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default). 
- Een [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) die is geregistreerd bij de [resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)van de SQL-VM.


## <a name="change-the-license-for-vms-already-registered-with-the-resource-provider"></a>De licentie wijzigen voor Vm's die al zijn geregistreerd bij de resource provider 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U kunt het licentie model rechtstreeks vanuit de portal wijzigen: 

1. Open de [Azure Portal](https://portal.azure.com) en open de [resource van de virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) voor uw SQL Server VM. 
1. Selecteer **configureren** onder **instellingen**. 
1. Selecteer de optie **Azure Hybrid Benefit** en schakel het selectie vakje in om te bevestigen dat u een SQL Server licentie hebt met Software Assurance. 
1. Selecteer **Toep assen** onder aan de pagina **configureren** . 

![Azure Hybrid Benefit in de portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

U kunt de Azure CLI gebruiken om uw licentie model te wijzigen.  

Het volgende code fragment wisselt uw licentie model voor betalen per gebruik naar uw eigen licentie (of met behulp van Azure Hybrid Benefit):

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

Met het volgende code fragment kunt u uw eigen licentie model overzetten naar betalen per gebruik: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
U kunt Power shell gebruiken om uw licentie model te wijzigen.

Het volgende code fragment wisselt uw licentie model voor betalen per gebruik naar uw eigen licentie (of met behulp van Azure Hybrid Benefit):

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="AHUB"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```

Met het volgende code fragment kunt u uw eigen licentie model overzetten naar betalen per gebruik:

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
#example: $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName AHBTest -ResourceName AHBTest
$SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
$SqlVm.Properties.sqlServerLicenseType="PAYG"
<# the following code snippet is only necessary if using Azure Powershell version > 4
$SqlVm.Kind= "LicenseChange"
$SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
$SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new() #>
$SqlVm | Set-AzResource -Force 
```
---

## <a name="change-the-license-for-vms-not-registered-with-the-resource-provider"></a>De licentie wijzigen voor Vm's die niet zijn geregistreerd bij de resource provider

Als u een SQL Server VM hebt ingericht op basis van betalen per gebruik, Azure Marketplace-installatie kopieën, wordt het SQL Server licentie type betalen naar gebruik. Als u een SQL Server-VM hebt ingericht met behulp van een kopie van uw eigen licentie via Azure Marketplace, wordt het licentie type AHUB. Alle SQL Server Vm's die zijn ingericht op basis van standaard (betalen per gebruik) of uw eigen licentie Azure Marketplace-installatie kopieën worden automatisch geregistreerd bij de resource provider van de SQL-VM, zodat ze het [licentie type](#change-the-license-for-vms-already-registered-with-the-resource-provider)kunnen wijzigen.

U kunt SQL Server zelf installeren op een virtuele Azure-machine via Azure Hybrid Benefit. U moet [deze vm's registreren bij de resource provider](virtual-machines-windows-sql-register-with-resource-provider.md) van de SQL-VM door de SQL Server-licentie in te stellen als Azure Hybrid Benefit, om aan te geven dat het Azure Hybrid Benefit gebruik is gebaseerd op de product voorwaarden van micro soft.

U kunt het licentie type van een SQL Server-VM wijzigen als betalen naar gebruik of alleen Azure Hybrid Benefit als de SQL Server VM is geregistreerd bij de resource provider van de SQL-VM.

## <a name="remarks"></a>Opmerkingen

- Azure Cloud Solution Provider-klanten (CSP) kunnen de Azure Hybrid Benefit gebruiken door eerst een ' betalen per gebruik-VM ' te implementeren en deze vervolgens te converteren naar uw eigen licentie, als ze actieve Software Assurance hebben.
- Als u uw SQL Server VM-resource weghaalt, gaat u terug naar de hardcoded licentie-instelling van de installatie kopie. 
- De mogelijkheid om het licentie model te wijzigen, is een functie van de resource provider van de SQL-VM. Als u een installatie kopie van Azure Marketplace implementeert via de Azure Portal, wordt automatisch een SQL Server VM geregistreerd bij de resource provider. Klanten die zelf een SQL Server installeren, moeten [hun SQL Server VM](virtual-machines-windows-sql-register-with-resource-provider.md)hand matig registreren. 
- Voor het toevoegen van een SQL Server-VM aan een beschikbaarheidsset moet de virtuele machine opnieuw worden gemaakt. Als zodanig worden Vm's die aan een beschikbaarheidsset zijn toegevoegd, teruggestuurd naar het standaard licentie type voor betalen naar gebruik. Azure Hybrid Benefit moet opnieuw worden ingeschakeld. 


## <a name="limitations"></a>Beperkingen

- Het wijzigen van het licentie model is alleen beschikbaar voor klanten met Software Assurance.
- Het wijzigen van het licentie model wordt alleen ondersteund voor de Standard-en Enter prise-edities van SQL Server. Licentie wijzigingen voor Express, Web en Developer worden niet ondersteund. 
- Het wijzigen van het licentie model wordt alleen ondersteund voor virtuele machines die zijn geïmplementeerd via het Azure Resource Manager model. Vm's die via het klassieke model zijn geïmplementeerd, worden niet ondersteund. U kunt uw VM migreren van klassiek naar het Resource Manager-model en deze registreren bij de resource provider van de SQL-VM. Nadat de virtuele machine is geregistreerd bij de resource provider van de SQL-VM, zijn de wijzigingen van het licentie model beschikbaar op de virtuele machine.
- Het wijzigen van het licentie model is alleen ingeschakeld voor open bare Cloud installaties.
- Het wijzigen van het licentie model wordt alleen ondersteund op virtuele machines met één NIC (netwerk interface). Op virtuele machines met meer dan één NIC moet u eerst een van de Nic's (met behulp van de Azure Portal) verwijderen voordat u de procedure probeert uit te voeren. Anders wordt er een fout bericht van de volgende strekking weer gegeven: 
   
  `The virtual machine '\<vmname\>' has more than one NIC associated.` 
   
  Hoewel het mogelijk is om de NIC weer aan de virtuele machine toe te voegen nadat u het licentie model hebt gewijzigd, worden bewerkingen die zijn uitgevoerd via de pagina SQL Server configuratie in de Azure Portal, zoals automatische patching en back-up, niet meer als ondersteund beschouwd.

## <a name="known-errors"></a>Bekende fouten

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>De resource micro soft. SqlVirtualMachine/SqlVirtualMachines/\<resource-group > onder resource groep\<resource group > is niet gevonden.
Deze fout treedt op wanneer u probeert het licentie model op een SQL Server virtuele machine te wijzigen die niet is geregistreerd bij de resource provider van de SQL-VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

U moet uw abonnement registreren bij de resource provider en vervolgens [uw SQL Server-VM registreren bij de resource provider](virtual-machines-windows-sql-register-with-resource-provider.md). 

### <a name="cannot-validate-argument-on-parameter-sku"></a>Kan argument niet valideren voor de para meter ' SKU '
Deze fout kan optreden wanneer u probeert om uw SQL Server VM-licentie model te wijzigen met behulp van Azure PowerShell-versies later dan 4,0:

`Set-AzResource: Cannot validate argument on parameter 'Sku'. The argument is null or empty. Provide an argument that is not null or empty, and then try the command again.`

Als u deze fout wilt oplossen, moet u de opmerkingen van deze regels in het eerder genoemde Power shell-code fragment opheffen wanneer u uw licentie model wijzigt:

  ```powershell-interactive
  # the following code snippet is necessary if using Azure Powershell version > 4
  $SqlVm.Kind= "LicenseChange"
  $SqlVm.Plan= [Microsoft.Azure.Management.ResourceManager.Models.Plan]::new()
  $SqlVm.Sku= [Microsoft.Azure.Management.ResourceManager.Models.Sku]::new()
  ```
  
Gebruik de volgende code om uw Azure PowerShell-versie te controleren:
  
  ```powershell-interactive
  Get-Module -ListAvailable -Name Azure -Refresh
  ```

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)



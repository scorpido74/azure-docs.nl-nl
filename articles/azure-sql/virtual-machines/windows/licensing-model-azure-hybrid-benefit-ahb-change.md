---
title: Het licentie model voor een SQL-VM in azure wijzigen
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
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 07f5896ab43cd02abc9c83667763c1989355b8bc
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/30/2020
ms.locfileid: "84219415"
---
# <a name="change-the-license-model-for-a-sql-virtual-machine-in-azure"></a>Het licentie model voor een virtuele SQL-machine wijzigen in azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]


In dit artikel wordt beschreven hoe u het licentie model voor een SQL Server virtuele machine (VM) in azure wijzigt met behulp van de nieuwe SQL Server VM-resource provider **micro soft. SqlVirtualMachine**.

Er zijn drie licentie modellen voor een virtuele machine die als host fungeert voor SQL Server: betalen naar gebruik, Azure Hybrid Benefit (AHB) en nood herstel (DR). U kunt het licentie model van uw SQL Server-VM wijzigen met behulp van de Azure Portal, de Azure CLI of Power shell. 

- Het **betalen naar gebruik** -model betekent dat de kosten per seconde van het uitvoeren van de Azure-VM de kosten van de SQL Server licentie bevatten.
- Met [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) kunt u uw eigen SQL Server licentie gebruiken met een VM waarop SQL Server wordt uitgevoerd. 
- Het licentie type voor **herstel na nood gevallen** wordt gebruikt voor de [gratis Dr-replica](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure) in Azure. 

Azure Hybrid Benefit staat het gebruik toe van SQL Server licenties met Software Assurance (' gekwalificeerde licentie ') op virtuele machines van Azure. Met Azure Hybrid Benefit worden klanten niet in rekening gebracht voor het gebruik van een SQL Server licentie op een virtuele machine. Ze moeten echter wel betalen voor de kosten van de onderliggende Cloud Compute (dat wil zeggen, het basis tarief), opslag en back-ups. Ze moeten ook betalen voor I/O die is gekoppeld aan het gebruik van de Services (indien van toepassing).

Volgens de micro soft-product termen: ' klanten moeten aangeven dat ze Azure SQL Database (beheerde instantie, Elastische pool en Individuele database), Azure Data Factory, SQL Server Integration Services of SQL Server virtual machines onder Azure Hybrid Benefit voor SQL Server worden gebruikt bij het configureren van werk belastingen op Azure. "

Om het gebruik van Azure Hybrid Benefit voor SQL Server op Azure VM aan te geven en compatibel te zijn, hebt u drie opties:

- Richt een virtuele machine in met behulp van een SQL Server installatie kopie van uw eigen licentie vanuit Azure Marketplace. Deze optie is alleen beschikbaar voor klanten die een Enterprise Agreement hebben.
- Richt een virtuele machine in met behulp van een betalen naar gebruik-SQL Server installatie kopie van Azure Marketplace en activeer de Azure Hybrid Benefit.
- Zelf SQL Server installeren op de virtuele machine van Azure, hand matig [registreren bij de resource provider van de SQL-VM](sql-vm-resource-provider-register.md)en Azure Hybrid Benefit activeren.

Het licentie type van SQL Server kan worden geconfigureerd wanneer de virtuele machine wordt ingericht of op elk gewenst moment. Als u overschakelt tussen licentie modellen, wordt geen downtime gestart, wordt de VM of de SQL Server-service niet opnieuw opgestart, worden er geen extra kosten in rekening gebracht en direct van kracht. Het activeren van Azure Hybrid Benefit *verlaagt* de kosten.

## <a name="prerequisites"></a>Vereisten

Het wijzigen van het licentie model van uw SQL Server virtuele machine heeft de volgende vereisten: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) die is geregistreerd bij de [resource provider](sql-vm-resource-provider-register.md)van de SQL-VM.
- [Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) is een vereiste voor het gebruik van de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/). 


## <a name="vms-already-registered-with-the-resource-provider"></a>Vm's die al zijn geregistreerd bij de resource provider 

# <a name="the-azure-portal"></a>[Azure Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U kunt het licentie model rechtstreeks vanuit de portal wijzigen: 

1. Open de [Azure Portal](https://portal.azure.com) en open de [resource van de virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) voor uw SQL Server VM. 
1. Selecteer **configureren** onder **instellingen**. 
1. Selecteer de optie **Azure Hybrid Benefit** en schakel het selectie vakje in om te bevestigen dat u een SQL Server licentie hebt met Software Assurance. 
1. Selecteer **Toep assen** onder aan de pagina **configureren** . 

![Azure Hybrid Benefit in de portal](./media/licensing-model-azure-hybrid-benefit-ahb-change/ahb-in-portal.png)


# <a name="the-azure-cli"></a>[De Azure CLI](#tab/azure-cli)

U kunt de Azure CLI gebruiken om uw licentie model te wijzigen.  


**Azure Hybrid Benefit**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Betalen**naar gebruik: 

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type PAYG

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type PAYG
```

**Herstel na noodgeval (DR; disaster recovery)**

```azurecli-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
# example: az sql vm update -n AHBTest -g AHBTest --license-type DR

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type DR
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

U kunt Power shell gebruiken om uw licentie model te wijzigen.

**Azure Hybrid Benefit**

```powershell-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType AHUB
```

**Betalen per gebruik**

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType PAYG
```

**Herstel na noodgevallen** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>Vm's die niet zijn geregistreerd bij de resource provider

Als u een SQL Server VM hebt ingericht op basis van betalen per gebruik, Azure Marketplace-installatie kopieën, wordt het SQL Server licentie type betalen naar gebruik. Als u een SQL Server virtuele machine hebt ingericht met behulp van een kopie van uw eigen licentie via Azure Marketplace, wordt het licentie type AHUB. Alle SQL Server Vm's die zijn ingericht op basis van standaard (betalen per gebruik) of uw eigen licentie Azure Marketplace-installatie kopieën worden automatisch geregistreerd bij de resource provider van de SQL-VM, zodat ze het [licentie type](#vms-already-registered-with-the-resource-provider)kunnen wijzigen.

U bent alleen in aanmerking voor automatische installatie van SQL Server op Azure VM via Azure Hybrid Benefit. U moet [deze vm's registreren bij de resource provider](sql-vm-resource-provider-register.md) van de SQL-VM door de SQL Server-licentie in te stellen als Azure Hybrid Benefit, om aan te geven dat het Azure Hybrid Benefit gebruik is gebaseerd op de product voorwaarden van micro soft.

U kunt het licentie type van een SQL Server-VM wijzigen als betalen naar gebruik of alleen Azure Hybrid Benefit als de SQL Server VM is geregistreerd bij de resource provider van de SQL-VM.

## <a name="remarks"></a>Opmerkingen

- Azure Cloud Solution Provider-klanten (CSP) kunnen de Azure Hybrid Benefit gebruiken door eerst een ' betalen per gebruik-VM ' te implementeren en deze vervolgens te converteren naar uw eigen licentie, als ze actieve Software Assurance hebben.
- Als u uw SQL Server VM-resource weghaalt, gaat u terug naar de hardcoded licentie-instelling van de installatie kopie. 
- De mogelijkheid om het licentie model te wijzigen, is een functie van de resource provider van de SQL-VM. Als u een installatie kopie van Azure Marketplace implementeert via de Azure Portal, wordt automatisch een SQL Server VM geregistreerd bij de resource provider. Klanten die zelf een SQL Server installeren, moeten [hun SQL Server VM](sql-vm-resource-provider-register.md)hand matig registreren. 
- Voor het toevoegen van een SQL Server-VM aan een beschikbaarheidsset moet de virtuele machine opnieuw worden gemaakt. Als zodanig worden Vm's die aan een beschikbaarheidsset zijn toegevoegd, teruggestuurd naar het standaard licentie type voor betalen naar gebruik. Azure Hybrid Benefit moet opnieuw worden ingeschakeld. 


## <a name="limitations"></a>Beperkingen

Het wijzigen van het licentie model is:
   - Alleen beschikbaar voor klanten met [Software Assurance](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview).
   - Alleen ondersteund voor de Standard-en Enter prise-edities van SQL Server. Licentie wijzigingen voor Express, Web en Developer worden niet ondersteund. 
   - Alleen ondersteund voor virtuele machines die zijn geïmplementeerd via het Azure Resource Manager model. Virtuele machines die via het klassieke model zijn geïmplementeerd, worden niet ondersteund. 
   - Alleen beschikbaar voor de open bare of Azure Government Clouds. 
   - Alleen ondersteund op virtuele machines met één netwerk interface (NIC). 


## <a name="known-errors"></a>Bekende fouten

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>De resource ' micro soft. SqlVirtualMachine/SqlVirtualMachines/ \<resource-group> ' onder de resource groep \<resource-group> is niet gevonden.

Deze fout treedt op wanneer u probeert het licentie model op een SQL Server virtuele machine te wijzigen die niet is geregistreerd bij de resource provider van de SQL-VM:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

U moet uw abonnement registreren bij de resource provider en vervolgens [uw SQL Server-VM registreren bij de resource provider](sql-vm-resource-provider-register.md). 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>Aan de virtuele machine zijn \<vmname\> meer dan één NIC gekoppeld

Deze fout doet zich voor op virtuele machines met meer dan één NIC. Verwijder een van de Nic's voordat u het licentie model wijzigt. Hoewel u de NIC opnieuw kunt toevoegen aan de VM nadat u het licentie model hebt gewijzigd, worden de bewerkingen in de Azure Portal, zoals automatische back-up en patching, niet meer ondersteund. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](../../database/doc-changes-updates-release-notes.md)



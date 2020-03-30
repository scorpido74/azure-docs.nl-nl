---
title: Het licentiemodel voor een SQL Server VM in Azure wijzigen
description: Lees hoe u licenties voor een virtuele SQL Server-machine in Azure overschakelen van pay-as-you-go naar bring-your-own-license met behulp van het Azure Hybrid Benefit.
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
ms.openlocfilehash: 502d1fe599accb29ccc99c9e527f8d1c8e1d52b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201816"
---
# <a name="change-the-license-model-for-a-sql-server-virtual-machine-in-azure"></a>Het licentiemodel voor een virtuele SQL Server-machine in Azure wijzigen
In dit artikel wordt beschreven hoe u het licentiemodel voor een VIRTUELE SQL Server -virtuele machine (VM) in Azure wijzigen met behulp van de nieuwe SQL VM-bronprovider **Microsoft.SqlVirtualMachine**.

Er zijn drie licentiemodellen voor een VM die SQL Server host: pay-as-you-go, Azure Hybrid Benefit en disaster recovery (DR). U het licentiemodel van uw SQL Server VM wijzigen met behulp van de Azure-portal, de Azure CLI of PowerShell. 

- Het **pay-as-you-go-model** betekent dat de kosten per seconde voor het uitvoeren van de Azure VM de kosten van de SQL Server-licentie omvatten.
- [Met Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/) u uw eigen SQL Server-licentie gebruiken met een VM waarop SQL Server wordt uitgevoerd. 
- Het type **disaster recovery-licentie** wordt gebruikt voor de [gratis DR-replica](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure) in Azure. 

Azure Hybrid Benefit maakt het gebruik van SQL Server-licenties met Software Assurance ('Gekwalificeerde licentie') op virtuele Azure-machines mogelijk. Met Azure Hybrid Benefit worden klanten niet in rekening gebracht voor het gebruik van een SQL Server-licentie op een VM. Maar ze moeten nog steeds betalen voor de kosten van de onderliggende cloud compute (dat wil zeggen, de basissnelheid), opslag en back-ups. Zij moeten ook betalen voor I/O in verband met hun gebruik van de diensten (indien van toepassing).

Volgens de Microsoft-productvoorwaarden: "Klanten moeten aangeven dat ze Azure SQL Database (Managed Instance, Elastic Pool en Single Database), Azure Data Factory, SQL Server Integration Services of SQL Server Virtual Machines onder Azure Hybride voordeel voor SQL Server bij het configureren van workloads op Azure."

Als u het gebruik van Azure Hybrid Benefit voor SQL Server op een Azure VM wilt aangeven en compatibel wilt zijn, hebt u drie opties:

- Inrichten van een virtuele machine met behulp van een SQL Server-afbeelding met uw eigen licentie van Azure Marketplace. Deze optie is alleen beschikbaar voor klanten die een Enterprise Agreement hebben.
- Inrichten van een virtuele machine met behulp van een pay-as-you-go SQL Server-afbeelding van Azure Marketplace en activeer het Azure Hybrid Benefit.
- Installeer SQL Server zelf op een Azure VM, registreer u handmatig [bij de SQL VM-bronprovider](virtual-machines-windows-sql-register-with-resource-provider.md)en activeer Azure Hybrid Benefit.

Het licentietype SQL Server wordt ingesteld wanneer de VM is ingericht. U het op elk gewenst moment daarna wijzigen. Het schakelen tussen licentiemodellen leidt niet tot downtime, start de VM of de SQL Server-service niet opnieuw op, voegt geen extra kosten toe en is onmiddellijk van kracht. Het activeren van Azure Hybrid Benefit *verlaagt de* kosten.

## <a name="prerequisites"></a>Vereisten

Als u het licentiemodel van uw SQL Server VM wijzigt, gelden de volgende vereisten: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision) die is geregistreerd bij de SQL [VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md).
- [Software assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default) is een vereiste om het [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)te gebruiken. 


## <a name="vms-already-registered-with-the-resource-provider"></a>VM's die al zijn geregistreerd bij de resourceprovider 

# <a name="portal"></a>[Portal](#tab/azure-portal)

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

U het licentiemodel rechtstreeks vanuit de portal wijzigen: 

1. Open de [Azure-portal](https://portal.azure.com) en open de [SQL virtual machines-bron](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) voor uw SQL Server VM. 
1. Selecteer **Configureren** onder **Instellingen**. 
1. Schakel de optie **Azure Hybrid Benefit in** en schakel het selectievakje in om te bevestigen dat u een SQL Server-licentie hebt met Software Assurance. 
1. Selecteer **Toepassen** onder aan de pagina **Configureren.** 

![Azure Hybrid Benefit in de portal](media/virtual-machines-windows-sql-ahb/ahb-in-portal.png)


# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

U de Azure CLI gebruiken om uw licentiemodel te wijzigen.  


**Azure hybride voordeel**

```azurecli-interactive
# Switch your SQL Server VM license from pay-as-you-go to bring-your-own
# example: az sql vm update -n AHBTest -g AHBTest --license-type AHUB

az sql vm update -n <VMName> -g <ResourceGroupName> --license-type AHUB
```

**Betaal naar eigen zeggen:** 

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

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

U PowerShell gebruiken om uw licentiemodel te wijzigen.

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

**Noodherstel** 

```powershell-interactive
# Switch your SQL Server VM license from bring-your-own to pay-as-you-go
Update-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name> -LicenseType DR
```

---

## <a name="vms-not-registered-with-the-resource-provider"></a>VM's die niet zijn geregistreerd bij de resourceprovider

Als u een SQL Server VM hebt ingericht op basis van pay-as-you-go Azure Marketplace-afbeeldingen, is het SQL Server-licentietype betalen per gebruik. Als u een SQL Server VM hebt ingericht met behulp van een bring-your-own-license-afbeelding van Azure Marketplace, is het licentietype AHUB. Alle SQL Server VM's die zijn ingericht vanaf standaard (pay-as-you-go) of Azure Marketplace-afbeeldingen met bring-your-own-license worden automatisch geregistreerd bij de SQL VM-resourceprovider, zodat ze het [licentietype](#vms-already-registered-with-the-resource-provider)kunnen wijzigen.

U komt alleen in aanmerking voor zelfinstallatie van SQL Server op een Azure VM via Azure Hybrid Benefit. U moet [deze VM's registreren bij de SQL VM-resourceprovider](virtual-machines-windows-sql-register-with-resource-provider.md) door de SQL Server-licentie in te stellen als Azure Hybrid Benefit om het Azure Hybrid Benefit-gebruik aan te geven volgens microsoft-productvoorwaarden.

U het licentietype van een SQL Server VM alleen wijzigen als pay-as-you-go- of Azure Hybrid Benefit als de SQL Server VM is geregistreerd bij de SQL VM-resourceprovider.

## <a name="remarks"></a>Opmerkingen

- Klanten van Azure Cloud Solution Provider (CSP) kunnen het Azure Hybrid Benefit gebruiken door eerst een pay-as-you-go-VM te implementeren en deze vervolgens om te zetten naar bring-your-own-license, als ze actieve Software Assurance hebben.
- Als u uw SQL Server VM-bron laat vallen, gaat u terug naar de hard-coded licentie-instelling van de afbeelding. 
- De mogelijkheid om het licentiemodel te wijzigen is een functie van de SQL VM-resourceprovider. Als u een Azure Marketplace-afbeelding implementeert via de Azure-portal, wordt automatisch een SQL Server VM bij de resourceprovider geregistreerd. Maar klanten die SQL Server zelf installeren, moeten hun SQL Server VM handmatig [registreren.](virtual-machines-windows-sql-register-with-resource-provider.md) 
- Als u een SQL Server VM toevoegt aan een beschikbaarheidsset, moet de VM opnieuw worden gemaakt. Als zodanig gaan alle VM's die aan een beschikbaarheidsset zijn toegevoegd, terug naar het standaard licentietype voor betalen per gebruik. Azure Hybrid Benefit moet opnieuw worden ingeschakeld. 


## <a name="limitations"></a>Beperkingen

Het licentiemodel wijzigen is:
   - Alleen beschikbaar voor klanten met [Software Assurance.](https://www.microsoft.com/en-us/licensing/licensing-programs/software-assurance-overview)
   - Alleen ondersteund voor de Standard- en Enterprise-edities van SQL Server. Licentiewijzigingen voor Express, Web en Ontwikkelaar worden niet ondersteund. 
   - Alleen ondersteund voor virtuele machines die worden geïmplementeerd via het Azure Resource Manager-model. Virtuele machines die via het klassieke model worden geïmplementeerd, worden niet ondersteund. 
   - Alleen beschikbaar voor openbare of Azure Government-clouds. 
   - Alleen ondersteund op virtuele machines met één netwerkinterface (NIC). 


## <a name="known-errors"></a>Bekende fouten

### <a name="the-resource-microsoftsqlvirtualmachinesqlvirtualmachinesresource-group-under-resource-group-resource-group-was-not-found"></a>De Bron 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>'\<onder resource group ' resource-group>' is niet gevonden.

Deze fout treedt op wanneer u het licentiemodel op een SQL Server VM probeert te wijzigen die niet is geregistreerd bij de SQL VM-bronprovider:

`The Resource 'Microsoft.SqlVirtualMachine/SqlVirtualMachines/\<resource-group>' under resource group '\<resource-group>' was not found. The property 'sqlServerLicenseType' cannot be found on this object. Verify that the property exists and can be set.`

U moet uw abonnement registreren bij de resourceprovider en [vervolgens uw SQL Server VM registreren bij de resourceprovider.](virtual-machines-windows-sql-register-with-resource-provider.md) 


### <a name="the-virtual-machine-vmname-has-more-than-one-nic-associated"></a>De virtuele\<machine\>' vmname ' heeft meer dan een NIC geassocieerd

Deze fout treedt op bij virtuele machines met meer dan één NIC. Verwijder een van de NIC's voordat u het licentiemodel wijzigt. Hoewel u de NIC terug toevoegen aan de VM nadat u het licentiemodel hebt gewijzigd, worden bewerkingen in de Azure-portal, zoals automatische back-up en patchen, niet langer ondersteund. 


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-vm](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server op een Windows-vm](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notities voor SQL Server vrijgeven op een Windows-vm](virtual-machines-windows-sql-server-iaas-release-notes.md)



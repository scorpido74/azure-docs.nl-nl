---
title: Beheer taken automatiseren op virtuele machines van Azure met behulp van de SQL Server IaaS agent-extensie | Microsoft Docs
description: In dit artikel wordt beschreven hoe u de SQL Server IaaS agent-extensie beheert, waarmee specifieke SQL Server beheer taken worden geautomatiseerd. Dit zijn onder andere automatische back-ups, automatische patching en integratie van Azure Key Vault.
services: virtual-machines-windows
documentationcenter: ''
author: MashaMSFT
manager: jroth
editor: ''
tags: azure-resource-manager
ms.assetid: effe4e2f-35b5-490a-b5ef-b06746083da4
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 59b5138950e0fb94ea0051fa9cfe9aa75cd7d770
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877795"
---
# <a name="automate-management-tasks-on-azure-virtual-machines-by-using-the-sql-server-iaas-agent-extension"></a>Beheer taken automatiseren op virtuele machines van Azure met behulp van de SQL Server IaaS agent-extensie
> [!div class="op_single_selector"]
> * [Resource Manager](virtual-machines-windows-sql-server-agent-extension.md)
> * [Klassiek](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md)

De SQL Server IaaS Agent-extensie (SqlIaasExtension) wordt uitgevoerd op virtuele Azure-machines beheertaken te automatiseren. Dit artikel bevat een overzicht van de services die door de extensie worden ondersteund. Dit artikel bevat ook instructies voor de installatie, status en verwijdering van de uitbrei ding.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Als u de klassieke versie van dit artikel wilt bekijken, raadpleegt u [SQL Server IaaS agent extension for SQL Server vm's (klassiek)](../sqlclassic/virtual-machines-windows-classic-sql-server-agent-extension.md).

Er zijn drie beheer baarheids modi voor de SQL Server IaaS-extensie: 

- **Volledige** modus biedt alle functionaliteit, maar vereist dat de SQL Server-en systeem beheerders machtigingen opnieuw worden gestart. Dit is de optie die standaard wordt geïnstalleerd. Gebruik dit voor het beheren van een SQL Server virtuele machine met één exemplaar. 

- Voor **licht gewicht** is het opnieuw opstarten van SQL Server niet vereist, maar het ondersteunt alleen het wijzigen van het licentie type en de versie van SQL Server. Gebruik deze optie voor het SQL Server van Vm's met meerdere exemplaren of voor deelname aan een FCI (failover cluster instance). 

- Geen **agent** is toegewezen voor SQL Server 2008 en SQL Server 2008 R2 geïnstalleerd op Windows Server 2008. Zie [Windows server 2008 Registration](virtual-machines-windows-sql-register-with-resource-provider.md#register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms)(Engelstalig) voor meer informatie over het gebruik van deze modus voor uw windows server 2008-installatie kopie. 

## <a name="supported-services"></a>Ondersteunde services
De SQL Server IaaS agent-extensie ondersteunt de volgende beheer taken:

| Beheer functie | Description |
| --- | --- |
| **Automatische back-up SQL Server** |Automatiseert het plannen van back-ups voor alle data bases voor het standaard exemplaar of een [correct geïnstalleerd](virtual-machines-windows-sql-server-iaas-faq.md#administration) benoemd exemplaar van SQL Server op de virtuele machine. Zie [automatische back-up voor SQL Server in azure virtual machines (Resource Manager)](virtual-machines-windows-sql-automated-backup.md)voor meer informatie. |
| **Automatische patching SQL Server** |Hiermee configureert u een onderhouds venster waarin belang rijke Windows-updates voor uw virtuele machine kunnen worden uitgevoerd, zodat u updates kunt voor komen tijdens piek tijden voor uw werk belasting. Zie voor meer informatie [automatische patching voor SQL Server in azure virtual machines (Resource Manager)](virtual-machines-windows-sql-automated-patching.md). |
| **Integratie van Azure Key Vault** |Hiermee kunt u Azure Key Vault automatisch installeren en configureren op uw SQL Server-VM. Zie [Azure Key Vault-integratie configureren voor SQL Server op Azure virtual machines (Resource Manager)](virtual-machines-windows-ps-sql-keyvault.md)voor meer informatie. |

Nadat de uitbrei ding voor de SQL Server IaaS-agent is geïnstalleerd en wordt uitgevoerd, worden de beheer functies beschikbaar:

* In het SQL Server paneel van de virtuele machine in de Azure Portal en via Azure PowerShell voor SQL Server installatie kopieën op Azure Marketplace.
* Via Azure PowerShell voor hand matige installaties van de uitbrei ding. 

## <a name="prerequisites"></a>Vereisten
Hier volgen de vereisten voor het gebruik van de SQL Server IaaS agent-extensie op uw VM:

**Besturingssysteem**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019 

**SQL Server versie**:

* SQL Server 2008 
* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017

**Azure PowerShell**:

* [De meest recente Azure PowerShell-opdrachten downloaden en configureren](/powershell/azure/overview)

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]


## <a name="change-management-modes"></a>Beheer modi wijzigen

U kunt de huidige modus van uw SQL Server IaaS-agent weer geven met behulp van Power shell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server Vm's waarop de IaaS -uitbrei ding is geïnstalleerd, kunnen de modus naar _volledig_ bijwerken met behulp van de Azure Portal. SQL Server Vm's in de modus voor _niet-agents_ kunnen worden bijgewerkt naar _volledig_ nadat het besturings systeem is bijgewerkt naar Windows 2008 R2 of hoger. Het is niet mogelijk om te downgradeen: u moet de SQL IaaS-extensie volledig verwijderen en opnieuw installeren. 

De agent modus bijwerken naar Full: 


# <a name="azure-portaltabazure-portal"></a>[Azure-portal](#tab/azure-portal)

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de resource van de [virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selecteer uw SQL Server virtuele machine en selecteer **overzicht**. 
1. Selecteer voor SQL Server Vm's met de IaaS of de modus voor licht gewicht de optie **alleen licentie type en editie-updates zijn beschikbaar met het uitbreidings bericht van de SQL IaaS-extensie** .

   ![Selecties voor het wijzigen van de modus vanuit de portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Schakel het selectie vakje **Ik ga akkoord met het opnieuw opstarten van de SQL Server-service op de virtuele machine** in en selecteer vervolgens **bevestigen** om de IaaS-modus naar volledig bij te werken. 

    ![Selectie vakje voor het accepteren van de SQL Server-service op de virtuele machine opnieuw](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Voer het volgende AZ CLI-code fragment uit:

  ```azurecli-interactive
  # Update to full mode

  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Voer het volgende Power shell-code fragment uit:

  ```powershell-interactive
  # Update to full mode

  $SqlVm = Get-AzResource -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines -ResourceGroupName <resource_group_name> -ResourceName <VM_name>
  $SqlVm.Properties.sqlManagement="Full"
  $SqlVm | Set-AzResource -Force
  ```

---


##  <a name="installation"></a>Installatie
De SQL Server IaaS-extensie wordt geïnstalleerd wanneer u uw SQL Server virtuele machine registreert bij de [resource provider](virtual-machines-windows-sql-register-with-resource-provider.md)van de SQL-VM. Indien nodig kunt u de SQL Server IaaS-agent hand matig installeren met behulp van de volledige of lichte modus. 

De SQL Server IaaS agent-extensie in de volledige modus wordt automatisch geïnstalleerd wanneer u een van de SQL Server virtuele machine Azure Marketplace-installatie kopieën inricht met behulp van de Azure Portal. 

### <a name="install-in-full-mode"></a>Installeren in de volledige modus
De volledige modus voor de SQL Server IaaS-uitbrei ding biedt volledige beheer mogelijkheden voor één exemplaar op de SQL Server VM. Als er een standaard exemplaar is, werkt de uitbrei ding met het standaard exemplaar en wordt het beheren van andere exemplaren niet ondersteund. Als er geen standaard exemplaar maar slechts één benoemd exemplaar is, wordt het benoemde exemplaar beheerd. Als er geen standaard exemplaar is en er meerdere benoemde instanties zijn, kan de uitbrei ding niet worden geïnstalleerd. 

Installeer de SQL Server IaaS-agent met de volledige modus met behulp van Power shell:

  ```powershell-interactive
     #Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with 'Full' SQL Server IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='Full'}  
  
  ```

| Parameter | Acceptabele waarden                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` of `PAYG`     |
| &nbsp;             | &nbsp;                          |


> [!NOTE]
> Als de extensie nog niet is geïnstalleerd, wordt de SQL Server-service opnieuw gestart wanneer u de volledige extensie installeert. Om te voor komen dat de SQL Server-service opnieuw wordt gestart, installeert u in plaats daarvan de licht gewicht modus met beperkte beheer baarheid.
> 
> Als de SQL Server IaaS-extensie wordt bijgewerkt, wordt de SQL Server-service niet opnieuw gestart. 

### <a name="install-on-a-vm-with-a-single-named-sql-server-instance"></a>Installeren op een virtuele machine met één benoemde SQL Server instantie
De uitbrei ding SQL Server IaaS werkt met een benoemd exemplaar op SQL Server als het standaard exemplaar wordt verwijderd en de uitbrei ding IaaS opnieuw wordt geïnstalleerd.

Een benoemd exemplaar van SQL Server gebruiken:
   1. Implementeer een SQL Server-VM vanuit Azure Marketplace. 
   1. Verwijder de IaaS-extensie uit het [Azure Portal](https://portal.azure.com).
   1. Verwijder SQL Server volledig in de SQL Server VM.
   1. Installeer SQL Server met een benoemd exemplaar binnen de SQL Server VM. 
   1. Installeer de IaaS-extensie vanuit het Azure Portal.  


### <a name="install-in-lightweight-mode"></a>Installeren in de Lightweight-modus
De SQL Server-service wordt niet opnieuw gestart in de licht modus, maar biedt beperkte functionaliteit. 

Installeer de SQL Server IaaS-agent met de licht gewicht modus met behulp van Power shell:


  ```powershell-interactive
     /#Get the existing  Compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     #Register the SQL Server VM with the 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;sqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

| Parameter | Acceptabele waarden                        |
| :------------------| :-------------------------------|
| **sqlServerLicenseType** | `AHUB` of `PAYG`     |
| &nbsp;             | &nbsp;                          |


## <a name="get-the-status-of-the-sql-server-iaas-extension"></a>De status van de SQL Server IaaS-extensie ophalen
Een manier om te controleren of de uitbrei ding is geïnstalleerd, is de agent status weer geven in de Azure Portal. Selecteer **alle instellingen** in het venster virtuele machine en selecteer vervolgens **uitbrei dingen**. De uitbrei ding **SqlIaasExtension** wordt weer gegeven.

![Status van de uitbrei ding van de SQL Server IaaS-agent in de Azure Portal](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

U kunt ook de cmdlet **Get-AzVMSqlServerExtension** Azure PowerShell gebruiken:

   ```powershell-interactive
   Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
   ```

Met de vorige opdracht wordt bevestigd dat de agent is geïnstalleerd en algemene status informatie bevat. U krijgt specifieke status informatie over automatische back-ups en patching met behulp van de volgende opdrachten:

   ```powershell-interactive
    $sqlext = Get-AzVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings
   ```

## <a name="removal"></a>Verwijdering
In de Azure Portal, kunt u de uitbrei ding verwijderen door het beletsel teken te selecteren in het venster **extensies** van de eigenschappen van de virtuele machine. Selecteer vervolgens **Verwijderen**.

![De uitbrei ding van de SQL Server IaaS-agent in Azure Portal verwijderen](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

U kunt ook de Power shell **-cmdlet Remove-AzVMSqlServerExtension** gebruiken:

   ```powershell-interactive
    Remove-AzVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SqlIaasExtension"
   ```

## <a name="next-steps"></a>Volgende stappen
Begin met het gebruik van een van de services die door de extensie worden ondersteund. Zie de artikelen waarnaar wordt verwezen in de sectie [ondersteunde services](#supported-services) van dit artikel voor meer informatie.

Zie voor meer informatie over het uitvoeren van SQL Server op Azure Virtual Machines de [Wat is SQL Server op azure virtual machines?](virtual-machines-windows-sql-server-iaas-overview.md).


---
title: SQL Server IaaS agent-extensie installeren
description: Registreer uw Azure SQL Server-virtuele machine met de resource provider van de SQL-VM om functies in te scha kelen voor SQL Server virtuele machines die buiten Azure Marketplace zijn geïmplementeerd, evenals naleving en verbeterde beheer baarheid.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/30/2020
ms.author: mathoma
ms.reviewer: jroth
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 8099bf87410e8359894d8b40d7637451891ad664
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286137"
---
# <a name="install-sql-server-iaas-agent-extension"></a>SQL Server IaaS agent-extensie installeren
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Als u uw SQL Server-VM registreert bij de resource provider van de SQL-VM, wordt de [SQL Server IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md)geïnstalleerd. 

In dit artikel leert u hoe u een enkele SQL Server VM kunt registreren bij de resource provider van de SQL-VM. U kunt ook alle SQL Server Vm's [automatisch](sql-vm-resource-provider-automatic-registration.md) registreren of [in bulk scripts](sql-vm-resource-provider-bulk-register.md).


## <a name="overview"></a>Overzicht

Bij de registratie van de resource provider wordt de [SQL Server IaaS agent-extensie](sql-server-iaas-agent-extension-automate-management.md) geïnstalleerd en wordt de **virtuele SQL-machine** _resource_ in uw abonnement gemaakt. Dit is een _afzonderlijke_ resource van de bron van de virtuele machine. Bij het ongedaan maken van de registratie van uw SQL Server-VM van de resource provider wordt de virtuele machine van **SQL-machines** verwijderd _, maar wordt_ de werkelijke virtuele machine niet weggehaald.

Als u een Azure Marketplace-installatie kopie van SQL Server VM implementeert via de Azure Portal, wordt de SQL Server VM automatisch geregistreerd bij de resource provider. Als u echter zelf SQL Server wilt installeren op een virtuele machine van Azure of als u een virtuele machine van Azure wilt inrichten vanaf een aangepaste VHD, moet u uw SQL Server VM registreren bij de resource provider van de SQL-VM om de SQL IaaS agent-extensie te installeren. 

Als u de resource provider van de SQL-VM wilt gebruiken, moet u [uw abonnement eerst registreren bij de resource provider](#register-subscription-with-rp), waardoor de resource provider de mogelijkheid heeft om resources te maken binnen dat specifieke abonnement.

> [!IMPORTANT]
> Met de SQL IaaS agent-extensie worden gegevens verzameld voor het snelle doel om klanten optionele voor delen te geven bij het gebruik van SQL Server binnen Azure Virtual Machines. Micro soft gebruikt deze gegevens niet voor licentie controles zonder voorafgaande toestemming van de klant. Raadpleeg de [SQL Server-aanvulling voor privacy](/sql/sql-server/sql-server-privacy#non-personal-data) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren bij de resource provider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een Azure resource model [Windows Server 2008 (of hoger) virtuele machine](../../../virtual-machines/windows/quick-create-portal.md) met [SQL Server 2008 (of hoger) die is](https://www.microsoft.com/sql-server/sql-server-downloads) geïmplementeerd op de open bare of Azure Government Cloud. 
- De nieuwste versie van [Azure cli](/cli/azure/install-azure-cli) of [Azure PowerShell (mini maal 5,0)](/powershell/azure/install-az-ps). 


## <a name="register-subscription-with-rp"></a>Abonnement registreren bij RP

Als u uw SQL Server-VM wilt registreren bij de resource provider van de SQL-VM, moet u uw abonnement eerst registreren bij de resource provider. Dit geeft de resource provider van de SQL-VM de mogelijkheid om resources binnen uw abonnement te maken.  U kunt dit doen met behulp van de Azure Portal, de Azure CLI of Azure PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Open de Azure Portal en ga naar **alle services**. 
1. Ga naar **abonnementen** en selecteer het gewenste abonnement.  
1. Ga op de pagina **abonnementen** naar **resource providers**. 
1. Voer **SQL** in het filter in om de SQL-gerelateerde resource providers te openen. 
1. Selecteer **registreren** , **opnieuw registreren** of **registratie ongedaan** maken voor de  **micro soft. SqlVirtualMachine** -provider, afhankelijk van de gewenste actie. 

   ![De provider wijzigen](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Opdrachtregel

Registreer uw SQL VM-resource provider bij uw Azure-abonnement met behulp van Azure CLI of Azure PowerShell. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-with-rp"></a>Registreren bij RP

Er zijn drie beheer modi voor de [uitbrei ding van de SQL Server IaaS-agent](sql-server-iaas-agent-extension-automate-management.md#management-modes). 

Als u de uitbrei ding in de modus volledig beheer installeert, wordt de SQL Server-service opnieuw gestart. u wordt aangeraden de uitbrei ding eerst in de Lightweight-modus te installeren en vervolgens [bij te werken naar volledig](#upgrade-to-full) tijdens een onderhouds venster. 

### <a name="lightweight-management-mode"></a>Licht gewicht beheer modus

Gebruik de Azure CLI of Azure PowerShell om uw SQL Server virtuele machine bij de resource provider te registreren en de SQL IaaS-uitbrei ding in de Lightweight-modus te installeren. Hiermee wordt de SQL Server-service niet opnieuw gestart. U kunt op elk gewenst moment een upgrade uitvoeren naar de volledige modus, maar dan wordt de SQL Server-service opnieuw gestart, zodat u wordt geadviseerd te wachten tot een gepland onderhouds venster. 

Geef SQL Server licentie type op als betalen `PAYG` per gebruik () als u `AHUB` een eigen licentie wilt gebruiken, Azure Hybrid Benefit () voor het gebruik van uw eigen License of herstel na nood gevallen ( `DR` ) om de [gratis licentie](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)voor de herstel-replica te activeren.

Failover-cluster exemplaren en implementaties met meerdere exemplaren kunnen alleen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Registreer een SQL Server-VM in de licht gewicht modus met de Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registreer een SQL Server-VM in Lightweight-modus met Azure PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Volledige beheer modus

Als u uw SQL Server-VM in de volledige modus registreert, wordt de SQL Server-service opnieuw gestart. Ga door met de waarschuwing. 

Gebruik de volgende Azure PowerShell opdracht om uw SQL Server-VM rechtstreeks te registreren in de volledige modus (en mogelijk de SQL Server-service opnieuw te starten): 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Beheer modus voor niet-agent 

SQL Server 2008 en 2008 R2 geïnstalleerd op Windows Server 2008 ( _niet R2_ ) kunnen worden geregistreerd bij de resource provider van de SQL-vm in de modus niet- [agent](sql-server-iaas-agent-extension-automate-management.md#management-modes). Met deze optie wordt de naleving gegarandeerd en kan de SQL Server virtuele machine worden bewaakt in de Azure Portal met beperkte functionaliteit.

Geef `AHUB` , `PAYG` , of `DR` als **sqlLicenseType** , en `SQL2008-WS2008` of `SQL2008R2-WS2008` als de **sqlImageOffer** op. 

Als u uw SQL Server 2008 of 2008 R2 op Windows Server 2008-exemplaar wilt registreren, gebruikt u het volgende Azure CLI-of Azure PowerShell code fragment: 


# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Registreer uw virtuele SQL Server 2008-machine in de modus niet-agent met de Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registreer uw SQL Server 2008 R2 virtuele machine in de modus niet-agent met de Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Registreer uw virtuele SQL Server 2008-machine in de modus niet-agent met Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  

Registreer uw SQL Server 2008 R2 virtuele machine in de modus niet-agent met Azure PowerShell: 

  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="verify-mode"></a>Modus controleren

U kunt de huidige modus van uw SQL Server IaaS-agent weer geven met behulp van Azure PowerShell: 

```powershell-interactive
# Get the SqlVirtualMachine
$sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
$sqlvm.SqlManagementType
```

## <a name="upgrade-to-full"></a>Upgraden naar volledig  

SQL Server Vm's waarop de uitbrei ding *Lightweight* IaaS is geïnstalleerd, kunnen de modus upgraden naar _volledig_ met behulp van de Azure Portal, de Azure CLI of de Azure PowerShell. SQL Server Vm's in de modus niet- _agent_ kunnen naar _volledig_ worden bijgewerkt nadat het besturings systeem is bijgewerkt naar Windows 2008 R2 of hoger. Het is niet mogelijk om te downgradeen: u moet de registratie van de SQL Server VM bij de resource provider van de SQL-VM [ongedaan maken](#unregister-from-rp) . Als u dit doet, wordt de _bron_ van de **virtuele SQL-machine** verwijderd, maar wordt de werkelijke virtuele machine niet verwijderd. 


### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar de resource van de [virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selecteer uw SQL Server-VM en selecteer **overzicht**. 
1. Selecteer voor SQL Server Vm's met de IaaS of de modus voor licht gewicht de optie **alleen licentie type en editie-updates zijn beschikbaar met het uitbreidings bericht van de SQL IaaS-extensie** .

   ![Selecties voor het wijzigen van de modus vanuit de portal](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Schakel het selectie vakje **Ik ga akkoord met het opnieuw opstarten van de SQL Server-service op de virtuele machine** in en selecteer vervolgens **bevestigen** om de IaaS-modus naar volledig bij te werken. 

    ![Selectie vakje voor het accepteren van de SQL Server-service op de virtuele machine opnieuw](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Opdrachtregel

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)

Voer het volgende Azure CLI-code fragment uit:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Voer het volgende Azure PowerShell code fragment uit:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Registratie status verifiëren
U kunt controleren of uw SQL Server virtuele machine al is geregistreerd bij de resource provider van de SQL-VM door gebruik te maken van de Azure Portal, de Azure CLI of de Azure PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Ga naar uw [SQL Server vm's](manage-sql-vm-portal.md).
1. Selecteer uw SQL Server-VM in de lijst. Als uw SQL Server VM hier niet wordt vermeld, is deze waarschijnlijk niet geregistreerd bij de resource provider van de SQL-VM. 
1. Bekijk de waarde onder **status**. Als de **status** is **geslaagd** , is de SQL Server VM geregistreerd bij de resource provider van de SQL-VM. 

   ![Status controleren met SQL RP-registratie](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Opdrachtregel

Controleer de huidige SQL Server VM-registratie status met behulp van Azure CLI of Azure PowerShell. `ProvisioningState` geeft aan `Succeeded` of de registratie is geslaagd. 

# <a name="azure-cli"></a>[Azure-CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Een fout geeft aan dat de SQL Server VM niet is geregistreerd bij de resource provider. 


## <a name="unregister-from-rp"></a>Registratie bij RP verwijderen

Als u de registratie van uw SQL Server-VM bij de resource provider van de SQL-VM ongedaan wilt maken, verwijdert u de virtuele SQL-machine *resource* met de Azure portal of Azure cli. Als u de *resource* van de virtuele SQL-machine verwijdert, wordt de SQL Server VM niet verwijderd. Wees echter voorzichtig en volg de stappen om de virtuele machine per ongeluk te verwijderen bij het verwijderen van de *resource*. 

Het ongedaan maken van de registratie van de virtuele SQL-machine bij de resource provider van de SQL-VM is nodig om de beheer modus te verlagen van de volledige. 

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om de registratie van uw SQL Server virtuele machine bij de resource provider ongedaan te maken met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de SQL-VM-resource. 
  
   ![Resource van virtuele SQL-machines](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Selecteer **Verwijderen**. 

   ![Selecteer verwijderen in de bovenste navigatie balk](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Typ de naam van de virtuele SQL-machine en **Schakel het selectie vakje naast de virtuele machine uit**.

   ![Schakel de VM uit om te voor komen dat de werkelijke virtuele machine wordt verwijderd en selecteer vervolgens verwijderen om door te gaan met het verwijderen van de SQL-VM-resource](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Als u het selectie vakje naast de naam van de virtuele machine uitschakelt, wordt de virtuele machine volledig *verwijderd* . Schakel het selectie vakje uit als u de registratie van de SQL Server VM van de resource provider ongedaan wilt maken, maar *de werkelijke virtuele machine niet wilt verwijderen*. 

1. Selecteer **verwijderen** om het verwijderen van de *bron* van de virtuele SQL-machine te bevestigen en niet de SQL Server VM. 

### <a name="command-line"></a>Opdrachtregel

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u de registratie van uw SQL Server-VM van de resource provider met Azure CLI ongedaan wilt maken, gebruikt u de opdracht [AZ SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest&preserve-view=true#az-sql-vm-delete) . Hiermee wordt de SQL Server VM- *resource* verwijderd, maar wordt de virtuele machine niet verwijderd. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gebruik de opdracht [Remove-AzSqlVM](/powershell/module/az.sqlvirtualmachine/remove-azsqlvm)om de registratie van uw SQL Server virtuele machine bij de resource provider ongedaan te maken met Azure PowerShell. Hiermee wordt de SQL Server VM- *resource* verwijderd, maar wordt de virtuele machine niet verwijderd. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---



## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)  
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](../../database/doc-changes-updates-release-notes.md)

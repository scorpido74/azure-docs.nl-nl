---
title: Registreren bij de resource provider van de SQL-VM
description: Registreer uw Azure SQL Server-virtuele machine met de resource provider van de SQL-VM om functies in te scha kelen voor SQL Server Vm's die buiten Azure Marketplace zijn geïmplementeerd, evenals naleving en verbeterde beheer baarheid.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/13/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: af2d23104f07991fc9833951bb4e2395d39be9b3
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84051148"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Een virtuele SQL Server-machine registreren in Azure met de SQL VM-resourceprovider
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

In dit artikel wordt beschreven hoe u uw SQL Server virtuele machine (VM) in azure kunt registreren met de resource provider van de SQL-VM. Bij het registreren van de resource provider maakt u de **virtuele SQL-machine** _resource_ binnen uw abonnement. Dit is een afzonderlijke resource van de bron van de virtuele machine. Bij het ongedaan maken van de registratie van uw SQL Server-VM van de resource provider wordt de virtuele machine van **SQL-machines** verwijderd _, maar wordt_ de werkelijke virtuele machine niet weggehaald. 

Als u een Azure Marketplace-installatie kopie van SQL Server VM implementeert via de Azure Portal, wordt de SQL Server VM automatisch geregistreerd bij de resource provider. Als u echter zelf SQL Server wilt installeren op een virtuele machine van Azure of als u een virtuele machine van Azure wilt inrichten vanaf een aangepaste VHD, moet u uw SQL Server VM registreren bij de resource provider voor:

- **Voor delen van functies**: bij het registreren van uw SQL Server-VM met de resource provider wordt [automatische patches](automated-patching.md), [automatische back-ups](automated-backup.md)en bewakings-en beheer mogelijkheden ontgrendeld. Daarnaast worden de [licenties](licensing-model-azure-hybrid-benefit-ahb-change.md) en de flexibiliteit van de [editie](change-sql-server-edition.md) ontgrendeld. Voorheen waren deze functies alleen beschikbaar voor het SQL Server van VM-installatie kopieën die zijn geïmplementeerd vanuit Azure Marketplace. 

- **Naleving**: registreren met de resource provider van de SQL-VM biedt een vereenvoudigde methode om aan micro soft te melden dat de Azure Hybrid Benefit is ingeschakeld, zoals is opgegeven in de product termen. Dit proces voor komt dat het nodig is om licentie registratie formulieren voor elke resource te beheren.  

- **Gratis beheer**: registreren met de resource provider van de SQL-vm in alle drie de beheer baarheids modi is volledig gratis. Er zijn geen extra kosten verbonden aan de resource provider of bij het wijzigen van de beheer modus. 

- **Vereenvoudigd licentie beheer**: registreren met de resource provider van de SQL-VM vereenvoudigt het beheer van SQL Server licenties en stelt u in staat om snel SQL Server vm's te identificeren met de Azure Hybrid Benefit die is ingeschakeld met behulp van de [Azure Portal](manage-sql-vm-portal.md), de Azure CLI of Power shell: 

   # <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell-interactive
   Get-AzSqlVM | Where-Object {$_.LicenseType -eq 'AHUB'}
   ```

   ---

Als u de resource provider van de SQL-VM wilt gebruiken, moet u [uw abonnement eerst registreren bij de resource provider](#register-subscription-with-rp), waardoor de resource provider de mogelijkheid heeft om resources te maken binnen dat specifieke abonnement.

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren bij de resource provider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een Azure-resource model [SQL Server VM](create-sql-vm-portal.md) geïmplementeerd op de open bare of Azure Government Cloud. 
- De nieuwste versie van [Azure cli](/cli/azure/install-azure-cli) of [Power shell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Beheer modi

Als de [SQL IaaS-extensie](sql-server-iaas-agent-extension-automate-management.md) nog niet is geïnstalleerd, installeert de SQL Server IaaS-uitbrei ding automatisch in een van de drie beheer modi die tijdens het registratie proces worden opgegeven. Als u de beheer modus niet opgeeft, wordt de SQL IaaS-uitbrei ding in de volledige beheer modus geïnstalleerd.  

Als de SQL IaaS-uitbrei ding al hand matig is geïnstalleerd, is het al in de modus volledig beheer en wordt de SQL Server-service niet opnieuw gestart bij de registratie van de resource provider in de volledige modus.

De drie beheer modi zijn:

- Het opnieuw opstarten van SQL Server is niet vereist voor de ondersteunings **modus,** maar alleen voor het wijzigen van het licentie type en de versie van SQL Server. Gebruik deze optie voor het SQL Server van Vm's met meerdere exemplaren, of voor deelname aan een FCI (failover cluster instance). Er is geen invloed op geheugen of CPU wanneer u de Lightweight-modus gebruikt, en er zijn geen kosten verbonden. Het is raadzaam om eerst uw SQL Server-VM in de Lightweight-modus te registreren en vervolgens tijdens het geplande onderhouds venster naar de volledige modus te upgraden.  

- **Volledige** modus biedt alle functionaliteit, maar vereist dat de SQL Server-en systeem beheerders machtigingen opnieuw worden gestart. Dit is de optie die standaard wordt geïnstalleerd bij het hand matig installeren van de SQL IaaS-extensie. Gebruik dit voor het beheren van een SQL Server virtuele machine met één exemplaar. In de modus volledig worden twee Windows-Services met een minimale impact op geheugen en CPU geïnstalleerd. deze kunnen worden bewaakt via taak beheer. Er zijn geen kosten verbonden aan het gebruik van de volledige beheer modus. 

- De modus voor exclusieve **agents** is toegewezen aan SQL Server 2008 en SQL Server 2008 R2 geïnstalleerd op Windows Server 2008. Er is geen invloed op geheugen of CPU wanneer u de modus geen agent gebruikt. Er zijn geen kosten verbonden aan het gebruik van de beheer bare modus voor de agent. 

U kunt de huidige modus van uw SQL Server IaaS-agent weer geven met behulp van Power shell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Abonnement registreren bij RP

Als u uw SQL Server-VM wilt registreren bij de resource provider van de SQL-VM, moet u uw abonnement eerst registreren bij de resource provider. Dit geeft de resource provider van de SQL-VM de mogelijkheid om resources binnen uw abonnement te maken.  U kunt dit doen met behulp van de Azure Portal, de Azure CLI of Power shell.

### <a name="azure-portal"></a>Azure Portal

1. Open de Azure Portal en ga naar **alle services**. 
1. Ga naar **abonnementen** en selecteer het gewenste abonnement.  
1. Ga op de pagina **abonnementen** naar **resource providers**. 
1. Voer **SQL** in het filter in om de SQL-gerelateerde resource providers te openen. 
1. Selecteer **registreren**, **opnieuw registreren**of **registratie ongedaan** maken voor de **micro soft. SqlVirtualMachine** -provider, afhankelijk van de gewenste actie. 

![De provider wijzigen](./media/sql-vm-resource-provider-register/select-resource-provider-sql.png)


### <a name="command-line"></a>Opdrachtregel

Registreer uw SQL VM-resource provider bij uw Azure-abonnement met behulp van Azure CLI of Power shell. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

```azurecli-interactive
# Register the SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```

---

## <a name="register-sql-vm-with-rp"></a>SQL-VM registreren met RP 

### <a name="lightweight-management-mode"></a>Licht gewicht beheer modus

Als de [SQL Server-Agent extensie](sql-server-iaas-agent-extension-automate-management.md) niet is geïnstalleerd op de virtuele machine, moet u zich registreren bij de resource provider van de SQL-vm in de Lightweight-modus. Hiermee wordt de SQL IaaS-uitbrei ding in de [Lightweight-modus](#management-modes) geïnstalleerd en wordt voor komen dat de SQL Server-service opnieuw wordt gestart. U kunt op elk gewenst moment een upgrade uitvoeren naar de volledige modus, maar dan wordt de SQL Server-service opnieuw gestart, zodat u wordt geadviseerd te wachten tot een gepland onderhouds venster. 

Geef SQL Server licentie type op als betalen `PAYG` per gebruik () als u `AHUB` een eigen licentie wilt gebruiken, Azure Hybrid Benefit () voor het gebruik van uw eigen License of herstel na nood gevallen ( `DR` ) om de [gratis licentie](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)voor de herstel-replica te activeren.

Failover-cluster exemplaren en implementaties met meerdere exemplaren kunnen alleen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registreer SQL Server VM in de Lightweight-modus met de Azure CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

SQL Server VM registreren in Lightweight-modus met Power shell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Volledige beheer modus


Als de SQL IaaS-uitbrei ding al hand matig is geïnstalleerd op de virtuele machine, kunt u de SQL Server VM in de volledige modus registreren zonder de SQL Server-service opnieuw te starten. **Als de SQL IaaS-extensie echter niet is geïnstalleerd, wordt de SQL IaaS-uitbrei ding in de volledige modus geïnstalleerd en wordt de SQL Server-service opnieuw gestart. Ga door met de waarschuwing.**


Gebruik de volgende Power shell-opdracht om uw SQL Server-VM rechtstreeks te registreren in de volledige modus (en mogelijk de SQL Server-service opnieuw te starten): 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>Beheer modus voor niet-agent 

SQL Server 2008 en 2008 R2 geïnstalleerd op Windows Server 2008 (_niet R2_) kunnen worden geregistreerd bij de resource provider van de SQL-vm in de modus niet- [agent](#management-modes). Met deze optie wordt de naleving gegarandeerd en kan de SQL Server virtuele machine worden bewaakt in de Azure Portal met beperkte functionaliteit.

Geef `AHUB` , `PAYG` , of `DR` als **sqlLicenseType**, en `SQL2008-WS2008` of `SQL2008R2-WS2008` als de **sqlImageOffer**op. 

Als u uw SQL Server 2008 of 2008 R2 wilt registreren op Windows Server 2008-exemplaar, gebruikt u het volgende Azure CLI-of Power shell-code fragment: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registreer uw SQL Server 2008 VM in de modus niet-agent met de Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registreer uw SQL Server 2008 R2 VM in de modus niet-agent met de Azure CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registreer SQL Server 2008 VM in de modus niet-agent met Power shell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registreer SQL Server 2008 R2 VM in de modus niet-agent met Power shell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Bijwerken naar volledige beheer modus 

SQL Server Vm's waarop de uitbrei ding *Lightweight* IaaS is geïnstalleerd, kunnen de modus upgraden naar _volledig_ met behulp van de Azure Portal, de Azure CLI of Power shell. SQL Server Vm's in de modus niet- _agent_ kunnen naar _volledig_ worden bijgewerkt nadat het besturings systeem is bijgewerkt naar Windows 2008 R2 of hoger. Het is niet mogelijk om te downgradeen: u moet de registratie van de SQL Server VM bij de resource provider van de SQL-VM [ongedaan maken](#unregister-vm-from-rp) . Als u dit doet, wordt de _bron_van de **virtuele SQL-machine** verwijderd, maar wordt de werkelijke virtuele machine niet verwijderd. 

U kunt de huidige modus van uw SQL Server IaaS-agent weer geven met behulp van Power shell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

De agent modus bijwerken naar Full: 


### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar de resource van de [virtuele SQL-machines](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selecteer uw SQL Server virtuele machine en selecteer **overzicht**. 
1. Selecteer voor SQL Server Vm's met de IaaS of de modus voor licht gewicht de optie **alleen licentie type en editie-updates zijn beschikbaar met het uitbreidings bericht van de SQL IaaS-extensie** .

   ![Selecties voor het wijzigen van de modus vanuit de portal](./media/sql-vm-resource-provider-register/change-sql-iaas-mode-portal.png)

1. Schakel het selectie vakje **Ik ga akkoord met het opnieuw opstarten van de SQL Server-service op de virtuele machine** in en selecteer vervolgens **bevestigen** om de IaaS-modus naar volledig bij te werken. 

    ![Selectie vakje voor het accepteren van de SQL Server-service op de virtuele machine opnieuw](./media/sql-vm-resource-provider-register/enable-full-mode-iaas.png)

### <a name="command-line"></a>Opdrachtregel

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Voer het volgende Azure CLI-code fragment uit:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Voer het volgende Power shell-code fragment uit:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Registratie status verifiëren
U kunt controleren of uw SQL Server virtuele machine al is geregistreerd bij de resource provider van de SQL-VM met behulp van de Azure Portal, de Azure CLI of Power shell. 

### <a name="azure-portal"></a>Azure Portal 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Ga naar de [virtuele machines van SQL Server](manage-sql-vm-portal.md).
1. Selecteer uw SQL Server-VM in de lijst. Als uw SQL Server VM hier niet wordt vermeld, is deze waarschijnlijk niet geregistreerd bij de resource provider van de SQL-VM. 
1. Bekijk de waarde onder **status**. Als de **status** is **geslaagd**, is de SQL Server VM geregistreerd bij de resource provider van de SQL-VM. 

![Status controleren met SQL RP-registratie](./media/sql-vm-resource-provider-register/verify-registration-status.png)

### <a name="command-line"></a>Opdrachtregel

Controleer de huidige SQL Server VM-registratie status met behulp van Azure CLI of Power shell. `ProvisioningState`geeft aan `Succeeded` of de registratie is geslaagd. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Een fout geeft aan dat de SQL Server VM niet is geregistreerd bij de resource provider. 


## <a name="unregister-vm-from-rp"></a>De registratie van de virtuele machine bij RP ongedaan maken

Als u de registratie van uw SQL Server-VM bij de resource provider van de SQL-VM ongedaan wilt maken, verwijdert u de virtuele SQL-machine *resource* met de Azure portal of Azure cli. Als u de *resource* van de virtuele SQL-machine verwijdert, wordt de SQL Server VM niet verwijderd. Wees echter voorzichtig en volg de stappen om de virtuele machine per ongeluk te verwijderen bij het verwijderen van de *resource*. 

Het ongedaan maken van de registratie van de SQL-VM met de resource provider van de SQL-VM is nood zakelijk om de beheer modus te verlagen van de volledige. 

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om de registratie van uw SQL Server virtuele machine bij de resource provider ongedaan te maken met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Ga naar de SQL Server VM-resource. 
  
   ![Resource van virtuele SQL-machines](./media/sql-vm-resource-provider-register/sql-vm-manage.png)

1. Selecteer **verwijderen**. 

   ![SQL-VM-resource provider verwijderen](./media/sql-vm-resource-provider-register/delete-sql-vm-resource-provider.png)

1. Typ de naam van de virtuele SQL-machine en **Schakel het selectie vakje naast de virtuele machine uit**.

   ![SQL-VM-resource provider verwijderen](./media/sql-vm-resource-provider-register/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Als u het selectie vakje naast de naam van de virtuele machine uitschakelt, wordt de virtuele machine volledig *verwijderd* . Schakel het selectie vakje uit als u de registratie van de SQL Server VM van de resource provider ongedaan wilt maken, maar *de werkelijke virtuele machine niet wilt verwijderen*. 

1. Selecteer **verwijderen** om het verwijderen van de *bron*van de virtuele SQL-machine te bevestigen en niet de SQL Server virtuele machine. 

### <a name="command-line"></a>Opdrachtregel

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u de registratie van uw SQL Server virtuele machine van de resource provider met Azure CLI ongedaan wilt maken, gebruikt u de opdracht [AZ SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Hiermee wordt de SQL Server virtuele machine *bron* verwijderd, maar wordt de virtuele machine niet verwijderd. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Gebruik de opdracht [New-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm)om de registratie van uw SQL Server virtuele machine bij de resource provider bij te werken met Azure cli. Hiermee wordt de SQL Server virtuele machine *bron* verwijderd, maar wordt de virtuele machine niet verwijderd. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Beperkingen

De resource provider van de SQL-VM ondersteunt alleen:
- SQL Server Vm's die via de Azure Resource Manager zijn geïmplementeerd. SQL Server Vm's die via het klassieke model zijn geïmplementeerd, worden niet ondersteund. 
- SQL Server Vm's die op de open bare of Azure Government Cloud zijn geïmplementeerd. Implementaties naar andere privé-of overheids Clouds worden niet ondersteund. 


## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

**Moet ik mijn SQL Server VM die is ingericht van een SQL Server-installatie kopie registreren in azure Marketplace?**

Nee. Micro soft registreert automatisch Vm's die zijn ingericht op de SQL Server installatie kopieën in azure Marketplace. Registratie met de resource provider van de SQL-VM is alleen vereist als de virtuele machine *niet* is ingericht vanaf de SQL Server installatie kopieën in azure Marketplace en SQL Server zelf is geïnstalleerd.

**Is de SQL VM-resourceprovider beschikbaar voor alle klanten?** 

Ja. Klanten moeten hun SQL Server Vm's registreren bij de resource provider van de SQL-VM als ze geen SQL Server-installatie kopie van Azure Marketplace hebben gebruikt en in plaats daarvan zelf een geïnstalleerd SQL Server of als ze hun aangepaste VHD hebben. Vm's die eigendom zijn van alle typen abonnementen (direct, Enterprise Agreement en Cloud Solution Provider) kunnen worden geregistreerd bij de resource provider van de SQL-VM.

**Moet ik registreren bij de resource provider van de SQL-VM als mijn SQL Server-VM al de SQL Server IaaS-extensie geïnstalleerd heeft?**

Als uw SQL Server virtuele machine zelf is geïnstalleerd en niet is ingericht vanaf de SQL Server installatie kopieën in azure Marketplace, moet u zich registreren bij de resource provider van de SQL-VM, zelfs als u de SQL Server IaaS-extensie hebt geïnstalleerd. Bij het registreren van de resource provider van de SQL-VM wordt een nieuwe resource van het type micro soft. SqlVirtualMachines gemaakt. Als u de extensie SQL Server IaaS installeert, wordt deze resource niet gemaakt.

**Wat is de standaard beheer modus bij de registratie bij de resource provider van de SQL-VM?**

De standaard beheer modus wanneer u zich registreert bij de resource provider van de SQL-VM is *vol*. Als de eigenschap SQL Server beheer niet is ingesteld als u zich registreert bij de resource provider van de SQL-VM, wordt de modus ingesteld op volledig beheer en wordt de SQL Server-service opnieuw gestart. U kunt het beste eerst registreren bij de resource provider van de SQL-VM in de Lightweight-modus en vervolgens een upgrade uitvoeren naar Full tijdens een onderhouds venster. 

**Wat zijn de vereisten voor registratie bij de resource provider van de SQL-VM?**

Er zijn geen vereisten om te registreren bij de resource provider van de SQL-VM in de modus licht gewicht of op de agent. De vereiste voor het registreren van de resource provider van de SQL-VM in de volledige modus is de SQL Server IaaS-uitbrei ding geïnstalleerd op de VM, anders wordt de SQL Server-service opnieuw gestart. 

**Kan ik registreren bij de resource provider van de SQL-VM als ik niet de SQL Server IaaS-extensie op de VM heb geïnstalleerd?**

Ja, u kunt zich registreren bij de resource provider van de SQL-VM in de modus licht gewicht beheer als u niet de SQL Server IaaS-extensie op de virtuele machine hebt geïnstalleerd. In de Lightweight-modus gebruikt de resource provider van de SQL-VM een console-app om de versie en editie van het SQL Server exemplaar te controleren. 

De standaard modus voor SQL-beheer bij het registreren met de SQL-VM-resource provider is _vol_. Als de SQL Management-eigenschap niet is ingesteld bij het registreren met een SQL-VM-resource provider, wordt de modus ingesteld als volledige beheer baarheid. U kunt het beste eerst registreren bij de resource provider van de SQL-VM in de Lightweight-modus en vervolgens een upgrade uitvoeren naar Full tijdens een onderhouds venster. 

**Registreert u met de resource provider van de SQL-VM een agent op mijn VM installeren?**

Nee. Bij het registreren met de resource provider van de SQL-VM wordt alleen een nieuwe meta gegevens resource gemaakt. Er wordt geen agent op de virtuele machine geïnstalleerd.

De uitbrei ding SQL Server IaaS is alleen nodig voor het inschakelen van volledig beheer. Als u de beheer baarheids modus van Lightweight naar Full bijwerkt, wordt de SQL Server IaaS-extensie geïnstalleerd en wordt SQL Server opnieuw opgestart.

**Wordt de registratie van de SQL Server VM-resource provider opnieuw gestart SQL Server op mijn VM?**

Dit is afhankelijk van de modus die tijdens de registratie is opgegeven. Als de modus licht gewicht of niet-agent is opgegeven, wordt de SQL Server-service niet opnieuw opgestart. Als u echter de beheer modus vol opgeeft of als u de beheer modus leeg laat, wordt de SQL IaaS-uitbrei ding in de volledige beheer modus geïnstalleerd, waardoor de SQL Server-service opnieuw wordt gestart. 

**Wat is het verschil tussen Lightweight en no-agent beheer modi bij het registreren met de resource provider van de SQL-VM?** 

Geen agent beheer modus is alleen beschikbaar voor SQL Server 2008 en SQL Server 2008 R2 op Windows Server 2008. Het is de enige beschik bare beheer modus voor deze versies. Voor alle andere versies van SQL Server zijn de twee beschik bare beheer baarheids modi licht gewicht en volledig. 

Voor geen enkele agent modus moeten SQL Server versie-en editie-eigenschappen worden ingesteld door de klant. In de Lightweight-modus wordt een query uitgevoerd op de VM om de versie en editie van het SQL Server-exemplaar te vinden.

**Kan ik bij de resource provider van de SQL-VM registreren zonder het SQL Server licentie type op te geven?**

Nee. Het SQL Server licentie type is geen optionele eigenschap wanneer u zich registreert bij de resource provider van de SQL-VM. U moet het SQL Server licentie type instellen op betalen naar gebruik of Azure Hybrid Benefit wanneer u zich registreert bij de resource provider van de SQL-VM in alle beheer modi (geen agent, licht gewicht en volledig).

**Kan ik de SQL Server IaaS-extensie upgraden van de modus no-agent naar de volledige modus?**

Nee. Het bijwerken van de beheer baarheids modus naar volledig of licht is niet beschikbaar voor de modus geen agent. Dit is een technische beperking van Windows Server 2008. U moet eerst een upgrade uitvoeren van het besturings systeem naar Windows Server 2008 R2 of hoger, en vervolgens kunt u een upgrade uitvoeren naar de volledige beheer modus. 

**Kan ik de SQL Server IaaS-uitbrei ding van de Lightweight-modus naar de volledige modus bijwerken?**

Ja. Het bijwerken van de beheer baarheids modus van Lightweight naar Full wordt ondersteund via Power shell of de Azure Portal. Hiervoor moet SQL Server-service opnieuw worden gestart.

**Kan ik de IaaS-uitbrei ding van de SQL Server van de volledige modus verlagen naar de modus zonder agent of lichtgewicht beheer?**

Nee. Het downgradeen van de SQL Server IaaS-extensie modus wordt niet ondersteund. De beheer baarheids modus kan niet worden gedowngraded van de volledige modus naar de modus Lightweight of no-agent, en kan niet worden gedowngraded van de modus voor niet-agent naar de No-modus. 

Als u de beheer baarheids modus van volledig beheer wilt wijzigen, verwijdert u de [registratie](#unregister-vm-from-rp) van de SQL Server virtuele machine van de resource provider van SQL Server door de SQL Server *resource* te verwijderen en de SQL Server VM opnieuw te registreren met de SQL VM-resource provider in een andere beheer modus.

**Kan ik bij de Azure Portal van de resource provider van de SQL-VM registreren?**

Nee. Registreren met de resource provider van de SQL-VM is niet beschikbaar in de Azure Portal. Registreren met de resource provider van de SQL-VM wordt alleen ondersteund met de Azure CLI of Power shell. 

**Kan ik een virtuele machine registreren bij de resource provider van de SQL-VM voordat SQL Server is geïnstalleerd?**

Nee. Een virtuele machine moet ten minste één SQL Server-exemplaar (data base-engine) hebben om te kunnen worden geregistreerd bij de resource provider van de SQL-VM. Als er geen SQL Server-exemplaar op de VM aanwezig is, heeft de nieuwe resource micro soft. SqlVirtualMachine de status mislukt.

**Kan ik een virtuele machine met de resource provider van de SQL-VM registreren als er meerdere exemplaren van SQL Server zijn?**

Ja. Met de resource provider van de SQL-VM wordt slechts één exemplaar van de SQL Server (data base-engine) geregistreerd. De resource provider van de SQL-VM registreert de standaard SQL Server-instantie in het geval van meerdere exemplaren. Als er geen standaard exemplaar is, wordt alleen de registratie in Lightweight-modus ondersteund. Als u een upgrade wilt uitvoeren van Lightweight naar full managed-modus, moet het standaard SQL Server exemplaar bestaan of moet de virtuele machine slechts één benoemd SQL Server exemplaar hebben.

**Kan ik een SQL Server failover-cluster exemplaar registreren bij de resource provider van de SQL-VM?**

Ja. SQL Server failover-cluster exemplaren op een virtuele machine van Azure kunnen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. SQL Server failover-cluster exemplaren kunnen echter niet worden bijgewerkt naar de volledige beheer modus.

**Kan ik mijn VM registreren bij de resource provider van de SQL-VM als een AlwaysOn-beschikbaarheids groep is geconfigureerd?**

Ja. Er zijn geen beperkingen voor het registreren van een SQL Server exemplaar op een virtuele machine van Azure met de resource provider van de SQL-VM als u deelneemt aan de configuratie van een AlwaysOn-beschikbaarheids groep.

**Wat zijn de kosten voor registratie bij de resource provider van de SQL-VM of een upgrade naar de volledige beheer baarheids modus?**
Geen. Er zijn geen kosten verbonden aan de registratie van de resource provider van de SQL-VM of met het gebruik van een van de drie beheer modi. Het beheren van uw SQL Server-VM met de resource provider is volledig gratis. 

**Wat is de invloed van de prestaties van het gebruik van de verschillende beheer modi?**
Er is geen invloed op het gebruik van de modi geen *agent* en *licht gewicht* beheer. Het gebruik van de *volledige* beheer baarheids modus van twee services die op het besturings systeem zijn geïnstalleerd, heeft een minimale invloed. Deze kunnen worden bewaakt via taak beheer en worden weer gegeven in de ingebouwde Windows Services-console. 

De twee service namen zijn:
- `SqlIaaSExtensionQuery`(Weergave naam- `Microsoft SQL Server IaaS Query Service` )
- `SQLIaaSExtension`(Weergave naam- `Microsoft SQL Server IaaS Agent` )


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](frequently-asked-questions-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](../../database/doc-changes-updates-release-notes.md)

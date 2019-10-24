---
title: Een SQL Server virtuele machine registreren in azure met de resource provider van de SQL-VM | Microsoft Docs
description: Registreer uw SQL Server-VM met de resource provider van de SQL-VM om de beheer baarheid te verbeteren.
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
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 2f0fac5e1951f593ea769f73feb21a60afe9c02b
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756193"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Een SQL Server virtuele machine registreren in azure met de resource provider van de SQL-VM

In dit artikel wordt beschreven hoe u uw SQL Server virtuele machine (VM) in azure kunt registreren met de resource provider van de SQL-VM. 

Als u een Azure Marketplace-installatie kopie van SQL Server VM implementeert via de Azure Portal, wordt de SQL Server VM automatisch geregistreerd bij de resource provider. Als u zelf SQL Server wilt installeren op een virtuele Azure-machine in plaats van een installatie kopie te kiezen vanuit Azure Marketplace, of als u een Azure VM inricht vanaf een aangepaste VHD met SQL Server, moet u uw SQL Server VM registreren bij de resource provider voor :

- **Beheer van licenties vereenvoudigen**: klanten moeten op basis van de product voorwaarden van micro soft vertellen wanneer ze de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)gebruiken. Het registreren van de resource provider van de SQL-VM vereenvoudigt het SQL Server licentie beheer en biedt u de mogelijkheid om SQL Server Vm's snel te identificeren met behulp van de Azure Hybrid Benefit in de [Portal](virtual-machines-windows-sql-manage-portal.md) of AZ cli: 

   ```azurecli-interactive
   $vms = az sql vm list | ConvertFrom-Json
   $vms | Where-Object {$_.sqlServerLicenseType -eq "AHUB"}
   ```

- **Voor delen van functies**: bij het registreren van uw SQL Server-VM met de resource provider worden [automatische patches](virtual-machines-windows-sql-automated-patching.md), [automatische back-ups](virtual-machines-windows-sql-automated-backup-v2.md)en bewakings mogelijkheden en beheer baarheid ontgrendeld. Daarnaast worden de [licenties](virtual-machines-windows-sql-ahb.md) en de flexibiliteit van de [editie](virtual-machines-windows-sql-change-edition.md) ontgrendeld. Voorheen waren deze functies alleen beschikbaar voor het SQL Server van VM-installatie kopieën vanuit Azure Marketplace.

- **Gratis beheer**: registreren met de resource provider van de SQL-VM en alle beheer baarheids modi zijn volledig gratis. Er zijn geen extra kosten verbonden aan de resource provider of bij het wijzigen van de beheer modus. 

Als u de resource provider van de SQL-VM wilt gebruiken, moet u ook de resource provider van de SQL-VM registreren bij uw abonnement. U kunt dit doen met behulp van de Azure Portal, de Azure CLI of Power shell. 

  > [!NOTE]
  > Er zijn geen aanvullende licentie vereisten voor de registratie bij de resource provider. Registratie bij de resource provider van de SQL-VM biedt een vereenvoudigde methode om aan micro soft te voldoen dat de Azure Hybrid Benefit is ingeschakeld op de plaats van het beheer van licentie registratie formulieren voor elke resource. 

Voor meer informatie over de voor delen van het gebruik van de resource provider van de SQL-VM raadpleegt u de volgende [Channel 9](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) -video: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Profiteer van de resource provider van de SQL-VM bij het zelf installeren van SQL Server op Azure-micro soft Channel 9 video"></iframe>


## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren bij de resource provider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- De nieuwste versie van [Azure cli](/cli/azure/install-azure-cli) of [Power shell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Registreren bij SQL-VM-resource provider
Als de [SQL Server IaaS-agent extensie](virtual-machines-windows-sql-server-agent-extension.md) niet is geïnstalleerd op de virtuele machine, kunt u zich registreren bij de resource provider van de SQL-VM door de Lightweight SQL Management-modus op te geven. 

Wanneer licht gewicht is opgegeven tijdens het registratie proces, installeert de SQL-VM-resource provider automatisch SQL IaaS-uitbrei ding in de [Lightweight-modus](#change-management-modes) en worden de meta gegevens van het SQL Server-exemplaar gecontroleerd. Hiermee wordt de SQL Server-service niet opnieuw opgestart. U moet het type SQL Server vereiste licentie opgeven wanneer u zich registreert bij de resource provider van de SQL-VM als ' PAYG ' of ' AHUB '.

Als u zich registreert bij de resource provider van de SQL-VM in de Lightweight-modus, wordt de naleving gegarandeerd en kunnen flexibele licentie verlening worden ingeschakeld, evenals in-place SQL Server editie-updates. Failover-cluster instanties en implementaties met meerdere exemplaren kunnen alleen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. U kunt op elk gewenst moment een [upgrade uitvoeren](#change-management-modes) naar de volledige beheer modus, maar hierdoor wordt de SQL Server-service opnieuw gestart. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Gebruik het volgende code fragment om u te registreren bij de resource provider van de SQL-VM als de SQL Server IaaS-uitbrei ding al is geïnstalleerd op de VM. U moet het type SQL Server licentie opgeven dat u wilt wanneer u zich registreert bij de resource provider van de SQL-VM: betalen naar gebruik (`PAYG`) of Azure Hybrid Benefit (`AHUB`). 

Registreer de SQL Server-VM met behulp van het volgende Power shell-code fragment:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Voor betaalde edities (Enter prise of Standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 

  ```

Voor gratis edities (Developer, Web of Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Als de SQL IaaS-extensie hand matig is geïnstalleerd op de virtuele machine, kunt u zich bij de volledige modus registreren bij de SQL VM-resource provider door simpelweg een meta gegevens bron van het type micro soft. SqlVirtualMachine/SqlVirtualMachines te maken. Hieronder ziet u het code fragment dat moet worden geregistreerd bij de resource provider van de SQL-VM als de SQL IaaS-uitbrei ding al is geïnstalleerd op de virtuele machine. U moet het type SQL Server licentie opgeven dat u nodig hebt als ' PAYG ' of ' AHUB '. Gebruik de volgende Power shell-opdracht om in de volledige beheer modus te registreren:

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registreer SQL Server 2008 of 2008 R2 op Windows Server 2008 Vm's

SQL Server 2008 en 2008 R2 geïnstalleerd op Windows Server 2008 kunnen worden geregistreerd bij de resource provider van de SQL-VM in de [modus voor niet-agents](#change-management-modes). Met deze optie wordt de naleving gegarandeerd en kan de SQL Server virtuele machine worden bewaakt in de Azure Portal met beperkte functionaliteit.

De volgende tabel bevat een overzicht van de acceptabele waarden voor de para meters die tijdens de registratie worden opgegeven:

| Parameter | Acceptabele waarden                                 |
| :------------------| :--------------------------------------- |
| **sqlLicenseType** | `AHUB` of `PAYG`                    |
| **sqlImageOffer**  | `SQL2008-WS2008` of `SQL2008R2-WS2008`|
| &nbsp;             | &nbsp;                                   |


Als u uw SQL Server 2008-of 2008 R2-exemplaar op Windows Server 2008-exemplaar wilt registreren, gebruikt u het volgende Power shell-of AZ CLI-code fragment:  

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
    New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='PAYG'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Registratie status verifiëren
U kunt controleren of uw SQL Server virtuele machine al is geregistreerd bij de resource provider van de SQL-VM met behulp van de Azure Portal, de Azure CLI of Power shell. 

### <a name="azure-portal"></a>Azure Portal 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Ga naar de [virtuele machines van SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selecteer uw SQL Server-VM in de lijst. Als uw SQL Server VM hier niet wordt vermeld, is deze waarschijnlijk niet geregistreerd bij de resource provider van de SQL-VM. 
1. Bekijk de waarde onder **status**. Als de **status** is **geslaagd**, is de SQL Server VM geregistreerd bij de resource provider van de SQL-VM. 

![Status controleren met SQL RP-registratie](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Opdrachtregel

Controleer de huidige SQL Server VM-registratie status met behulp van AZ CLI of Power shell. in `ProvisioningState` wordt `Succeeded` weer gegeven als de registratie is geslaagd. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzResource -ResourceName <vm_name> -ResourceGroupName <resource_group> `
  -ResourceType Microsoft.SqlVirtualMachine/sqlVirtualMachines
  ```

---

Een fout geeft aan dat de SQL Server VM niet is geregistreerd bij de resource provider. 

## <a name="change-management-modes"></a>Beheer modi wijzigen

Er zijn drie opties voor gratis beheer baarheid voor de IaaS-extensie van SQL Server: 

- **Volledige** modus biedt alle functionaliteit, maar vereist dat de SQL Server-en systeem beheerders machtigingen opnieuw worden gestart. Dit is de optie die standaard wordt geïnstalleerd. Gebruik dit voor het beheren van een SQL Server virtuele machine met één exemplaar. In de modus volledig worden twee Windows-Services met een minimale impact op geheugen en CPU geïnstalleerd. deze kunnen worden bewaakt via taak beheer. Er zijn geen kosten verbonden aan het gebruik van de volledige beheer modus. 

- Voor **licht gewicht** is het opnieuw opstarten van SQL Server niet vereist, maar het ondersteunt alleen het wijzigen van het licentie type en de versie van SQL Server. Gebruik deze optie voor het SQL Server van Vm's met meerdere exemplaren of voor deelname aan een FCI (failover cluster instance). Er is geen invloed op geheugen of CPU wanneer u de Lightweight-modus gebruikt. Er zijn geen kosten verbonden aan het gebruik van de Lightweight-beheer modus. 

- Geen **agent** is toegewezen aan SQL Server 2008 en SQL Server 2008 R2 geïnstalleerd op Windows Server 2008. Er is geen invloed op geheugen of CPU wanneer u de modus geen agent gebruikt. Er zijn geen kosten verbonden aan het gebruik van de beheer bare modus voor de agent. 

U kunt de huidige modus van uw SQL Server IaaS-agent weer geven met behulp van Power shell: 

  ```powershell-interactive
     #Get the SqlVirtualMachine
     $sqlvm = Get-AzResource -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName  -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines
     $sqlvm.Properties.sqlManagement
  ```

SQL Server Vm's waarop *de IaaS-* uitbrei ding is geïnstalleerd, kunnen de modus naar _volledig_ bijwerken met behulp van de Azure Portal. SQL Server Vm's in de modus voor _niet-agents_ kunnen worden bijgewerkt naar _volledig_ nadat het besturings systeem is bijgewerkt naar Windows 2008 R2 of hoger. Het is niet mogelijk om dit te downgradeen: u moet de registratie van de SQL Server VM bij de resource provider van de SQL-VM [ongedaan maken](#unregister-vm-from-resource-provider) door de SQL-VM-resource te verwijderen en opnieuw te registreren bij de resource provider van de SQL-VM. 

De agent modus bijwerken naar Full: 


### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar de resource van de [virtuele SQL-machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) . 
1. Selecteer uw SQL Server virtuele machine en selecteer **overzicht**. 
1. Selecteer voor SQL Server Vm's met de IaaS of de modus voor licht gewicht de optie **alleen licentie type en editie-updates zijn beschikbaar met het uitbreidings bericht van de SQL IaaS-extensie** .

   ![Selecties voor het wijzigen van de modus vanuit de portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Schakel het selectie vakje **Ik ga akkoord met het opnieuw opstarten van de SQL Server-service op de virtuele machine** in en selecteer vervolgens **bevestigen** om de IaaS-modus naar volledig bij te werken. 

    ![Selectie vakje voor het accepteren van de SQL Server-service op de virtuele machine opnieuw](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Opdrachtregel

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

## <a name="register-the-sql-vm-resource-provider-with-a-subscription"></a>De resource provider van de SQL-VM registreren bij een abonnement 

Als u uw SQL Server-VM wilt registreren bij de resource provider van de SQL-VM, moet u de resource provider registreren bij uw abonnement. U kunt dit doen met behulp van de Azure Portal, de Azure CLI of Power shell.

### <a name="azure-portal"></a>Azure Portal

1. Open de Azure Portal en ga naar **alle services**. 
1. Ga naar **abonnementen** en selecteer het gewenste abonnement.  
1. Ga op de pagina **abonnementen** naar **resource providers**. 
1. Voer **SQL** in het filter in om de SQL-gerelateerde resource providers te openen. 
1. Selecteer **registreren**, **opnieuw registreren**of **registratie ongedaan** maken voor de **micro soft. SqlVirtualMachine** -provider, afhankelijk van de gewenste actie. 

![De provider wijzigen](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Opdrachtregel

Registreer de resource provider van uw SQL-VM in uw Azure-abonnement met behulp van AZ CLI of Power shell. 

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)
Met het volgende code fragment wordt de resource provider van de SQL-VM geregistreerd bij uw Azure-abonnement. 

```azurecli-interactive
# Register the new SQL VM resource provider to your subscription 
az provider register --namespace Microsoft.SqlVirtualMachine 
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell-interactive
# Register the new SQL VM resource provider to your subscription
Register-AzResourceProvider -ProviderNamespace Microsoft.SqlVirtualMachine
```
---

## <a name="unregister-vm-from-resource-provider"></a>De registratie van de virtuele machine bij de resource provider ongedaan maken 

Als u de registratie van uw SQL Server-VM bij de resource provider van de SQL-VM ongedaan wilt maken, verwijdert u de virtuele SQL-machine *resource* met de Azure portal of Azure cli. Als u de *resource* van de virtuele SQL-machine verwijdert, wordt de SQL Server VM niet verwijderd. Wees echter voorzichtig en volg de stappen om de virtuele machine per ongeluk te verwijderen bij het verwijderen van de *resource*. 

Het ongedaan maken van de registratie van de SQL-VM met de resource provider van de SQL-VM is nood zakelijk om de beheer modus te verlagen van de volledige. 

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om de registratie van uw SQL Server virtuele machine bij de resource provider ongedaan te maken met behulp van de Azure Portal:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).
1. Ga naar de SQL Server VM-resource. 
  
   ![Resource van virtuele SQL-machines](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selecteer **Verwijderen**. 

   ![SQL-VM-resource provider verwijderen](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Typ de naam van de virtuele SQL-machine en **Schakel het selectie vakje naast de virtuele machine uit**.

   ![SQL-VM-resource provider verwijderen](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Als u het selectie vakje naast de naam van de virtuele machine uitschakelt, wordt de virtuele machine volledig *verwijderd* . Schakel het selectie vakje uit als u de registratie van de SQL Server VM van de resource provider ongedaan wilt maken, maar *de werkelijke virtuele machine niet wilt verwijderen*. 

1. Selecteer **verwijderen** om het verwijderen van de *bron*van de virtuele SQL-machine te bevestigen en niet de SQL Server virtuele machine. 


### <a name="azure-cli"></a>Azure CLI 

Als u de registratie van uw SQL Server virtuele machine van de resource provider met Azure CLI ongedaan wilt maken, gebruikt u de opdracht [AZ SQL VM delete](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) . Hiermee wordt de SQL Server virtuele machine *bron* verwijderd, maar wordt de virtuele machine niet verwijderd. 


```azurecli-interactive
   az sql vm delete 
     --name <SQL VM resource name> |
     --resource-group <Resource group name> |
     --yes 
```



## <a name="remarks"></a>Opmerkingen

- De resource provider van de SQL-VM ondersteunt alleen SQL Server Vm's die via Azure Resource Manager zijn geïmplementeerd. SQL Server Vm's die via het klassieke model zijn geïmplementeerd, worden niet ondersteund. 
- De resource provider van de SQL-VM ondersteunt alleen SQL Server Vm's die in de open bare Cloud zijn geïmplementeerd. Implementaties naar de privécloud of de overheids Cloud worden niet ondersteund. 
 

## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

**Moet ik mijn SQL Server VM die is ingericht van een SQL Server-installatie kopie registreren in azure Marketplace?**

Nee. Micro soft registreert automatisch Vm's die zijn ingericht op de SQL Server installatie kopieën in azure Marketplace. Registratie met de resource provider van de SQL-VM is alleen vereist als de virtuele machine *niet* is ingericht vanaf de SQL Server installatie kopieën in azure Marketplace en SQL Server zelf is geïnstalleerd.

**Is de SQL VM-resourceprovider beschikbaar voor alle klanten?** 

Ja. Klanten moeten hun SQL Server Vm's registreren bij de resource provider van de SQL-VM als ze geen SQL Server-installatie kopie van Azure Marketplace hebben gebruikt en in plaats daarvan zelf een geïnstalleerd SQL Server of als ze hun aangepaste VHD hebben. Vm's die eigendom zijn van alle typen abonnementen (direct, Enterprise Agreement en Cloud Solution Provider) kunnen worden geregistreerd bij de resource provider van de SQL-VM.

**Moet ik registreren bij de resource provider van de SQL-VM als mijn SQL Server-VM al de SQL Server IaaS-extensie geïnstalleerd heeft?**

Als uw SQL Server virtuele machine zelf is geïnstalleerd en niet is ingericht vanaf de SQL Server installatie kopieën in azure Marketplace, moet u zich registreren bij de resource provider van de SQL-VM, zelfs als u de SQL Server IaaS-extensie hebt geïnstalleerd. Bij het registreren van de resource provider van de SQL-VM wordt een nieuwe resource van het type micro soft. SqlVirtualMachines gemaakt. Als u de extensie SQL Server IaaS installeert, wordt deze resource niet gemaakt.

**Wat is de standaard beheer modus bij de registratie bij de resource provider van de SQL-VM?**

De standaard beheer modus wanneer u zich registreert bij de resource provider van de SQL-VM is *vol*. Als de eigenschap SQL Server beheer niet is ingesteld als u zich registreert bij de resource provider van de SQL-VM, wordt de modus ingesteld op volledig beheer. Als de SQL Server IaaS-extensie op de virtuele machine is geïnstalleerd, is de vereiste voor het registreren van de resource provider voor de SQL-VM in de volledige beheer modus.

**Wat zijn de vereisten voor registratie bij de resource provider van de SQL-VM?**

Er zijn geen vereisten om te registreren bij de resource provider van de SQL-VM in de modus licht gewicht of op de agent. De vereiste voor het registreren van de resource provider van de SQL-VM in de volledige modus is de SQL Server IaaS-uitbrei ding geïnstalleerd op de virtuele machine.

**Kan ik registreren bij de resource provider van de SQL-VM als ik niet de SQL Server IaaS-extensie op de VM heb geïnstalleerd?**

Ja, u kunt zich registreren bij de resource provider van de SQL-VM in de modus licht gewicht beheer als u niet de SQL Server IaaS-extensie op de virtuele machine hebt geïnstalleerd. In de Lightweight-modus gebruikt de resource provider van de SQL-VM een console-app om de versie en editie van het SQL Server exemplaar te controleren. 

De standaard modus voor SQL-beheer bij het registreren met de SQL-VM-resource provider is _vol_. Als de SQL Management-eigenschap niet is ingesteld bij het registreren met een SQL-VM-resource provider, wordt de modus ingesteld als volledige beheer baarheid. De SQL IaaS-extensie die is geïnstalleerd op de VM, is de vereiste voor het registreren van de resource provider van de SQL-VM in de volledige beheer modus.

**Registreert u met de resource provider van de SQL-VM een agent op mijn VM installeren?**

Nee. Bij het registreren met de resource provider van de SQL-VM wordt alleen een nieuwe meta gegevens resource gemaakt. Er wordt geen agent op de virtuele machine geïnstalleerd.

De uitbrei ding SQL Server IaaS is alleen nodig voor het inschakelen van volledig beheer. Als u de beheer baarheids modus van Lightweight naar Full bijwerkt, wordt de SQL Server IaaS-extensie geïnstalleerd en wordt SQL Server opnieuw opgestart.

**Wordt de registratie van de SQL Server VM-resource provider opnieuw gestart SQL Server op mijn VM?**

Nee. Bij het registreren met de resource provider van de SQL-VM wordt alleen een nieuwe meta gegevens resource gemaakt. De computer wordt niet opnieuw opgestart SQL Server op de VM. 

Het opnieuw starten van SQL Server is alleen nodig om de SQL Server IaaS-extensie te installeren. En de SQL Server IaaS-extensie is vereist voor het inschakelen van volledige beheer baarheid. Als u de beheer baarheids modus van Lightweight naar Full bijwerkt, wordt de SQL Server IaaS-extensie geïnstalleerd en wordt SQL Server opnieuw opgestart.

**Wat is het verschil tussen Lightweight en no-agent beheer modi bij het registreren met de resource provider van de SQL-VM?** 

Geen agent beheer modus is alleen beschikbaar voor SQL Server 2008 en SQL Server 2008 R2 op Windows Server 2008. Het is de enige beschik bare beheer modus voor deze versies. Voor alle andere versies van SQL Server zijn de twee beschik bare beheer baarheids modi licht gewicht en volledig. 

Voor geen enkele agent modus moeten SQL Server versie-en editie-eigenschappen worden ingesteld door de klant. In de Lightweight-modus wordt een query uitgevoerd op de VM om de versie en editie van het SQL Server-exemplaar te vinden.

**Kan ik bij de SQL-VM-resource provider registreren in de modus licht gewicht of zonder agent met behulp van de Azure CLI?**

Nee. De eigenschap beheer modus is alleen beschikbaar als u zich registreert bij de resource provider van de SQL-VM met behulp van Azure PowerShell. De Azure CLI biedt geen ondersteuning voor het instellen van de SQL Server-beheer baarheid-eigenschap. Deze wordt altijd geregistreerd bij de resource provider van de SQL-VM in de standaard modus, volledig beheer.

**Kan ik bij de resource provider van de SQL-VM registreren zonder het SQL Server licentie type op te geven?**

Nee. Het SQL Server licentie type is geen optionele eigenschap wanneer u zich registreert bij de resource provider van de SQL-VM. U moet het SQL Server licentie type instellen op betalen per gebruik of Azure Hybrid Benefit wanneer u zich registreert bij de resource provider van de SQL-VM (geen agent, licht gewicht en volledig) met behulp van Azure CLI en Power shell.

**Kan ik de SQL Server IaaS-extensie upgraden van de modus no-agent naar de volledige modus?**

Nee. Het bijwerken van de beheer baarheids modus naar volledig of licht is niet beschikbaar voor de modus geen agent. Dit is een technische beperking van Windows Server 2008.

**Kan ik de SQL Server IaaS-uitbrei ding van de Lightweight-modus naar de volledige modus bijwerken?**

Ja. Het bijwerken van de beheer baarheids modus van Lightweight naar Full wordt ondersteund via Power shell of de Azure Portal. Hiervoor moet SQL Server opnieuw worden gestart.

**Kan ik de IaaS-uitbrei ding van de SQL Server van de volledige modus verlagen naar de modus zonder agent of lichtgewicht beheer?**

Nee. Het downgradeen van de SQL Server IaaS-extensie modus wordt niet ondersteund. De beheer baarheids modus kan niet worden gedowngraded van de volledige modus naar de modus Lightweight of no-agent, en kan niet worden gedowngraded van de modus voor niet-agent naar de No-modus. 

Als u de beheer baarheids modus wilt wijzigen van de volledige beheer baarheid, verwijdert u de [registratie](#unregister-vm-from-resource-provider) van de SQL Server virtuele machine van de resource provider van SQL Server door de SQL Server *resource* te verwijderen en de SQL Server VM opnieuw te registreren met de resource provider van de SQL-VM opnieuw in een andere beheer modus.

**Kan ik bij de Azure Portal van de resource provider van de SQL-VM registreren?**

Nee. Registreren met de resource provider van de SQL-VM is niet beschikbaar in de Azure Portal. Registratie bij de resource provider van de SQL-VM in de volledige beheer modus wordt ondersteund met Azure CLI of Power shell. Registratie bij de resource provider van de SQL-VM in Lightweight of no-agent beheer modus wordt alleen ondersteund door Azure PowerShell-Api's.

**Kan ik een virtuele machine registreren bij de resource provider van de SQL-VM voordat SQL Server is geïnstalleerd?**

Nee. Een virtuele machine moet ten minste één SQL Server-exemplaar hebben om te kunnen worden geregistreerd bij de resource provider van de SQL-VM. Als er geen SQL Server-exemplaar op de VM aanwezig is, heeft de nieuwe resource micro soft. SqlVirtualMachine de status mislukt.

**Kan ik een virtuele machine met de resource provider van de SQL-VM registreren als er meerdere exemplaren van SQL Server zijn?**

Ja. De resource provider van de SQL-VM registreert slechts één SQL Server-exemplaar. De resource provider van de SQL-VM registreert de standaard SQL Server-instantie in het geval van meerdere exemplaren. Als er geen standaard exemplaar is, wordt alleen de registratie in Lightweight-modus ondersteund. Als u een upgrade wilt uitvoeren van Lightweight naar full managed-modus, moet het standaard SQL Server exemplaar bestaan of moet de virtuele machine slechts één benoemd SQL Server exemplaar hebben.

**Kan ik een SQL Server failover-cluster exemplaar registreren bij de resource provider van de SQL-VM?**

Ja. SQL Server failover-cluster exemplaren op een virtuele machine van Azure kunnen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. SQL Server failover-cluster exemplaren kunnen echter niet worden bijgewerkt naar de volledige beheer modus.

**Kan ik mijn VM registreren bij de resource provider van de SQL-VM als een AlwaysOn-beschikbaarheids groep is geconfigureerd?**

Ja. Er zijn geen beperkingen voor het registreren van een SQL Server exemplaar op een virtuele machine van Azure met de resource provider van de SQL-VM als u deelneemt aan de configuratie van een AlwaysOn-beschikbaarheids groep.

**Wat zijn de kosten voor registratie bij de resource provider van de SQL-VM of een upgrade naar de volledige beheer baarheids modus?**
Geen. Er zijn geen kosten verbonden aan de registratie van de resource provider van de SQL-VM of met het gebruik van een van de drie beheer modi. Het beheren van uw SQL Server-VM met de resource provider is volledig gratis. 

**Wat is de invloed van de prestaties van het gebruik van de verschillende beheer modi?**
Er is geen invloed op het gebruik van de modi geen *agent* en *licht gewicht* beheer. Het gebruik van de *volledige* beheer baarheids modus van twee services die op het besturings systeem zijn geïnstalleerd, heeft een minimale invloed. Deze kunnen worden bewaakt via taak beheer. 

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)

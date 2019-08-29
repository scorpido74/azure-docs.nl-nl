---
title: Een SQL Server virtuele machine registreren in azure met de resource provider van de SQL-VM | Microsoft Docs
description: Registreer uw SQL Server-VM met de resource provider van de SQL-VM om de beheer baarheid te verbeteren.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/24/2019
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: a4e217ce3fcfae0f7d103c545ff385f2dffe582d
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100503"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Een SQL Server virtuele machine registreren in azure met de resource provider van de SQL-VM

In dit artikel wordt beschreven hoe u uw SQL Server virtuele machine (VM) in azure kunt registreren met de resource provider van de SQL-VM. 

Als u een Azure Marketplace-installatie kopie van SQL Server VM implementeert via de Azure Portal, wordt de SQL Server VM automatisch geregistreerd bij de resource provider. Als u zelf SQL Server wilt installeren op een virtuele Azure-machine in plaats van een installatie kopie te kiezen vanuit Azure Marketplace, of als u een Azure VM inricht vanaf een aangepaste VHD met SQL Server, moet u uw SQL Server VM registreren bij de resource provider voor :

- **Naleving**: Volgens de product bepalingen van micro soft moeten klanten micro soft vertellen wanneer ze de [Azure Hybrid Benefit](https://azure.microsoft.com/pricing/hybrid-benefit/)gebruiken. Hiervoor moeten ze worden geregistreerd bij de resource provider van de SQL-VM. 

- **Functie voordelen**: Als u uw SQL Server-VM registreert bij de resource provider, worden [automatische patches](virtual-machines-windows-sql-automated-patching.md), [automatische back-ups](virtual-machines-windows-sql-automated-backup-v2.md)en bewakings mogelijkheden en beheer baarheid ontgrendeld. Daarnaast worden de [licenties](virtual-machines-windows-sql-ahb.md) en de flexibiliteit van de [editie](virtual-machines-windows-sql-change-edition.md) ontgrendeld. Voorheen waren deze functies alleen beschikbaar voor het SQL Server van VM-installatie kopieën vanuit Azure Marketplace.

Als u de resource provider van de SQL-VM wilt gebruiken, moet u ook de resource provider van de SQL-VM registreren bij uw abonnement. U kunt dit doen met behulp van de Azure Portal, de Azure CLI of Power shell. 

## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server-VM wilt registreren bij de resource provider, hebt u het volgende nodig: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een [SQL Server-VM](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). 
- De [Azure cli](/cli/azure/install-azure-cli) en [Power shell](/powershell/azure/new-azureps-module-az). 


## <a name="register-with-sql-vm-resource-provider"></a>Registreren bij SQL-VM-resource provider
Als [SQL Server de IaaS-agent extensie](virtual-machines-windows-sql-server-agent-extension.md) niet is geïnstalleerd op de virtuele machine, kunt u zich registreren bij de SQL-VM-resource provider door de Lightweight SQL Management-modus op te geven. In de Lightweight SQL-beheer modus installeert SQL VM-resource provider automatisch SQL IaaS-uitbrei ding in de [Lightweight-modus](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) en worden de meta gegevens van SQL Server-exemplaren gecontroleerd. Hiermee wordt de SQL Server-service niet opnieuw opgestart. U moet het type SQL Server vereiste licentie opgeven wanneer u zich registreert bij de resource provider van de SQL-VM als ' PAYG ' of ' AHUB '.

Als u zich registreert bij de resource provider van de SQL-VM in de [Lightweight-modus](virtual-machines-windows-sql-server-agent-extension.md#install-in-lightweight-mode) , wordt de naleving gegarandeerd en kunnen flexibele licentie verlening worden ingeschakeld, evenals in-place SQL Server editie-updates. Failover-cluster instanties en implementaties met meerdere exemplaren kunnen alleen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. U kunt de instructies op de Azure Portal volgen om een upgrade uit te voeren naar de [volledige modus](virtual-machines-windows-sql-server-agent-extension.md#install-in-full-mode) en de uitgebreide beheer baarheid-functieset in te scha kelen met een SQL Server elke keer opnieuw op te starten. 


# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)
Gebruik het volgende code fragment om u te registreren bij de resource provider van de SQL-VM als de SQL Server IaaS-uitbrei ding al is geïnstalleerd op de VM. U moet het type SQL Server licentie opgeven dat u wilt wanneer u zich registreert bij de resource provider van de SQL-VM: betalen naar gebruik (`PAYG`) of Azure Hybrid Benefit (`AHUB`). 

Registreer de SQL Server-VM met behulp van het volgende Power shell-code fragment:

  ```powershell-interactive
     # Get the existing compute VM
     $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
     # Register SQL VM with 'Lightweight' SQL IaaS agent
     New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
        -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
        -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB';sqlManagement='LightWeight'}  
  
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

Voor betaalde edities (Enter prise of Standard):

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type AHUB 

  ```

Voor gratis edities (Developer, Web of Express):

  ```azurecli-interactive
  # Register Developer, Web, or Express self-installed VM in Lightweight mode

  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```
---

Als de SQL IaaS-uitbrei ding al is geïnstalleerd op de virtuele machine, moet u zich registreren met de resource provider van de SQL-VM, maar een meta gegevens bron van het type micro soft. SqlVirtualMachine/SqlVirtualMachines maken. Hieronder ziet u het code fragment dat moet worden geregistreerd bij de resource provider van de SQL-VM als de SQL IaaS-uitbrei ding al is geïnstalleerd op de virtuele machine. U moet het type SQL Server vereiste licentie opgeven wanneer u zich registreert bij de resource provider van de SQL-VM als ' PAYG ' of ' AHUB '.

  ```powershell-interactive
  # Get the existing  Compute VM
   $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
   # Register with SQL VM resource provider
   New-AzResource -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
      -ResourceType Microsoft.SqlVirtualMachine/SqlVirtualMachines `
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'}
  ```


## <a name="register-sql-server-2008-or-2008-r2-on-windows-server-2008-vms"></a>Registreer SQL Server 2008 of 2008 R2 op Windows Server 2008 Vm's

SQL Server 2008 en 2008 R2 geïnstalleerd op Windows Server 2008 kunnen worden geregistreerd bij de resource provider van de SQL-VM in de modus voor [niet-agents](virtual-machines-windows-sql-server-agent-extension.md) . Met deze optie wordt de naleving gegarandeerd en kan de SQL Server virtuele machine worden bewaakt in de Azure Portal met beperkte functionaliteit.

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
      -Properties @{virtualMachineResourceId=$vm.Id;SqlServerLicenseType='AHUB'; `
       sqlManagement='NoAgent';sqlImageSku='Standard';sqlImageOffer='SQL2008R2-WS2008'}
  ```

# <a name="az-clitabbash"></a>[AZ CLI](#tab/bash)

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type AHUB --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008R2
 ```

---

## <a name="verify-registration-status"></a>Registratie status verifiëren
U kunt controleren of uw SQL Server virtuele machine al is geregistreerd bij de resource provider van de SQL-VM met behulp van de Azure Portal, de Azure CLI of Power shell. 

### <a name="azure-portal"></a>Azure Portal 

1. Meld u aan bij [Azure Portal](https://portal.azure.com). 
1. Ga naar de [virtuele machines van SQL Server](virtual-machines-windows-sql-manage-portal.md).
1. Selecteer uw SQL Server-VM in de lijst. Als uw SQL Server VM hier niet wordt vermeld, is deze waarschijnlijk niet geregistreerd bij de resource provider van de SQL-VM. 
1. Bekijk de waarde onder **status**. Als de **status** is **geslaagd**, is de SQL Server VM geregistreerd bij de resource provider van de SQL-VM. 

![Status controleren met SQL RP-registratie](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Opdrachtregel

Controleer de huidige SQL Server VM-registratie status met behulp van AZ CLI of Power shell. `ProvisioningState`geeft aan `Succeeded` of de registratie is geslaagd. 

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

Als u de beheer baarheids modus wilt wijzigen van volledig beheer, verwijdert u de SQL Server IaaS-extensie. Vervolgens verwijdert u de resource Micorsoft. SqlVirtualMachine en registreert u de SQL Server VM opnieuw bij de resource provider van de SQL-VM.

**Kan ik bij de Azure Portal van de resource provider van de SQL-VM registreren?**

Nee. Registreren met de resource provider van de SQL-VM is niet beschikbaar in de Azure Portal. Registratie bij de resource provider van de SQL-VM in de volledige beheer modus wordt ondersteund met Azure CLI of Power shell. Registratie bij de resource provider van de SQL-VM in Lightweight of no-agent beheer modus wordt alleen ondersteund door Azure PowerShell-Api's.

**Kan ik een virtuele machine registreren bij de resource provider van de SQL-VM voordat SQL Server is geïnstalleerd?**

Nee. Een virtuele machine moet ten minste één SQL Server-exemplaar hebben om te kunnen worden geregistreerd bij de resource provider van de SQL-VM. Als er geen SQL Server-exemplaar op de VM aanwezig is, heeft de nieuwe Micosoft. SqlVirtualMachine-resource de status mislukt.

**Kan ik een virtuele machine met de resource provider van de SQL-VM registreren als er meerdere exemplaren van SQL Server zijn?**

Ja. De resource provider van de SQL-VM registreert slechts één SQL Server-exemplaar. De resource provider van de SQL-VM registreert de standaard SQL Server-instantie in het geval van meerdere exemplaren. Als er geen standaard exemplaar is, wordt alleen de registratie in Lightweight-modus ondersteund. Als u een upgrade wilt uitvoeren van Lightweight naar full managed-modus, moet het standaard SQL Server exemplaar bestaan of moet de virtuele machine slechts één benoemd SQL Server exemplaar hebben.

**Kan ik een SQL Server failover-cluster exemplaar registreren bij de resource provider van de SQL-VM?**

Ja. SQL Server failover-cluster exemplaren op een virtuele machine van Azure kunnen worden geregistreerd bij de resource provider van de SQL-VM in de Lightweight-modus. SQL Server failover-cluster exemplaren kunnen echter niet worden bijgewerkt naar de volledige beheer modus.

**Kan ik mijn VM registreren bij de resource provider van de SQL-VM als een AlwaysOn-beschikbaarheids groep is geconfigureerd?**

Ja. Er zijn geen beperkingen voor het registreren van een SQL Server exemplaar op een virtuele machine van Azure met de resource provider van de SQL-VM als u deelneemt aan de configuratie van een AlwaysOn-beschikbaarheids groep.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-faq.md)
* [Prijs informatie voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Release opmerkingen voor SQL Server op een Windows-VM](virtual-machines-windows-sql-server-iaas-release-notes.md)

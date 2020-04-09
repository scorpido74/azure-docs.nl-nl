---
title: Registreren bij de SQL VM-resourceprovider
description: Registreer uw Azure SQL Server virtual machine bij de SQL VM-resourceprovider om functies in te schakelen voor SQL Server VM's die buiten Azure Marketplace zijn geïmplementeerd, evenals naleving en verbeterde beheerbaarheid.
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
ms.openlocfilehash: d9c1cff53d5d0f0385d3d61938c7fb6309efb7b1
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985385"
---
# <a name="register-a-sql-server-virtual-machine-in-azure-with-the-sql-vm-resource-provider"></a>Een virtuele SQL Server-machine registreren in Azure met de SQL VM-bronprovider

In dit artikel wordt beschreven hoe u uw VIRTUELE SQL Server-virtuele machine (VM) in Azure registreert bij de SQL VM-bronprovider. Als u zich registreert bij de resourceprovider, wordt de **SQL-bron voor virtuele machines** _resource_ in uw abonnement gemaakt, een afzonderlijke bron van de bron van de virtuele machine. Als u uw SQL Server VM uitdelijstt van de resourceprovider, wordt de **SQL-virtuele** _machinebron_ verwijderd, maar wordt de werkelijke virtuele machine niet laten vallen. 

Als u een SQL Server VM Azure Marketplace-afbeelding implementeert via de Azure-portal, wordt de SQL Server VM automatisch bij de resourceprovider geregistreerd. Als u er echter voor kiest om SQL Server zelf te installeren op een virtuele Azure-machine of een Virtuele Azure-machine in te richten vanaf een aangepaste VHD, moet u uw SQL Server VM registreren bij de resourceprovider voor:

- **Functievoordelen:** Het registreren van uw SQL Server VM bij de resourceprovider ontgrendelt [geautomatiseerd patchen,](virtual-machines-windows-sql-automated-patching.md) [geautomatiseerde back-up,](virtual-machines-windows-sql-automated-backup-v2.md)evenals bewakings- en beheerbaarheidsmogelijkheden. Het ontgrendelt ook [licenties](virtual-machines-windows-sql-ahb.md) en [editie](virtual-machines-windows-sql-change-edition.md) flexibiliteit. Voorheen waren deze functies alleen beschikbaar voor SQL Server VM-afbeeldingen die zijn geïmplementeerd vanuit Azure Marketplace. 

- **Naleving**: Het registreren bij de SQL VM-resourceprovider biedt een vereenvoudigde methode om te voldoen aan de vereiste om Microsoft te laten weten dat het Azure Hybrid Benefit is ingeschakeld zoals is opgegeven in de productvoorwaarden. Dit proces ontkent dat er voor elke resource registratieformulieren voor licenties moeten worden beheren.  

- **Gratis beheer**: Registreren bij de SQL VM-resourceprovider in alle drie de beheermodi is volledig gratis. Er zijn geen extra kosten verbonden aan de resourceprovider of aan het wijzigen van beheermodi. 

- **Vereenvoudigd licentiebeheer**: Door te registreren bij de SQL VM-resourceprovider vereenvoudigt SQL Server-licentiebeheer en u sql server-VM's snel identificeren met de Azure Hybrid Benefit ingeschakeld met behulp van de [Azure-portal,](virtual-machines-windows-sql-manage-portal.md)de AZ CLI of PowerShell: 

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

Als u de SQL VM-resourceprovider wilt gebruiken, moet u uw abonnement eerst [registreren bij de resourceprovider](#register-subscription-with-rp), waardoor de resourceprovider de mogelijkheid heeft om bronnen binnen dat specifieke abonnement te maken.

Bekijk deze [channel9-video](https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure?WT.mc_id=dataexposed-c9-niner) voor meer informatie over de voordelen van het gebruik van de SQL VM-resourceprovider: 

<iframe src="https://channel9.msdn.com/Shows/Data-Exposed/Benefit-from-SQL-VM-Resource-Provider-when-self-installing-SQL-Server-on-Azure/player" width="960" height="540" allowFullScreen frameBorder="0" title="Profiteer van SQL VM Resource Provider bij het zelf installeren van SQL Server op Azure - Microsoft Channel 9 Video"></iframe>


## <a name="prerequisites"></a>Vereisten

Als u uw SQL Server VM wilt registreren bij de resourceprovider, hebt u het: 

- Een [Azure-abonnement](https://azure.microsoft.com/free/).
- Een Azure Resource Model [SQL Server VM](virtual-machines-windows-portal-sql-server-provision.md) geïmplementeerd in de openbare of Azure Government cloud. 
- De nieuwste versie van [Azure CLI](/cli/azure/install-azure-cli) of [PowerShell](/powershell/azure/new-azureps-module-az). 

## <a name="management-modes"></a>Beheermodi

Als de [SQL IaaS-extensie](virtual-machines-windows-sql-server-agent-extension.md) nog niet is geïnstalleerd, installeert het registreren bij de SQL VM-resourceprovider automatisch de SQL Server IaaS-extensie in een van de drie beheermodi, die tijdens het registratieproces zijn opgegeven. Als u de beheermodus niet opgeeft, wordt de SQL IaaS-extensie in de volledige beheermodus geïnstalleerd.  

Als de SQL IaaS-extensie al handmatig is geïnstalleerd, is deze al in de volledige beheermodus en wordt de SQL Server-service niet opnieuw gestart bij de resourceprovider in de volledige modus.

De drie managementmodi zijn:

- **De lichtgewicht** modus vereist niet het opnieuw opstarten van SQL Server, maar ondersteunt alleen het wijzigen van het licentietype en de editie van SQL Server. Gebruik deze optie voor SQL Server VM's met meerdere exemplaren of deelname aan een failoverclusterinstantie (FCI). Er is geen invloed op het geheugen of cpu bij het gebruik van de lichtgewicht modus, en er zijn geen bijbehorende kosten. Het wordt aanbevolen om uw SQL Server VM eerst in de lichtgewicht modus te registreren en vervolgens te upgraden naar de volledige modus tijdens een gepland onderhoudsvenster.  

- **De volledige** modus biedt alle functionaliteit, maar vereist een herstart van de SQL Server- en systeembeheerdersmachtigingen. Dit is de optie die standaard is geïnstalleerd bij het handmatig installeren van de SQL IaaS-extensie. Gebruik deze voor het beheren van een SQL Server VM met één exemplaar. De volledige modus installeert twee windows-services die een minimale impact hebben op het geheugen en de CPU - deze kunnen worden gecontroleerd via taakbeheer. Er zijn geen kosten verbonden aan het gebruik van de volledige beheermodus. 

- **De NoAgent-modus** is gewijd aan SQL Server 2008 en SQL Server 2008 R2 die zijn geïnstalleerd op Windows Server 2008. Er is geen invloed op het geheugen of de CPU bij het gebruik van de NoAgent-modus. Er zijn geen kosten verbonden aan het gebruik van de beheermodus NoAgent. 

U de huidige modus van uw SQL Server IaaS-agent bekijken met PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

## <a name="register-subscription-with-rp"></a>Abonnement registreren bij RP

Als u uw SQL Server VM wilt registreren bij de SQL VM-resourceprovider, moet u eerst uw abonnement registreren bij de resourceprovider. Dit geeft de SQL VM-resourceprovider de mogelijkheid om bronnen binnen uw abonnement te maken.  U dit doen met de Azure-portal, de Azure CLI of PowerShell.

### <a name="azure-portal"></a>Azure Portal

1. Open de Azure-portal en ga naar **Alle services**. 
1. Ga naar **Abonnementen** en selecteer het abonnementsabonnement.  
1. Ga **op** de pagina Abonnementen naar **Resourceproviders**. 
1. Voer **sql** in het filter in om de SQL-gerelateerde resourceproviders weer te nemen. 
1. Selecteer **Registreren,** **Opnieuw registreren**of **Registreren** voor de **Microsoft.SqlVirtualMachine-provider,** afhankelijk van de gewenste actie. 

![De provider wijzigen](media/virtual-machines-windows-sql-ahb/select-resource-provider-sql.png)


### <a name="command-line"></a>Opdrachtregel

Registreer uw SQL VM-resourceprovider op uw Azure-abonnement met Az CLI of PowerShell. 

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

## <a name="register-sql-vm-with-rp"></a>SQL VM registreren met RP 

### <a name="lightweight-management-mode"></a>Lichtgewicht beheermodus

Als de [SQL Server IaaS Agent Extension](virtual-machines-windows-sql-server-agent-extension.md) niet op de VM is geïnstalleerd, is de aanbeveling om je in lichtgewicht modus te registreren bij de SQL VM-resourceprovider. Hierdoor wordt de SQL IaaS-extensie in [de lichtgewicht modus geïnstalleerd](#management-modes) en wordt voorkomen dat de SQL Server-service opnieuw wordt opgestart. U dan op elk gewenst moment upgraden naar de volledige modus, maar als u dit doet, wordt de SQL Server-service opnieuw opgestart, dus het wordt aanbevolen om te wachten tot een gepland onderhoudsvenster. 

Geef SQL Server-licentietype als pay-as-you-go`PAYG`() om per`AHUB`gebruik te betalen, Azure Hybrid`DR`Benefit ( ) om uw eigen licentie te gebruiken of disaster recovery ( ) om de [gratis DR-replicalicentie](virtual-machines-windows-sql-high-availability-dr.md#free-dr-replica-in-azure)te activeren.

Failoverclusterinstanties en implementaties met meerdere instanties kunnen alleen worden geregistreerd bij de SQL VM-resourceprovider in de lichtgewicht modus. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registreer SQL Server VM in lichtgewicht modus met de Az CLI: 

  ```azurecli-interactive
  # Register Enterprise or Standard self-installed VM in Lightweight mode
  az sql vm create --name <vm_name> --resource-group <resource_group_name> --location <vm_location> --license-type PAYG 
  ```


# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registreer SQL Server VM in lichtgewicht modus met PowerShell:  


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  # Register SQL VM with 'Lightweight' SQL IaaS agent
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType LightWeight  
  ```

---

### <a name="full-management-mode"></a>Volledige beheermodus


Als de SQL IaaS-extensie al handmatig op de VM is geïnstalleerd, u de SQL Server VM in volledige modus registreren zonder de SQL Server-service opnieuw op te starten. **Als de SQL IaaS-extensie echter niet is geïnstalleerd, installeert u de SQL IaaS-extensie in de volledige modus en start u de SQL Server-service opnieuw. Ga voorzichtig te werk.**


Als u uw SQL Server VM rechtstreeks in de volledige modus wilt registreren (en eventueel uw SQL Server-service opnieuw wilt starten), gebruikt u de volgende PowerShell-opdracht: 

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

### <a name="noagent-management-mode"></a>NoAgent-beheermodus 

SQL Server 2008 en 2008 R2 die zijn geïnstalleerd op Windows Server 2008 (_niet R2_) kunnen worden geregistreerd bij de SQL VM-resourceprovider in de [NoAgent-modus](#management-modes). Met deze optie u naleving garanderen en kan de SQL Server VM worden gecontroleerd in de Azure-portal met beperkte functionaliteit.

Geef `AHUB`een `PAYG`sqlLicenseType op `DR` of `SQL2008-WS2008` als `SQL2008R2-WS2008` **sqlImageType**en of als **sqlImageOffer**. 

Als u uw SQL Server 2008 of 2008 R2 wilt registreren op de instantie Windows Server 2008, gebruikt u het volgende az CLI- of PowerShell-codefragment: 


# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Registreer uw SQL Server 2008 VM in noagent-modus met de Az CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008-WS2008
 ```
 
 
Registreer uw SQL Server 2008 R2 VM in NoAgent-modus met de AZ CLI: 

  ```azurecli-interactive
   az sql vm create -n sqlvm -g myresourcegroup -l eastus |
   --license-type PAYG --sql-mgmt-type NoAgent 
   --image-sku Enterprise --image-offer SQL2008R2-WS2008
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Registreer SQL Server 2008 VM in NoAgent-modus met PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008-WS2008
  ```
  
  Registreer SQL Server 2008 R2 VM in NoAgent-modus met PowerShell: 


  ```powershell-interactive
  # Get the existing compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
          
  New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
    -LicenseType PAYG -SqlManagementType NoAgent -Sku Standard -Offer SQL2008R2-WS2008
  ```

---

## <a name="upgrade-to-full-management-mode"></a>Upgraden naar volledige beheermodus 

SQL Server VM's waarop de *lichtgewicht* IaaS-extensie is geïnstalleerd, kunnen de modus _volledig upgraden_ met behulp van de Azure-portal, de Az CLI of PowerShell. SQL Server VM's in _noagent-modus_ kunnen worden geüpgraded naar _volledig_ nadat het besturingssysteem is geüpgraded naar Windows 2008 R2 en hoger. Het is niet mogelijk om te downgraden - om dit te doen, moet u de SQL Server VM [uitschrijven](#unregister-vm-from-rp) bij de SQL VM-resourceprovider. Als u dit doet, wordt de **SQL-bron voor virtuele machines** _resource_verwijderd, maar wordt de werkelijke virtuele machine niet verwijderd. 

U de huidige modus van uw SQL Server IaaS-agent bekijken met PowerShell: 

  ```powershell-interactive
  # Get the SqlVirtualMachine
  $sqlvm = Get-AzSqlVM -Name $vm.Name  -ResourceGroupName $vm.ResourceGroupName
  $sqlvm.SqlManagementType
  ```

Ga als u de agentmodus naar volledig upgraden: 


### <a name="azure-portal"></a>Azure Portal

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Ga naar uw [SQL virtuele machines](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource) bron. 
1. Selecteer uw virtuele SQL Server-machine en selecteer **Overzicht**. 
1. Voor SQL Server VM's met de NoAgent- of lichtgewicht IaaS-modus selecteert u het **alleen licentietype en zijn editieupdates beschikbaar met het SQL IaaS-extensiebericht.**

   ![Selecties voor het wijzigen van de modus van de portal](media/virtual-machines-windows-sql-server-agent-extension/change-sql-iaas-mode-portal.png)

1. Schakel het **selectievakje Akkoord gaan met akkoord met het starten van de SQL Server-service op de virtuele machine** en selecteer Vervolgens **Bevestigen** om de IaaS-modus volledig te upgraden. 

    ![Selectievakje om ermee in te stemmen de SQL Server-service opnieuw op de virtuele machine te starten](media/virtual-machines-windows-sql-server-agent-extension/enable-full-mode-iaas.png)

### <a name="command-line"></a>Opdrachtregel

# <a name="az-cli"></a>[AZ CLI](#tab/bash)

Voer het volgende AZ CLI-codefragment uit:

  ```azurecli-interactive
  # Update to full mode
  az sql vm update --name <vm_name> --resource-group <resource_group_name> --sql-mgmt-type full  
  ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Voer het volgende PowerShell-codefragment uit:

  ```powershell-interactive
  # Get the existing  Compute VM
  $vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
        
  # Register with SQL VM resource provider in full mode
  Update-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -SqlManagementType Full
  ```

---

## <a name="verify-registration-status"></a>Registratiestatus verifiëren
U controleren of uw SQL Server VM al is geregistreerd bij de SQL VM-resourceprovider met behulp van de Azure-portal, de Azure CLI of PowerShell. 

### <a name="azure-portal"></a>Azure Portal 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com). 
1. Ga naar uw [virtuele SQL Server-machines.](virtual-machines-windows-sql-manage-portal.md)
1. Selecteer uw SQL Server VM in de lijst. Als uw SQL Server VM hier niet wordt vermeld, is deze waarschijnlijk niet geregistreerd bij de SQL VM-bronprovider. 
1. Bekijk de waarde onder **Status**. Als **de status** is **geslaagd,** is de SQL Server VM geregistreerd bij de SQL VM-resourceprovider. 

![Status verifiëren met SQL RP-registratie](media/virtual-machines-windows-sql-register-with-rp/verify-registration-status.png)

### <a name="command-line"></a>Opdrachtregel

Controleer de huidige SQL Server VM-registratiestatus met Az CLI of PowerShell. `ProvisioningState`zal `Succeeded` laten zien of de registratie succesvol is geweest. 

# <a name="az-cli"></a>[AZ CLI](#tab/bash)


  ```azurecli-interactive
  az sql vm show -n <vm_name> -g <resource_group>
 ```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

  ```powershell-interactive
  Get-AzSqlVM -Name <vm_name> -ResourceGroupName <resource_group>
  ```

---

Een fout geeft aan dat de SQL Server VM niet is geregistreerd bij de resourceprovider. 


## <a name="unregister-vm-from-rp"></a>VM uitschrijven van RP

Als u uw SQL Server VM wilt uitschrijven bij de SQL VM-bronprovider, verwijdert u de SQL Virtual *Machine-bron* met behulp van de Azure-portal of Azure CLI. Als u de SQL Virtual *Machine-bron* verwijdert, wordt de SQL Server VM niet verwijderd. Wees echter voorzichtig en volg de stappen zorgvuldig, omdat het mogelijk is om per ongeluk de virtuele machine te verwijderen wanneer u probeert de *bron*te verwijderen. 

Het uitschrijven van de SQL VM met de SQL VM-resourceprovider is noodzakelijk om de beheermodus volledig te downgraden. 

### <a name="azure-portal"></a>Azure Portal

Voer de volgende stappen uit om uw SQL Server VM bij de resourceprovider uit te schrijven met behulp van de Azure-portal:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Navigeer naar de SQL Server VM-bron. 
  
   ![SQL-bron voor virtuele machines](media/virtual-machines-windows-sql-manage-portal/sql-vm-manage.png)

1. Selecteer **Verwijderen**. 

   ![SQL VM-bronprovider verwijderen](media/virtual-machines-windows-sql-register-with-rp/delete-sql-vm-resource-provider.png)

1. Typ de naam van de virtuele SQL-machine en **schakel het selectievakje uit naast de virtuele machine.**

   ![SQL VM-bronprovider verwijderen](media/virtual-machines-windows-sql-register-with-rp/confirm-delete-of-resource-uncheck-box.png)

   >[!WARNING]
   > Als u het selectievakje naast de naam van de virtuele machine niet uitzet, wordt de virtuele machine volledig *verwijderd.* Schakel het selectievakje uit om de SQL Server VM uit te schakelen bij de resourceprovider, maar *verwijder de werkelijke virtuele machine niet.* 

1. Selecteer **Verwijderen** om de verwijdering van de SQL-virtuele *machinebron*te bevestigen en niet de virtuele SQL Server-machine. 

### <a name="command-line"></a>Opdrachtregel

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Als u uw virtuele SQL Server-machine wilt uitschrijven van de resourceprovider met Azure CLI, gebruikt u de opdracht [az sql vm delete.](/cli/azure/sql/vm?view=azure-cli-latest#az-sql-vm-delete) Hiermee wordt de sql server-bron *voor* virtuele machines verwijderd, maar wordt de virtuele machine niet verwijderd. 


```azurecli-interactive
az sql vm delete 
  --name <SQL VM resource name> |
  --resource-group <Resource group name> |
  --yes 
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Als u uw virtuele SQL Server-machine wilt uitschrijven van de resourceprovider met Azure CLI, gebruikt u de opdracht [Nieuw-AzSqlVM.](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) Hiermee wordt de sql server-bron *voor* virtuele machines verwijderd, maar wordt de virtuele machine niet verwijderd. 

```powershell-interactive
Remove-AzSqlVM -ResourceGroupName <resource_group_name> -Name <VM_name>
```

---

## <a name="limitations"></a>Beperkingen

De SQL VM-resourceprovider ondersteunt alleen:
- SQL Server VM's geïmplementeerd via Azure Resource Manager. SQL Server VM's die via het klassieke model worden geïmplementeerd, worden niet ondersteund. 
- SQL Server VM's geïmplementeerd in de openbare of Azure Government cloud. Implementaties naar andere privé- of overheidsclouds worden niet ondersteund. 


## <a name="frequently-asked-questions"></a>Veelgestelde vragen 

**Moet ik mijn SQL Server VM registreren op basis van een SQL Server-afbeelding in Azure Marketplace?**

Nee. Microsoft registreert automatisch VM's die zijn ingericht vanaf de SQL Server-afbeeldingen in Azure Marketplace. Registreren bij de SQL VM-resourceprovider is alleen vereist als de VM *niet* is ingericht vanaf de SQL Server-afbeeldingen in Azure Marketplace en SQL Server zelf is geïnstalleerd.

**Is de SQL VM-resourceprovider beschikbaar voor alle klanten?** 

Ja. Klanten moeten hun SQL Server VM's registreren bij de SQL VM-resourceprovider als ze geen SQL Server-afbeelding van Azure Marketplace hebben gebruikt en in plaats daarvan sql server zelf hebben geïnstalleerd, of als ze hun aangepaste VHD hebben meegenomen. VM's die eigendom zijn van alle soorten abonnementen (Direct, Enterprise Agreement en Cloud Solution Provider) kunnen zich registreren bij de SQL VM-resourceprovider.

**Moet ik me registreren bij de SQL VM-resourceprovider als mijn SQL Server VM al de SQL Server IaaS-extensie heeft geïnstalleerd?**

Als uw SQL Server VM zelf is geïnstalleerd en niet is ingericht vanuit de SQL Server-afbeeldingen in Azure Marketplace, moet u zich registreren bij de SQL VM-bronprovider, zelfs als u de SQL Server IaaS-extensie hebt geïnstalleerd. Als u zich registreert bij de SQL VM-resourceprovider, wordt een nieuwe bron van het type Microsoft.SqlVirtualMachines gemaakt. Het installeren van de SQL Server IaaS-extensie maakt die bron niet.

**Wat is de standaardbeheermodus bij het registreren bij de SQL VM-resourceprovider?**

De standaardbeheermodus wanneer u zich registreert bij de SQL VM-resourceprovider is *vol.* Als de eigenschap SQL Server-beheer niet is ingesteld wanneer u zich registreert bij de SQL VM-resourceprovider, wordt de modus ingesteld als volledig beheerbaarheid en wordt uw SQL Server-service opnieuw opgestart. Het wordt aanbevolen om eerst te registreren bij de SQL VM-resourceprovider in de lichtgewicht modus en vervolgens te upgraden naar volledig tijdens een onderhoudsvenster. 

**Wat zijn de vereisten om te registreren bij de SQL VM-resourceprovider?**

Er zijn geen vereisten voor het registreren bij de SQL VM-resourceprovider in de lichtgewicht modus of no-agent-modus. De voorwaarde voor het registreren bij de SQL VM-resourceprovider in volledige modus is het hebben van de SQL Server IaaS-extensie op de VM, omdat anders de SQL Server-service opnieuw wordt opgestart. 

**Kan ik me registreren bij de SQL VM-resourceprovider als ik de SQL Server IaaS-extensie niet op de VM heb geïnstalleerd?**

Ja, u zich registreren bij de SQL VM-resourceprovider in de lichtgewicht beheermodus als u de SQL Server IaaS-extensie niet op de VM hebt geïnstalleerd. In de lichtgewicht modus gebruikt de SQL VM-bronprovider een console-app om de versie en editie van het SQL Server-exemplaar te verifiëren. 

De standaard SQL-beheermodus bij het registreren bij SQL VM-resourceprovider is _Volledig_. Als de eigenschap SQL Management niet is ingesteld bij het registreren bij SQL VM-resourceprovider, wordt de modus ingesteld als Volledig beheerbaarheid. Het wordt aanbevolen om eerst te registreren bij de SQL VM-resourceprovider in de lichtgewicht modus en vervolgens te upgraden naar volledig tijdens een onderhoudsvenster. 

**Installeert u zich bij de SQL VM-resourceprovider een agent op mijn VM?**

Nee. Als u zich registreert bij de SQL VM-resourceprovider, wordt alleen een nieuwe bron voor metagegevens gemaakt. Het zal geen agent op VM installeren.

De SQL Server IaaS-extensie is alleen nodig om volledige beheerbaarheid mogelijk te maken. Als u de beheermodus van lichtgewicht naar volledig upgradet, wordt de SQL Server IaaS-extensie geïnstalleerd en wordt SQL Server opnieuw opgestart.

**Start u sql server opnieuw bij de SQL Server VM-provider op mijn VM?**

Het hangt af van de modus die tijdens de registratie is opgegeven. Als de lichtgewicht- of NoAgent-modus is opgegeven, wordt de SQL Server-service niet opnieuw opgestart. Als u echter de beheermodus als volledig opgeeft of de beheermodus leeg laat, installeert u de SQL IaaS-extensie in de volledige beheermodus, waardoor de SQL Server-service opnieuw wordt opgestart. 

**Wat is het verschil tussen lichtgewicht en no-agent management modi bij het registreren bij de SQL VM resource provider?** 

De beheermodus voor no-agent is alleen beschikbaar voor SQL Server 2008 en SQL Server 2008 R2 op Windows Server 2008. Het is de enige beschikbare beheermodus voor deze versies. Voor alle andere versies van SQL Server zijn de twee beschikbare beheermodi licht en vol. 

No-agent-modus vereist SQL Server-versie- en editieeigenschappen die door de klant moeten worden ingesteld. In de lichtgewicht modus wordt de VM opgevraagd om de versie en editie van de SQL Server-instantie te vinden.

**Kan ik me registreren bij de SQL VM-resourceprovider zonder het SQL Server-licentietype op te geven?**

Nee. Het SQL Server-licentietype is geen optionele eigenschap wanneer u zich registreert bij de SQL VM-bronprovider. U moet het SQL Server-licentietype instellen als pay-as-you-go of Azure Hybrid Benefit wanneer u zich registreert bij de SQL VM-resourceprovider in alle beheerbaarheidsmodi (no-agent, lichtgewicht en volledig).

**Kan ik de SQL Server IaaS-extensie upgraden van no-agent-modus naar de volledige modus?**

Nee. Upgraden van de beheermodus naar volledig of lichtgewicht is niet beschikbaar voor no-agent-modus. Dit is een technische beperking van Windows Server 2008. U moet het besturingssysteem eerst upgraden naar Windows Server 2008 R2 of hoger, en dan u upgraden naar de volledige beheermodus. 

**Kan ik de SQL Server IaaS-extensie upgraden van de lichtgewicht modus naar de volledige modus?**

Ja. Het upgraden van de beheermodus van lichtgewicht naar volledig wordt ondersteund via PowerShell of de Azure-portal. Hiervoor moet de SQL Server-service opnieuw worden opgestart.

**Kan ik de SQL Server IaaS-extensie downgraden van de volledige modus naar no-agent of lichtgewicht beheermodus?**

Nee. Het verlagen van de beheerbaarheidsmodus van de SQL Server IaaS-extensie wordt niet ondersteund. De beheermodus kan niet worden gedegradeerd van de volledige modus naar de lichtgewicht- of no-agent-modus en kan niet worden gedegradeerd van de lichtgewicht modus naar de no-agent-modus. 

Als u de beheermodus wilt wijzigen van volledige beheerbaarheid, [ontschrijft u](#unregister-vm-from-rp) de virtuele SQL Server-machine van de SQL Server-resourceprovider door de SQL Server-bron te laten vallen en de SQL Server VM opnieuw te registreren bij de SQL VM-resourceprovider opnieuw in een andere beheermodus. *resource*

**Kan ik me registreren bij de SQL VM-resourceprovider vanuit de Azure-portal?**

Nee. Registreren bij de SQL VM-resourceprovider is niet beschikbaar in de Azure-portal. Registreren bij de SQL VM-resourceprovider wordt alleen ondersteund met azure cli of PowerShell. 

**Kan ik een VM registreren bij de SQL VM-resourceprovider voordat SQL Server is geïnstalleerd?**

Nee. Een VM moet ten minste één SQL Server-instantie (Database Engine) hebben om zich met succes te registreren bij de SQL VM-resourceprovider. Als er geen SQL Server-instantie op de VM staat, is de nieuwe Microsoft.SqlVirtualMachine-bron in een mislukte status.

**Kan ik een VM registreren bij de SQL VM-resourceprovider als er meerdere SQL Server-exemplaren zijn?**

Ja. De SQL VM-resourceprovider registreert slechts één SQL Server-instantie (Database Engine). De SQL VM-resourceprovider registreert de standaard SQL Server-instantie in het geval van meerdere exemplaren. Als er geen standaardexemplaar is, wordt alleen registreren in de lichtgewicht modus ondersteund. Als u wilt upgraden van lichtgewicht naar volledige beheermodus, moet de standaardSQL Server-instantie bestaan of moet de VM slechts één SQL Server-instantie hebben.

**Kan ik een SQL Server-failoverclusterinstantie registreren bij de SQL VM-resourceprovider?**

Ja. SQL Server-failoverclusterexemplaren op een Azure VM kunnen in lichtgewicht modus worden geregistreerd bij de SQL VM-resourceprovider. SQL Server-failoverclusterexemplaren kunnen echter niet worden geüpgraded naar de volledige beheermodus.

**Kan ik mijn VM registreren bij de SQL VM-resourceprovider als een groep always on beschikbaarheid is geconfigureerd?**

Ja. Er zijn geen beperkingen voor het registreren van een SQL Server-instantie op een Azure VM bij de SQL VM-resourceprovider als u deelneemt aan een configuratie van de beschikbaarheidalways ingeschakeld.

**Wat zijn de kosten voor het registreren bij de SQL VM-resourceprovider of met een upgrade naar de volledige beheermodus?**
Geen. Er zijn geen kosten verbonden aan het registreren bij de SQL VM-resourceprovider of met het gebruik van een van de drie beheermodi. Het beheren van uw SQL Server VM met de resourceprovider is volledig gratis. 

**Wat is de impact van de prestaties van het gebruik van de verschillende beheermodi?**
Er is geen impact bij het gebruik van de *NoAgent* en *lichtgewicht* beheermodi. Er is minimale impact bij het gebruik van de *volledige* beheermodus van twee services die zijn geïnstalleerd op het besturingssysteem. Deze kunnen worden gecontroleerd via taakbeheer en worden gezien in de ingebouwde Windows Services-console. 

De twee servicenamen zijn:
- `SqlIaaSExtensionQuery`(Weergavenaam `Microsoft SQL Server IaaS Query Service`- )
- `SQLIaaSExtension`(Weergavenaam `Microsoft SQL Server IaaS Agent`- )


## <a name="next-steps"></a>Volgende stappen

Raadpleeg voor meer informatie de volgende artikelen: 

* [Overzicht van SQL Server op een Windows VM](virtual-machines-windows-sql-server-iaas-overview.md)
* [Veelgestelde vragen over SQL Server op een Windows-vm](virtual-machines-windows-sql-server-iaas-faq.md)
* [Richtlijnen voor prijzen voor SQL Server op een Windows-vm](virtual-machines-windows-sql-server-pricing-guidance.md)
* [Notities voor SQL Server vrijgeven op een Windows-vm](virtual-machines-windows-sql-server-iaas-release-notes.md)

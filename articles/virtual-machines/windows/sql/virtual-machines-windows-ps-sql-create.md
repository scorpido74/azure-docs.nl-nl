---
title: Inrichtingshandleiding voor SQL Server VM's met Azure PowerShell | Microsoft Documenten
description: Biedt stappen en PowerShell-opdrachten voor het maken van een Azure VM met sql server-galerieafbeeldingen voor virtuele machines.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-resource-manager
ms.assetid: 98d50dd8-48ad-444f-9031-5378d8270d7b
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/21/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: b1578547fbca4caaecb209021569f0fbb2f1ae24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790628"
---
# <a name="how-to-provision-sql-server-virtual-machines-with-azure-powershell"></a>Sql Server-virtuele machines inrichten met Azure PowerShell

In deze handleiding worden uw opties uitgelegd voor het maken van Windows SQL Server VM's met Azure PowerShell. Zie de [SQL VM Azure PowerShell snelstart](quickstart-sql-vm-create-powershell.md)voor een gestroomlijnd Azure PowerShell-voorbeeld met meer standaardwaarden.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

## <a name="configure-your-subscription"></a>Uw abonnement configureren

1. Open PowerShell en zorg dat u toegang hebt tot uw Azure-account door de opdracht **Connect-AzAccount** uit te voeren.

   ```powershell
   Connect-AzAccount
   ```

1. Als het goed is, ziet u nu een scherm waarin u uw referenties kunt invoeren. Gebruik hetzelfde e-mailadres en wachtwoord waarmee u zich aanmeldt bij Azure Portal.

## <a name="define-image-variables"></a>Afbeeldingsvariabelen definiëren
Als u waarden wilt hergebruiken en het maken van scripts wilt vereenvoudigen, moet u eerst een aantal variabelen definiëren. Wijzig de parameterwaarden zoals u dat wilt, maar houd rekening met naamgevingsbeperkingen met betrekking tot naamlengtes en speciale tekens bij het wijzigen van de opgegeven waarden.

### <a name="location-and-resource-group"></a>Locatie- en resourcegroep
Definieer het gegevensgebied en de resourcegroep waarin u de andere VM-resources maakt.

Wijzig zoals u wilt en voer deze cmdlets uit om deze variabelen te initialiseren.

```powershell
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"
```

### <a name="storage-properties"></a>Opslageigenschappen
Definieer het opslagaccount en het type opslag dat door de virtuele machine moet worden gebruikt.

Wijzig zoals u wilt en voer vervolgens de volgende cmdlet uit om deze variabelen te initialiseren. We raden u aan [premium SSD's te](../disks-types.md#premium-ssd) gebruiken voor productieworkloads.

```powershell
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"
```

### <a name="network-properties"></a>Netwerkeigenschappen
Definieer de eigenschappen die door het netwerk in de virtuele machine moeten worden gebruikt. 

- Netwerkinterface
- TCP/IP-toewijzingsmethode
- Naam van virtueel netwerk
- Virtuele subnetnaam
- Bereik van IP-adressen voor het virtuele netwerk
- Bereik van IP-adressen voor het subnet
- Label voor openbare domeinnaam

Wijzig zoals u wilt en voer deze cmdlet uit om deze variabelen te initialiseren.

```powershell
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$TCPIPAllocationMethod = "Dynamic"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$DomainName = $ResourceGroupName
```

### <a name="virtual-machine-properties"></a>Eigenschappen van virtuele machines
Definieer de naam van de virtuele machine, de computernaam, de grootte van de virtuele machine en de schijfnaam van het besturingssysteem voor de virtuele machine.

Wijzig zoals u wilt en voer deze cmdlet uit om deze variabelen te initialiseren.

```powershell
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"
```

### <a name="choose-a-sql-server-image"></a>Een SQL Server-afbeelding kiezen

Gebruik de volgende variabelen om de SQL Server-afbeelding te definiëren die u voor de virtuele machine wilt gebruiken. 

1. Vermeld eerst alle SQL Server-afbeeldingsaanbiedingen `Get-AzVMImageOffer` met de opdracht. Met deze opdracht worden actuele afbeeldingen weergegeven die beschikbaar zijn in de Azure Portal en ook oudere afbeeldingen die alleen met PowerShell kunnen worden geïnstalleerd:

   ```powershell
   Get-AzVMImageOffer -Location $Location -Publisher 'MicrosoftSQLServer'
   ```

1. Gebruik voor deze zelfstudie de volgende variabelen om SQL Server 2017 op Windows Server 2016 op te geven.

   ```powershell
   $OfferName = "SQL2017-WS2016"
   $PublisherName = "MicrosoftSQLServer"
   $Version = "latest"
   ```

1. Vermeld vervolgens de beschikbare edities voor uw aanbieding.

   ```powershell
   Get-AzVMImageSku -Location $Location -Publisher 'MicrosoftSQLServer' -Offer $OfferName | Select Skus
   ```

1. Gebruik voor deze zelfstudie de SQL Server 2017 Developer edition **(SQLDEV).** De Developer-editie is vrij gelicentieerd voor testen en ontwikkeling en u betaalt alleen voor de kosten van het uitvoeren van de VM.

   ```powershell
   $Sku = "SQLDEV"
   ```

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Met het implementatiemodel ResourceManager is het eerste object dat u maakt de resourcegroep. Gebruik de cmdlet [Nieuw-AzResourceGroep](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) om een Azure-brongroep en de bijbehorende resources te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam en locatie van de resourcegroep.

Voer deze cmdlet uit om uw nieuwe resourcegroep te maken.

```powershell
New-AzResourceGroup -Name $ResourceGroupName -Location $Location
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken
De virtuele machine vereist opslagbronnen voor de schijf van het besturingssysteem en voor de SQL Server-gegevens en logboekbestanden. Voor de eenvoud maakt u één schijf voor beide. U later extra schijven toevoegen met de cmdlet [Add-Azure Disk](https://docs.microsoft.com/powershell/module/servicemanagement/azure/add-azuredisk) om uw SQL Server-gegevens en logboekbestanden op speciale schijven te plaatsen. Gebruik de cmdlet [Nieuw-AzStorageAccount](https://docs.microsoft.com/powershell/module/az.storage/new-azstorageaccount) om een standaardopslagaccount in uw nieuwe brongroep te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam van het opslagaccount, de naam van opslagsku en de locatie.

Voer deze cmdlet uit om uw nieuwe opslagaccount aan te maken.

```powershell
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName `
   -Name $StorageName -SkuName $StorageSku `
   -Kind "Storage" -Location $Location
```

> [!TIP]
> Het maken van het opslagaccount kan enkele minuten duren.

## <a name="create-network-resources"></a>Netwerkbronnen maken
De virtuele machine vereist een aantal netwerkbronnen voor netwerkconnectiviteit.

* Elke virtuele machine vereist een virtueel netwerk.
* Een virtueel netwerk moet ten minste één subnet hebben gedefinieerd.
* Een netwerkinterface moet worden gedefinieerd met een openbaar of een privé-IP-adres.

### <a name="create-a-virtual-network-subnet-configuration"></a>Een subnetconfiguratie voor virtuele netwerken maken
Begin met het maken van een subnetconfiguratie voor uw virtuele netwerk. Maak voor deze zelfstudie een standaardsubnet met de cmdlet [Nieuw-AzVirtualNetworkSubnetConfig.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetworksubnetconfig) Geef de variabelen op die u eerder hebt geïnitialiseerd voor het subnetnaam en adresvoorvoegsel.

> [!NOTE]
> U extra eigenschappen van de subnetconfiguratie van het virtuele netwerk definiëren met behulp van deze cmdlet, maar dat valt buiten het bereik van deze zelfstudie.

Voer deze cmdlet uit om uw virtuele subnetconfiguratie te maken.

```powershell
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
```

### <a name="create-a-virtual-network"></a>Een virtueel netwerk maken
Maak vervolgens uw virtuele netwerk in uw nieuwe brongroep met de cmdlet [Nieuw-AzVirtualNetwork.](https://docs.microsoft.com/powershell/module/az.network/new-azvirtualnetwork) Geef de variabelen op die u eerder hebt geïnitialiseerd voor het voorvoegsel naam, locatie en adres. Gebruik de subnetconfiguratie die u in de vorige stap hebt gedefinieerd.

Voer deze cmdlet uit om uw virtuele netwerk te maken.

```powershell
$VNet = New-AzVirtualNetwork -Name $VNetName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
```

### <a name="create-the-public-ip-address"></a>Het openbare IP-adres maken
Nu uw virtuele netwerk is gedefinieerd, moet u een IP-adres configureren voor connectiviteit met de virtuele machine. Maak voor deze zelfstudie een openbaar IP-adres met dynamische IP-adressering ter ondersteuning van internetverbinding. Gebruik de cmdlet [Nieuw-AzPublicIpAddress](https://docs.microsoft.com/powershell/module/az.network/new-azpublicipaddress) om het openbare IP-adres in uw nieuwe brongroep te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam, locatie, toewijzingsmethode en DNS-domeinnaamlabel.

> [!NOTE]
> U extra eigenschappen van het openbare IP-adres definiëren met deze cmdlet, maar dat valt buiten het bereik van deze eerste zelfstudie. U ook een privéadres of een adres met een statisch adres maken, maar dat valt ook buiten het bereik van deze zelfstudie.

Voer deze cmdlet uit om uw openbare IP-adres te maken.

```powershell
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
```

### <a name="create-the-network-security-group"></a>De netwerkbeveiligingsgroep maken
Als u het VM- en SQL Server-verkeer wilt beveiligen, maakt u een netwerkbeveiligingsgroep.

1. Maak eerst een netwerkbeveiligingsgroepregel voor RDP om externe bureaubladverbindingen toe te staan.

   ```powershell
   $NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp `
      -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
   ```
1. Configureer een netwerkbeveiligingsgroepregel die verkeer op TCP-poort 1433 mogelijk maakt. Hierdoor kunnen verbindingen met SQL Server via internet worden verbroken.

   ```powershell
   $NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp `
      -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * `
      -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
   ```

1. Maak de netwerkbeveiligingsgroep.

   ```powershell
   $Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName `
      -Location $Location -Name $NsgName `
      -SecurityRules $NsgRuleRDP,$NsgRuleSQL
   ```

### <a name="create-the-network-interface"></a>De netwerkinterface maken
U bent nu klaar om de netwerkinterface voor uw virtuele machine te maken. Gebruik de cmdlet [Nieuw-AzNetworkInterface](https://docs.microsoft.com/powershell/module/az.network/new-aznetworkinterface) om uw netwerkinterface te maken in uw nieuwe brongroep. Geef de naam, locatie, subnet en openbaar IP-adres op dat eerder is gedefinieerd.

Voer deze cmdlet uit om uw netwerkinterface te maken.

```powershell
$Interface = New-AzNetworkInterface -Name $InterfaceName `
   -ResourceGroupName $ResourceGroupName -Location $Location `
   -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id `
   -NetworkSecurityGroupId $Nsg.Id
```

## <a name="configure-a-vm-object"></a>Een VM-object configureren
Nu opslag- en netwerkbronnen zijn gedefinieerd, u rekenbronnen voor de virtuele machine definiëren.

- Geef de grootte van de virtuele machine en de verschillende eigenschappen van het besturingssysteem op.
- Geef de netwerkinterface op die u eerder hebt gemaakt.
- Definieer blobopslag.
- Geef de schijf van het besturingssysteem op.

### <a name="create-the-vm-object"></a>Het VM-object maken
Begin met het opgeven van de grootte van de virtuele machine. Geef voor deze zelfstudie een DS13 op. Gebruik de [cmdlet Nieuw-AzVMConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azvmconfig) om een configureerbaar virtueel machineobject te maken. Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam en grootte.

Voer deze cmdlet uit om het virtuele machineobject te maken.

```powershell
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
```

### <a name="create-a-credential-object-to-hold-the-name-and-password-for-the-local-administrator-credentials"></a>Een referentieobject maken om de naam en het wachtwoord voor de lokale beheerdersreferenties vast te houden
Voordat u de eigenschappen van het besturingssysteem voor de virtuele machine instellen, moet u de referenties voor het lokale beheerdersaccount als een beveiligde tekenreeks leveren. Gebruik hiervoor de cmdlet [Get-Credential.](https://technet.microsoft.com/library/hh849815.aspx)

Voer de volgende cmdlet uit en typ in het venster PowerShell-referentieaanvraag de naam en het wachtwoord dat u wilt gebruiken voor het lokale beheerdersaccount in de virtuele machine.

```powershell
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
```

### <a name="set-the-operating-system-properties-for-the-virtual-machine"></a>De eigenschappen van het besturingssysteem instellen voor de virtuele machine
Nu bent u klaar om de eigenschappen van het besturingssysteem van de virtuele machine in te stellen met de [cmdlet Set-AzVMOperatingSystem.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmoperatingsystem)

- Stel het type besturingssysteem in als Windows.
- Moet de [virtuele machineagent](../../extensions/agent-windows.md) worden geïnstalleerd.
- Geef op dat de cmdlet automatische update inschakelt.
- Geef de variabelen op die u eerder hebt geïnitialiseerd voor de naam van de virtuele machine, de computernaam en de referenties.

Voer deze cmdlet uit om de eigenschappen van het besturingssysteem voor uw virtuele machine in te stellen.

```powershell
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine `
   -Windows -ComputerName $ComputerName -Credential $Credential `
   -ProvisionVMAgent -EnableAutoUpdate
```

### <a name="add-the-network-interface-to-the-virtual-machine"></a>De netwerkinterface toevoegen aan de virtuele machine
Gebruik vervolgens de cmdlet [Add-AzVMNetworkInterface](https://docs.microsoft.com/powershell/module/az.compute/add-azvmnetworkinterface) om de netwerkinterface toe te voegen met de variabele die u eerder hebt gedefinieerd.

Voer deze cmdlet uit om de netwerkinterface voor uw virtuele machine in te stellen.

```powershell
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
```

### <a name="set-the-blob-storage-location-for-the-disk-to-be-used-by-the-virtual-machine"></a>De blobopslaglocatie instellen voor de schijf die door de virtuele machine moet worden gebruikt
Stel vervolgens de blobopslaglocatie in voor de schijf van de vm met de variabelen die u eerder hebt gedefinieerd.

Voer deze cmdlet uit om de blobopslaglocatie in te stellen.

```powershell
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
```

### <a name="set-the-operating-system-disk-properties-for-the-virtual-machine"></a>De schijfeigenschappen van het besturingssysteem instellen voor de virtuele machine
Stel vervolgens de schijfeigenschappen van het besturingssysteem in voor de virtuele machine met de cmdlet [Set-AzVMOSDisk.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmosdisk) 

- Geef op dat het besturingssysteem voor de virtuele machine afkomstig is van een afbeelding.
- Caching alleen lezen instellen (omdat SQL Server op dezelfde schijf wordt geïnstalleerd).
- Geef de variabelen op die u eerder hebt geïnitialiseerd voor de VM-naam en de schijf van het besturingssysteem.

Voer deze cmdlet uit om de schijfeigenschappen van het besturingssysteem voor uw virtuele machine in te stellen.

```powershell
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name `
   $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage
```

### <a name="specify-the-platform-image-for-the-virtual-machine"></a>De platformafbeelding voor de virtuele machine opgeven
De laatste configuratiestap is het opgeven van de platformafbeelding voor uw virtuele machine. Gebruik voor deze zelfstudie de nieuwste SQL Server 2016 CTP-afbeelding. Gebruik de cmdlet [Set-AzVMSourceImage](https://docs.microsoft.com/powershell/module/az.compute/set-azvmsourceimage) om deze afbeelding te gebruiken met de variabelen die u eerder hebt gedefinieerd.

Voer deze cmdlet uit om de platformafbeelding voor uw virtuele machine op te geven.

```powershell
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine `
   -PublisherName $PublisherName -Offer $OfferName `
   -Skus $Sku -Version $Version
```

## <a name="create-the-sql-vm"></a>De SQL-VM maken
Nu u de configuratiestappen hebt voltooid, bent u klaar om de virtuele machine te maken. Gebruik de [nieuw-AzVM-cmdlet](https://docs.microsoft.com/powershell/module/az.compute/new-azvm) om de virtuele machine te maken met behulp van de variabelen die u hebt gedefinieerd.

> [!TIP]
> Het maken van de VM kan enkele minuten duren.

Voer deze cmdlet uit om uw virtuele machine te maken.

```powershell
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine
```

De virtuele machine wordt gemaakt.

> [!NOTE]
> Als u een fout krijgt over opstartdiagnoses, u deze negeren. Er wordt een standaardopslagaccount gemaakt voor opstartdiagnoses omdat het opgegeven opslagaccount voor de schijf van de virtuele machine een premium opslagaccount is.

## <a name="install-the-sql-iaas-agent"></a>SQL IaaS-agent installeren
VIRTUELE SQL Server-machines ondersteunen geautomatiseerde beheerfuncties met de [SQL Server IaaS Agent Extension.](virtual-machines-windows-sql-server-agent-extension.md) Als u de agent op de nieuwe VM wilt installeren en deze wilt registreren bij de resourceprovider, voert u de opdracht [Nieuw-AzSqlVM](/powershell/module/az.sqlvirtualmachine/new-azsqlvm) uit nadat de virtuele machine is gemaakt. Geef het licentietype voor uw SQL Server VM op en kies tussen betalen per gebruik of bring-your-own-license via het [Azure Hybrid Benefit.](https://azure.microsoft.com/pricing/hybrid-benefit/) Zie [licentiemodel voor](virtual-machines-windows-sql-ahb.md)meer informatie over licenties. 


   ```powershell
   New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
   ```


## <a name="stop-or-remove-a-vm"></a>Een virtuele machine stoppen of verwijderen

Als het niet nodig is dat de VM continu wordt uitgevoerd, kunt u onnodige kosten voorkomen door de virtuele machine te stoppen wanneer deze niet in gebruik is. Met de volgende opdracht wordt de VM gestopt, maar blijft deze beschikbaar voor toekomstig gebruik.

```powershell
Stop-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
```

U kunt ook alle resources die aan de virtuele machine zijn gekoppeld, definitief verwijderen met de opdracht **Remove-AzResourceGroup**. Wees voorzichtig met het gebruik van deze opdracht, want hiermee verwijdert u ook de virtuele machine zelf definitief.

## <a name="example-script"></a>Voorbeeldscript
Het volgende script bevat het volledige PowerShell-script voor deze zelfstudie. Er wordt van uitgegaan dat u het Azure-abonnement al hebt ingesteld voor gebruik met de opdrachten **Connect-AzAccount** en **Select-AzSubscription.**

```powershell
# Variables

## Global
$Location = "SouthCentralUS"
$ResourceGroupName = "sqlvm2"

## Storage
$StorageName = $ResourceGroupName + "storage"
$StorageSku = "Premium_LRS"

## Network
$InterfaceName = $ResourceGroupName + "ServerInterface"
$NsgName = $ResourceGroupName + "nsg"
$VNetName = $ResourceGroupName + "VNet"
$SubnetName = "Default"
$VNetAddressPrefix = "10.0.0.0/16"
$VNetSubnetAddressPrefix = "10.0.0.0/24"
$TCPIPAllocationMethod = "Dynamic"
$DomainName = $ResourceGroupName

##Compute
$VMName = $ResourceGroupName + "VM"
$ComputerName = $ResourceGroupName + "Server"
$VMSize = "Standard_DS13"
$OSDiskName = $VMName + "OSDisk"

##Image
$PublisherName = "MicrosoftSQLServer"
$OfferName = "SQL2017-WS2016"
$Sku = "SQLDEV"
$Version = "latest"

# Resource Group
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Storage
$StorageAccount = New-AzStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageName -SkuName $StorageSku -Kind "Storage" -Location $Location

# Network
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $VNetSubnetAddressPrefix
$VNet = New-AzVirtualNetwork -Name $VNetName -ResourceGroupName $ResourceGroupName -Location $Location -AddressPrefix $VNetAddressPrefix -Subnet $SubnetConfig
$PublicIp = New-AzPublicIpAddress -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -AllocationMethod $TCPIPAllocationMethod -DomainNameLabel $DomainName
$NsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name "RDPRule" -Protocol Tcp -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 3389 -Access Allow
$NsgRuleSQL = New-AzNetworkSecurityRuleConfig -Name "MSSQLRule"  -Protocol Tcp -Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * -DestinationPortRange 1433 -Access Allow
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location -Name $NsgName -SecurityRules $NsgRuleRDP,$NsgRuleSQL
$Interface = New-AzNetworkInterface -Name $InterfaceName -ResourceGroupName $ResourceGroupName -Location $Location -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PublicIp.Id -NetworkSecurityGroupId $Nsg.Id

# Compute
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$Credential = Get-Credential -Message "Type the name and password of the local administrator account."
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate #-TimeZone = $TimeZone
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Interface.Id
$OSDiskUri = $StorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $OSDiskName + ".vhd"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $OSDiskName -VhdUri $OSDiskUri -Caching ReadOnly -CreateOption FromImage

# Image
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName $PublisherName -Offer $OfferName -Skus $Sku -Version $Version

# Create the VM in Azure
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VirtualMachine

# Add the SQL IaaS Extension, and choose the license type
New-AzSqlVM -ResourceGroupName $ResourceGroupName -Name $VMName -Location $Location -LicenseType <PAYG/AHUB> 
```

## <a name="next-steps"></a>Volgende stappen
Nadat de virtuele machine is gemaakt, u:

- Verbinding maken met de virtuele machine via RDP
- SQL Server-instellingen configureren in de portal voor uw vm, waaronder:
   - [Opslaginstellingen](virtual-machines-windows-sql-server-storage-configuration.md) 
   - [Geautomatiseerde beheertaken](virtual-machines-windows-sql-server-agent-extension.md)
- [Connectiviteit configureren](virtual-machines-windows-sql-connect.md)
- Clients en toepassingen verbinden met het nieuwe SQL Server-exemplaar


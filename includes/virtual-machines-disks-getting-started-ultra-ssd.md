---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 05/11/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 4468025f6389d31269d9e587fca25390f19bdbbc
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84200318"
---
Azure Ultra disks biedt hoge door Voer, hoge IOPS en een consistente schijf opslag met lage latentie voor Azure IaaS virtual machines (Vm's). Deze nieuwe aanbieding biedt de hoogste prestaties van de lijn op dezelfde beschikbaarheids niveaus als onze bestaande schijven. Een belang rijk voor deel van ultra schijven is de mogelijkheid om de prestaties van de SSD in combi natie met uw workloads dynamisch te wijzigen zonder dat u uw Vm's opnieuw hoeft op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, databases in de bovenste laag en workloads met veel transacties.

## <a name="ga-scope-and-limitations"></a>GA bereik en beperkingen

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>De beschik baarheid van de VM-grootte en-regio bepalen

### <a name="vms-using-availability-zones"></a>Vm's met beschikbaarheids zones

Als u ultra disks wilt gebruiken, moet u bepalen in welke beschikbaarheids zone u zich bevindt. Niet elke regio ondersteunt elke VM-grootte met ultra schijven. Als u wilt bepalen of uw regio, zone en VM-grootte Ultra Disk ondersteunen, voert u een van de volgende opdrachten uit. Zorg ervoor dat u de **regio**-, **vmSize**-en **abonnements** waarden eerst vervangt:

#### <a name="cli"></a>CLI

```azurecli
subscription = "<yourSubID>"
# example value is southeastasia
region = "<yourLocation>"
# example value is Standard_E64s_v3
vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Het antwoord komt overeen met het onderstaande formulier, waarbij X de zone is die moet worden gebruikt voor de implementatie in de gekozen regio. X kan 1, 2 of 3 zijn.

De waarde **van de zone behouden** , het vertegenwoordigt uw beschikbaarheids zone en u hebt deze nodig om een ultra schijf te kunnen implementeren.

|ResourceType  |Name  |Locatie  |Zones  |Beperking  |Mogelijkheid  |Waarde  |
|---------|---------|---------|---------|---------|---------|---------|
|cd's     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Als er geen antwoord is ontvangen van de opdracht, wordt de geselecteerde VM-grootte niet ondersteund met ultra disks in de geselecteerde regio.

Nu u weet in welke zone u wilt implementeren, volgt u de implementatie stappen in dit artikel om een virtuele machine te implementeren met een ultra schijf die is gekoppeld of een ultra schijf aan een bestaande virtuele machine toe te voegen.

### <a name="vms-with-no-redundancy-options"></a>Vm's zonder opties voor redundantie

Ultra-schijven die zijn geïmplementeerd in het VS-West, moeten nu zonder enige redundantie opties worden geïmplementeerd. Niet elke schijf grootte die ondersteuning biedt voor Ultra schijven, kan echter wel deel uitmaken van deze regio. U kunt een van de volgende code fragmenten gebruiken om te bepalen welke van de VS-West-rom's ondersteunen. Zorg ervoor dat u de `vmSize` waarden en het `subscription` eerst vervangt:

```azurecli
subscription = "<yourSubID>"
region = "westus"
# example value is Standard_E64s_v3
vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Het antwoord ziet er ongeveer uit als in het volgende formulier, `UltraSSDAvailable   True` geeft aan of de VM-grootte Ultra schijven in deze regio ondersteunt.

```
Name                                         Value
----                                         -----
MaxResourceVolumeMB                          884736
OSVhdSizeMB                                  1047552
vCPUs                                        64
HyperVGenerations                            V1,V2
MemoryGB                                     432
MaxDataDiskCount                             32
LowPriorityCapable                           True
PremiumIO                                    True
VMDeploymentTypes                            IaaS
vCPUsAvailable                               64
ACUs                                         160
vCPUsPerCore                                 2
CombinedTempDiskAndCachedIOPS                128000
CombinedTempDiskAndCachedReadBytesPerSecond  1073741824
CombinedTempDiskAndCachedWriteBytesPerSecond 1073741824
CachedDiskBytes                              1717986918400
UncachedDiskIOPS                             80000
UncachedDiskBytesPerSecond                   1258291200
EphemeralOSDiskSupported                     True
AcceleratedNetworkingEnabled                 True
RdmaEnabled                                  False
MaxNetworkInterfaces                         8
UltraSSDAvailable                            True
```

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Een ultra schijf implementeren met behulp van Azure Resource Manager

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Zie de sectie [Ga naar bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-grootten.

Als u een virtuele machine met meerdere Ultra schijven wilt maken, raadpleegt u het voor beeld [een VM met meerdere Ultra schijven maken](https://aka.ms/ultradiskArmTemplate).

Als u uw eigen sjabloon wilt gebruiken, moet u ervoor zorgen dat **apiVersion** voor `Microsoft.Compute/virtualMachines` en `Microsoft.Compute/Disks` is ingesteld als `2018-06-01` (of hoger).

Stel de schijf-SKU in op **UltraSSD_LRS**en stel vervolgens de schijf capaciteit, IOPS, beschikbaarheids zone en door Voer in Mbps in om een ultra schijf te maken.

Zodra de VM is ingericht, kunt u de gegevens schijven partitioneren en Format teren, en configureren voor uw workloads.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Een ultra schijf implementeren met behulp van de Azure Portal

In deze sectie wordt beschreven hoe u een virtuele machine implementeert met een ultra schijf als een gegevens schijf. Hierbij wordt ervan uitgegaan dat u bekend bent met het implementeren van een virtuele machine. als dat niet het geval is, raadpleegt u onze [Snelstartgids: een virtuele Windows-machine maken in de Azure Portal](../articles/virtual-machines/windows/quick-create-portal.md).

- Meld u aan bij de [Azure Portal](https://portal.azure.com/) en navigeer om een virtuele machine (VM) te implementeren.
- Zorg ervoor dat u een [ondersteunde VM-grootte en-regio](#ga-scope-and-limitations)kiest.
- Selecteer **beschikbaarheids zone** in **beschik bare opties**.
- Vul de resterende items in met de selecties van uw keuze.
- Selecteer **Schijven**.

![Create-Ultra-Disk-enabled-VM. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Selecteer op de Blade schijven de optie **Ja** voor het **inschakelen van ultra Disk-compatibiliteit**.
- Selecteer **maken en koppel een nieuwe schijf** om nu een ultra schijf te koppelen.

![Enable-and-attach-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Voer op de Blade **een nieuwe schijf maken** een naam in en selecteer vervolgens **grootte wijzigen**.
- Wijzig het **account type** in **Ultra Disk**.
- Wijzig de waarden van **aangepaste schijf grootte (GIB)**, **schijf-IOPS**en **schijf doorvoer** naar keuze.
- Selecteer **OK** in beide Blades.
- Ga verder met de implementatie van de virtuele machine. Dit is dezelfde manier als bij het implementeren van een andere VM.

![Create-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Een ultra schijf koppelen met behulp van de Azure Portal

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken. Door Ultra schijven op uw bestaande virtuele machine in te scha kelen en vervolgens als gegevens schijven te koppelen.

- Navigeer naar uw VM en selecteer **schijven**.
- Selecteer **bewerken**.

![Options-selector-Ultra-disks. png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selecteer **Ja** als u **Ultra Disk-compatibiliteit wilt inschakelen**.

![Ultra-Options-Yes-Enable. png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selecteer **Opslaan**.
- Selecteer **gegevens schijf toevoegen** en selecteer in de vervolg keuzelijst voor **naam** de optie **schijf maken**.

![Create-and-attach-New-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Vul een naam in voor de nieuwe schijf en selecteer vervolgens **grootte wijzigen**.
- Wijzig het **account type** in **Ultra Disk**.
- Wijzig de waarden van **aangepaste schijf grootte (GIB)**, **schijf-IOPS**en **schijf doorvoer** naar keuze.
- Selecteer **OK** en selecteer vervolgens **maken**.

![Making-a-New-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Wanneer u terugkeert naar de Blade van uw schijf, selecteert u **Opslaan**.

![Saving-and-Attaching-New-Ultra-disk. png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>De prestaties van een ultra schijf aanpassen met behulp van de Azure Portal

Ultra disks bieden een unieke mogelijkheid waarmee u de prestaties ervan kunt aanpassen. U kunt deze aanpassingen aanbrengen vanaf de Azure Portal, op de schijven zelf.

- Navigeer naar uw VM en selecteer **schijven**.
- Selecteer de ultra schijf waarvan u de prestaties wilt wijzigen.

![selecting-Ultra-disk-to-Modify. png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selecteer **configuratie** en breng vervolgens de gewenste wijzigingen aan.
- Selecteer **Opslaan**.

![Configuring-Ultra-Disk-Performance-and-size. png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Een ultra schijf implementeren met behulp van CLI

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Zie de sectie [Ga bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-grootten.

U moet een virtuele machine maken die geschikt is voor het gebruik van ultra disks om een ultra schijf te koppelen.

Vervang of stel de **$vmname**, **$rgname**, **$diskname**, **$Location**, **$Password** **$User** variabelen met uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheids zone die u aan het [begin van dit artikel](#determine-vm-size-and-region-availability)hebt gekregen. Voer vervolgens de volgende CLI-opdracht uit om een virtuele-VM te maken:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Compatibiliteit met hoge schijven inschakelen op een bestaande virtuele machine

Als uw virtuele machine voldoet aan de vereisten die worden beschreven in de stappen [en beperkingen](#ga-scope-and-limitations) van het oog en die zich in de [juiste zone voor uw account](#determine-vm-size-and-region-availability)bevinden, kunt u compatibiliteit met hoge schijven inschakelen op uw virtuele machine.

Als u ultra Disk-compatibiliteit wilt inschakelen, moet u de virtuele machine stoppen. Nadat u de virtuele machine hebt gestopt, kunt u de compatibiliteit inschakelen, een ultra schijf koppelen en vervolgens de virtuele machine opnieuw opstarten:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Een ultra schijf maken met CLI

Nu u een virtuele machine hebt die geschikt is voor het koppelen van ultra schijven, kunt u een ultra schijf maken en koppelen.

```azurecli-interactive
location="eastus2"
subscription="xxx"
rgname="ultraRG"
diskname="ssd1"
vmname="ultravm1"
zone=123

#create an ultra disk
az disk create `
--subscription $subscription `
-n $diskname `
-g $rgname `
--size-gb 4 `
--location $location `
--zone $zone `
--sku UltraSSD_LRS `
--disk-iops-read-write 1000 `
--disk-mbps-read-write 50
```

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Een ultra schijf koppelen aan een virtuele machine met behulp van CLI

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken.

```azurecli
rgName = "<yourResourceGroupName>"
vmName = "<yourVMName>"
diskName = "<yourDiskName>"
subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>De prestaties van een ultra schijf aanpassen met behulp van CLI

Ultra disks bieden een unieke mogelijkheid waarmee u de prestaties kunt aanpassen. de volgende opdracht ziet u hoe u deze functie kunt gebruiken:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Een ultra schijf implementeren met behulp van Power shell

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Zie de sectie [Ga bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-grootten.

Als u ultra disks wilt gebruiken, moet u een virtuele machine maken die geschikt is voor het gebruik van ultra Disk-schijven. Vervang of stel de **$ResourceGroup** en **$vmName** variabelen met uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheids zone die u aan het [begin van dit artikel](#determine-vm-size-and-region-availability)hebt gekregen. Voer vervolgens de volgende opdracht uit voor het maken van een virtuele machine met een [nieuwe AzVm](/powershell/module/az.compute/new-azvm) :

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD $true `
    -size "Standard_D4s_v3" `
    -zone $zone
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Compatibiliteit met hoge schijven inschakelen op een bestaande virtuele machine

Als uw virtuele machine voldoet aan de vereisten die worden beschreven in de stappen [en beperkingen](#ga-scope-and-limitations) van het oog en die zich in de [juiste zone voor uw account](#determine-vm-size-and-region-availability)bevinden, kunt u compatibiliteit met hoge schijven inschakelen op uw virtuele machine.

Als u ultra Disk-compatibiliteit wilt inschakelen, moet u de virtuele machine stoppen. Nadat u de virtuele machine hebt gestopt, kunt u de compatibiliteit inschakelen, een ultra schijf koppelen en vervolgens de virtuele machine opnieuw opstarten:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Een ultra schijf maken met Power shell

Nu u een virtuele machine hebt die geschikt is voor het gebruik van ultra schijven, kunt u een ultra schijf maken en koppelen:

```powershell
$diskconfig = New-AzDiskConfig `
-Location 'EastUS2' `
-DiskSizeGB 8 `
-DiskIOPSReadWrite 1000 `
-DiskMBpsReadWrite 100 `
-AccountType UltraSSD_LRS `
-CreateOption Empty `
-zone $zone;

New-AzDisk `
-ResourceGroupName $resourceGroup `
-DiskName 'Disk02' `
-Disk $diskconfig;
```

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Een ultra schijf koppelen aan een virtuele machine met behulp van Power shell

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken.

```powershell
# add disk to VM
$subscription = "<yourSubscriptionID>"
$resourceGroup = "<yourResourceGroup>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$lun = 1
Login-AzureRMAccount -SubscriptionId $subscription
$vm = Get-AzVM -ResourceGroupName $resourceGroup -Name $vmName
$disk = Get-AzDisk -ResourceGroupName $resourceGroup -Name $diskName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun $lun
Update-AzVM -VM $vm -ResourceGroupName $resourceGroup
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>De prestaties van een ultra schijf aanpassen met behulp van Power shell

Ultra disks hebben een unieke functie waarmee u de prestaties kunt aanpassen, de volgende opdracht is een voor beeld waarmee de prestaties worden aangepast zonder dat de schijf moet worden losgekoppeld:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```

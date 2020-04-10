---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 04/08/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: dfb094bc9f84e7129a3e1c733a054c5f6cd96372
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81008625"
---
Azure ultra disks bieden een hoge doorvoer, hoge IOPS en consistente schijfopslag met lage latentie voor Azure IaaS virtuele machines (VM's). Dit nieuwe aanbod biedt top prestaties op dezelfde beschikbaarheid sommals als onze bestaande schijven aanbod. Een groot voordeel van ultra schijven is de mogelijkheid om de prestaties van de SSD dynamisch te veranderen, samen met uw workloads zonder de noodzaak om uw VM's opnieuw op te starten. Ultraschijven zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, databases in de bovenste laag en workloads met veel transacties.

## <a name="ga-scope-and-limitations"></a>GA-bereik en beperkingen

[!INCLUDE [managed-disks-ultra-disks-GA-scope-and-limitations](managed-disks-ultra-disks-GA-scope-and-limitations.md)]

## <a name="determine-vm-size-and-region-availability"></a>Vm-grootte en regiobeschikbaarheid bepalen

### <a name="vms-using-availability-zones"></a>VM's met beschikbaarheidszones

Als u gebruik wilt maken van ultraschijven, moet u bepalen in welke beschikbaarheidszone u zich bevindt. Niet elke regio ondersteunt elke VM-grootte met ultraschijven. Als u wilt bepalen of uw regio, zone en VM-grootte ultraschijven ondersteunen, moet u een van de volgende opdrachten uitvoeren, moet u eerst de **regio,** **vmSize**en **abonnementswaarden** vervangen:

#### <a name="cli"></a>CLI

```azurecli
$subscription = "<yourSubID>"
# example value is southeastasia
$region = "<yourLocation>"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

#### <a name="powershell"></a>PowerShell

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Het antwoord is vergelijkbaar met het onderstaande formulier, waarbij X de zone is die moet worden gebruikt voor implementatie in de door u gekozen regio. X kan 1, 2 of 3 zijn.

Behouden van de **waarde zones,** het vertegenwoordigt uw beschikbaarheid zone en je hebt het nodig om een Ultra schijf te implementeren.

|ResourceType  |Name  |Locatie  |Zones  |Beperking  |Mogelijkheid  |Waarde  |
|---------|---------|---------|---------|---------|---------|---------|
|Schijven     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Als er geen antwoord van de opdracht is ontvangen, wordt de geselecteerde VM-grootte niet ondersteund met ultraschijven in het geselecteerde gebied.

Nu u weet naar welke zone u moet worden geïmplementeerd, volgt u de implementatiestappen in dit artikel om een VM met een ultraschijf te implementeren of een ultraschijf aan een bestaande VM te koppelen.

### <a name="vms-with-no-redundancy-options"></a>VM's zonder redundantieopties

Ultra schijven geïmplementeerd in West US moet worden ingezet zonder redundantie opties, voor nu. Echter, niet elke schijfgrootte die ultra schijven ondersteunt, mag zich in deze regio bevinden. Om te bepalen welke in West US ultraschijven ondersteunen, u een van de volgende codefragmenten gebruiken. Zorg ervoor dat `vmSize` `subscription` u de waarden en waarden eerst vervangt:

```azurecli
$subscription = "<yourSubID>"
$region = "westus"
# example value is Standard_E64s_v3
$vmSize = "<yourVMSize>"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].capabilities" --subscription $subscription
```

```azurepowershell
$region = "westus"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) })[0].Capabilities
```

Het antwoord is vergelijkbaar met `UltraSSDAvailable   True` het volgende formulier en geeft aan of de VM-grootte ultraschijven in deze regio ondersteunt.

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

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Een ultraschijf implementeren met Azure Resource Manager

Bepaal eerst de VM-grootte die moet worden geïmplementeerd. Zie de sectie [GA-scope en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-formaten.

Als u een VM met meerdere ultraschijven wilt maken, raadpleegt u het voorbeeld [Een vm maken met meerdere ultraschijven.](https://aka.ms/ultradiskArmTemplate)

Als u van plan bent om uw eigen `Microsoft.Compute/virtualMachines` `Microsoft.Compute/Disks` sjabloon te gebruiken, zorg ervoor dat **apiVersion** voor en is ingesteld als `2018-06-01` (of later).

Stel de schijfsku in op **UltraSSD_LRS**en stel vervolgens de schijfcapaciteit, IOPS, beschikbaarheidszone en doorvoer in MBps in om een ultraschijf te maken.

Zodra de vm is ingericht, u de gegevensschijven partitioneren en opmaken en configureren voor uw workloads.


## <a name="deploy-an-ultra-disk-using-the-azure-portal"></a>Een ultraschijf implementeren met de Azure-portal

Deze sectie omvat de implementatie van een virtuele machine uitgerust met een ultra schijf als een dataschijf. Het gaat ervan uit dat u vertrouwd bent met het implementeren van een virtuele machine, als u dat niet doet, zie onze [Quickstart: Maak een Virtuele Windows-machine in de Azure-portal.](../articles/virtual-machines/windows/quick-create-portal.md)

- Meld u aan bij de [Azure-portal](https://portal.azure.com/) en navigeer om een virtuele machine (VM) te implementeren.
- Zorg ervoor dat u een [ondersteunde VM-grootte en -regio kiest.](#ga-scope-and-limitations)
- Selecteer **Beschikbaarheidszone** in **beschikbaarheidsopties**.
- Vul de overige items in met selecties naar keuze.
- Selecteer **Schijven**.

![create-ultra-disk-enabled-vm.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk-enabled-vm.png)

- Selecteer op het blade Schijven de optie **Ja** voor Compatibiliteit **met ultraschijf inschakelen**.
- Selecteer **Een nieuwe schijf maken en koppelen** om nu een ultraschijf te bevestigen.

![enable-and-attach-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/enable-and-attach-ultra-disk.png)

- Voer **in het nieuwe schijfblad een** naam maken in en selecteer **Grootte wijzigen**.
- Wijzig het **accounttype** in **Ultra Disk**.
- Wijzig de waarden **van aangepaste schijfgrootte (GiB),** **SchijfIOPS**en **Schijfdoorvoer** in de waarden van uw keuze.
- Selecteer **OK** in beide bladen.
- Ga door met de VM-implementatie, het zal hetzelfde zijn als wanneer u elke andere VM zou implementeren.

![create-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-ultra-disk.png)

## <a name="attach-an-ultra-disk-using-the-azure-portal"></a>Een ultraschijf koppelen met de Azure-portal

Als uw bestaande VM zich in een regio/beschikbaarheidszone bevindt die ultraschijven kan gebruiken, u ook gebruik maken van ultraschijven zonder dat u een nieuwe vm hoeft te maken. Door ultraschijven in te schakelen op uw bestaande VM en deze vervolgens als gegevensschijven te koppelen.

- Navigeer naar uw virtuele machine en selecteer **Schijven**.
- Selecteer **Bewerken**.

![options-selector-ultra-disks.png](media/virtual-machines-disks-getting-started-ultra-ssd/options-selector-ultra-disks.png)

- Selecteer **Ja** voor **Compatibiliteit met ultraschijf inschakelen**.

![ultra-options-yes-enable.png](media/virtual-machines-disks-getting-started-ultra-ssd/ultra-options-yes-enable.png)

- Selecteer **Opslaan**.
- Selecteer **Gegevensschijf toevoegen** en selecteer vervolgens in de vervolgkeuzelijst voor **Naam** selecteer **Schijf maken**.

![create-and-attach-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/create-and-attach-new-ultra-disk.png)

- Vul een naam in voor uw nieuwe schijf en selecteer **Grootte wijzigen**.
- Wijzig het **accounttype** in **Ultra Disk**.
- Wijzig de waarden **van aangepaste schijfgrootte (GiB),** **SchijfIOPS**en **Schijfdoorvoer** in de waarden van uw keuze.
- Selecteer **OK** en selecteer **Maken**.

![making-a-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/making-a-new-ultra-disk.png)

- Nadat u bent teruggestuurd naar het blad van uw schijf, selecteert u **Opslaan**.

![saving-and-attaching-new-ultra-disk.png](media/virtual-machines-disks-getting-started-ultra-ssd/saving-and-attaching-new-ultra-disk.png)

### <a name="adjust-the-performance-of-an-ultra-disk-using-the-azure-portal"></a>De prestaties van een ultraschijf aanpassen met behulp van de Azure-portal

Ultra schijven bieden een unieke mogelijkheid waarmee u hun prestaties aanpassen. U deze aanpassingen uitvoeren vanuit de Azure-portal, op de schijven zelf.

- Navigeer naar uw virtuele machine en selecteer **Schijven**.
- Selecteer de ultraschijf waarvan u de prestaties wilt wijzigen.

![selecteren-ultra-disk-to-modify.png](media/virtual-machines-disks-getting-started-ultra-ssd/selecting-ultra-disk-to-modify.png)

- Selecteer **Configuratie** en breng vervolgens uw wijzigingen aan.
- Selecteer **Opslaan**.

![configureren-ultra-disk-performance-and-size.png](media/virtual-machines-disks-getting-started-ultra-ssd/configuring-ultra-disk-performance-and-size.png)

## <a name="deploy-an-ultra-disk-using-cli"></a>Een ultraschijf implementeren met CLI

Bepaal eerst de VM-grootte die moet worden geïmplementeerd. Zie de sectie [GA-bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-formaten.

U moet een VM maken die ultraschijven kan gebruiken om een ultraschijf te bevestigen.

Vervang of stel de **$vmname**, **$rgname,** **$diskname,** **$location,** **$password**$user variabelen **in** met uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheidszone die u vanaf het [begin van dit artikel](#determine-vm-size-and-region-availability)hebt gekregen. Voer vervolgens de volgende OPDRACHT CLI uit om een vm met ultra-ingeschakelde ring te maken:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Ultradiskcompatibiliteit inschakelen op een bestaande virtuele machine

Als uw vm voldoet aan de vereisten die zijn beschreven in [GA-scope en -beperkingen](#ga-scope-and-limitations) en zich in de [juiste zone voor uw account bevindt,](#determine-vm-size-and-region-availability)u ultradiskcompatibiliteit op uw vm inschakelen.

Als u ultraschijfcompatibiliteit wilt inschakelen, moet u de vm stoppen. Nadat u de VM hebt gestopt, u compatibiliteit inschakelen, een ultraschijf koppelen en de VM opnieuw starten:

```azurecli
az vm deallocate -n $vmName -g $rgName
az vm update -n $vmName -g $rgName --ultra-ssd-enabled true
az vm start -n $vmName -g $rgName
```

### <a name="create-an-ultra-disk-using-cli"></a>Een ultraschijf maken met CLI

Nu u een VM hebt die ultraschijven kan bevestigen, u er een ultraschijf aan maken en eraan koppelen.

```azurecli-interactive
$location="eastus2"
$subscription="xxx"
$rgname="ultraRG"
$diskname="ssd1"
$vmname="ultravm1"
$zone=123

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Een ultraschijf aan een vm koppelen met CLI

Als uw bestaande VM zich in een regio/beschikbaarheidszone bevindt die ultraschijven kan gebruiken, u ook gebruik maken van ultraschijven zonder dat u een nieuwe vm hoeft te maken.

```azurecli
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

az vm disk attach -g $rgName --vm-name $vmName --disk $diskName --subscription $subscriptionId
```

### <a name="adjust-the-performance-of-an-ultra-disk-using-cli"></a>De prestaties van een ultraschijf aanpassen met CLI

Ultra schijven bieden een unieke mogelijkheid waarmee u hun prestaties aan te passen, de volgende opdracht toont hoe deze functie te gebruiken:

```azurecli-interactive
az disk update `
--subscription $subscription `
--resource-group $rgname `
--name $diskName `
--set diskIopsReadWrite=80000 `
--set diskMbpsReadWrite=800
```

## <a name="deploy-an-ultra-disk-using-powershell"></a>Een ultraschijf implementeren met PowerShell

Bepaal eerst de VM-grootte die moet worden geïmplementeerd. Zie de sectie [GA-bereik en beperkingen](#ga-scope-and-limitations) voor een lijst met ondersteunde VM-formaten.

Als u ultraschijven wilt gebruiken, moet u een VM maken die ultraschijven kan gebruiken. Vervang of stel de **$resourcegroup-** en **$vmName** variabelen in op uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheidszone die u vanaf het [begin van dit artikel](#determine-vm-size-and-region-availability)hebt gekregen. Voer vervolgens de volgende opdracht [Nieuw-AzVm](/powershell/module/az.compute/new-azvm) uit om een vm met ultra-ingeschakelde ring te maken:

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

### <a name="enable-ultra-disk-compatibility-on-an-existing-vm"></a>Ultradiskcompatibiliteit inschakelen op een bestaande virtuele machine

Als uw vm voldoet aan de vereisten die zijn beschreven in [GA-scope en -beperkingen](#ga-scope-and-limitations) en zich in de [juiste zone voor uw account bevindt,](#determine-vm-size-and-region-availability)u ultradiskcompatibiliteit op uw vm inschakelen.

Als u ultraschijfcompatibiliteit wilt inschakelen, moet u de vm stoppen. Nadat u de VM hebt gestopt, u compatibiliteit inschakelen, een ultraschijf koppelen en de VM opnieuw starten:

```azurepowershell
#stop the VM
$vm1 = Get-AzureRMVM -name $vmName -ResourceGroupName $rgName
Update-AzureRmVM -ResourceGroupName $rgName -VM $vm1 -UltraSSDEnabled 1
#start the VM
```

### <a name="create-an-ultra-disk-using-powershell"></a>Een ultraschijf maken met PowerShell

Nu u een VM hebt die ultraschijven kan gebruiken, u er een ultraschijf aan maken en eraan koppelen:

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

## <a name="attach-an-ultra-disk-to-a-vm-using-powershell"></a>Een ultraschijf aan een virtuele machine koppelen met PowerShell

Als uw bestaande VM zich in een regio/beschikbaarheidszone bevindt die ultraschijven kan gebruiken, u ook gebruik maken van ultraschijven zonder dat u een nieuwe vm hoeft te maken.

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

### <a name="adjust-the-performance-of-an-ultra-disk-using-powershell"></a>De prestaties van een ultraschijf aanpassen met PowerShell

Ultra schijven hebben een unieke mogelijkheid waarmee u hun prestaties aan te passen, de volgende opdracht is een voorbeeld dat de prestaties aanpast zonder dat de schijf los:

```powershell
$diskupdateconfig = New-AzDiskUpdateConfig -DiskMBpsReadWrite 2000
Update-AzDisk -ResourceGroupName $resourceGroup -DiskName $diskName -DiskUpdate $diskupdateconfig
```
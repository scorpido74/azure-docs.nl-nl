---
title: bestand opnemen
description: bestand opnemen
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/15/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 3f910a3d0466153bd60fe23ef2f9f656cac292ee
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70919667"
---
# <a name="using-azure-ultra-disks"></a>Azure Ultra Disk gebruiken

Azure Ultra disks biedt hoge door Voer, hoge IOPS en een consistente schijf opslag met lage latentie voor Azure IaaS virtual machines (Vm's). Deze nieuwe aanbieding biedt de hoogste prestaties van de lijn op dezelfde beschikbaarheids niveaus als onze bestaande schijven. Een belang rijk voor deel van ultra schijven is de mogelijkheid om de prestaties van de SSD in combi natie met uw workloads dynamisch te wijzigen zonder dat u uw Vm's opnieuw hoeft op te starten. Ultra disks zijn geschikt voor gegevensintensieve workloads, zoals SAP HANA, data bases in de bovenste laag en transactie zware werk belastingen.

## <a name="check-if-your-subscription-has-access"></a>Controleren of uw abonnement toegang heeft

Als u zich al hebt geregistreerd voor Ultra schijven en u wilt controleren of uw abonnement is ingeschakeld voor Ultra schijven, gebruikt u een van de volgende opdrachten: 

CLI`az feature show --namespace Microsoft.Compute --name UltraSSD`

PowerShell: `Get-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName UltraSSD`

Als uw abonnement is ingeschakeld, ziet de uitvoer er ongeveer als volgt uit:

```bash
{
  "id": "/subscriptions/<yoursubID>/providers/Microsoft.Features/providers/Microsoft.Compute/features/UltraSSD",
  "name": "Microsoft.Compute/UltraSSD",
  "properties": {
    "state": "Registered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

## <a name="determine-your-availability-zone"></a>Uw beschikbaarheids zone bepalen

Na goed keuring moet u bepalen in welke beschikbaarheids zone u zich bevindt, om ultra schijven te kunnen gebruiken. Voer een van de volgende opdrachten uit om te bepalen welke zone uw Ultra schijf moet implementeren. Vervang eerst de **regio**-, **vmSize**-en **abonnements** waarden door:

CLI:

```bash
$subscription = "<yourSubID>"
$region = "<yourLocation>, example value is southeastasia"
$vmSize = "<yourVMSize>, example value is Standard_E64s_v3"

az vm list-skus --resource-type virtualMachines  --location $region --query "[?name=='$vmSize'].locationInfo[0].zoneDetails[0].Name" --subscription $subscription
```

PowerShell:

```powershell
$region = "southeastasia"
$vmSize = "Standard_E64s_v3"
(Get-AzComputeResourceSku | where {$_.Locations.Contains($region) -and ($_.Name -eq $vmSize) -and $_.LocationInfo[0].ZoneDetails.Count -gt 0})[0].LocationInfo[0].ZoneDetails
```

Het antwoord komt overeen met het onderstaande formulier, waarbij X de zone is die moet worden gebruikt voor de implementatie in de gekozen regio. X kan 1, 2 of 3 zijn. Momenteel ondersteunen slechts drie regio's Ultra disks: VS-Oost 2, Zuidoost-Azië en Europa-noord.

De waarde **van de zone behouden** , het vertegenwoordigt uw beschikbaarheids zone en u hebt deze nodig om een ultra schijf te kunnen implementeren.

|ResourceType  |Name  |Location  |Zones  |Beperking  |Mogelijkheid  |Value  |
|---------|---------|---------|---------|---------|---------|---------|
|schijven     |UltraSSD_LRS         |eastus2         |X         |         |         |         |

> [!NOTE]
> Als er geen antwoord van de opdracht is ontvangen, is de registratie van de functie nog in behandeling of gebruikt u een oude versie van CLI of Power shell.

Nu u weet in welke zone u wilt implementeren, volgt u de implementatie stappen in dit artikel om een virtuele machine te implementeren met een ultra schijf die is gekoppeld of een ultra schijf aan een bestaande virtuele machine toe te voegen.

## <a name="deploy-an-ultra-disk-using-azure-resource-manager"></a>Een ultra schijf implementeren met behulp van Azure Resource Manager

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Nu ondersteunen alleen DsV3-en EsV3-VM-families Ultra disks. Raadpleeg de tweede tabel in dit [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) voor meer informatie over deze VM-grootten.

Als u een virtuele machine met meerdere Ultra schijven wilt maken, raadpleegt u het voor beeld [een VM met meerdere Ultra schijven maken](https://aka.ms/ultradiskArmTemplate).

Als u uw eigen sjabloon wilt gebruiken, moet u ervoor zorgen dat apiVersion `Microsoft.Compute/virtualMachines` voor `Microsoft.Compute/Disks` en is ingesteld `2018-06-01` als (of hoger).

Stel de schijf-SKU in op **UltraSSD_LRS**en stel vervolgens de schijf capaciteit, IOPS, beschikbaarheids zone en door Voer in Mbps in om een ultra schijf te maken.

Zodra de VM is ingericht, kunt u de gegevens schijven partitioneren en Format teren, en configureren voor uw workloads.

## <a name="deploy-an-ultra-disk-using-cli"></a>Een ultra schijf implementeren met behulp van CLI

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Nu ondersteunen alleen DsV3-en EsV3-VM-families Ultra disks. Raadpleeg de tweede tabel in dit [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) voor meer informatie over deze VM-grootten.

U moet een virtuele machine maken die geschikt is voor het gebruik van ultra disks om een ultra schijf te koppelen.

Vervang of stel de **$vmname**, **$rgname**, **$diskname**, **$Location**, **$Password** **$User** variabelen met uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheids zone die u aan het [begin van dit artikel](#determine-your-availability-zone)hebt gekregen. Voer vervolgens de volgende CLI-opdracht uit om een virtuele-VM te maken:

```azurecli-interactive
az vm create --subscription $subscription -n $vmname -g $rgname --image Win2016Datacenter --ultra-ssd-enabled true --zone $zone --authentication-type password --admin-password $password --admin-username $user --size Standard_D4s_v3 --location $location
```

### <a name="create-an-ultra-disk-using-cli"></a>Een ultra schijf maken met CLI

Nu u een virtuele machine hebt die geschikt is voor het koppelen van ultra schijven, kunt u een ultra schijf maken en koppelen.

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

## <a name="attach-an-ultra-disk-to-a-vm-using-cli"></a>Een ultra schijf koppelen aan een virtuele machine met behulp van CLI

Als uw bestaande virtuele machine zich in een regio/beschikbaarheids zone bevindt die geschikt is voor het gebruik van ultra schijven, kunt u ook gebruikmaken van ultra schijven zonder dat u een nieuwe virtuele machine hoeft te maken.

```bash
$rgName = "<yourResourceGroupName>"
$vmName = "<yourVMName>"
$diskName = "<yourDiskName>"
$subscriptionId = "<yourSubscriptionID>"

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

Bepaal eerst welke VM-grootte moet worden geïmplementeerd. Nu ondersteunen alleen DsV3-en EsV3-VM-families Ultra disks. Raadpleeg de tweede tabel in dit [blog](https://azure.microsoft.com/blog/introducing-the-new-dv3-and-ev3-vm-sizes/) voor meer informatie over deze VM-grootten.

Als u ultra disks wilt gebruiken, moet u een virtuele machine maken die geschikt is voor het gebruik van ultra Disk-schijven. Vervang of stel de **$ResourceGroup** en **$vmName** variabelen met uw eigen waarden. Stel **$zone** in op de waarde van uw beschikbaarheids zone die u aan het [begin van dit artikel](#determine-your-availability-zone)hebt gekregen. Voer vervolgens de volgende opdracht uit voor het maken van een virtuele machine met een [nieuwe AzVm](/powershell/module/az.compute/new-azvm) :

```powershell
New-AzVm `
    -ResourceGroupName $resourcegroup `
    -Name $vmName `
    -Location "eastus2" `
    -Image "Win2016Datacenter" `
    -EnableUltraSSD `
    -size "Standard_D4s_v3" `
    -zone $zone
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

## <a name="next-steps"></a>Volgende stappen

Als u het nieuwe type schijf verzoek toegang wilt proberen [met deze enquête](https://aka.ms/UltraDiskSignup).
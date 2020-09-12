---
title: 'Voor beeld: een installatie kopie versie maken die is versleuteld met uw eigen sleutels'
description: Maak een installatie kopie versie in een galerie met gedeelde afbeeldingen met door de klant beheerde versleutelings sleutels.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 08/11/2020
ms.author: cynthn
ms.openlocfilehash: 91f485d03717ab80bac26abd16da165d7b0dead7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/02/2020
ms.locfileid: "89291922"
---
# <a name="preview-use-customer-managed-keys-for-encrypting-images"></a>Voor beeld: door de klant beheerde sleutels gebruiken voor het versleutelen van installatie kopieën

Galerie-installatie kopieën worden opgeslagen als managed disks, zodat ze automatisch worden versleuteld met versleuteling aan de server zijde. Versleuteling aan de server zijde maakt gebruik van 256-bits [AES-versleuteling](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), een van de krach tigste blok cijfers die beschikbaar zijn en is compatibel met FIPS 140-2. Zie [crypto GRAFIE API: Next Generation](/windows/desktop/seccng/cng-portal) (Engelstalig) voor meer informatie over de onderliggende cryptografische modules die worden beheerd door Azure Managed disks.

U kunt vertrouwen op door het platform beheerde sleutels voor de versleuteling van uw installatie kopieën, uw eigen sleutels gebruiken of u kunt beide gebruiken voor dubbele versleuteling. Als u ervoor kiest om versleuteling met uw eigen sleutels te beheren, kunt u een door de *klant beheerde sleutel* opgeven die moet worden gebruikt voor het versleutelen en ontsleutelen van alle schijven in uw installatie kopieën. 

Versleuteling aan de server zijde met door de klant beheerde sleutels maakt gebruik van Azure Key Vault. U kunt [uw RSA-sleutels](../key-vault/keys/hsm-protected-keys.md) importeren naar uw Key Vault of nieuwe RSA-sleutels in azure Key Vault genereren.

## <a name="prerequisites"></a>Vereisten

Voor dit artikel moet u al een schijf versleuteling instellen die voor uw installatie kopie moet worden gebruikt.

- Als u alleen een door de klant beheerde sleutel wilt gebruiken, raadpleegt u door de **klant beheerde sleutels met versleuteling aan de server zijde inschakelen** met behulp van de [Azure Portal](./disks-enable-customer-managed-keys-portal.md) of [Power shell](./windows/disks-enable-customer-managed-keys-powershell.md#set-up-your-azure-key-vault-and-diskencryptionset).

- Als u zowel door het platform beheerde als door de klant beheerde sleutels wilt gebruiken (voor dubbele versleuteling), raadpleegt u **dubbele versleuteling inschakelen bij rest** met behulp van de [Azure Portal](./disks-enable-double-encryption-at-rest-portal.md) of [Power shell](./windows/disks-enable-double-encryption-at-rest-powershell.md).
    > [!IMPORTANT]
    > U moet deze koppeling gebruiken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) om toegang te krijgen tot de Azure Portal. Dubbele versleuteling bij rest is momenteel niet zichtbaar in de open bare Azure Portal zonder de koppeling te gebruiken.

## <a name="limitations"></a>Beperkingen

Er zijn enkele beperkingen bij het gebruik van door de klant beheerde sleutels voor het versleutelen van afbeeldingen uit de galerie met gedeelde afbeeldingen:  

- Versleutelings sleutel sets moeten zich in hetzelfde abonnement en dezelfde regio bevinden als uw installatie kopie.

- U kunt geen afbeeldingen delen die gebruikmaken van door de klant beheerde sleutels. 

- U kunt geen installatie kopieën repliceren die door de klant beheerde sleutels worden gebruikt naar andere regio's.

- Wanneer u uw eigen sleutels hebt gebruikt om een schijf of installatie kopie te versleutelen, kunt u niet teruggaan naar met een platform beheerde sleutel voor het versleutelen van die schijven of installatie kopieën.


> [!IMPORTANT]
> Versleuteling met door de klant beheerde sleutels is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


## <a name="powershell"></a>PowerShell

Voor de open bare Preview moet u eerst de functie registreren.

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Het duurt enkele minuten voordat de registratie is voltooid. Gebruik Get-AzProviderFeature om de status van de functie registratie te controleren.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName SIGEncryption -ProviderNamespace Microsoft.Compute
```

Wanneer RegistrationState is geregistreerd, kunt u door gaan met de volgende stap.

Controleer de registratie van uw provider. Controleer of het wordt geretourneerd `Registered` .

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
```

Als dat niet het geval `Registered` is, gebruikt u het volgende om de providers te registreren:

```azurepowershell-interactive
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```

Als u een schijf versleuteling wilt opgeven voor een installatie kopie versie, gebruikt u  [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion) met de `-TargetRegion` para meter. 

```azurepowershell-interactive

$sourceId = <ID of the image version source>

$osDiskImageEncryption = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet'}

$dataDiskImageEncryption1 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet1';Lun=1}

$dataDiskImageEncryption2 = @{DiskEncryptionSetId='subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/myDESet2';Lun=2}

$dataDiskImageEncryptions = @($dataDiskImageEncryption1,$dataDiskImageEncryption2)

$encryption1 = @{OSDiskImage=$osDiskImageEncryption;DataDiskImages=$dataDiskImageEncryptions}

$region1 = @{Name='West US';ReplicaCount=1;StorageAccountType=Standard_LRS;Encryption=$encryption1}

$targetRegion = @($region1)


# Create the image
New-AzGalleryImageVersion `
   -ResourceGroupName $rgname `
   -GalleryName $galleryName `
   -GalleryImageDefinitionName $imageDefinitionName `
   -Name $versionName -Location $location `
   -SourceImageId $sourceId `
   -ReplicaCount 2 `
   -StorageAccountType Standard_LRS `
   -PublishingProfileEndOfLifeDate '2020-12-01' `
   -TargetRegion $targetRegion
```

### <a name="create-a-vm"></a>Een virtuele machine maken

U kunt een virtuele machine maken op basis van een galerie met gedeelde afbeeldingen en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. De syntaxis is hetzelfde als bij het maken van een [gegeneraliseerde](vm-generalized-image-version-powershell.md) of [gespecialiseerde](vm-specialized-image-version-powershell.md) virtuele machine uit een installatie kopie, u moet de uitgebreide parameterset gebruiken en toevoegen `Set-AzVMOSDisk -Name $($vmName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage` aan de VM-configuratie.

Voor gegevens schijven moet u de `-DiskEncryptionSetId $setID` para meter toevoegen wanneer u [add-AzVMDataDisk](/powershell/module/az.compute/add-azvmdatadisk)gebruikt.


## <a name="cli"></a>CLI 

Voor de open bare Preview moet u eerst de functie registreren.

```azurecli-interactive
az feature register --namespace Microsoft.Compute --name SIGEncryption
```

Controleer de status van de functie registratie.

```azurecli-interactive
az feature show --namespace Microsoft.Compute --name SIGEncryption | grep state
```

Als dit wordt geretourneerd `"state": "Registered"` , kunt u door gaan met de volgende stap.

Controleer uw registratie.

```azurecli-interactive
az provider show -n Microsoft.Compute | grep registrationState
```

Als het niet is geregistreerd, voert u de volgende handelingen uit:

```azurecli-interactive
az provider register -n Microsoft.Compute
```


Als u een schijf versleuteling wilt opgeven die is ingesteld op voor een installatie kopie versie, gebruikt u  [AZ afbeeldingen galerie Create-Image-version](/cli/azure/sig/image-version#az-sig-image-version-create) met de `--target-region-encryption` para meter. De notatie voor `--target-region-encryption` is een door komma's gescheiden lijst met sleutels voor het versleutelen van het besturings systeem en de gegevens schijven. Dit ziet er als volgt uit: `<encryption set for the OS disk>,<Lun number of the data disk>,<encryption set for the data disk>,<Lun number for the second data disk>,<encryption set for the second data disk>`. 

Als de bron voor de besturingssysteem schijf een beheerde schijf of virtuele machine is, gebruikt `--managed-image` u om de bron voor de versie van de installatie kopie op te geven. In dit voor beeld is de bron een beheerde installatie kopie met een besturingssysteem schijf en een gegevens schijf op LUN 0. De besturingssysteem schijf wordt versleuteld met DiskEncryptionSet1 en de gegevens schijf wordt versleuteld met DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus \
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage \
   --managed-image "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
```

Als de bron voor de besturingssysteem schijf een moment opname is, gebruikt `--os-snapshot` u om de besturingssysteem schijf op te geven. Als er moment opnamen van gegevens schijven zijn die ook deel moeten uitmaken van de installatie kopie versie, voegt u deze toe met `--data-snapshot-luns` om het LUN op te geven en `--data-snapshots` om de moment opnamen op te geven.

In dit voor beeld zijn de bronnen schijf momentopnamen. Er is een besturingssysteem schijf en ook een gegevens schijf op LUN 0. De besturingssysteem schijf wordt versleuteld met DiskEncryptionSet1 en de gegevens schijf wordt versleuteld met DiskEncryptionSet2.

```azurecli-interactive
az sig image-version create \
   -g MyResourceGroup \
   --gallery-image-version 1.0.0 \
   --location westus\
   --target-regions westus=2=standard_lrs \
   --target-region-encryption DiskEncryptionSet1,0,DiskEncryptionSet2 \
   --os-snapshot "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myOSSnapshot" \
   --data-snapshot-luns 0 \
   --data-snapshots "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/myDDSnapshot" \
   --gallery-name MyGallery \
   --gallery-image-definition MyImage 
   
```

### <a name="create-the-vm"></a>De virtuele machine maken

U kunt een virtuele machine maken op basis van een galerie met gedeelde afbeeldingen en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. De syntaxis is hetzelfde als het maken van een [gegeneraliseerde](vm-generalized-image-version-cli.md) of [gespecialiseerde](vm-specialized-image-version-cli.md) virtuele machine uit een installatie kopie, maar u hoeft alleen de para meter toe te voegen `--os-disk-encryption-set` met de id van de versleutelings. Voeg voor gegevens schijven `--data-disk-encryption-sets` een door spaties gescheiden lijst van de schijf versleutelings sets voor de gegevens schijven toe.


## <a name="portal"></a>Portal

Wanneer u de versie van de installatie kopie maakt in de portal, kunt u het tabblad **versleuteling** gebruiken om uw opslag versleutelings sets Toep assen toe te voegen.

> [!IMPORTANT]
> Als u dubbele versleuteling wilt gebruiken, moet u deze koppeling gebruiken [https://aka.ms/diskencryptionupdates](https://aka.ms/diskencryptionupdates) om toegang te krijgen tot de Azure Portal. Dubbele versleuteling bij rest is momenteel niet zichtbaar in de open bare Azure Portal zonder de koppeling te gebruiken.


1. Selecteer op de pagina **versie van een installatie kopie maken** het tabblad **versleuteling** .
2. Selecteer in **versleutelings type** **versleuteling op-rest met een door de klant beheerde sleutel** of **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels**. 
3. Selecteer voor elke schijf in de installatie kopie de **schijf versleutelings** die u wilt gebruiken in de vervolg keuzelijst. 

### <a name="create-the-vm"></a>De virtuele machine maken

U kunt een virtuele machine maken op basis van een installatie kopie versie en door de klant beheerde sleutels gebruiken om de schijven te versleutelen. Wanneer u de virtuele machine in de portal maakt, selecteert u op het tabblad **schijven** de optie **versleuteling in rust met door de klant beheerde sleutels** of **dubbele versleuteling met door het platform beheerde en door de klant beheerde sleutels** voor het **versleutelings type**. Vervolgens kunt u de versleutelings reeks selecteren in de vervolg keuzelijst.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [schijf versleuteling aan de server zijde](./windows/disk-encryption.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).

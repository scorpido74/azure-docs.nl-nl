---
title: 'CLI: een installatie kopie maken van een moment opname of VHD in een galerie met gedeelde afbeeldingen'
description: Meer informatie over het maken van een installatie kopie van een moment opname of VHD in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: b5dcadd2381596509a3d2f512d0f4ebbbfbba893
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86502874"
---
# <a name="create-an-image-from-a-vhd-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Een installatie kopie maken van een VHD of moment opname in een galerie met gedeelde afbeeldingen met behulp van de Azure CLI

Als u een bestaande moment opname of VHD hebt die u wilt migreren naar een gedeelde installatie kopie galerie, kunt u rechtstreeks vanuit de VHD of de moment opname een installatie kopie van een gedeelde installatie kopie maken. Nadat u de nieuwe installatie kopie hebt getest, kunt u de bron-VHD of-moment opname verwijderen. U kunt ook een installatie kopie maken op basis van een VHD of een moment opname in een galerie met gedeelde afbeeldingen met behulp van de [Azure PowerShell](image-version-snapshot-powershell.md).

Afbeeldingen in een afbeeldings galerie hebben twee onderdelen, die we in dit voor beeld gaan maken:
- Een **definitie van een installatie kopie** bevat informatie over de installatie kopie en vereisten voor het gebruik ervan. Dit omvat of de installatie kopie Windows of Linux, gespecialiseerde of gegeneraliseerde, release opmerkingen en minimale en maximale geheugen vereisten zijn. Het is een definitie van een type installatie kopie. 
- Een **installatie kopie versie** is wat wordt gebruikt om een virtuele machine te maken wanneer u een galerie met gedeelde afbeeldingen gebruikt. U kunt meerdere versies van een installatie kopie naar behoefte hebben voor uw omgeving. Wanneer u een virtuele machine maakt, wordt de versie van de installatie kopie gebruikt voor het maken van nieuwe schijven voor de virtuele machine. Installatie kopie versies kunnen meerdere keren worden gebruikt.


## <a name="before-you-begin"></a>Voordat u begint

U moet een moment opname of VHD hebben om dit artikel te volt ooien. 

Als u een gegevens schijf wilt toevoegen, mag de grootte van de gegevens schijf niet groter zijn dan 1 TB.

Wanneer u dit artikel doorwerkt, vervangt u de resource namen waar nodig.

## <a name="find-the-snapshot-or-vhd"></a>De moment opname of VHD zoeken 

U kunt een lijst weer geven met moment opnamen die beschikbaar zijn in een resource groep met behulp van [AZ snap shot List](/cli/azure/snapshot#az-snapshot-list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

U kunt ook een VHD gebruiken in plaats van een moment opname. Gebruik [AZ Disk List](/cli/azure/disk#az-disk-list)om een VHD op te halen. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

Nadat u de ID van de moment opname of VHD hebt gemaakt en deze aan een variabele met de naam hebt toegewezen, kunt u deze `$source` later gebruiken.

U kunt hetzelfde proces gebruiken voor het ophalen van alle gegevens schijven die u wilt toevoegen aan uw installatie kopie. Wijs ze toe aan variabelen en gebruik deze variabelen later wanneer u de versie van de installatie kopie maakt.


## <a name="find-the-gallery"></a>De galerie zoeken

U hebt informatie over de galerie met installatie kopieën nodig om de definitie van de installatie kopie te maken.

Informatie weer geven over de beschik bare afbeeldings galerieën met de [lijst AZ sig](/cli/azure/sig#az-sig-list). Houd er rekening mee dat de naam van de resource groep in de galerie later moet worden gebruikt.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Een definitie voor de installatiekopie maken

Definities van installatiekopieën maken een logische groepering voor installatiekopieën. Ze worden gebruikt om informatie over de installatie kopie te beheren. Namen van installatiekopiedefinities kunnen bestaan uit hoofdletters, kleine letters, cijfers, streepjes en punten. 

Wanneer u de definitie van de installatie kopie maakt, moet u ervoor zorgen dat alle juiste gegevens worden verstrekt. In dit voor beeld gaan we ervan uit dat de moment opname of VHD afkomstig is van een virtuele machine die in gebruik is en niet is gegeneraliseerd. Als de VHD of moment opname is gemaakt van een gegeneraliseerd besturings systeem (na het uitvoeren van Sysprep voor Windows of [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` of `-deprovision+user` voor Linux), wijzigt u vervolgens de `-OsState` to `generalized` . 

Zie [Installatiekopiedefinities](./linux/shared-image-galleries.md#image-definitions) voor meer informatie over de waarden die u kunt specificeren voor een installatiekopiedefinitie.

Een installatiekopiedefinitie in de galerie maken met [az sig image-definition create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

In dit voorbeeld heeft de definitie van de installatiekopie de naam *myImageDefinition* en is deze voor een [gespecialiseerde](./linux/shared-image-galleries.md#generalized-and-specialized-images) installatiekopie van een Linux-besturingssysteem. Als u een definitie voor installatie kopieën wilt maken met een Windows-besturings systeem, gebruikt u `--os-type Windows` . 

In dit voor beeld heeft de galerie de naam *myGallery*, het bevindt zich in de resource groep *myGalleryRG* en de naam van de definitie van de installatie kopie *mImageDefinition*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>De installatiekopieversie maken

Maak een installatie kopie versie met behulp van [AZ image galerie Create-Image-version](/cli/azure/sig/image-version#az-sig-image-version-create). 

Toegestane tekens voor een installatiekopieversie zijn cijfers en punten. Cijfers moeten binnen het bereik van een 32-bits geheel getal zijn. Indeling: *MajorVersion*.*MinorVersion*.*Patch*.

In dit voor beeld is de versie van onze installatie kopie *1.0.0* en we gaan 1 replica maken in de regio *Zuid-Centraal VS* en 1 replica in de regio *VS-Oost 2* met zone-redundante opslag. Wanneer u doel regio's voor replicatie kiest, moet u er ook voor kiezen om de *bron* regio van de VHD of moment opname op te nemen als een doel voor replicatie.

Geef de ID van de moment opname of VHD op in de `--os-snapshot` para meter.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Als u gegevens schijven wilt toevoegen aan de installatie kopie, moet u beide `--data-snapshot-luns` para meters instellen op het LUN-nummer en de `--data-snapshots` set met de id van de gegevens schijf of moment opname.

> [!NOTE]
> U moet wachten tot de installatiekopieversie volledig is gebouwd en gerepliceerd voordat u dezelfde beheerde installatiekopie kunt gebruiken om een andere versie van de installatiekopie te maken.
>
> U kunt ook al uw afbeeldings versie replica's opslaan in [zone redundante opslag](../storage/common/storage-redundancy.md) door toe te voegen `--storage-account-type standard_zrs` Wanneer u de versie van de installatie kopie maakt.
>

## <a name="next-steps"></a>Volgende stappen

Maak een virtuele machine op basis van een [gespecialiseerde afbeeldings versie](vm-specialized-image-version-cli.md).

Zie voor meer informatie over het verstrekken van informatie over het aankoop plan [Azure Marketplace-informatie over het aankoop plan bij het maken van installatie kopieën](marketplace-images.md).

---
title: Een aangepaste Linux-VM uploaden of kopiëren met Azure CLI
description: Een aangepaste virtuele machine uploaden of kopiëren met behulp van het implementatiemodel Resource Manager en het Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 3306647078c46a7c66b3d7b257b213c7a48e690d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81460423"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Een Linux-vm maken op basis van een aangepaste schijf met de Azure CLI

<!-- rename to create-vm-specialized -->

In dit artikel ziet u hoe u een aangepaste virtuele harde schijf (VHD) uploadt en hoe u een bestaande VHD in Azure kopiëren. De nieuw gecreëerde VHD wordt vervolgens gebruikt om nieuwe Linux virtuele machines (VM's) te maken. U een Linux-distro installeren en configureren volgens uw vereisten en die VHD vervolgens gebruiken om een nieuwe Virtuele Azure-machine te maken.

Als u meerdere VM's wilt maken vanaf uw aangepaste schijf, maakt u eerst een afbeelding van uw VM of VHD. Zie [Een aangepaste afbeelding van een Azure VM maken met de CLI](tutorial-custom-images.md)voor meer informatie.

U hebt twee opties om een aangepaste schijf te maken:
* Een VHD uploaden
* Een bestaande Azure VM kopiëren


## <a name="requirements"></a>Vereisten
Als u de volgende stappen wilt uitvoeren, hebt u het volgende nodig:

- Een Virtuele Linux-machine die is voorbereid voor gebruik in Azure. In de sectie [Vm voorbereiden](#prepare-the-vm) van dit artikel wordt uitgelegd hoe u distro-specifieke informatie vinden over het installeren van de Azure Linux Agent (waagent), die nodig is om verbinding te maken met een VM met SSH.
- Het VHD-bestand van een bestaande [Door Azure goedgekeurde Linux-distributie](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (of zie [informatie voor niet-goedgekeurde distributies)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)naar een virtuele schijf in de VHD-indeling. Er bestaan meerdere hulpprogramma's om een VM en VHD te maken:
  - Installeer en configureer [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) of [KVM](https://www.linux-kvm.org/page/RunningKVM), zorg ervoor dat VHD wordt gebruikt als uw afbeeldingsindeling. Indien nodig u een `qemu-img convert`afbeelding [converteren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) met.
  - U Hyper-V ook gebruiken [op Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) of [op Windows Server 2012/2012 R2.](https://technet.microsoft.com/library/hh846766.aspx)

> [!NOTE]
> De nieuwere VHDX-indeling wordt niet ondersteund in Azure. Wanneer u een vm maakt, geeft u VHD op als indeling. Indien nodig u VHDX-schijven converteren naar VHD met [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) of de [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx) PowerShell-cmdlet. Azure biedt geen ondersteuning voor het uploaden van dynamische VHD's, dus u moet dergelijke schijven converteren naar statische VHD's voordat u deze uploadt. U hulpprogramma's zoals [Azure VHD-hulpprogramma's voor GO](https://github.com/Microsoft/azure-vhd-utils-for-go) gebruiken om dynamische schijven om te zetten tijdens het uploaden naar Azure.
> 
> 


- Zorg ervoor dat u de nieuwste [Azure CLI hebt](/cli/azure/install-az-cli2) geïnstalleerd en dat u bent aangemeld bij een Azure-account met [az-aanmelding.](/cli/azure/reference-index#az-login)

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw `myResourceGroup` `mystorageaccount`eigen `mydisks`waarden, zoals , en .

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden

Azure ondersteunt verschillende Linux-distributies (zie [Onderschreven distributies).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) In de volgende artikelen wordt beschreven hoe u de verschillende Linux-distributies voorbereidt die op Azure worden ondersteund:

* [CentOS-distributies](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SLES en OpenSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Anderen: Niet-onderschreven distributies](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Zie ook de [Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes) voor meer algemene tips over het voorbereiden van Linux-afbeeldingen voor Azure.

> [!NOTE]
> Het [Azure-platform SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) is alleen van toepassing op VM's waarop Linux wordt uitgevoerd wanneer een van de goedgekeurde distributies wordt gebruikt met de configuratiegegevens zoals opgegeven onder 'Ondersteunde versies' in [Linux op door Azure goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="option-1-upload-a-vhd"></a>Optie 1: Een VHD uploaden

U VHD nu rechtstreeks uploaden naar een beheerde schijf. Zie Een [VHD uploaden naar Azure met Azure CLI](disks-upload-vhd-to-managed-disk-cli.md)voor instructies.

## <a name="option-2-copy-an-existing-vm"></a>Optie 2: Een bestaande VM kopiëren

U ook een aangepaste VM maken in Azure en vervolgens de OS-schijf kopiëren en aan een nieuwe virtuele machine koppelen om een andere kopie te maken. Dit is prima voor het testen, maar als u een bestaande Azure VM wilt gebruiken als model voor meerdere nieuwe VM's, maakt u in plaats daarvan een *afbeelding.* Zie Een aangepaste afbeelding van een Azure [VM maken met de CLI](tutorial-custom-images.md)voor meer informatie over het maken van een afbeelding van een bestaande Azure VM.

Als u een bestaande virtuele machine naar een andere regio wilt kopiëren, u azcopy gebruiken om een kopie van een schijf in een andere regio te [createn.](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) 

Anders moet u een momentopname van de VM maken en vervolgens een nieuw OS VHD maken vanaf de momentopname.

### <a name="create-a-snapshot"></a>Een momentopname maken

In dit voorbeeld wordt een momentopname gemaakt van een VM met de naam *myVM* in de brongroep *myResourceGroup* en wordt een momentopname gemaakt met de naam *osDiskSnapshot*.

```azurecli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>De beheerde schijf maken

Maak een nieuwe beheerde schijf op basis van de momentopname.

Haal de id van de momentopname. In dit voorbeeld wordt de momentopname *osDiskSnapshot* genoemd en bevindt deze zich in de *brongroep myResourceGroup.*

```azurecli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Maak de beheerde schijf. In dit voorbeeld maken we een beheerde schijf met de naam *myManagedDisk* op basis van onze momentopname, waarbij de schijf in standaardopslag staat en 128 GB groot is.

```azurecli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>De virtuele machine maken

Maak uw VM met [az vm maken](/cli/azure/vm#az-vm-create) en hechten (--attach-os-disk) de beheerde schijf als de OS-schijf. In het volgende voorbeeld wordt een VM met de naam *myNewVM gemaakt* met behulp van de beheerde schijf die u hebt gemaakt met uw geüploade VHD:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

U moet in staat zijn om SSH in de VM met de referenties van de bron VM. 

## <a name="next-steps"></a>Volgende stappen
Nadat u uw aangepaste virtuele schijf hebt voorbereid en geüpload, u meer lezen over [het gebruik van Resource Manager en sjablonen.](../../azure-resource-manager/management/overview.md) U ook [een gegevensschijf toevoegen](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aan uw nieuwe VM's. Als u toepassingen hebt die worden uitgevoerd op uw VM's die u moet openen, moet u [poorten en eindpunten openen.](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

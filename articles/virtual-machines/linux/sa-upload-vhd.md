---
title: Een aangepaste Linux-schijf uploaden met Azure CLI
description: Een virtuele harde schijf (VHD) maken en uploaden naar Azure met behulp van het Resource Manager-implementatie model en de Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758567"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Een virtuele Linux-machine uploaden en maken op basis van een aangepaste schijf met de Azure CLI

In dit artikel wordt beschreven hoe u een virtuele harde schijf (VHD) uploadt naar een Azure-opslag account met de Azure CLI en Linux-Vm's maakt op basis van deze aangepaste schijf. Met deze functie kunt u een Linux-distributie installeren en configureren op uw vereisten en vervolgens die VHD gebruiken om snel virtuele machines (Vm's) voor Azure te maken.

In dit onderwerp worden opslag accounts gebruikt voor de uiteindelijke Vhd's, maar u kunt deze stappen ook uitvoeren met behulp van [Managed disks](upload-vhd.md). 

## <a name="quick-commands"></a>Snelle opdrachten
Als u de taak snel moet uitvoeren, wordt in de volgende sectie de basis opdrachten voor het uploaden van een VHD naar Azure beschreven. Meer gedetailleerde informatie en context voor elke stap vindt u in de rest van het document, [beginnend hier](#requirements).

Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en bent aangemeld bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter `myResourceGroup`namen `mystorageaccount`zijn opgenomen `mydisks`, en.

Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Maak een opslag account voor het opslaan van uw virtuele schijven met [AZ Storage account create](/cli/azure/storage/account). In het volgende voor beeld wordt een opslag `mystorageaccount`account gemaakt met de naam:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Vermeld de toegangs sleutels voor uw opslag account met [AZ Storage account Keys List](/cli/azure/storage/account/keys). Noteer het `key1`volgende:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Maak een container in uw opslag account met behulp van de opslag sleutel die u hebt verkregen met [AZ storage container Create](/cli/azure/storage/container). In het volgende voor beeld wordt een `mydisks` container gemaakt met de naam van `key1`de opslag sleutel waarde van:

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Upload ten slotte uw VHD naar de container die u hebt gemaakt met [AZ Storage BLOB upload](/cli/azure/storage/blob). Geef het lokale pad naar uw VHD op `/path/to/disk/mydisk.vhd`onder:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Geef de URI op uw schijf (`--image`) op met [AZ VM Create](/cli/azure/vm). In het volgende voor beeld wordt een `myVM` virtuele machine gemaakt met de naam met de eerder geüploade virtuele schijf:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Het doel-opslag account moet hetzelfde zijn als de locatie waar u de virtuele schijf hebt geüpload. U moet ook vragen opgeven, of antwoord geven op alle aanvullende para meters die vereist zijn voor de opdracht **AZ VM Create** , zoals virtueel netwerk, openbaar IP-adres, gebruikers naam en SSH-sleutels. Meer informatie over de [beschik bare klassieke cli-Resource Manager-para meters](../azure-cli-arm-commands.md#virtual-machines)kunt u lezen.

## <a name="requirements"></a>Vereisten
Als u de volgende stappen wilt uitvoeren, moet u:

* **Linux-besturings systeem geïnstalleerd in een VHD-bestand** : Installeer een door [Azure goedgekeurde Linux-distributie](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (of Zie [informatie over niet-goedgekeurde distributies](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) naar een virtuele schijf in de VHD-indeling. Er zijn meerdere hulpprogram ma's voor het maken van een virtuele machine en VHD:
  * Installeer en configureer [qemu](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) of [KVM](https://www.linux-kvm.org/page/RunningKVM), waarbij u gebruik maakt van VHD als uw installatie kopie-indeling. Als dat nodig is, kunt u [een installatie kopie converteren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) met behulp van `qemu-img convert`.
  * U kunt ook Hyper-V gebruiken [in Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) of [op Windows Server 2012/2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> De nieuwere VHDX-indeling wordt niet ondersteund in Azure. Wanneer u een virtuele machine maakt, geeft u VHD op als de indeling. Indien nodig kunt u VHDX-schijven converteren naar VHD met [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) of de [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) Power shell-cmdlet. Verder biedt Azure geen ondersteuning voor het uploaden van dynamische Vhd's. Daarom moet u dergelijke schijven converteren naar statische Vhd's voordat u deze uploadt. U kunt hulpprogram ma's zoals [Azure VHD Utilities voor Go](https://github.com/Microsoft/azure-vhd-utils-for-go) gebruiken om dynamische schijven te converteren tijdens het uploaden naar Azure.
> 
> 

* Vm's die zijn gemaakt op basis van uw aangepaste schijf, moeten zich in hetzelfde opslag account als de schijf zelf bevinden
  * Maak een opslag account en een container voor het opslaan van de aangepaste schijf en de gemaakte Vm's
  * Nadat u alle Vm's hebt gemaakt, kunt u uw schijf veilig verwijderen

Zorg ervoor dat u de nieuwste [Azure cli](/cli/azure/install-az-cli2) hebt geïnstalleerd en bent aangemeld bij een Azure-account met de opdracht [AZ login](/cli/azure/reference-index).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter `myResourceGroup`namen `mystorageaccount`zijn opgenomen `mydisks`, en.

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>De schijf voorbereiden om te worden geüpload
Azure ondersteunt diverse Linux-distributies (Zie [goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). De volgende artikelen begeleiden u bij het voorbereiden van de verschillende Linux-distributies die worden ondersteund in Azure:

* **[Distributies op basis van CentOS](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Andere-niet-goedgekeurde distributies](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zie ook de **[installatie notities van Linux](create-upload-generic.md#general-linux-installation-notes)** voor meer algemene tips over het voorbereiden van Linux-installatie kopieën voor Azure.

> [!NOTE]
> De [Sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/) van het Azure-platform is alleen van toepassing op Vm's waarop Linux wordt uitgevoerd wanneer een van de goedgekeurde distributies wordt gebruikt met de configuratie gegevens zoals opgegeven onder ondersteunde versies in [Linux op door Azure goedgekeurde distributies](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Met resource groepen worden alle Azure-resources logisch samengebracht ter ondersteuning van uw virtuele machines, zoals de virtuele netwerken en opslag. Zie [overzicht van resource groepen](../../azure-resource-manager/management/overview.md)voor meer informatie over resource groepen. Voordat u uw aangepaste schijf uploadt en Vm's maakt, moet u eerst een resource groep maken met [AZ Group Create](/cli/azure/group).

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Create a storage account

Maak een opslag account voor uw aangepaste schijf en virtuele machines met [AZ Storage account create](/cli/azure/storage/account). Alle Vm's met niet-beheerde schijven die u maakt op basis van uw aangepaste schijf, moeten zich in hetzelfde opslag account bevindt als die schijf. 

In het volgende voor beeld wordt een opslag `mystorageaccount` account gemaakt met de naam in de resource groep die eerder is gemaakt:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Sleutels van opslag account weer geven
Azure genereert 2 512-bits toegangs sleutels voor elk opslag account. Deze toegangs sleutels worden gebruikt bij het verifiëren van het opslag account, zoals om schrijf bewerkingen uit te voeren. Zie [toegangs sleutels voor opslag accounts beheren](../../storage/common/storage-account-keys-manage.md)voor meer informatie over toegangs sleutels voor opslag accounts. U kunt de toegangs sleutels weer geven met [AZ Storage account Keys List](/cli/azure/storage/account/keys).

Bekijk de toegangs sleutels voor het opslag account dat u hebt gemaakt:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

De uitvoer is vergelijkbaar met:

```output
info:    Executing command storage account keys list
+ Getting storage account keys
data:    Name  Key                                                                                       Permissions
data:    ----  ----------------------------------------------------------------------------------------  -----------
data:    key1  d4XAvZzlGAgWdvhlWfkZ9q4k9bYZkXkuPCJ15NTsQOeDeowCDAdB80r9zA/tUINApdSGQ94H9zkszYyxpe8erw==  Full
data:    key2  Ww0T7g4UyYLaBnLYcxIOTVziGAAHvU+wpwuPvK4ZG0CDFwu/mAxS/YYvAQGHocq1w7/3HcalbnfxtFdqoXOw8g==  Full
info:    storage account keys list command OK
```

Noteer `key1` wanneer u deze gebruikt om te communiceren met uw opslag account in de volgende stappen.

## <a name="create-a-storage-container"></a>Een opslag container maken
Op dezelfde manier als u verschillende directory's maakt om uw lokale bestands systeem logisch te organiseren, maakt u containers in een opslag account om uw schijven te organiseren. Een opslag account kan een wille keurig aantal containers bevatten. Maak een container met [AZ storage container Create](/cli/azure/storage/container).

In het volgende voor beeld wordt een `mydisks`container gemaakt met de naam:

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD uploaden
Upload nu uw aangepaste schijf met [AZ Storage BLOB upload](/cli/azure/storage/blob). U uploadt en slaat uw aangepaste schijf op als een pagina-blob.

Geef uw toegangs sleutel op, de container die u in de vorige stap hebt gemaakt en vervolgens het pad naar de aangepaste schijf op uw lokale computer:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>De virtuele machine maken
Als u een virtuele machine met onbeheerde schijven wilt maken, geeft u de URI`--image`op uw schijf () op met [AZ VM Create](/cli/azure/vm). In het volgende voor beeld wordt een `myVM` virtuele machine gemaakt met de naam met de eerder geüploade virtuele schijf:

U geeft de `--image` para meter op met [AZ VM Create](/cli/azure/vm) om naar uw aangepaste schijf te verwijzen. Zorg ervoor `--storage-account` dat overeenkomt met het opslag account waarin uw aangepaste schijf is opgeslagen. U hoeft niet dezelfde container te gebruiken als de aangepaste schijf om uw Vm's op te slaan. Zorg ervoor dat u aanvullende containers op dezelfde manier maakt als de eerdere stappen voordat u de aangepaste schijf uploadt.

In het volgende voor beeld wordt een `myVM` VM gemaakt met de naam van uw aangepaste schijf:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

U moet nog steeds vragen opgeven, of antwoord geven op alle aanvullende para meters die vereist zijn voor de opdracht **AZ VM Create** , zoals gebruikers naam en SSH-sleutel.


## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Azure Resource Manager sjablonen zijn JavaScript Object Notation (JSON)-bestanden die de omgeving definiëren die u wilt bouwen. De sjablonen worden uitgesplitst naar verschillende resource providers, zoals Compute en netwerk. U kunt bestaande sjablonen gebruiken of uw eigen sjabloon schrijven. Lees meer over het [gebruik van Resource Manager en sjablonen](../../azure-resource-manager/management/overview.md).

Binnen de `Microsoft.Compute/virtualMachines` provider van uw sjabloon hebt u een `storageProfile` knoop punt met de configuratie gegevens voor uw VM. De twee belangrijkste para meters die u `image` kunt `vhd` bewerken, zijn de en uri's die verwijzen naar uw aangepaste schijf en de virtuele schijf van de nieuwe VM. Hieronder ziet u een voor beeld van de JSON voor het gebruik van een aangepaste schijf:

```json
"storageProfile": {
          "osDisk": {
            "name": "myVM",
            "osType": "Linux",
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "image": {
              "uri": "https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd"
            },
            "vhd": {
              "uri": "https://mystorageaccount.blob.core.windows.net/vhds/newvmname.vhd"
            }
          }
```

U kunt [deze bestaande sjabloon gebruiken om een VM te maken op basis van een aangepaste installatie kopie of om](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) te lezen over [het maken van uw eigen Azure Resource Manager sjablonen](../../azure-resource-manager/templates/template-syntax.md). 

Wanneer u een sjabloon hebt geconfigureerd, gebruikt u [AZ Group Deployment Create](/cli/azure/group/deployment) om uw vm's te maken. Geef de URI van de JSON-sjabloon op `--template-uri` met de para meter:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Als een JSON-bestand lokaal op uw computer is opgeslagen, kunt u in plaats `--template-file` daarvan de para meter gebruiken:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Volgende stappen
Nadat u uw aangepaste virtuele schijf hebt voor bereid en geüpload, kunt u meer lezen over het [gebruik van Resource Manager en sjablonen](../../azure-resource-manager/management/overview.md). Het is ook mogelijk dat u [een gegevens schijf wilt toevoegen](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aan de nieuwe virtuele machines. Als u toepassingen hebt die worden uitgevoerd op uw Vm's die u wilt openen, moet u [poorten en eind punten openen](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


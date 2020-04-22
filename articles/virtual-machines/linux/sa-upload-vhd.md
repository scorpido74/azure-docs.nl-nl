---
title: Een aangepaste Linux-schijf uploaden met Azure CLI
description: Een virtuele harde schijf (VHD) maken en uploaden naar Azure met behulp van het implementatiemodel Resource Manager en het Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 07/10/2017
ms.author: cynthn
ms.custom: storage accounts
ms.openlocfilehash: 7ec9b670f8b2eb1731511deb1d01cfc7db55054f
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758567"
---
# <a name="upload-and-create-a-linux-vm-from-custom-disk-with-the-azure-cli"></a>Een Linux-vm uploaden en maken vanaf aangepaste schijf met de Azure CLI

In dit artikel ziet u hoe u een virtuele harde schijf (VHD) uploadt naar een Azure-opslagaccount met de Azure CLI en Linux VM's maakt vanaf deze aangepaste schijf. Met deze functionaliteit u een Linux-distro installeren en configureren volgens uw vereisten en die VHD vervolgens gebruiken om snel Virtuele Azure-machines (VM's) te maken.

In dit onderwerp worden opslagaccounts voor de uiteindelijke VHD's gebruikt, maar u deze stappen ook uitvoeren met [beheerde schijven.](upload-vhd.md) 

## <a name="quick-commands"></a>Snelle opdrachten
Als u de taak snel moet uitvoeren, worden in de volgende sectie de basisopdrachten beschreven om een VHD naar Azure te uploaden. Meer gedetailleerde informatie en context voor elke stap kan worden gevonden de rest van het document, [te beginnen hier](#requirements).

Zorg ervoor dat u de nieuwste [Azure CLI hebt](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index)

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeeldparameternamen `myResourceGroup`opgenomen `mystorageaccount`, `mydisks`en .

Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `WestUs`:

```azurecli
az group create --name myResourceGroup --location westus
```

Maak een opslagaccount aan om uw virtuele schijven vast te houden met [het AZ-opslagaccount.](/cli/azure/storage/account) In het volgende voorbeeld `mystorageaccount`wordt een opslagaccount gemaakt met de naam:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

Vermeld de toegangssleutels voor uw opslagaccount met [de lijst met az-opslagaccountsleutels](/cli/azure/storage/account/keys). `key1`Noteer:

```azurecli
az storage account keys list --resource-group myResourceGroup --account-name mystorageaccount
```

Maak een container aan in uw opslagaccount met behulp van de opslagsleutel die u hebt verkregen met [az-opslagcontainer maken.](/cli/azure/storage/container) In het volgende voorbeeld `mydisks` wordt een container `key1`gemaakt met de naam van de opslagsleutelwaarde van :

```azurecli
az storage container create --account-name mystorageaccount \
    --account-key key1 --name mydisks
```

Upload ten slotte je VHD naar de container die je hebt gemaakt met [het uploaden van AZ-opslagblobs.](/cli/azure/storage/blob) Geef het lokale pad naar `/path/to/disk/mydisk.vhd`uw VHD op onder :

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

Geef de URI op`--image`uw schijf ( ) met [az vm maken](/cli/azure/vm). In het volgende voorbeeld `myVM` wordt een vm gemaakt met de naam met behulp van de virtuele schijf die eerder is geüpload:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisk/myDisks.vhd \
    --use-unmanaged-disk
```

Het doelopslagaccount moet hetzelfde zijn als waar u uw virtuele schijf hebt geüpload. U moet ook alle extra parameters opgeven of beantwoorden die vereist zijn door de opdracht **az vm create,** zoals virtueel netwerk, openbaar IP-adres, gebruikersnaam en SSH-toetsen. U meer lezen over de [beschikbare klassieke CLI Resource Manager-parameters.](../azure-cli-arm-commands.md#virtual-machines)

## <a name="requirements"></a>Vereisten
Als u de volgende stappen wilt uitvoeren, moet u het volgende doen:

* **Linux-besturingssysteem geïnstalleerd in een .vhd-bestand** - Installeer een [door Azure goedgekeurde Linux-distributie](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (of zie [informatie voor niet-goedgekeurde distributies)](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)naar een virtuele schijf in de VHD-indeling. Er bestaan meerdere hulpprogramma's om een VM en VHD te maken:
  * Installeer en configureer [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) of [KVM](https://www.linux-kvm.org/page/RunningKVM), zorg ervoor dat VHD wordt gebruikt als uw afbeeldingsindeling. Indien nodig u een `qemu-img convert`afbeelding [converteren](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) met behulp van.
  * U Hyper-V ook gebruiken [op Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) of [op Windows Server 2012/2012 R2.](https://technet.microsoft.com/library/hh846766.aspx)

> [!NOTE]
> De nieuwere VHDX-indeling wordt niet ondersteund in Azure. Wanneer u een vm maakt, geeft u VHD op als indeling. Indien nodig u VHDX-schijven [`qemu-img convert`](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) converteren [`Convert-VHD`](https://technet.microsoft.com/library/hh848454.aspx) naar VHD met behulp van of de PowerShell-cmdlet. Verder ondersteunt Azure geen uploaden van dynamische VHD's, dus u moet dergelijke schijven converteren naar statische VHD's voordat u uploadt. U hulpprogramma's zoals [Azure VHD-hulpprogramma's voor GO](https://github.com/Microsoft/azure-vhd-utils-for-go) gebruiken om dynamische schijven om te zetten tijdens het uploaden naar Azure.
> 
> 

* VM's die zijn gemaakt met uw aangepaste schijf moeten zich in hetzelfde opslagaccount bevinden als de schijf zelf
  * Een opslagaccount en container maken om zowel uw aangepaste schijf als gemaakte VM's vast te houden
  * Nadat u al uw VM's hebt gemaakt, u uw schijf veilig verwijderen

Zorg ervoor dat u de nieuwste [Azure CLI hebt](/cli/azure/install-az-cli2) geïnstalleerd en ingelogd op een Azure-account met behulp van [az-aanmelding.](/cli/azure/reference-index)

Vervang in de volgende voorbeelden voorbeeldparameternamen door uw eigen waarden. Voorbeeldparameternamen `myResourceGroup`opgenomen `mystorageaccount`, `mydisks`en .

<a id="prepimage"> </a>

## <a name="prepare-the-disk-to-be-uploaded"></a>De schijf voorbereiden om te worden geüpload
Azure ondersteunt verschillende Linux-distributies (zie [Onderschreven distributies).](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) In de volgende artikelen u de verschillende Linux-distributies voorbereiden die op Azure worden ondersteund:

* **[CentOS-gebaseerde distributies](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Debian Linux](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Red Hat Enterprise Linux](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[SLES & openSUSE](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**
* **[Andere - Niet-goedgekeurde distributies](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)**

Zie ook de **[Linux Installatienotities](create-upload-generic.md#general-linux-installation-notes)** voor meer algemene tips over het voorbereiden van Linux-afbeeldingen voor Azure.

> [!NOTE]
> Het [Azure-platform SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) is alleen van toepassing op VM's waarop Linux wordt uitgevoerd wanneer een van de goedgekeurde distributies wordt gebruikt met de configuratiegegevens zoals opgegeven onder 'Ondersteunde versies' in [Linux op Azure-goedgekeurde distributies.](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
> 
> 

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Resourcegroepen brengen logischerwijs alle Azure-bronnen samen om uw virtuele machines te ondersteunen, zoals het virtuele netwerk en de opslag. Zie overzicht [van resourcegroepen](../../azure-resource-manager/management/overview.md)voor meer informatiebrongroepen. Voordat u uw aangepaste schijf uploadt en VM's maakt, moet u eerst een resourcegroep maken met [de AZ-groep.](/cli/azure/group)

In het volgende voorbeeld wordt een resourcegroep met de naam `myResourceGroup` gemaakt op de locatie `westus`:

```azurecli
az group create --name myResourceGroup --location westus
```

## <a name="create-a-storage-account"></a>Create a storage account

Maak een opslagaccount aan voor uw aangepaste schijf en VM's met [een AZ-opslagaccount.](/cli/azure/storage/account) Vm's met onbeheerde schijven die u vanaf uw aangepaste schijf maakt, moeten zich in hetzelfde opslagaccount als die schijf bevindt. 

In het volgende voorbeeld `mystorageaccount` wordt een opslagaccount gemaakt met de naam in de eerder gemaakte brongroep:

```azurecli
az storage account create --resource-group myResourceGroup --location westus \
  --name mystorageaccount --kind Storage --sku Standard_LRS
```

## <a name="list-storage-account-keys"></a>Opslagaccountsleutels van de lijst
Azure genereert twee 512-bits toegangssleutels voor elk opslagaccount. Deze toegangssleutels worden gebruikt bij het verifiëren van het opslagaccount, zoals het uitvoeren van schrijfbewerkingen. Zie [Toegangssleutels voor opslagaccount beheren](../../storage/common/storage-account-keys-manage.md)voor meer informatie over toegangssleutels voor opslagaccount. U bekijkt de toegangssleutels met [de lijst met az-opslagaccountsleutels.](/cli/azure/storage/account/keys)

Bekijk de toegangssleutels voor het opslagaccount dat u hebt gemaakt:

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

Noteer `key1` als u het gebruikt om te communiceren met uw opslagaccount in de volgende stappen.

## <a name="create-a-storage-container"></a>Een opslagcontainer maken
Op dezelfde manier dat u verschillende mappen maakt om uw lokale bestandssysteem logisch te organiseren, maakt u containers binnen een opslagaccount om uw schijven te ordenen. Een opslagaccount kan een willekeurig aantal containers bevatten. Maak een container met [az-opslagcontainer maken](/cli/azure/storage/container).

In het volgende voorbeeld `mydisks`wordt een container met de naam :

```azurecli
az storage container create \
    --account-name mystorageaccount \
    --name mydisks
```

## <a name="upload-vhd"></a>VHD uploaden
Upload nu je aangepaste schijf met [het uploaden van az-opslagblobs.](/cli/azure/storage/blob) U uploadt en slaat uw aangepaste schijf op als een paginablob.

Geef uw toegangssleutel op, de container die u in de vorige stap hebt gemaakt en vervolgens het pad naar de aangepaste schijf op uw lokale computer:

```azurecli
az storage blob upload --account-name mystorageaccount \
    --account-key key1 --container-name mydisks --type page \
    --file /path/to/disk/mydisk.vhd --name myDisk.vhd
```

## <a name="create-the-vm"></a>De virtuele machine maken
Als u een vm met niet-beheerde schijven`--image`wilt maken, geeft u de URI op uw schijf ( ) met [az vm maken](/cli/azure/vm). In het volgende voorbeeld `myVM` wordt een vm gemaakt met de naam met behulp van de virtuele schijf die eerder is geüpload:

U geeft `--image` de parameter op met [az vm maken](/cli/azure/vm) om naar uw aangepaste schijf te wijzen. Zorg `--storage-account` ervoor dat dit overeenkomt met het opslagaccount waar uw aangepaste schijf is opgeslagen. U hoeft niet dezelfde container als de aangepaste schijf te gebruiken om uw VM's op te slaan. Zorg ervoor dat u extra containers maakt op dezelfde manier als de eerdere stappen voordat u uw aangepaste schijf uploadt.

In het volgende voorbeeld `myVM` wordt een VM gemaakt met de naam van uw aangepaste schijf:

```azurecli
az vm create --resource-group myResourceGroup --location westus \
    --name myVM --storage-account mystorageaccount --os-type linux \
    --admin-username azureuser --ssh-key-value ~/.ssh/id_rsa.pub \
    --image https://mystorageaccount.blob.core.windows.net/mydisks/myDisk.vhd \
    --use-unmanaged-disk
```

U moet nog steeds alle extra parameters opgeven of beantwoorden die vereist zijn door de opdracht **AZ VM create,** zoals gebruikersnaam en SSH-toetsen.


## <a name="resource-manager-template"></a>Resource Manager-sjabloon
Azure Resource Manager-sjablonen zijn JSON-bestanden (JavaScript Object Notation) die de omgeving definiëren die u wilt bouwen. De sjablonen zijn onderverdeeld in verschillende resourceproviders, zoals compute of netwerk. U bestaande sjablonen gebruiken of uw eigen sjablonen schrijven. Lees meer over [het gebruik van Resource Manager en sjablonen](../../azure-resource-manager/management/overview.md).

Binnen `Microsoft.Compute/virtualMachines` de aanbieder van uw `storageProfile` sjabloon hebt u een knooppunt dat de configuratiegegevens voor uw vm bevat. De twee belangrijkste parameters die `image` `vhd` u moet bewerken zijn de URL's en URI's die wijzen op uw aangepaste schijf en de virtuele schijf van uw nieuwe vm. Het volgende toont een voorbeeld van de JSON voor het gebruik van een aangepaste schijf:

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

U deze bestaande sjabloon gebruiken [om een VM te maken op basis van een aangepaste afbeelding](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image) of om uw eigen Azure Resource [Manager-sjablonen te maken.](../../azure-resource-manager/templates/template-syntax.md) 

Zodra u een sjabloon hebt geconfigureerd, gebruikt u [de implementatie van AZ-groepen](/cli/azure/group/deployment) om uw VM's te maken. Geef de URI van uw `--template-uri` JSON-sjabloon op met de parameter:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-uri https://uri.to.template/mytemplate.json
```

Als u een JSON-bestand lokaal op uw computer `--template-file` hebt opgeslagen, u in plaats daarvan de parameter gebruiken:

```azurecli
az group deployment create --resource-group myNewResourceGroup \
  --template-file /path/to/mytemplate.json
```


## <a name="next-steps"></a>Volgende stappen
Nadat u uw aangepaste virtuele schijf hebt voorbereid en geüpload, u meer lezen over [het gebruik van Resource Manager en sjablonen.](../../azure-resource-manager/management/overview.md) U ook [een gegevensschijf toevoegen](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) aan uw nieuwe VM's. Als u toepassingen hebt die worden uitgevoerd op uw VM's die u moet openen, moet u [poorten en eindpunten openen.](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)


---
title: Een OpenBSD-afbeelding maken en uploaden
description: Meer informatie over het maken en uploaden van een virtuele harde schijf (VHD) die het OpenBSD-besturingssysteem bevat om een Azure-virtuele machine te maken via Azure CLI
author: gbowerman
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 05/24/2017
ms.author: guybo
ms.openlocfilehash: 1ad1a66d67be7aefe4d9a7acae993e8788cbb193
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066748"
---
# <a name="create-and-upload-an-openbsd-disk-image-to-azure"></a>Een OpenBSD-schijfafbeelding maken en uploaden naar Azure
In dit artikel ziet u hoe u een virtuele harde schijf (VHD) maken en uploaden die het OpenBSD-besturingssysteem bevat. Nadat u het hebt geüpload, u het als uw eigen afbeelding gebruiken om een virtuele machine (VM) in Azure te maken via Azure CLI.


## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u de volgende objecten hebt:

* **Een Azure-abonnement** - Als u geen account hebt, u er in slechts een paar minuten een maken. Zie [Maandelijks Azure-tegoed voor Visual Studio-abonnees](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)als u een MSDN-abonnement hebt. Leer anders hoe u [een gratis proefaccount maakt.](https://azure.microsoft.com/pricing/free-trial/)  
* **Azure CLI** - Zorg ervoor dat u de nieuwste [Azure CLI](/cli/azure/install-azure-cli) hebt geïnstalleerd en ingelogd op uw Azure-account met [az-aanmelding.](/cli/azure/reference-index)
* **OpenBSD-besturingssysteem geïnstalleerd in een .vhd-bestand** - Een ondersteund OpenBSD-besturingssysteem ([6.6 versie AMD64](https://ftp.openbsd.org/pub/OpenBSD/6.6/amd64/)) moet worden geïnstalleerd op een virtuele harde schijf. Er bestaan meerdere hulpprogramma's om .vhd-bestanden te maken. U bijvoorbeeld een virtualisatieoplossing zoals Hyper-V gebruiken om het .vhd-bestand te maken en het besturingssysteem te installeren. Zie Hyper-V installeren en een virtuele machine maken voor instructies over het installeren en gebruiken van [Hyper-V.](https://technet.microsoft.com/library/hh846766.aspx)


## <a name="prepare-openbsd-image-for-azure"></a>OpenBSD-afbeelding voorbereiden voor Azure
Op de VM waar u het OpenBSD-besturingssysteem 6.1 hebt geïnstalleerd, waaraan Hyper-V-ondersteuning is toegevoegd, u de volgende procedures doorlopen:

1. Als DHCP niet is ingeschakeld tijdens de installatie, schakelt u de service als volgt in:

    ```sh    
    echo dhcp > /etc/hostname.hvn0
    ```

2. Stel als volgt een seriële console in:

    ```sh
    echo "stty com0 115200" >> /etc/boot.conf
    echo "set tty com0" >> /etc/boot.conf
    ```

3. Pakketinstallatie als volgt configureren:

    ```sh
    echo "https://ftp.openbsd.org/pub/OpenBSD" > /etc/installurl
    ```
   
4. Standaard is `root` de gebruiker uitgeschakeld op virtuele machines in Azure. Gebruikers kunnen opdrachten met verhoogde bevoegdheden `doas` uitvoeren met behulp van de opdracht op OpenBSD VM. Doas is standaard ingeschakeld. Zie [doas.conf](https://man.openbsd.org/doas.conf.5)voor meer informatie. 

5. Installeer en configureer als volgt vereisten voor de Azure Agent:

    ```sh
    pkg_add py-setuptools openssl git
    ln -sf /usr/local/bin/python2.7 /usr/local/bin/python
    ln -sf /usr/local/bin/python2.7-2to3 /usr/local/bin/2to3
    ln -sf /usr/local/bin/python2.7-config /usr/local/bin/python-config
    ln -sf /usr/local/bin/pydoc2.7  /usr/local/bin/pydoc
    ```

6. De nieuwste versie van de Azure-agent is altijd te vinden op [GitHub.](https://github.com/Azure/WALinuxAgent/releases) Ga als volgt te werk om de agent te installeren:

    ```sh
    git clone https://github.com/Azure/WALinuxAgent 
    cd WALinuxAgent
    python setup.py install
    waagent -register-service
    ```

    > [!IMPORTANT]
    > Nadat u Azure Agent hebt geïnstalleerd, is het een goed idee om te controleren of deze als volgt wordt uitgevoerd:
    >
    > ```bash
    > ps auxw | grep waagent
    > root     79309  0.0  1.5  9184 15356 p1  S      4:11PM    0:00.46 python /usr/local/sbin/waagent -daemon (python2.7)
    > cat /var/log/waagent.log
    > ```

7. Deprovision het systeem om het schoon te maken en geschikt te maken voor hervoorziening. Met de volgende opdracht worden ook het laatst ingerichte gebruikersaccount en de bijbehorende gegevens verwijderd:

    ```sh
    waagent -deprovision+user -force
    ```

Nu u uw VM uitschakelen.


## <a name="prepare-the-vhd"></a>Bereid de VHD voor
De VHDX-indeling wordt niet ondersteund in Azure, alleen **vaste VHD**. U de schijf converteren naar een vast VHD-formaat met Hyper-V Manager of de [Powershell-convert-vhd-cmdlet.](https://technet.microsoft.com/itpro/powershell/windows/hyper-v/convert-vhd) Een voorbeeld is als volgt.

```powershell
Convert-VHD OpenBSD61.vhdx OpenBSD61.vhd -VHDType Fixed
```

## <a name="create-storage-resources-and-upload"></a>Opslagbronnen maken en uploaden
Maak eerst een resourcegroep met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli
az group create --name myResourceGroup --location eastus
```

Als u uw VHD wilt uploaden, maakt u een opslagaccount aan met [het AZ-opslagaccount.](/cli/azure/storage/account) Namen van opslagaccounten moeten uniek zijn, dus geef uw eigen naam op. In het volgende voorbeeld wordt een opslagaccount met de naam *mystorageaccount gemaakt:*

```azurecli
az storage account create --resource-group myResourceGroup \
    --name mystorageaccount \
    --location eastus \
    --sku Premium_LRS
```

Als u de toegang tot het opslagaccount wilt beheren, moet u de opslagsleutel met [de lijst met AZ-opslagaccountsleutels](/cli/azure/storage/account/keys) als volgt verkrijgen:

```azurecli
STORAGE_KEY=$(az storage account keys list \
    --resource-group myResourceGroup \
    --account-name mystorageaccount \
    --query "[?keyName=='key1']  | [0].value" -o tsv)
```

Als u de VHD's die u uploadt logisch scheidt, maakt u een container in het opslagaccount met [az-opslagcontainer:](/cli/azure/storage/container)

```azurecli
az storage container create \
    --name vhds \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```

Upload tot slot je VHD met [het uploaden van AZ-opslagblobs](/cli/azure/storage/blob) als volgt:

```azurecli
az storage blob upload \
    --container-name vhds \
    --file ./OpenBSD61.vhd \
    --name OpenBSD61.vhd \
    --account-name mystorageaccount \
    --account-key ${STORAGE_KEY}
```


## <a name="create-vm-from-your-vhd"></a>VM maken vanaf uw VHD
U een VM maken met een [voorbeeldscript](../scripts/virtual-machines-linux-cli-sample-create-vm-vhd.md) of rechtstreeks met [az vm maken.](/cli/azure/vm) Als u de OpenBSD VHD wilt `--image` opgeven die u hebt geüpload, gebruikt u de parameter als volgt:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myOpenBSD61 \
    --image "https://mystorageaccount.blob.core.windows.net/vhds/OpenBSD61.vhd" \
    --os-type linux \
    --admin-username azureuser \
    --ssh-key-value ~/.ssh/id_rsa.pub
```

Verkrijg het IP-adres voor uw OpenBSD VM met [az vm list-ip-adressen](/cli/azure/vm) als volgt:

```azurecli
az vm list-ip-addresses --resource-group myResourceGroup --name myOpenBSD61
```

Nu u SSH naar uw OpenBSD VM als normaal:
        
```bash
ssh azureuser@<ip address>
```


## <a name="next-steps"></a>Volgende stappen
Als u meer wilt weten over Hyper-V-ondersteuning op OpenBSD6.1, leest u [OpenBSD 6.1](https://www.openbsd.org/61.html) en [hyperv.4](https://man.openbsd.org/hyperv.4).

Als u een VM wilt maken vanaf de beheerde schijf, leest u [az-schijf](/cli/azure/disk). 

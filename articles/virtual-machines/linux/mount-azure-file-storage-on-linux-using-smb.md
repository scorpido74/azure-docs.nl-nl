---
title: Azure-bestandsopslag op Linux VM's monteren met behulp van SMB
description: Azure-bestandsopslag monteren op Linux VM's met Behulp van SMB met de Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 0314095a053087a7d490926c41c6ae386c304919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066652"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Azure-bestandsopslag op Linux VM's monteren met behulp van SMB

In dit artikel ziet u hoe u de Azure File storage-service op een Linux-vm gebruiken met behulp van een SMB-mount met de Azure CLI. Azure File storage biedt bestandsshares in de cloud met behulp van het standaard SMB-protocol. 

Bestandsopslag biedt bestandsshares in de cloud die gebruikmaken van het standaard SMB-protocol. U een bestandsshare monteren vanaf elk besturingssysteem dat SMB 3.0 ondersteunt. Wanneer u een SMB-mount op Linux gebruikt, krijgt u eenvoudige back-ups naar een robuuste, permanente opslaglocatie voor archivering die wordt ondersteund door een SLA.

Het verplaatsen van bestanden van een VM naar een SMB-mount die wordt gehost op bestandsopslag is een geweldige manier om logboeken te debuggen. Hetzelfde Mkb-deel kan lokaal worden gemonteerd op uw Mac-, Linux- of Windows-werkstation. SMB is niet de beste oplossing voor het streamen van Linux of applicatie logs in real time, omdat het SMB-protocol is niet gebouwd om dergelijke zware logging taken te behandelen. Een speciale, uniforme logging laag tool zoals Fluentd zou een betere keuze dan SMB voor het verzamelen van Linux en applicatie logging output.

Deze handleiding vereist dat u de Azure CLI-versie 2.0.4 of hoger uitvoert. Voer **az --version** uit om de versie op te vragen. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resourcegroep met de naam *myResourceGroup* op de locatie *Oost-VS.*

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak een nieuw opslagaccount aan binnen de resourcegroep die u hebt gemaakt met behulp van [het AZ-opslagaccount.](/cli/azure/storage/account) In dit voorbeeld wordt een opslagaccount met de naam *mySTORAGEACCT\<random number>* en wordt de naam van dat opslagaccount in de variabele **STORAGEACCT vermeld.** Namen van opslagaccounten `$RANDOM` moeten uniek zijn, met behulp van een nummer aan het einde om het uniek te maken.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>De opslagsleutel aanschaffen

Wanneer u een opslagaccount maakt, worden de accountsleutels in paren gemaakt, zodat ze kunnen worden gedraaid zonder onderbreking van de service. Wanneer u overschakelt naar de tweede sleutel in het paar, maakt u een nieuw sleutelpaar. Nieuwe opslagaccountsleutels worden altijd in paren gemaakt, zodat u altijd ten minste één ongebruikte opslagaccountsleutel klaar hebt om naar over te schakelen.

Bekijk de opslagaccountsleutels met [de lijst met az-opslagaccountsleutels](/cli/azure/storage/account/keys). In dit voorbeeld wordt de waarde van toets 1 opgeslagen in de variabele **STORAGEKEY.**

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Een bestandsshare maken

Maak het aandeel bestandsopslag met [behulp van het maken van az-opslagshare.](/cli/azure/storage/share) 

Namen delen moeten alle kleine letters, cijfers en enkele koppeltekens zijn, maar kunnen niet beginnen met een koppelteken. Zie [Naming and Referencing Shares, Directories, Files, and Metadata](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata) (Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen) voor meer informatie over de naamgeving van bestandsshares en bestanden.

In dit voorbeeld wordt een aandeel met de naam myshare met de naam *MyShare* met een quotum van 10 GiB. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Een bevestigingspunt maken

Als u de Azure-bestandsshare op uw Linux-computer wilt monteren, moet u ervoor zorgen dat het **cifs-utils-pakket** is geïnstalleerd. Zie [Het cifs-utils-pakket voor uw Linux-distributie installeren voor](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils)installatie-instructies.

Azure Files maakt gebruik van het SMB-protocol, dat communiceert via TCP-poort 445.  Als u problemen ondervindt bij het installeren van uw Azure-bestandsshare, controleert u of uw firewall TCP-poort 445 niet blokkeert.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>Zet het aandeel op

Monteer het Azure-bestandsaandeel op de lokale map. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

De bovenstaande opdracht gebruikt de [opdracht monteren](https://linux.die.net/man/8/mount) om de Azure-bestandsshare en opties te monteren die specifiek zijn voor [cifs.](https://linux.die.net/man/8/mount.cifs) In het bijzonder stellen de opties voor `0777`file_mode en dir_mode bestanden en mappen in op toestemming. De `0777` toestemming geeft lees-, schrijf- en uitvoermachtigingen voor alle gebruikers. U deze machtigingen wijzigen door de waarden te vervangen door andere [chmod-machtigingen.](https://en.wikipedia.org/wiki/Chmod) U ook gebruik maken van andere [cifs](https://linux.die.net/man/8/mount.cifs) opties zoals gid of uid. 


## <a name="persist-the-mount"></a>Zet de mount voort

Wanneer u de Linux-VM opnieuw opstart, wordt het gemonteerde SMB-aandeel tijdens het afsluiten ontkoppeld. Als u het Aandeel SMB opnieuw wilt monteren op het opstarten, voegt u een regel toe aan de Linux /etc/fstab. Linux gebruikt het fstab-bestand om de bestandssystemen weer te geven die het tijdens het opstartproces moet monteren. Het toevoegen van het SMB-aandeel zorgt ervoor dat het aandeel Bestandsopslag een permanent opgezet bestandssysteem is voor de Linux-VM. Het toevoegen van het SMB-aandeel Bestandsopslag aan een nieuwe virtuele machine is mogelijk wanneer u cloud-init gebruikt.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Voor meer beveiliging in productieomgevingen moet u uw referenties buiten fstab opslaan.

## <a name="next-steps"></a>Volgende stappen

- [Cloud-init gebruiken om een Linux-vm aan te passen tijdens het maken](using-cloud-init.md)
- [Een schijf toevoegen aan een virtuele Linux-machine](add-disk.md)
- [Azure-schijfversleuteling voor Linux-VM's](disk-encryption-overview.md)


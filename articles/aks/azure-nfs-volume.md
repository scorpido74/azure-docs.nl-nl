---
title: NFS Ubuntu Linux Server-volume maken
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het handmatig maken van een NFS Ubuntu Linux Server-volume voor gebruik met pods in Azure Kubernetes Service (AKS)
services: container-service
author: ozboms
ms.topic: article
ms.date: 4/25/2019
ms.author: obboms
ms.openlocfilehash: 7db3f806df88e5b23012e97ba5c2f14ca65b2508
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803463"
---
# <a name="manually-create-and-use-an-nfs-network-file-system-linux-server-volume-with-azure-kubernetes-service-aks"></a>Handmatig een Linux-servervolume (NFS (Network File System) maken en gebruiken met Azure Kubernetes Service (AKS)
Het delen van gegevens tussen containers is vaak een noodzakelijk onderdeel van containergebaseerde diensten en toepassingen. U hebt meestal verschillende pods die toegang nodig hebben tot dezelfde informatie op een extern persistent volume.    
Hoewel Azure-bestanden een optie zijn, is het maken van een NFS-server op een Azure-vm een andere vorm van permanente gedeelde opslag. 

In dit artikel ziet u hoe u een NFS-server maakt op een virtuele Ubuntu-machine. En geef uw AKS-containers ook toegang tot dit gedeelde bestandssysteem.

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

Uw AKS-cluster moet in dezelfde of peered virtuele netwerken wonen als de NFS-server. Het cluster moet worden gemaakt in een bestaand VNET, dat dezelfde VNET kan zijn als uw VM.

De stappen voor het configureren met een bestaand VNET worden beschreven in de documentatie: [AKS-cluster maken in bestaande VNET][aks-virtual-network] en [virtuele netwerken verbinden met VNET-peering][peer-virtual-networks]

Het gaat er ook van uit dat je een Ubuntu Linux Virtual Machine hebt gemaakt (bijvoorbeeld 18.04 LTS). Instellingen en grootte kunnen naar wens zijn en kunnen worden geïmplementeerd via Azure. Voor Linux quickstart, zie [Linux VM management][linux-create].

Als u uw AKS-cluster eerst implementeert, vult Azure automatisch het virtuele netwerkveld in bij het implementeren van uw Ubuntu-machine, waardoor deze live is binnen hetzelfde VNET. Maar als u in plaats daarvan met peered-netwerken wilt werken, raadpleegt u de bovenstaande documentatie.

## <a name="deploying-the-nfs-server-onto-a-virtual-machine"></a>De NFS-server implementeren op een virtuele machine
Hier is het script om een NFS Server in te stellen in uw Virtuele Ubuntu-machine:
```bash
#!/bin/bash

# This script should be executed on Linux Ubuntu Virtual Machine

EXPORT_DIRECTORY=${1:-/export/data}
DATA_DIRECTORY=${2:-/data}
AKS_SUBNET=${3:-*}

echo "Updating packages"
apt-get -y update

echo "Installing NFS kernel server"

apt-get -y install nfs-kernel-server

echo "Making data directory ${DATA_DIRECTORY}"
mkdir -p ${DATA_DIRECTORY}

echo "Making new directory to be exported and linked to data directory: ${EXPORT_DIRECTORY}"
mkdir -p ${EXPORT_DIRECTORY}

echo "Mount binding ${DATA_DIRECTORY} to ${EXPORT_DIRECTORY}"
mount --bind ${DATA_DIRECTORY} ${EXPORT_DIRECTORY}

echo "Giving 777 permissions to ${EXPORT_DIRECTORY} directory"
chmod 777 ${EXPORT_DIRECTORY}

parentdir="$(dirname "$EXPORT_DIRECTORY")"
echo "Giving 777 permissions to parent: ${parentdir} directory"
chmod 777 $parentdir

echo "Appending bound directories into fstab"
echo "${DATA_DIRECTORY}    ${EXPORT_DIRECTORY}   none    bind  0  0" >> /etc/fstab

echo "Appending localhost and Kubernetes subnet address ${AKS_SUBNET} to exports configuration file"
echo "/export        ${AKS_SUBNET}(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports
echo "/export        localhost(rw,async,insecure,fsid=0,crossmnt,no_subtree_check)" >> /etc/exports

nohup service nfs-kernel-server restart
```
De server wordt opnieuw opgestart (vanwege het script) en u de NFS-server op AKS monteren.

>[!IMPORTANT]  
>Zorg ervoor dat u de **AKS_SUBNET** vervangt door de juiste van uw cluster of anders opent "*" uw NFS-server voor alle poorten en verbindingen.

Nadat u uw vm hebt gemaakt, kopieert u het bovenstaande script naar een bestand. Vervolgens u het verplaatsen van uw lokale machine, of waar het script zich ook bevindt, naar de VM met behulp van: 
```console
scp /path/to/script_file username@vm-ip-address:/home/{username}
```
Zodra uw script in uw VM staat, u in de VM ssh en het uitvoeren via de opdracht:
```console
sudo ./nfs-server-setup.sh
```
Als de uitvoering mislukt vanwege een fout met geweigerde toestemming, stelt u uitvoeringsmachtiging in via de opdracht:
```console
chmod +x ~/nfs-server-setup.sh
```

## <a name="connecting-aks-cluster-to-nfs-server"></a>AKS-cluster verbinden met NFS-server
We kunnen de NFS-server verbinden met ons cluster door een permanente volume- en aanhoudende volumeclaim in te richten die aangeeft hoe toegang tot het volume moet worden gemaakt.

Het aansluiten van de twee services in dezelfde of peered virtuele netwerken is noodzakelijk. Instructies voor het instellen van het cluster in hetzelfde VNET zijn hier: [AKS-cluster maken in bestaande VNET][aks-virtual-network]

Zodra ze zich in hetzelfde virtuele netwerk (of peered) bevinden, moet u een blijvend volume en een aanhoudende volumeclaim in uw AKS-cluster indienen. De containers kunnen vervolgens de NFS-schijf aan hun lokale directory monteren.

Hier is een voorbeeld Kubernetes definitie voor het aanhoudende volume (Deze definitie gaat ervan uit dat uw cluster en VM zijn in dezelfde VNET):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: <NFS_NAME>
  labels:
    type: nfs
spec:
  capacity:
    storage: 1Gi
  accessModes:
    - ReadWriteMany
  nfs:
    server: <NFS_INTERNAL_IP>
    path: <NFS_EXPORT_FILE_PATH>
```
Vervang **NFS_INTERNAL_IP,** **NFS_NAME** en **NFS_EXPORT_FILE_PATH** door NFS Server-informatie.

U hebt ook een bestand met permanente volumeclaim nodig. Hier is een voorbeeld van wat op te nemen:

>[!IMPORTANT]  
>**"storageClassName"** moet een lege tekenreeks blijven of de claim werkt niet.

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: <NFS_NAME>
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: ""
  resources:
    requests:
      storage: 1Gi
  selector: 
    matchLabels:
      type: nfs
```

## <a name="troubleshooting"></a>Problemen oplossen
Als u vanuit een cluster geen verbinding met de server maken, is een probleem mogelijk de geëxporteerde map of de bovenliggende, niet voldoende machtigingen om toegang te krijgen tot de server.

Controleer of zowel uw exportmap als de bovenliggende map 777 machtigingen hebben.

U machtigingen controleren door de onderstaande opdracht uit te voeren en de mappen moeten *'drwxrwxrwx'* machtigingen hebben:
```console
ls -l
```

## <a name="more-information"></a>Meer informatie
Om een volledige walkthrough te krijgen of om u te helpen uw NFS Server-installatie te debuggen, vindt u hier een uitgebreide zelfstudie:
  - [NFS-zelfstudie][nfs-tutorial]

## <a name="next-steps"></a>Volgende stappen

Zie [Aanbevolen procedures voor opslag en back-ups in AKS voor][operator-best-practices-storage]bijbehorende aanbevolen procedures.

<!-- LINKS - external -->
[kubernetes-volumes]: https://kubernetes.io/docs/concepts/storage/volumes/
[linux-create]: https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm
[nfs-tutorial]: https://help.ubuntu.com/community/SettingUpNFSHowTo#Pre-Installation_Setup
[aks-virtual-network]: https://docs.microsoft.com/azure/aks/configure-kubenet#create-an-aks-cluster-in-the-virtual-network
[peer-virtual-networks]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[operator-best-practices-storage]: operator-best-practices-storage.md

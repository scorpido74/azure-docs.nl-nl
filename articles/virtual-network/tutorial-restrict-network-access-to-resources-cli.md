---
title: Netwerktoegang beperken tot PaaS-resources - Azure CLI
description: In dit artikel leert u hoe u de netwerktoegang tot Azure-bronnen, zoals Azure Storage en Azure SQL Database, beperkt en beperkt met eindpunten voor virtuele netwerkservices met behulp van de Azure CLI.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: azure-resource-manager
Customer intent: I want only resources in a virtual network subnet to access an Azure PaaS resource, such as an Azure Storage account.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 03/14/2018
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: f2dcc714bc9052dd51f114e24f0b9bd74b87480c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74186407"
---
# <a name="restrict-network-access-to-paas-resources-with-virtual-network-service-endpoints-using-the-azure-cli"></a>Netwerktoegang tot PaaS-resources beperken met eindpunten voor virtuele netwerkservices met behulp van de Azure CLI

Met service-eindpunten voor virtuele netwerken kunt u de netwerktoegang tot sommige Azure-servicebronnen beperken tot een subnet van een virtueel netwerk. U kunt ook internettoegang tot de resources verwijderen. Service-eindpunten zorgen voor een rechtstreekse verbinding van uw virtuele netwerk met ondersteunde Azure-services, zodat u de privéadresruimte van uw virtuele netwerk kunt gebruiken voor toegang tot de Azure-services. Verkeer dat bestemd is voor Azure-resources via de service-eindpunten blijft altijd op het Microsoft Azure-backbone-netwerk. In dit artikel leert u het volgende:

* Een virtueel netwerk maken met één subnet
* Een subnet toevoegen en een service-eindpunt inschakelen
* Een Azure-resource maken en alleen toegang ertoe toestaan vanaf een subnet
* Een virtuele machine (VM) implementeren op elk subnet
* Toegang tot een resource vanaf een subnet bevestigen
* Bevestigen dat toegang wordt geweigerd aan een resource vanaf een subnet en internet

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze snelstart de Azure CLI versie 2.0.28 of later uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren]( /cli/azure/install-azure-cli). 

## <a name="create-a-virtual-network"></a>Een virtueel netwerk maken

Voordat u een virtueel netwerk maakt, moet u een brongroep maken voor het virtuele netwerk en alle andere bronnen die in dit artikel zijn gemaakt. Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *VS - oost*.

```azurecli-interactive
az group create \
  --name myResourceGroup \
  --location eastus
```

Maak een virtueel netwerk met één subnet met [az-netwerk vnet create](/cli/azure/network/vnet).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen 

U serviceeindpunten alleen inschakelen voor services die eindpunten van services ondersteunen. Serviceendpoint-services weergeven die beschikbaar zijn op een Azure-locatie met [vnet list-endpoint-services van het AZ-netwerk.](/cli/azure/network/vnet) In het volgende voorbeeld wordt een lijst geretourneerd met services die beschikbaar zijn in de *eastusregio.* De lijst met geretourneerde services wordt in de loop van de tijd groter, naarmate meer Azure-services serviceeindpunt zijn ingeschakeld.

```azurecli-interactive
az network vnet list-endpoint-services \
  --location eastus \
  --out table
``` 

Maak een extra subnet in het virtuele netwerk met [az-netwerk vnet subnet maken](/cli/azure/network/vnet/subnet). In dit voorbeeld wordt een serviceeindpunt voor *Microsoft.Storage* gemaakt voor het subnet: 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24 \
  --service-endpoints Microsoft.Storage
```

## <a name="restrict-network-access-for-a-subnet"></a>Netwerktoegang voor een subnet beperken

Maak een netwerkbeveiligingsgroep met [het AZ-netwerk nsg maken](/cli/azure/network/nsg). In het volgende voorbeeld wordt een netwerkbeveiligingsgroep met de naam *myNsgPrivate geopperd.*

```azurecli-interactive
az network nsg create \
  --resource-group myResourceGroup \
  --name myNsgPrivate
```

Koppel de netwerkbeveiligingsgroep aan het *subnet Privé* aan [de vnet-subnetupdate van het AZ-netwerk.](/cli/azure/network/vnet/subnet) In het volgende voorbeeld wordt de *myNsgPrivate-netwerkbeveiligingsgroep* aan het *subnet Privé* koppelt:

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Private \
  --resource-group myResourceGroup \
  --network-security-group myNsgPrivate
```

Maak beveiligingsregels met [de NSG-regel van het AZ-netwerk.](/cli/azure/network/nsg/rule) De volgende regel geeft uitgaande toegang tot de openbare IP-adressen die zijn toegewezen aan de Azure Storage-service: 

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-Storage-All \
  --access Allow \
  --protocol "*" \
  --direction Outbound \
  --priority 100 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Storage" \
  --destination-port-range "*"
```

Elke netwerkbeveiligingsgroep bevat verschillende [standaardbeveiligingsregels.](security-overview.md#default-security-rules) De regel die volgt overschrijft een standaardbeveiligingsregel die uitgaande toegang tot alle openbare IP-adressen mogelijk maakt. De `destination-address-prefix "Internet"` optie weigert uitgaande toegang tot alle openbare IP-adressen. De vorige regel overschrijft deze regel vanwege de hogere prioriteit, die toegang geeft tot de openbare IP-adressen van Azure Storage.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Deny-Internet-All \
  --access Deny \
  --protocol "*" \
  --direction Outbound \
  --priority 110 \
  --source-address-prefix "VirtualNetwork" \
  --source-port-range "*" \
  --destination-address-prefix "Internet" \
  --destination-port-range "*"
```

Met de volgende regel kan SSH-verkeer overal binnenkomen naar het subnet. De regel overschrijft een standaardbeveiligingsregel waardoor al het inkomende verkeer van internet wordt geweigerd. SSH is toegestaan om het subnet, zodat de connectiviteit kan worden getest in een latere stap.

```azurecli-interactive
az network nsg rule create \
  --resource-group myResourceGroup \
  --nsg-name myNsgPrivate \
  --name Allow-SSH-All \
  --access Allow \
  --protocol Tcp \
  --direction Inbound \
  --priority 120 \
  --source-address-prefix "*" \
  --source-port-range "*" \
  --destination-address-prefix "VirtualNetwork" \
  --destination-port-range "22"
```

## <a name="restrict-network-access-to-a-resource"></a>Netwerktoegang tot een resource beperken

De stappen die nodig zijn om netwerktoegang te beperken tot resources die zijn gemaakt met Azure-services waarvoor service-eindpunten zijn ingeschakeld, verschillen per service. Zie de documentatie voor afzonderlijke services voor specifieke stappen voor elke service. De rest van dit artikel bevat stappen om netwerktoegang voor een Azure Storage-account te beperken, als voorbeeld.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak een Azure-opslagaccount met [het AZ-opslagaccount.](/cli/azure/storage/account) Vervang `<replace-with-your-unique-storage-account-name>` door een naam die uniek is voor alle Azure-locaties, tussen 3-24 tekens in lengte, met alleen cijfers en kleine letters.

```azurecli-interactive
storageAcctName="<replace-with-your-unique-storage-account-name>"

az storage account create \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Nadat het opslagaccount is gemaakt, haalt u de verbindingstekenreeks voor het opslagaccount op in een variabele met [show-connection-string voor az-opslagaccount](/cli/azure/storage/account). De verbindingstekenreeks wordt gebruikt om een bestandsshare in een latere stap te maken.

```azurecli-interactive
saConnectionString=$(az storage account show-connection-string \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Bekijk de inhoud van de variabele en noteer de waarde voor **AccountKey** die is geretourneerd in de uitvoer, omdat deze in een latere stap wordt gebruikt.

```azurecli-interactive
echo $saConnectionString
```

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

Maak een bestandsshare in het opslagaccount met [az-opslagshare maken](/cli/azure/storage/share). In een latere stap wordt deze bestandsshare gemonteerd om de toegang tot het netwerk tot het bestand te bevestigen.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString > /dev/null
```

### <a name="deny-all-network-access-to-a-storage-account"></a>Alle netwerktoegang tot een opslagaccount weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaardactie in Weigeren met de update *van* [het AZ-opslagaccount.](/cli/azure/storage/account) Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-a-subnet"></a>Netwerktoegang vanuit een subnet inschakelen

Geef netwerktoegang tot het opslagaccount vanaf het *subnet Private* met [de add-rule van het AZ-opslagaccount.](/cli/azure/storage/account/network-rule)

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName \
  --vnet-name myVirtualNetwork \
  --subnet Private
```
## <a name="create-virtual-machines"></a>Virtuele machines maken

Implementeer een VM in elk subnet om de netwerktoegang tot een opslagaccount te testen.

### <a name="create-the-first-virtual-machine"></a>De eerste virtuele machine maken

Maak een VM in het *subnet Openbaar* met [az vm maken](/cli/azure/vm). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPublic \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Nadat de VM is gemaakt, toont de Azure CLI informatie die vergelijkbaar is met het volgende voorbeeld: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmPublic",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```

Let op de **publicIpAddress** in de geretourneerde uitvoer. Dit adres wordt gebruikt om toegang te krijgen tot de VM vanaf het internet in een latere stap.

### <a name="create-the-second-virtual-machine"></a>De tweede virtuele machine maken

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmPrivate \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Na het maken, kennis te nemen van de **publicIpAddress** in de output geretourneerd. Dit adres wordt gebruikt om toegang te krijgen tot de VM vanaf het internet in een latere stap.

## <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

SSH in de *myVmPrivate* VM. Vervang * \<publicIpAddress>* door het openbare IP-adres van uw *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Een map maken voor een bevestigingspunt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Monteer de Azure-bestandsshare op de map die u hebt gemaakt. Voordat u de volgende `<storage-account-name>` opdracht uitvoert, `<storage-account-key>` vervangt u de accountnaam en de sleutel die u hebt opgehaald in [Een opslagaccount maken.](#create-a-storage-account)

```bash
sudo mount --types cifs //<storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

U ontvangt `user@myVmPrivate:~$` de prompt. Het Azure-bestandsaandeel is is gemonteerd op */mnt/MyAzureFileShare*.

Controleer of de VM geen uitgaande connectiviteit heeft met andere openbare IP-adressen:

```bash
ping bing.com -c 4
```

U krijgt geen antwoorden, omdat de netwerkbeveiligingsgroep die is gekoppeld aan het *Privé*-subnet geen uitgaande toegang toestaat aan andere openbare IP-adressen dan de adressen die zijn toegewezen aan de Azure Storage-service.

Sluit de SSH-sessie af op de *myVmPrivate* VM.

## <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

Gebruik de volgende opdracht om een SSH-sessie te maken met de *myVmPublic* VM. Vervang `<publicIpAddress>` door het openbare IP-adres van uw *myVmPublic-vm:* 

```bash 
ssh <publicIpAddress>
```

Maak een map voor een bevestigingspunt:

```bash
sudo mkdir /mnt/MyAzureFileShare
```

Probeer de Azure-bestandsshare te monteren op de map die u hebt gemaakt. In dit artikel wordt ervan uitgegaan dat u de nieuwste versie van Ubuntu hebt geïmplementeerd. Als u eerdere versies van Ubuntu gebruikt, [raadpleegt](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) u Mount on Linux voor aanvullende instructies over het monteren van bestandsshares. Voordat u de volgende `<storage-account-name>` opdracht uitvoert, `<storage-account-key>` vervangt u de accountnaam en de sleutel die u hebt opgehaald in [Een opslagaccount maken:](#create-a-storage-account)

```bash
sudo mount --types cifs //storage-account-name>.file.core.windows.net/my-file-share /mnt/MyAzureFileShare --options vers=3.0,username=<storage-account-name>,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Toegang wordt geweigerd en `mount error(13): Permission denied` u ontvangt een fout, omdat de *myVmPublic-vm* wordt geïmplementeerd in het subnet *Openbaar.* Het *Openbare* subnet heeft geen service-eindpunt ingeschakeld voor Azure Storage en het opslagaccount staat alleen netwerktoegang toe van het *Privé*-subnet, en niet van het *Openbare*subnet.

Sluit de SSH-sessie af op de *myVmPublic* VM.

Probeer vanaf uw computer de aandelen in uw opslagaccount te bekijken met de lijst met az-opslagshare.From your computer, probeer de aandelen in uw opslagaccount te bekijken met [de az-opslagsharelijst.](/cli/azure/storage/share?view=azure-cli-latest) Vervang `<account-name>` `<account-key>` en met de naam en sleutel van het opslagaccount uit [Een opslagaccount maken:](#create-a-storage-account)

```azurecli-interactive
az storage share list \
  --account-name <account-name> \
  --account-key <account-key>
```

Toegang wordt geweigerd en u ontvangt een *dit verzoek is niet gemachtigd om deze bewerkingsfout uit te voeren,* omdat uw computer zich niet in het *privésubnet* van het virtuele *myvirtualnetwerk* bevindt.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, gebruikt u de verwijdering van [de AZ-groep](/cli/azure) om de brongroep en alle bronnen die deze bevat te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een serviceeindpunt ingeschakeld voor een virtueel netwerksubnet. U hebt geleerd dat service-eindpunten kunnen worden ingeschakeld voor met meerdere Azure-services geïmplementeerde resources. U hebt een Azure Storage-account gemaakt en netwerktoegang tot het opslagaccount beperkt tot alleen resources binnen een subnet van een virtueel netwerk. Zie voor meer informatie over service-eindpunten [Overzicht voor service-eindpunten](virtual-network-service-endpoints-overview.md) en [Subnetten beheren](virtual-network-manage-subnet.md).

Als u meerdere virtuele netwerken in uw account hebt, kunt u twee virtuele netwerken met elkaar verbinden zodat de resources in beide virtuele netwerken met elkaar kunnen communiceren. Zie [Virtuele netwerken verbinden](tutorial-connect-virtual-networks-cli.md)voor meer informatie.

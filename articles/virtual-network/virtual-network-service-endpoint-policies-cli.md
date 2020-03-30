---
title: Gegevensexfiltratie beperken tot Azure Storage - Azure CLI
description: In dit artikel leert u hoe u de exfiltratie van virtuele netwerkgegevens beperken en beperken tot Azure Storage-bronnen met eindpuntbeleid voor virtuele netwerkservices met behulp van azure cli.
services: virtual-network
documentationcenter: virtual-network
author: rdhillon
manager: ''
editor: ''
tags: azure-resource-manager
Customer intent: I want only specific Azure Storage account to be allowed access from a virtual network subnet.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: rdhillon
ms.custom: ''
ms.openlocfilehash: e01af052a936403162115965f2dc5b3ad46dd9cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271188"
---
# <a name="manage-data-exfiltration-to-azure-storage-accounts-with-virtual-network-service-endpoint-policies-using-the-azure-cli"></a>Gegevensexfiltratie beheren naar Azure Storage-accounts met eindpuntbeleid voor virtuele netwerkservices met behulp van de Azure CLI

Met eindpuntbeleidsregels voor virtuele netwerkservices u toegangsbeheer toepassen op Azure Storage-accounts vanuit een virtueel netwerk via serviceeindpunten. Dit is een sleutel tot het beveiligen van uw workloads, het beheren van welke opslagaccounts zijn toegestaan en waar gegevensexfiltratie is toegestaan.
In dit artikel leert u het volgende:

* Maak een virtueel netwerk en voeg een subnet toe.
* Serviceeindpunt inschakelen voor Azure Storage.
* Maak twee Azure Storage-accounts en geef netwerktoegang toe aan het subnet dat hierboven is gemaakt.
* Maak een eindpuntbeleid voor service om alleen toegang te geven tot een van de opslagaccounts.
* Implementeer een virtuele machine (VM) op het subnet.
* Bevestig de toegang tot het toegestane opslagaccount vanaf het subnet.
* Bevestig dat de toegang tot het niet-toegestane opslagaccount vanaf het subnet wordt geweigerd.

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
  --subnet-name Private \
  --subnet-prefix 10.0.0.0/24
```

## <a name="enable-a-service-endpoint"></a>Een service-eindpunt inschakelen 

In dit voorbeeld wordt een serviceeindpunt voor *Microsoft.Storage* gemaakt voor het subnet *Privé:* 

```azurecli-interactive
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.0.0/24 \
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

## <a name="restrict-network-access-to-azure-storage-accounts"></a>Netwerktoegang tot Azure Storage-accounts beperken

In deze sectie worden stappen weergegeven om de netwerktoegang voor een Azure Storage-account te beperken vanaf het opgegeven subnet in een virtueel netwerk via serviceeindpunt.

### <a name="create-a-storage-account"></a>Een opslagaccount maken

Maak twee Azure-opslagaccounts met [het AZ-opslagaccount.](/cli/azure/storage/account)

```azurecli-interactive
storageAcctName1="allowedstorageacc"

az storage account create \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2

storageAcctName2="notallowedstorageacc"

az storage account create \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --sku Standard_LRS \
  --kind StorageV2
```

Nadat de opslagaccounts zijn gemaakt, haalt u de verbindingstekenreeks voor de opslagaccounts op in een variabele met [show-connection-string voor az-opslagaccounts](/cli/azure/storage/account). De verbindingstekenreeks wordt gebruikt om een bestandsshare in een latere stap te maken.

```azurecli-interactive
saConnectionString1=$(az storage account show-connection-string \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)

saConnectionString2=$(az storage account show-connection-string \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --query 'connectionString' \
  --out tsv)
```

<a name="account-key"></a>Bekijk de inhoud van de variabele en noteer de waarde voor **AccountKey** die is geretourneerd in de uitvoer, omdat deze in een latere stap wordt gebruikt.

```azurecli-interactive
echo $saConnectionString1

echo $saConnectionString2
```

### <a name="create-a-file-share-in-the-storage-account"></a>Een bestandsshare maken in het opslagaccount

Maak een bestandsshare in het opslagaccount met [az-opslagshare maken](/cli/azure/storage/share). In een latere stap wordt deze bestandsshare gemonteerd om de toegang tot het netwerk tot het bestand te bevestigen.

```azurecli-interactive
az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString1 > /dev/null

az storage share create \
  --name my-file-share \
  --quota 2048 \
  --connection-string $saConnectionString2 > /dev/null
```

### <a name="deny-all-network-access-to-the-storage-account"></a>Alle netwerktoegang tot het opslagaccount weigeren

Standaard accepteren opslagaccounts netwerkverbindingen van clients in ieder netwerk. Als u de toegang tot geselecteerde netwerken wilt beperken, wijzigt u de standaardactie in Weigeren met de update *van* [het AZ-opslagaccount.](/cli/azure/storage/account) Nadat de netwerktoegang is geweigerd, is het opslagaccount niet via elk netwerk toegankelijk.

```azurecli-interactive
az storage account update \
  --name $storageAcctName1 \
  --resource-group myResourceGroup \
  --default-action Deny

az storage account update \
  --name $storageAcctName2 \
  --resource-group myResourceGroup \
  --default-action Deny
```

### <a name="enable-network-access-from-virtual-network-subnet"></a>Netwerktoegang inschakelen via subnet van virtueel netwerk

Geef netwerktoegang tot het opslagaccount vanaf het *subnet Private* met [de add-rule van het AZ-opslagaccount.](/cli/azure/storage/account/network-rule)

```azurecli-interactive
az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName1 \
  --vnet-name myVirtualNetwork \
  --subnet Private

az storage account network-rule add \
  --resource-group myResourceGroup \
  --account-name $storageAcctName2 \
  --vnet-name myVirtualNetwork \
  --subnet Private
```

## <a name="apply-policy-to-allow-access-to-valid-storage-account"></a>Beleid toepassen om toegang te verlenen tot een geldig opslagaccount

Azure Service Endpoint-beleidsregels zijn alleen beschikbaar voor Azure Storage. Daarom schakelen we Service Endpoint voor *Microsoft.Storage* in op dit subnet voor deze voorbeeldinstelling.

Serviceeindpuntbeleid wordt toegepast op serviceeindpunten. We beginnen met het maken van een service endpoint beleid. Vervolgens maken we de beleidsdefinities onder dit beleid voor Azure Storage-accounts die op de witte lijst moeten worden gezet voor dit subnet

Een eindpuntbeleid voor service maken

```azurecli-interactive
az network service-endpoint policy create \
  --resource-group myResourceGroup \
  --name mysepolicy \
  --location eastus
```

Sla de resource URI op voor het toegestane opslagaccount in een variabele. Voordat u de onderstaande opdracht uitvoert, vervangt * \<u uw abonnement-id>* met de werkelijke waarde van uw abonnements-ID.

```azurecli-interactive
$serviceResourceId="/subscriptions/<your-subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/allowedstorageacc"
```

Een beleidsdefinitie maken & toevoegen voor het toestaan van het bovenstaande Azure Storage-account aan het eindpuntbeleid van de service

```azurecli-interactive
az network service-endpoint policy-definition create \
  --resource-group myResourceGroup \
  --policy-name mysepolicy \
  --name mypolicydefinition \
  --service "Microsoft.Storage" \
  --service-resources $serviceResourceId
```

En werk het virtuele netwerksubnet bij om het eindpuntbeleid van de service dat in de vorige stap is gemaakt, te koppelen

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --service-endpoints Microsoft.Storage \
  --service-endpoint-policy mysepolicy
```

## <a name="validate-access-restriction-to-azure-storage-accounts"></a>Toegangsbeperking voor Azure Storage-accounts valideren

### <a name="create-the-virtual-machine"></a>De virtuele machine maken

Als u netwerktoegang tot een opslagaccount wilt testen, implementeert u een VM naar het subnet.

Maak een VM in het *subnet Privé* met [az vm create](/cli/azure/vm). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`.

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

### <a name="confirm-access-to-storage-account"></a>Toegang tot opslagaccount bevestigen

SSH in de *myVmPrivate* VM. Vervang * \<publicIpAddress>* door het openbare IP-adres van uw *myVmPrivate* VM.

```bash 
ssh <publicIpAddress>
```

Een map maken voor een bevestigingspunt:

```bash
sudo mkdir /mnt/MyAzureFileShare1
```

Monteer de Azure-bestandsshare op de map die u hebt gemaakt. Voordat u de onderstaande opdracht uitvoert, vervangt * \<u>opslag-accountsleutel* door de waarde van *AccountKey* van **$saConnectionString1**.

```bash
sudo mount --types cifs //allowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare1 --options vers=3.0,username=allowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

U ontvangt `user@myVmPrivate:~$` de prompt. Het Azure-bestandsaandeel is is gemonteerd op */mnt/MyAzureFileShare*.

### <a name="confirm-access-is-denied-to-storage-account"></a>Bevestigen dat toegang tot opslagaccount wordt geweigerd

Maak vanaf dezelfde VM *myVmPrivate*een map voor een bevestigingspunt:

```bash
sudo mkdir /mnt/MyAzureFileShare2
```

Probeer het Azure-bestandsaandeel te monteren vanaf het opslagaccount *dat niet is toegestaanopslagtoegang* toe te voegen aan de map die u hebt gemaakt. In dit artikel wordt ervan uitgegaan dat u de nieuwste versie van Ubuntu hebt geïmplementeerd. Als u eerdere versies van Ubuntu gebruikt, [raadpleegt](../storage/files/storage-how-to-use-files-linux.md?toc=%2fazure%2fvirtual-network%2ftoc.json) u Mount on Linux voor aanvullende instructies over het monteren van bestandsshares. 

Voordat u de onderstaande opdracht uitvoert, vervangt * \<u>opslagrekeningsleutel* door de waarde van *AccountKey* van **$saConnectionString2**.

```bash
sudo mount --types cifs //notallowedstorageacc.file.core.windows.net/my-file-share /mnt/MyAzureFileShare2 --options vers=3.0,username=notallowedstorageacc,password=<storage-account-key>,dir_mode=0777,file_mode=0777,serverino
```

Toegang wordt geweigerd en `mount error(13): Permission denied` u ontvangt een fout, omdat dit opslagaccount niet in de lijst met toegestane punten van het eindpuntbeleid van de service die we op het subnet hebben toegepast, bevat. 

Sluit de SSH-sessie af op de *myVmPublic* VM.

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u niet meer nodig bent, gebruikt u de verwijdering van [de AZ-groep](/cli/azure) om de brongroep en alle bronnen die deze bevat te verwijderen.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een eindpuntbeleid voor services toegepast op een eindpunt van de Azure-virtuele netwerkservice op Azure Storage. U hebt Azure Storage-accounts en beperkte netwerktoegang tot alleen bepaalde opslagaccounts (en dus anderen geweigerd) gemaakt vanuit een virtueel netwerksubnet. Zie Overzicht van [eindpunten voor servicevoor](virtual-network-service-endpoint-policies-overview.md)meer informatie over eindpuntbeleid voor services .

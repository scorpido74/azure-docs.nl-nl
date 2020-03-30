---
title: Diagnose van een probleem met vm-netwerkroutering - Azure CLI
titleSuffix: Azure Network Watcher
description: In dit artikel leert u hoe u een probleem met het routeren van een virtuele machine-netwerk diagnosticeren met behulp van de volgende hopmogelijkheid van Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: damendo
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose virtual machine (VM) network routing problem that prevents communication to different destinations.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: damendo
ms.custom: ''
ms.openlocfilehash: ae139ea7aca7c3896fcd7b0acf2bf6673490a2f4
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382899"
---
# <a name="diagnose-a-virtual-machine-network-routing-problem---azure-cli"></a>Diagnose van een probleem met de routering van een netwerk voor virtuele machines - Azure CLI

In dit artikel implementeert u een virtuele machine (VM) en controleert u de communicatie naar een IP-adres en URL. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest de Azure CLI lokaal te installeren en te gebruiken, moet u in dit artikel de Azure CLI-versie 2.0.28 of hoger uitvoeren. Voer `az --version` uit om te zien welke versie is geïnstalleerd. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). Nadat u de Azure CLI-versie hebt geverifieerd, voert u uit `az login` om een verbinding met Azure te maken. De Azure CLI-opdrachten in dit artikel zijn opgemaakt om in een Bash-shell uit te voeren.

## <a name="create-a-vm"></a>Een virtuele machine maken

Voordat u een VM kunt maken, maakt u eerst een resourcegroep die de VM bevat. Maak een resourcegroep maken met [az group create](/cli/azure/group#az-group-create). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus geaald:*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak een VM met [az vm create](/cli/azure/vm#az-vm-create). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. In het volgende voorbeeld wordt een VM met de naam *myVm gemaakt:*

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet verder met de resterende stappen totdat de VM is gemaakt en de Azure CLI de uitvoer retourneert.

## <a name="test-network-communication"></a>Netwerkcommunicatie testen

Als u de netwerkcommunicatie met Network Watcher wilt testen, moet u eerst een netwerkwatcher in de regio inschakelen waarin de VM zich bevindt en vervolgens de volgende hopcapaciteit van Network Watcher gebruiken om de communicatie te testen.

### <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

Als u al een netwerkwatcher hebt ingeschakeld in de regio Oost-VS, gaat u naar [Volgende hop gebruiken.](#use-next-hop) Gebruik de [opdracht az-netwerkwatcher om](/cli/azure/network/watcher#az-network-watcher-configure) een netwerkwatcher in de regio Oost-VS te maken:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-next-hop"></a>Volgende hop gebruiken

Azure maakt automatisch routes naar standaardbestemmingen. U kunt uw eigen, aangepaste routes maken om die standaardroutes te overschrijven. Soms hebben aangepaste routes tot gevolg dat de communicatie mislukt. Als u de routering van een VM wilt testen, gebruikt u [de show-next-hop van AZ-netwerkwatcher](/cli/azure/network/watcher?view=azure-cli-latest#az-network-watcher-show-next-hop) om de volgende routering hop te bepalen wanneer het verkeer is bestemd voor een specifiek adres.

Uitgaande communicatie van de VM naar een van de IP-adressen testen voor www.bing.com:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 13.107.21.200 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

Na een paar seconden, de uitvoer informeert u dat de **nextHopType** is **internet,** en dat de **routeTableId** is **Systeem route**. Dit resultaat laat u weten dat er een geldige route naar de bestemming is.

Uitgaande communicatie van de VM naar 172.31.0.100 testen:

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 172.31.0.100 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVm \
  --nic myVmVMNic \
  --out table
```

De geretourneerde uitvoer informeert u dat **Geen** de **nextHopType**is en dat de **routeTableId** ook **Systeemroute**is. Hieruit kunt u afleiden dat er wel een geldige systeemroute is naar de bestemming, maar dat er geen volgende hop is voor het routeren van het verkeer naar de bestemming.

## <a name="view-details-of-a-route"></a>Details van een route weergeven

Als u de routering verder wilt analyseren, bekijkt u de effectieve routes voor de netwerkinterface met de opdracht [az-netwerk nic show-effective-route-table:](/cli/azure/network/nic#az-network-nic-show-effective-route-table)

```azurecli-interactive
az network nic show-effective-route-table \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

De volgende tekst is opgenomen in de geretourneerde uitvoer:

```
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
  },
  "addressPrefix": [
    "0.0.0.0/0"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "Internet",
  "source": "Default",
  "state": "Active"
},
```

Toen u `az network watcher show-next-hop` de opdracht gebruikte om uitgaande communicatie te testen naar 13.107.21.200 in [Use next hop,](#use-next-hop)werd de route met het **adresVoorvoegsel** 0.0.0.0/0** gebruikt om verkeer naar het adres te leiden, omdat geen enkele andere route in de uitvoer het adres bevat. De standaardinstelling is dat alle adressen die niet zijn opgegeven in het adresvoorvoegsel van een andere route, worden doorgestuurd naar internet.

Wanneer u `az network watcher show-next-hop` de opdracht echter gebruikte om uitgaande communicatie naar 172.31.0.100 te testen, liet het resultaat u weten dat er geen volgende hoptype was. In de geretourneerde uitvoer ziet u ook de volgende tekst:

```
{
  "additionalProperties": {
    "disableBgpRoutePropagation": false
      },
  "addressPrefix": [
    "172.16.0.0/12"
  ],
  "name": null,
  "nextHopIpAddress": [],
  "nextHopType": "None",
  "source": "Default",
  "state": "Active"
},
```

Zoals u zien in `az network watcher nic show-effective-route-table` de uitvoer van de opdracht, hoewel er een standaardroute naar het voorvoegsel 172.16.0.0/12 is, dat het 172.31.0.100-adres bevat, is het **volgendeHopType** **Geen**. Azure maakt een standaardroute naar 172.16.0.0/12, maar stelt geen volgend hoptype in, tenzij daar een reden voor is. Als u bijvoorbeeld het adresbereik 172.16.0.0/12 hebt toegevoegd aan de adresruimte van het virtuele netwerk, wijzigt Azure het **nextHopType** in **virtueel netwerk** voor de route. Een controle zou dan **virtueel netwerk** als **nextHopType**tonen.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group#az-group-delete) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u een VM gemaakt en netwerkroutering van de VM gediagnosticeerd. U hebt geleerd dat Azure verschillende standaardroutes maakt en u hebt de routering naar twee verschillende bestemmingen getest. Lees hier meer over [routering in Azure](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en hoe u [aangepaste routes maakt](../virtual-network/manage-route-table.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-route).

Voor uitgaande VM-verbindingen u ook de latentie en toegestane en geweigerde netwerkverkeer tussen de VM en een eindpunt bepalen met behulp van de capaciteit [voor het oplossen van verbindingsproblemen](network-watcher-connectivity-cli.md) van Network Watcher. U de communicatie tussen een VM en een eindpunt, zoals een IP-adres of URL, na verloop van tijd controleren met behulp van de mogelijkheid voor netwerkwatcher-verbindingsmonitor. Zie [Een netwerkverbinding controleren](connection-monitor.md)voor meer informatie.

---
title: Pakketopnames beheren met Azure Network Watcher - Azure CLI | Microsoft Documenten
description: Op deze pagina wordt uitgelegd hoe u de functie voor het vastleggen van pakketten van Network Watcher beheert met behulp van de Azure CLI
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: cb0c1d10-f7f2-4c34-b08c-f73452430be8
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 7a69610d1ac176354a9d7e388a12ccc7f064d848
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382712"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-the-azure-cli"></a>Pakketopnames beheren met Azure Network Watcher met de Azure CLI

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure-CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Met Network Watcher-pakketopname u opnamesessies maken om het verkeer van en naar een virtuele machine bij te houden. Er zijn filters beschikbaar voor de opnamesessie om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Packet capture helpt bij het diagnosticeren van netwerkafwijkingen, zowel reactief als proactief. Andere toepassingen zijn het verzamelen van netwerkstatistieken, het verkrijgen van informatie over netwerkinbraken, het opsporen van client-servercommunicatie en nog veel meer. Door op afstand pakketopnames te kunnen activeren, verlicht deze mogelijkheid de last van het handmatig en op de gewenste machine uitvoeren van een pakket, wat kostbare tijd bespaart.

Als u de stappen in dit artikel wilt uitvoeren, moet u [de Azure Command-Line Interface voor Mac, Linux en Windows (Azure CLI) installeren.](/cli/azure/install-azure-cli)

In dit artikel neemt u de verschillende beheertaken mee die momenteel beschikbaar zijn voor het vastleggen van pakketten.

- [**Een pakketopname starten**](#start-a-packet-capture)
- [**Een pakketopname stoppen**](#stop-a-packet-capture)
- [**Een pakketopname verwijderen**](#delete-a-packet-capture)
- [**Een pakketopname downloaden**](#download-a-packet-capture)

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u de volgende bronnen hebt:

- Een exemplaar van Network Watcher in de regio die u wilt maken voor het vastleggen van een pakket
- Een virtuele machine met de packet capture extensie ingeschakeld.

> [!IMPORTANT]
> Voor het vastleggen van pakketten moet een agent op de virtuele machine worden uitgevoerd. De agent is geïnstalleerd als een extensie. Ga voor instructies over VM-extensies naar [Virtual Machine-extensies en -functies.](../virtual-machines/windows/extensions-features.md)

## <a name="install-vm-extension"></a>VM-extensie installeren

### <a name="step-1"></a>Stap 1

Voer `az vm extension set` de opdracht uit om de packet capture-agent op de virtuele gastmachine te installeren.

Voor virtuele Windows-machines:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentWindows --version 1.4
```

Voor Linux virtuele machines:

```azurecli-interactive
az vm extension set --resource-group resourceGroupName --vm-name virtualMachineName --publisher Microsoft.Azure.NetworkWatcher --name NetworkWatcherAgentLinux --version 1.4
```

### <a name="step-2"></a>Stap 2

Als u ervoor wilt zorgen `vm extension show` dat de agent is geïnstalleerd, voert u de opdracht uit en geeft u de naam van de resourcegroep en de virtuele machine door. Controleer de resulterende lijst om ervoor te zorgen dat de agent is geïnstalleerd.

Voor virtuele Windows-machines:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name NetworkWatcherAgentWindows
```

Voor Linux virtuele machines:

```azurecli-interactive
az vm extension show --resource-group resourceGroupName --vm-name virtualMachineName --name AzureNetworkWatcherExtension
```

Het volgende voorbeeld is een voorbeeld van de respons van`az vm extension show`

```json
{
  "autoUpgradeMinorVersion": true,
  "forceUpdateTag": null,
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/extensions/NetworkWatcherAgentWindows",
  "instanceView": null,
  "location": "westcentralus",
  "name": "NetworkWatcherAgentWindows",
  "protectedSettings": null,
  "provisioningState": "Succeeded",
  "publisher": "Microsoft.Azure.NetworkWatcher",
  "resourceGroup": "{resourceGroupName}",
  "settings": null,
  "tags": null,
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "typeHandlerVersion": "1.4",
  "virtualMachineExtensionType": "NetworkWatcherAgentWindows"
}
```

## <a name="start-a-packet-capture"></a>Een pakketopname starten

Zodra de voorgaande stappen zijn voltooid, wordt de packet capture-agent op de virtuele machine geïnstalleerd.

### <a name="step-1"></a>Stap 1

Een opslagaccount ophalen. Dit opslagaccount wordt gebruikt om het bestand voor het vastleggen van pakketten op te slaan.

```azurecli-interactive
az storage account list
```

### <a name="step-2"></a>Stap 2

Op dit punt bent u klaar om een pakketopname te maken.  Laten we eerst de parameters onderzoeken die u mogelijk wilt configureren. Filters zijn zo'n parameter die kan worden gebruikt om de gegevens die worden opgeslagen door de packet capture te beperken. In het volgende voorbeeld wordt een pakketopname met verschillende filters ingesteld.  De eerste drie filters verzamelen alleen uitgaand TCP-verkeer van lokaal IP 10.0.0.3 naar bestemmingspoorten 20, 80 en 443.  Het laatste filter verzamelt alleen UDP-verkeer.

```azurecli-interactive
az network watcher packet-capture create --resource-group {resourceGroupName} --vm {vmName} --name packetCaptureName --storage-account {storageAccountName} --filters "[{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"20\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"80\"},{\"protocol\":\"TCP\", \"remoteIPAddress\":\"1.1.1.1-255.255.255\",\"localIPAddress\":\"10.0.0.3\", \"remotePort\":\"443\"},{\"protocol\":\"UDP\"}]"
```

Het volgende voorbeeld is de `az network watcher packet-capture create` verwachte uitvoer van het uitvoeren van de opdracht.

```json
{
  "bytesToCapturePerPacket": 0,
  "etag": "W/\"b8cf3528-2e14-45cb-a7f3-5712ffb687ac\"",
  "filters": [
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "20"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "80"
    },
    {
      "localIpAddress": "10.0.0.3",
      "localPort": "",
      "protocol": "TCP",
      "remoteIpAddress": "1.1.1.1-255.255.255",
      "remotePort": "443"
    },
    {
      "localIpAddress": "",
      "localPort": "",
      "protocol": "UDP",
      "remoteIpAddress": "",
      "remotePort": ""
    }
  ],
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "provisioningState": "Succeeded",
  "resourceGroup": "NetworkWatcherRG",
  "storageLocation": {
    "filePath": null,
    "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/gwteststorage123abc",
    "storagePath": "https://gwteststorage123abc.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/{resourceGroupName}/p
roviders/microsoft.compute/virtualmachines/{vmName}/2017/05/25/packetcapture_16_22_34_630.cap"
  },
  "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}",
  "timeLimitInSeconds": 18000,
  "totalBytesPerSession": 1073741824
}
```

## <a name="get-a-packet-capture"></a>Een pakketopname krijgen

Als `az network watcher packet-capture show-status` u de opdracht uitvoert, wordt de status opgehaald van een momenteel actief of voltooid pakketvastleggen.

```azurecli-interactive
az network watcher packet-capture show-status --name packetCaptureName --location {networkWatcherLocation}
```

Het volgende voorbeeld is `az network watcher packet-capture show-status` de uitvoer van de opdracht. Het volgende voorbeeld is wanneer de opname is gestopt, met een StopReason of TimeExceeded.

```
{
  "additionalProperties": {
    "status": "Succeeded"
  },
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/pa
cketCaptures/packetCaptureName",
  "name": "packetCaptureName",
  "packetCaptureError": [],
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded"
}
```

## <a name="stop-a-packet-capture"></a>Een pakketopname stoppen

Door de `az network watcher packet-capture stop` opdracht uit te voeren, wordt deze gestopt als er een opnamesessie aan de gang is.

```azurecli-interactive
az network watcher packet-capture stop --name packetCaptureName --location westcentralus
```

> [!NOTE]
> De opdracht retourneert geen antwoord wanneer deze wordt uitgevoerd op een momenteel lopende opnamesessie of een bestaande sessie die al is gestopt.

## <a name="delete-a-packet-capture"></a>Een pakketopname verwijderen

```azurecli-interactive
az network watcher packet-capture delete --name packetCaptureName --location westcentralus
```

> [!NOTE]
> Als u een pakketopname verwijdert, wordt het bestand in het opslagaccount niet verwijderd.

## <a name="download-a-packet-capture"></a>Een pakketopname downloaden

Zodra uw pakketopnamesessie is voltooid, kan het opnamebestand worden geüpload naar blobopslag of naar een lokaal bestand op de vm. De opslaglocatie van de pakketopname wordt gedefinieerd bij het maken van de sessie. Microsoft Azure Storage Explorer is een handig hulpmiddel om toegang te krijgen tot deze vastleggenbestanden die zijn opgeslagen in een opslagaccount:https://storageexplorer.com/

Als een opslagaccount is opgegeven, worden pakketopnamebestanden opgeslagen in een opslagaccount op de volgende locatie:

```
https://{storageAccountName}.blob.core.windows.net/network-watcher-logs/subscriptions/{subscriptionId}/resourcegroups/{storageAccountResourceGroup}/providers/microsoft.compute/virtualmachines/{VMName}/{year}/{month}/{day}/packetCapture_{creationTime}.cap
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het automatiseren van pakketopnames met virtuele machinewaarschuwingen door [het maken van een waarschuwingsgeactiveerde pakketopname te](network-watcher-alert-triggered-packet-capture.md) bekijken

Find if certain traffic is allowed in or out of your VM by visiting [Check IP flow verify](diagnose-vm-network-traffic-filtering-problem.md)

<!-- Image references -->

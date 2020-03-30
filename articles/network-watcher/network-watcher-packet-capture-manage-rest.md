---
title: Pakketopnames beheren met Azure Network Watcher - REST API | Microsoft Documenten
description: Op deze pagina wordt uitgelegd hoe u de functie voor het vastleggen van pakketten van Network Watcher beheert met Azure REST API
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 5199cf95452f93db2c2dd747fcabc67a6722d31e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840890"
---
# <a name="manage-packet-captures-with-azure-network-watcher-using-azure-rest-api"></a>Pakketopnames beheren met Azure Network Watcher met Azure REST API

> [!div class="op_single_selector"]
> - [Azure-portal](network-watcher-packet-capture-manage-portal.md)
> - [Powershell](network-watcher-packet-capture-manage-powershell.md)
> - [Azure-CLI](network-watcher-packet-capture-manage-cli.md)
> - [Azure REST API](network-watcher-packet-capture-manage-rest.md)

Met Network Watcher-pakketopname u opnamesessies maken om het verkeer van en naar een virtuele machine bij te houden. Er zijn filters beschikbaar voor de opnamesessie om ervoor te zorgen dat u alleen het gewenste verkeer vastlegt. Packet capture helpt bij het diagnosticeren van netwerkafwijkingen, zowel reactief als proactief. Andere toepassingen zijn het verzamelen van netwerkstatistieken, het verkrijgen van informatie over netwerkinbraken, het opsporen van client-servercommunicatie en nog veel meer. Door op afstand pakketopnames te kunnen activeren, verlicht deze mogelijkheid de last van het handmatig en op de gewenste machine uitvoeren van een pakket, wat kostbare tijd bespaart.

In dit artikel neemt u de verschillende beheertaken mee die momenteel beschikbaar zijn voor het vastleggen van pakketten.

- [**Een pakketopname krijgen**](#get-a-packet-capture)
- [**Alle pakketopnames weergeven**](#list-all-packet-captures)
- [**De status van een pakketopname opvragen**](#query-packet-capture-status)
- [**Een pakketopname starten**](#start-packet-capture)
- [**Een pakketopname stoppen**](#stop-packet-capture)
- [**Een pakketopname verwijderen**](#delete-packet-capture)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="before-you-begin"></a>Voordat u begint

In dit scenario roept u de Network Watcher Rest API op om IP Flow Verify uit te voeren. ARMclient wordt gebruikt om de REST API aan te roepen met PowerShell. ARMClient is te vinden op chocolatey bij [ARMClient op Chocolatey](https://chocolatey.org/packages/ARMClient)

In dit scenario wordt ervan uitgegaan dat u de stappen in [Een netwerkwatcher maken](network-watcher-create.md) al hebt gevolgd om een netwerkwatcher te maken.

> Packet capture vereist een `AzureNetworkWatcherExtension`virtuele machine-extensie. Voor het installeren van de extensie op een Windows VM bezoek [Azure Network Watcher Agent virtuele machine extensie voor Windows](../virtual-machines/windows/extensions-nwa.md) en voor Linux VM bezoek Azure Network Watcher Agent virtuele machine extensie voor [Linux](../virtual-machines/linux/extensions-nwa.md).

## <a name="log-in-with-armclient"></a>Inloggen met ARMClient

```powershell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Een virtuele machine ophalen

Voer het volgende script uit om een virtuele machine terug te sturen. Deze informatie is nodig voor het starten van een packet capture.

De volgende code heeft variabelen nodig:

- **abonnementId** - De abonnements-id kan ook worden opgehaald met de **Get-AzSubscription** cmdlet.
- **resourceGroupName** - De naam van een resourcegroep die virtuele machines bevat.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Van de volgende uitvoer wordt de id van de virtuele machine in het volgende voorbeeld gebruikt.

```json
...
,
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```


## <a name="get-a-packet-capture"></a>Een pakketopname krijgen

In het volgende voorbeeld wordt de status van één pakketopname

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

De volgende antwoorden zijn voorbeelden van een standaardantwoord dat wordt geretourneerd bij het opvragen van de status van een pakketopname.

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Running",
  "packetCaptureError": []
}
```

```json
{
  "name": "TestPacketCapture5",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
  "captureStartTime": "2016-12-06T17:20:01.5671279Z",
  "packetCaptureStatus": "Stopped",
  "stopReason": "TimeExceeded",
  "packetCaptureError": []
}
```

## <a name="list-all-packet-captures"></a>Alle pakketopnames weergeven

In het volgende voorbeeld worden alle pakketopnamesessies in een regio optewel.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures?api-version=2016-12-01"
```

Het volgende antwoord is een voorbeeld van een typische reactie die wordt geretourneerd bij het verkrijgen van alle pakketopnames

```json
{
  "value": [
    {
      "name": "TestPacketCapture6",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture6",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Succeeded",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_19_53_056.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    },
    {
      "name": "TestPacketCapture7",
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/NetworkWatcherRG/providers/Microsoft.Network/networkWatchers/NetworkWatcher_westcentralus/packetCaptures/TestPacketCapture7",
      "etag": "W/\"091762e1-c23f-448b-89d5-37cf56e4c045\"",
      "properties": {
        "provisioningState": "Failed",
        "target": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute/virtualMachines/ContosoVM",
        "bytesToCapturePerPacket": 0,
        "totalBytesPerSession": 1073741824,
        "timeLimitInSeconds": 60,
        "storageLocation": {
          "storageId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374",
          "storagePath": "https://contosoexamplergdiag374.blob.core.windows.net/network-watcher-logs/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/contosoexamplerg/providers/microsoft.compute/virtualmachines/contosovm/2016/12/06/packetcap
ture_17_23_15_364.cap",
          "filePath": "c:\\temp\\packetcapture.cap"
        },
        "filters": [
          {
            "protocol": "Any",
            "localIPAddress": "",
            "localPort": "",
            "remoteIPAddress": "",
            "remotePort": ""
          }
        ]
      }
    }
  ]
}
```

## <a name="query-packet-capture-status"></a>Status voor het vastleggen van querypakketten

In het volgende voorbeeld worden alle pakketopnamesessies in een regio optewel.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient get "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/querystatus?api-version=2016-12-01"
```

Het volgende antwoord is een voorbeeld van een standaardantwoord dat wordt geretourneerd bij het opvragen van de status van een pakketopname.

```json
{
    "name": "vm1PacketCapture",
    "id": "/subscriptions/{guid}/resourceGroups/{resourceGroupName}/providers/Microsoft.Network/networkWatchers/{networkWatcherName}/packetCaptures/{packetCaptureName}",
    "captureStartTime" : "9/7/2016 12:35:24PM",
    "packetCaptureStatus" : "Stopped",
    "stopReason" : "TimeExceeded",
    "packetCaptureError" : [ ]
}
```

## <a name="start-packet-capture"></a>Pakketopname starten

In het volgende voorbeeld wordt een pakketopname gemaakt op een virtuele machine.  Het voorbeeld is geparameteriseerd om flexibiliteit mogelijk te maken bij het maken van een voorbeeld.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
$storageaccountname = "contosoexamplergdiag374"
$vmName = "ContosoVM"
$bytestoCaptureperPacket = "0"
$bytesPerSession = "1073741824"
$captureTimeinSeconds = "60"
$localIP = ""
$localPort = "" # Examples are: 80, or 80-120
$remoteIP = ""
$remotePort = "" # Examples are: 80, or 80-120
$protocol = "" # Valid values are TCP, UDP and Any.
$targetUri = "" # Example: /subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.compute/virtualMachine/$vmName
$storageId = "" #Example "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/ContosoExampleRG/providers/Microsoft.Storage/storageAccounts/contosoexamplergdiag374"
$storagePath = "" # Example: "https://mytestaccountname.blob.core.windows.net/capture/vm1Capture.cap"
$localFilePath = "c:\\temp\\packetcapture.cap" # Example: "d:\capture\vm1Capture.cap"

$requestBody = @"
{
    'properties':  {
                       'target':  '/${targetUri}',
                       'bytesToCapturePerPacket':  '${bytestoCaptureperPacket}',
                       'totalBytesPerSession':  '${bytesPerSession}',
                       'timeLimitinSeconds':  '${captureTimeinSeconds}',
                       'storageLocation':  {
                                               'storageId':  '${storageId}',
                                               'storagePath':  '${storagePath}',
                                               'filePath':  '${localFilePath}'
                                           },
                       'filters':  [
                                       {
                                           'protocol':  '${protocol}',
                                           'localIPAddress':  '${localIP}',
                                           'localPort':  '${localPort}',
                                           'remoteIPAddress':  '${remoteIP}',
                                           'remotePort':  '${remotePort}'
                                       }
                                   ]
                   }
}
"@

armclient PUT "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-07-01" $requestbody
```

## <a name="stop-packet-capture"></a>Pakketopname stoppen

In het volgende voorbeeld wordt een pakketopname op een virtuele machine gestopt.  Het voorbeeld is geparameteriseerd om flexibiliteit mogelijk te maken bij het maken van een voorbeeld.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}/stop?api-version=2016-12-01"
```

## <a name="delete-packet-capture"></a>Pakketvastleggen verwijderen

In het volgende voorbeeld wordt een pakketopname op een virtuele machine verwijderd.  Het voorbeeld is geparameteriseerd om flexibiliteit mogelijk te maken bij het maken van een voorbeeld.

```powershell
$subscriptionId = '<subscription id>'
$resourceGroupName = "NetworkWatcherRG"
$networkWatcherName = "NetworkWatcher_westcentralus"
$packetCaptureName = "TestPacketCapture5"

armclient delete "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/packetCaptures/${packetCaptureName}?api-version=2016-12-01"
```

> [!NOTE]
> Als u een pakketopname verwijdert, wordt het bestand in het opslagaccount niet verwijderd

## <a name="next-steps"></a>Volgende stappen

Raadpleeg aan [de slag met Azure Blob-opslag met .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)voor instructies voor het downloaden van bestanden uit azure-opslagaccounts. Een ander hulpmiddel dat kan worden gebruikt is Storage Explorer. Meer informatie over Storage Explorer vindt u hier via de volgende link: [Storage Explorer](https://storageexplorer.com/)

Meer informatie over het automatiseren van pakketopnames met virtuele machinewaarschuwingen door [het maken van een waarschuwingsgeactiveerde pakketopname te](network-watcher-alert-triggered-packet-capture.md) bekijken














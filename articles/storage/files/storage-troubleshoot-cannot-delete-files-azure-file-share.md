---
title: 'Azure-bestandsshare: verwijderen van bestanden uit de Azure-bestandsshare is mislukt'
description: Identificeer en los het niet verwijderen van bestanden uit Azure File Share op en los deze op.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196486"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Azure-bestandsshare: verwijderen van bestanden uit de Azure-bestandsshare is mislukt

Het niet verwijderen van bestanden uit Azure File Share kan verschillende symptomen hebben:

**Symptoom 1:**

Kan een bestand in azure-bestandsshare niet verwijderen vanwege een van de twee onderstaande problemen:

* Het bestand dat is gemarkeerd voor verwijderen
* De opgegeven resource kan worden gebruikt door een SMB-client

**Symptoom 2:**

Er is onvoldoende quotum beschikbaar om deze opdracht te verwerken

## <a name="cause"></a>Oorzaak

Fout 1816 treedt op wanneer u de bovengrens bereikt van gelijktijdige open handgrepen die zijn toegestaan voor een bestand, op de computer waar de bestandsshare wordt gemonteerd. Zie de checklist [voor prestaties en schaalbaarheid azure storage](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist)voor meer informatie.

## <a name="resolution"></a>Oplossing

Verminder het aantal gelijktijdige open handgrepen door sommige handgrepen te sluiten.

## <a name="prerequisite"></a>Vereiste

### <a name="install-the-latest-azure-powershell-module"></a>De nieuwste Azure PowerShell-module installeren

* [De Azure PowerShell-module installeren](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Verbinding maken met Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selecteer het abonnement op het doelopslagaccount:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Context maken voor het doelopslagaccount:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>De huidige geopende handgrepen van de bestandsshare opvragen:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Voorbeeldresultaat:

|HandleId|Pad|ClientIp|ClientPort|OpenTime|LastReconnectTime|FileId|ParentId (ParentId)|Sessionid|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nieuwe map/test.zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Sluit een open handgreep:

Als u een open handgreep wilt sluiten, gebruikt u de volgende opdracht:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Volgende stappen

* [Azure-bestanden in Windows oplossen](storage-troubleshoot-windows-file-connection-problems.md)
* [Azure-bestanden in Linux oplossen](storage-troubleshoot-linux-file-connection-problems.md)
* [Problemen met Azure Files Sync oplossen](storage-sync-files-troubleshoot.md)
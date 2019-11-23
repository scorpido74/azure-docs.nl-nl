---
title: bestand opnemen
description: bestand opnemen
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 07/08/2018
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 07cae1cee9810646de5bf9610a29991376736373
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72391616"
---
Deze fout kan optreden wanneer de Azure File Sync-Service niet toegankelijk is vanaf de server. U kunt deze fout oplossen door de volgende stappen te door lopen:

1. Controleer of de Windows-service `FileSyncSvc.exe` niet is geblokkeerd door uw firewall.
2. Controleer of poort 443 is geopend voor uitgaande verbindingen met de Azure File Sync-Service. U kunt dit doen met de cmdlet `Test-NetConnection`. De URL voor de tijdelijke aanduiding `<azure-file-sync-endpoint>` hieronder vindt u in het [Azure file sync proxy-en Firewall instellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) document. 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Zorg ervoor dat de proxy configuratie is ingesteld als verwacht. U kunt dit doen met de cmdlet `Get-StorageSyncProxyConfiguration`. Meer informatie over het configureren van de proxy configuratie voor Azure File Sync vindt u in de [Azure file sync proxy-en Firewall instellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall).

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
    
4. Neem contact op met de netwerk beheerder voor aanvullende hulp bij het oplossen van problemen met de netwerk verbinding.
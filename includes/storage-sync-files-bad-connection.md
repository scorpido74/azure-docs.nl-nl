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
ms.openlocfilehash: b2ff542d2782293e89b66e5d25cb67a9bcde6da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "75772908"
---
Deze fout kan optreden wanneer de Azure File Sync-service niet toegankelijk is vanaf de server. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. Controleer of `FileSyncSvc.exe` de Windows-service niet wordt geblokkeerd door uw firewall.
2. Controleer of poort 443 open staat voor uitgaande verbindingen met de Azure File Sync-service. U dit `Test-NetConnection` doen met de cmdlet. De URL voor de onderstaande tijdelijke aanduiding `<azure-file-sync-endpoint>` vindt u in het document [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Zorg ervoor dat de proxyconfiguratie is ingesteld zoals verwacht. U kunt dit doen met de cmdlet `Get-StorageSyncProxyConfiguration`. In [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) vindt u meer informatie over het configureren van de proxyconfiguratie voor Azure File Sync.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Gebruik de cmdlet Test-StorageSyncNetworkConnectivity om de netwerkverbinding met de serviceeindpunten te controleren. Zie [Netwerkconnectiviteit testen met serviceeindpunten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)voor meer informatie.    

5. Neem contact op met uw netwerkbeheerder voor extra hulp bij het oplossen van netwerkconnectiviteit.

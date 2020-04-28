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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75772908"
---
Deze fout kan optreden wanneer de Azure File Sync-service niet toegankelijk is vanaf de server. U kunt deze fout oplossen door de volgende stappen te doorlopen:

1. Controleer of de Windows `FileSyncSvc.exe` -service niet is geblokkeerd door uw firewall.
2. Controleer of poort 443 is geopend voor uitgaande verbindingen met de Azure File Sync-Service. U kunt dit doen met de `Test-NetConnection` cmdlet. De URL voor de onderstaande tijdelijke aanduiding `<azure-file-sync-endpoint>` vindt u in het document [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall). 

    ```powershell
    Test-NetConnection -ComputerName <azure-file-sync-endpoint> -Port 443
    ```

3. Zorg ervoor dat de proxyconfiguratie is ingesteld zoals verwacht. U kunt dit doen met de cmdlet `Get-StorageSyncProxyConfiguration`. In [Azure File Sync-proxy en -firewallinstellingen](../articles/storage/files/storage-sync-files-firewall-and-proxy.md#firewall) vindt u meer informatie over het configureren van de proxyconfiguratie voor Azure File Sync.

    ```powershell
    $agentPath = "C:\Program Files\Azure\StorageSyncAgent"
    Import-Module "$agentPath\StorageSync.Management.ServerCmdlets.dll"
    Get-StorageSyncProxyConfiguration
    ```
4. Gebruik de cmdlet test-StorageSyncNetworkConnectivity om de netwerk verbinding met de service-eind punten te controleren. Zie [netwerk connectiviteit testen op service-eind punten](https://docs.microsoft.com/azure/storage/files/storage-sync-files-firewall-and-proxy#test-network-connectivity-to-service-endpoints)voor meer informatie.    

5. Neem contact op met de netwerk beheerder voor aanvullende hulp bij het oplossen van problemen met de netwerk verbinding.

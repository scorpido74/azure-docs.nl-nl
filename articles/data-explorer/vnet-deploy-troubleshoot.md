---
title: Problemen met de toegang, opname en werking van uw Azure Data Explorer-cluster in uw virtuele netwerk oplossen
description: Problemen met de connectiviteit, opname, clustercreatie en -werking van uw Azure Data Explorer-cluster in uw virtuele netwerk oplossen
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241658"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Problemen met de toegang, opname en werking van uw Azure Data Explorer-cluster in uw virtuele netwerk oplossen

In deze sectie leert u hoe u problemen met het oplossen van connectiviteit, operationele en clustercreatie oplost voor een cluster dat is geïmplementeerd in uw [virtuele netwerk.](/azure/virtual-network/virtual-networks-overview)

## <a name="access-issues"></a>Toegangsproblemen

Als u een probleem hebt tijdens het openen van het cluster met behulp van het openbare (cluster.region.kusto.windows.net) of privé (private-cluster.region.kusto.windows.net) eindpunt en u vermoedt dat het verband houdt met de installatie van virtuele netwerken, voert u de volgende stappen uit om problemen op te lossen.

### <a name="check-tcp-connectivity"></a>TCP-connectiviteit controleren

De eerste stap omvat het controleren van TCP-connectiviteit met Windows of Linux OS.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Download [TCping](https://www.elifulkerson.com/projects/tcping.php) naar de machine die verbinding maakt met het cluster.
   2. Ping de bestemming vanaf de bronmachine met de volgende opdracht:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. *Netcat* installeren in de machine die verbinding maakt met het cluster

    ```bash
    $ apt-get install netcat
     ```

   2. Ping de bestemming vanaf de bronmachine met de volgende opdracht:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Als de test niet succesvol is, gaat u verder met de volgende stappen. Als de test is geslaagd, is het probleem niet te wijten aan een probleem met de TCP-connectiviteit. Ga naar [operationele problemen](#cluster-creation-and-operations-issues) om verder op te lossen.

### <a name="check-the-network-security-group-nsg"></a>Controleer de Network Security Group (NSG)

   Controleer of de [Network Security Group](/azure/virtual-network/security-overview) (NSG) die is gekoppeld aan het subnet van het cluster, een inkomende regel heeft die toegang biedt vanaf het IP-adres van de clientmachine voor poort 443.

### <a name="check-route-table"></a>Routetabel controleren

   Als het subnet van het cluster een force-tunneling-setup heeft om te firewallen (subnet met een [routetabel](/azure/virtual-network/virtual-networks-udr-overview) met de standaardroute '0.0.0.0/0'), moet u ervoor zorgen dat het IP-adres van de machine een route heeft met [het volgende hoptype](/azure/virtual-network/virtual-networks-udr-overview) naar VirtualNetwork/Internet. Deze route is nodig om asymmetrische routeproblemen te voorkomen.

## <a name="ingestion-issues"></a>Opnameproblemen

Als u problemen ondervindt met inname en u vermoedt dat dit verband houdt met de installatie van virtuele netwerken, voert u de volgende stappen uit.

### <a name="check-ingestion-health"></a>Innamestatus controleren

Controleer of de [statistieken voor de inname van het cluster](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) een gezonde status aangeven.

### <a name="check-security-rules-on-data-source-resources"></a>Beveiligingsregels controleren op gegevensbronbronnen

Als uit de statistieken blijkt dat er geen gebeurtenissen zijn verwerkt vanuit de gegevensbron *(Gebeurtenis metriek verwerkt* voor gebeurtenis/IoT-hubs), moet u ervoor zorgen dat de gegevensbronbronnen (gebeurtenishub of opslag) toegang verlenen vanaf het subnet van het cluster in de firewallregels of serviceeindpunten.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Beveiligingsregels controleren die zijn geconfigureerd op het subnet van het cluster

Zorg ervoor dat het subnet van het cluster NSG-, UDR- en firewallregels correct is geconfigureerd. Test bovendien de netwerkconnectiviteit voor alle afhankelijke eindpunten. 

## <a name="cluster-creation-and-operations-issues"></a>Problemen met het maken en uitvoeren van clusteren

Als u problemen ondervindt bij het maken of uitvoeren van groepen en u vermoedt dat dit verband houdt met het instellen van virtuele netwerken, voert u de volgende stappen uit om het probleem op te lossen.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnose van het virtuele netwerk met de REST API

De [ARMClient](https://chocolatey.org/packages/ARMClient) wordt gebruikt om de REST API aan te roepen met PowerShell. 

1. Inloggen met ARMClient

   ```powerShell
   armclient login
   ```

1. Diagnosebewerking aanroepen

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Controleer het antwoord

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Wachten op voltooiing van de bewerking

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Wacht tot de *eigenschap status* *Voltooid*weergeeft, dan moet *het* eigenschappenveld worden weergegeven:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Als de eigenschap *Bevindingen* een leeg resultaat weergeeft, betekent dit dat alle netwerktests zijn geslaagd en dat er geen verbindingen zijn verbroken. Als de volgende fout wordt weergegeven, is *uitgaande afhankelijkheid '{dependencyName}:{port}' mogelijk niet tevreden (Uitgaand),* kan het cluster de afhankelijke serviceeindpunten niet bereiken. Ga verder met de volgende stappen.

### <a name="check-network-security-group-nsg"></a>Netwerkbeveiligingsgroep controleren (NSG)

Controleer of de [netwerkbeveiligingsgroep](/azure/virtual-network/security-overview) correct is geconfigureerd volgens de instructies in [Afhankelijkheden voor VNet-implementatie](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Routetabel controleren

Als het subnet van het cluster force-tunneling heeft ingesteld op firewall (subnet met een [routetabel](/azure/virtual-network/virtual-networks-udr-overview) met de standaardroute '0.0.0.0/0') zorg ervoor dat de [IP-adressen](vnet-deployment.md#azure-data-explorer-management-ip-addresses)van het beheer) en [de IP-adressen voor statusbewaking](vnet-deployment.md#health-monitoring-addresses) een route hebben met [het volgende hoptype](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*en [bronadresvoorvoeging](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) naar *'management-ip/32'* en *'health-monitoring-ip/32'.* Deze route is nodig om asymmetrische routeproblemen te voorkomen.

### <a name="check-firewall-rules"></a>Firewallregels controleren

Als u tunnelsubnet uitgaand verkeer naar een firewall forceert, moet u ervoor zorgen dat alle afhankelijkheden FQDN (bijvoorbeeld *.blob.core.windows.net)* zijn toegestaan in de firewallconfiguratie zoals beschreven bij [het beveiligen van uitgaand verkeer met firewall.](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall)

---
title: Azure Data Explorer implementeren in uw Virtual Network (preview-versie)
description: Meer informatie over het implementeren van Azure Data Explorer in uw Virtual Network
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: e845b44c51b7611cd3f23f8b33e6576aced2d6ca
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851449"
---
# <a name="deploy-azure-data-explorer-into-your-virtual-network-preview"></a>Azure Data Explorer implementeren in uw Virtual Network (preview-versie)

In dit artikel worden de resources beschreven die aanwezig zijn wanneer u een Azure Data Explorer-cluster implementeert in een aangepaste Azure-Virtual Network. Deze informatie helpt u bij het implementeren van een cluster in een subnet in uw Virtual Network (VNet). Zie [Wat is azure Virtual Network?](/azure/virtual-network/virtual-networks-overview) voor meer informatie over virtuele Azure-netwerken.

   ![vnet-diagram](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer ondersteunt de implementatie van een cluster in een subnet in uw Virtual Network (VNet). Met deze mogelijkheid kunt u:

* NSG-regels ( [netwerk beveiligings groep](/azure/virtual-network/security-overview) ) afdwingen op uw Azure Data Explorer-cluster verkeer.
* Verbind uw on-premises netwerk met het subnet van Azure Data Explorer cluster.
* Beveilig uw gegevens verbindings bronnen ([Event hub](/azure/event-hubs/event-hubs-about) en [Event grid](/azure/event-grid/overview)) met [service-eind punten](/azure/virtual-network/virtual-network-service-endpoints-overview).

> [!NOTE]
> De Virtual Network-integratie en-implementatie bevindt zich in de preview-modus. Als u deze functie wilt inschakelen, opent u een [ondersteunings ticket](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Toegang tot uw Azure Data Explorer-cluster in uw VNet

U hebt toegang tot uw Azure Data Explorer-cluster met behulp van de volgende IP-adressen voor elke service (Engine-en gegevens beheer Services):

* **Privé-IP**: wordt gebruikt voor toegang tot het cluster in het VNet.
* **Openbaar IP-** adres dat wordt gebruikt om toegang te krijgen tot het cluster van buiten het VNet voor beheer en bewaking, en als bron adressen voor uitgaande verbindingen die vanuit het cluster worden gestart.

De volgende DNS-records worden gemaakt voor toegang tot de service: 

* `[clustername].[geo-region].kusto.windows.net` (Engine) `ingest-[clustername].[geo-region].kusto.windows.net` (gegevens beheer) worden toegewezen aan het open bare IP-adres voor elke service. 

* `private-[clustername].[geo-region].kusto.windows.net` (Engine) `private-ingest-[clustername].[geo-region].kusto.windows.net` (gegevens beheer) worden toegewezen aan het privé-IP-adres voor elke service.

## <a name="plan-subnet-size-in-your-vnet"></a>De grootte van het subnet plannen in uw VNet

De grootte van het subnet dat wordt gebruikt voor het hosten van een Azure Data Explorer-cluster kan niet worden gewijzigd nadat het subnet is geïmplementeerd. In uw VNet gebruikt Azure Data Explorer één privé-IP-adres voor elke virtuele machine en twee privé IP-adressen voor de interne load balancers (Engine en gegevens beheer). Azure-netwerken gebruiken ook vijf IP-adressen voor elk subnet. Azure Data Explorer voorziet in twee virtuele machines voor de gegevens beheer service. Machine service-Vm's worden ingericht per schaal capaciteit van gebruikers configuratie.

Het totale aantal IP-adressen:

| Gebruiken | Aantal adressen |
| --- | --- |
| Engine-service | 1 per instantie |
| Gegevens beheer service | 2 |
| Interne load balancers | 2 |
| Gereserveerde adressen van Azure | 5 |
| **Eind** | **#engine_instances + 9** |

> [!IMPORTANT]
> De grootte van het subnet moet vooraf worden gepland, omdat deze niet kan worden gewijzigd nadat Azure Data Explorer is geïmplementeerd. Reserveer daarom de benodigde subnet grootte dienovereenkomstig.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Service-eind punten voor het maken van verbinding met Azure Data Explorer

Met [Azure service-eind punten](/azure/virtual-network/virtual-network-service-endpoints-overview) kunt u uw Azure multi tenant-resources beveiligen naar uw virtuele netwerk.
Als u Azure Data Explorer cluster in uw subnet implementeert, kunt u gegevens verbindingen met [Event hub](/azure/event-hubs/event-hubs-about) of [Event grid](/azure/event-grid/overview) instellen tijdens het beperken van de onderliggende resources voor Azure Data Explorer subnet.

> [!NOTE]
> Wanneer u EventGrid-instellingen gebruikt met [opslag](/azure/storage/common/storage-introduction) en [Event hub], kan het opslag account dat in het abonnement wordt gebruikt, worden vergrendeld met Service-eind punten in het subnet van Azure Data Explorer terwijl er vertrouwde Azure-platform services worden toegestaan in de [firewall configuratie](/azure/storage/common/storage-network-security), maar de Event hub service-eind punt niet kan inschakelen omdat het geen ondersteuning biedt voor vertrouwde services van het [Azure-platform](/azure/event-hubs/event-hubs-service-endpoints).

## <a name="dependencies-for-vnet-deployment"></a>Afhankelijkheden voor VNet-implementatie

### <a name="network-security-groups-configuration"></a>Configuratie van netwerk beveiligings groepen

[Netwerk beveiligings groepen (NSG)](/azure/virtual-network/security-overview) bieden de mogelijkheid om netwerk toegang binnen een VNet te beheren. Azure Data Explorer kan worden geopend met twee eind punten: HTTPs (443) en TDS (1433). De volgende NSG-regels moeten worden geconfigureerd voor toegang tot deze eind punten voor beheer, bewaking en goede werking van uw cluster.

#### <a name="inbound-nsg-configuration"></a>Configuratie van binnenkomende NSG

| **Gebruiken**   | **Van**   | **Aan**   | **Protocol**   |
| --- | --- | --- | --- |
| Beheer  |[ADX beheer adressen](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement (ServiceTag) | ADX-subnet: 443  | TCP  |
| Statuscontrole  | [Adressen voor status controle ADX](#health-monitoring-addresses)  | ADX-subnet: 443  | TCP  |
| ADX interne communicatie  | ADX-subnet: alle poorten  | ADX-subnet: alle poorten  | Alle  |
| Azure load balancer-inkomend (Health probe) toestaan  | AzureLoadBalancer  | ADX-subnet: 80443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Configuratie van uitgaande NSG

| **Gebruiken**   | **Van**   | **Aan**   | **Protocol**   |
| --- | --- | --- | --- |
| Afhankelijkheid van Azure Storage  | ADX-subnet  | Opslag: 443  | TCP  |
| Afhankelijkheid van Azure Data Lake  | ADX-subnet  | AzureDataLake: 443  | TCP  |
| Opname van EventHub en service bewaking  | ADX-subnet  | EventHub: 443, 5671  | TCP  |
| Metrische gegevens publiceren  | ADX-subnet  | AzureMonitor: 443 | TCP  |
| Azure Monitor configuratie downloaden  | ADX-subnet  | [Azure monitor configuratie-eindpunt adressen](#azure-monitor-configuration-endpoint-addresses): 443 | TCP  |
| Active Directory (indien van toepassing) | ADX-subnet | AzureActiveDirectory: 443 | TCP |
| Certificeringsinstantie | ADX-subnet | Internet: 80 | TCP |
| Interne communicatie  | ADX-subnet  | ADX-subnet: alle poorten  | Alle  |
| Poorten die worden gebruikt voor `sql\_request`-en `http\_request`-invoeg toepassingen  | ADX-subnet  | Internet: aangepast  | TCP  |

### <a name="relevant-ip-addresses"></a>Relevante IP-adressen

#### <a name="azure-data-explorer-management-ip-addresses"></a>IP-adressen van Azure Data Explorer Management

| Regio | Komen |
| --- | --- |
| Australië - centraal | 20.37.26.134 |
| Australië-Central2 | 20.39.99.177 |
| Australië - oost | 40.82.217.84 |
| Australië - zuidoost | 20.40.161.39 |
| BrazilSouth | 191.233.25.183 |
| Canada - midden | 40.82.188.208 |
| Canada - oost | 40.80.255.12 |
| India - centraal | 40.81.249.251 |
| VS - centraal | 40.67.188.68 |
| Centrale VS-EUAP | 40.89.56.69 |
| Azië - oost | 20.189.74.103 |
| VS - oost | 52.224.146.56 |
| VS - oost2 | 52.232.230.201 |
| Oost-VS2 EUAP | 52.253.226.110 |
| Frankrijk - centraal | 40.66.57.91 |
| Frankrijk - zuid | 40.82.236.24 |
| Japan - oost | 20.43.89.90 |
| Japan - west | 40.81.184.86 |
| Korea - centraal | 40.82.156.149 |
| Korea - zuid | 40.80.234.9 |
| VS - noord-centraal | 40.81.45.254 |
| Europa - noord | 52.142.91.221 |
| Zuid-Afrika - noord | 102.133.129.138 |
| Zuid-Afrika - west | 102.133.0.97 |
| VS - zuid-centraal | 20.45.3.60 |
| Azië - zuidoost | 40.119.203.252 |
| India - zuid | 40.81.72.110 |
| Verenigd Koninkrijk Zuid | 40.81.154.254 |
| Verenigd Koninkrijk West | 40.81.122.39 |
| VS - west-centraal | 52.159.55.120 |
| Europa -west | 51.145.176.215 |
| India - west | 40.81.88.112 |
| VS - west | 13.64.38.225 |
| VS - west 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adressen voor status controle

| Regio | Komen |
| --- | --- |
| Australië - centraal | 191.239.64.128 |
| Australië - centraal 2 | 191.239.64.128 |
| Australië - oost | 191.239.64.128 |
| Australië - zuidoost | 191.239.160.47 |
| Brazilië - zuid | 23.98.145.105 |
| Canada - midden | 168.61.212.201 |
| Canada - oost | 168.61.212.201 |
| India - centraal | 23.99.5.162 |
| VS - centraal | 168.61.212.201 |
| Centrale VS-EUAP | 168.61.212.201 |
| Azië - oost | 168.63.212.33 |
| VS - oost | 137.116.81.189 |
| VS - oost 2 | 137.116.81.189 |
| VS-Oost 2 EUAP | 137.116.81.189 |
| Frankrijk - centraal | 23.97.212.5 |
| Frankrijk - zuid | 23.97.212.5 |
| Japan - oost | 138.91.19.129 |
| Japan - west | 138.91.19.129 |
| Korea - centraal | 138.91.19.129 |
| Korea - zuid | 138.91.19.129 |
| VS - noord-centraal | 23.96.212.108 |
| Europa - noord | 191.235.212.69 
| Zuid-Afrika - noord | 104.211.224.189 |
| Zuid-Afrika - west | 104.211.224.189 |
| VS - zuid-centraal | 23.98.145.105 |
| India - zuid | 23.99.5.162 |
| Azië - zuidoost | 168.63.173.234 |
| Verenigd Koninkrijk Zuid | 23.97.212.5 |
| Verenigd Koninkrijk West | 23.97.212.5 |
| VS - west-centraal | 168.61.212.201 |
| Europa -west | 23.97.212.5 |
| India - west | 23.99.5.162 |
| VS - west | 23.99.5.162 |
| VS - west 2 | 23.99.5.162 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Azure Monitor configuratie-eindpunt adressen

| Regio | Komen |
| --- | --- |
| Australië - centraal | 52.148.86.165 |
| Australië - centraal 2 | 52.148.86.165 |
| Australië - oost | 52.148.86.165 |
| Australië - zuidoost | 52.148.86.165 |
| Brazilië - zuid | 13.68.89.19 |
| Canada - centraal | 13.90.43.231 |
| Canada - oost | 13.90.43.231 |
| Centraal-India | 13.71.25.187 |
| VS-Centraal | 52.173.95.68 |
| Centrale VS-EUAP | 13.90.43.231 |
| Azië - oost | 13.75.117.221 |
| VS-Oost | 13.90.43.231 |
| VS-Oost 2 | 13.68.89.19 |    
| VS-Oost 2 EUAP | 13.68.89.19 |
| Frankrijk - centraal | 52.174.4.112 |
| Frankrijk - zuid | 52.174.4.112 |
| Japan-Oost | 13.75.117.221 |
| Japan-West | 13.75.117.221 |
| Korea - centraal | 13.75.117.221 |
| Korea - zuid | 13.75.117.221 |
| VS Noord-Centraal | 52.162.240.236 |
| Europa - noord | 52.169.237.246 |
| Zuid-Afrika - noord | 13.71.25.187 |
| Zuid-Afrika - west | 13.71.25.187 |
| VS Zuid-Centraal | 13.84.173.99 |
| India - zuid | 13.71.25.187 |
| Zuidoost-Azië | 52.148.86.165 |
| UK - zuid | 52.174.4.112 |
| UK - west | 52.169.237.246 |
| VS - west-centraal | 52.161.31.69 |
| Europa - west | 52.174.4.112 |
| India-West | 13.71.25.187 |
| VS-West | 40.78.70.148 |
| VS-West 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute-installatie

Gebruik ExpressRoute om verbinding te maken met het on-premises netwerk met Azure Virtual Network. Een gemeen schappelijke installatie is het adverteren van de standaard route (0.0.0.0/0) via de Border Gateway Protocol (BGP)-sessie. Dit zorgt ervoor dat verkeer dat afkomstig is van de Virtual Network wordt doorgestuurd naar het lokale netwerk van de klant, waardoor het verkeer kan worden verwijderd, waardoor uitgaande stromen kunnen worden verbroken. Om deze standaard waarde te overwinnen, kan de door de [gebruiker gedefinieerde route (UDR)](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) worden geconfigureerd en zal de volgende hop *Internet*zijn. Omdat de UDR prioriteit heeft boven BGP, is het verkeer bestemd voor het internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Uitgaand verkeer beveiligen met firewall

Als u uitgaand verkeer wilt beveiligen met [Azure firewall](/azure/firewall/overview) of een virtueel apparaat om domein namen te beperken, moet u de volgende FQDN-namen (Fully Qualified Domain names) in de Firewall toestaan.

```
prod.warmpath.msftcloudes.com:443
production.diagnostics.monitoring.core.windows.net:443
graph.windows.net:443
*.update.microsoft.com:443
shavamanifestcdnprod1.azureedge.net:443
login.live.com:443
wdcp.microsoft.com:443
login.microsoftonline.com:443
azureprofilerfrontdoor.cloudapp.net:443
*.core.windows.net:443
*.servicebus.windows.net:443
shoebox2.metrics.nsatc.net:443
prod-dsts.dsts.core.windows.net:443
ocsp.msocsp.com:80
*.windowsupdate.com:80
ocsp.digicert.com:80
go.microsoft.com:80
dmd.metaservices.microsoft.com:80
www.msftconnecttest.com:80
crl.microsoft.com:80
www.microsoft.com:80
adl.windows.com:80
crl3.digicert.com:80
```

U moet ook de [route tabel](/azure/virtual-network/virtual-networks-udr-overview) op het subnet definiëren met de [beheer adressen](#azure-data-explorer-management-ip-addresses) en [status controle adressen](#health-monitoring-addresses) met de volgende hop- *Internet* om problemen met asymmetrische routes te voor komen.

Voor de regio **VS-West** moet u bijvoorbeeld de volgende udr's definiëren:

| Naam | Adresvoorvoegsel | Volgende hop |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Data Explorer-cluster implementeren in uw VNet met behulp van een Azure Resource Manager sjabloon

Als u Azure Data Explorer cluster wilt implementeren in uw virtuele netwerk, gebruikt u het [azure Data Explorer-cluster implementeren in uw VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) -Azure Resource Manager sjabloon.

Met deze sjabloon maakt u het cluster, het virtuele netwerk, het subnet, de netwerk beveiligings groep en de open bare IP-adressen.

## <a name="troubleshooting"></a>Problemen oplossen

In deze sectie vindt u informatie over het oplossen van problemen met connectiviteit, operationele en het maken van clusters voor een cluster dat is geïmplementeerd in uw [Virtual Network](/azure/virtual-network/virtual-networks-overview).

### <a name="access-issues"></a>Toegangs problemen

Als u een probleem hebt bij het openen van het cluster met behulp van het open bare (cluster.region.kusto.windows.net) of particuliere (private-cluster.region.kusto.windows.net)-eind punt en u vermoedt dat dit is gerelateerd aan de installatie van het virtuele netwerk, voert u de volgende stappen uit om Los het probleem op.

#### <a name="check-tcp-connectivity"></a>TCP-connectiviteit controleren

De eerste stap omvat het controleren van TCP-verbindingen met behulp van Windows-of Linux-besturings systeem.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Down load [TCping](https://www.elifulkerson.com/projects/tcping.php) naar de computer die verbinding maakt met het cluster.
   2. Ping het doel van de bron machine met behulp van de volgende opdracht:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. *Netcat* installeren op de computer die verbinding maakt met het cluster

    ```bash
    $ apt-get install netcat
     ```

   2. Ping het doel van de bron machine met behulp van de volgende opdracht:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Als de test niet is geslaagd, gaat u verder met de volgende stappen. Als de test is geslaagd, wordt het probleem niet veroorzaakt door een probleem met de TCP-verbinding. Ga naar [operationele problemen](#cluster-creation-and-operations-issues) om verder te kunnen problemen oplossen.

#### <a name="check-the-network-security-group-nsg"></a>Controleer de netwerk beveiligings groep (NSG)

   Controleer of de [netwerk beveiligings groep](/azure/virtual-network/security-overview) (NSG) die is gekoppeld aan het subnet van het cluster, een regel voor binnenkomend verkeer heeft waarmee toegang vanaf het IP-adres van de client computer wordt toegestaan voor poort 443.

#### <a name="check-route-table"></a>Route tabel controleren

   Als het subnet van het cluster geforceerde tunneling heeft ingesteld op Firewall (subnet met een [route tabel](/azure/virtual-network/virtual-networks-udr-overview) met de standaard route 0.0.0.0/0), moet u ervoor zorgen dat het IP-adres van de computer een route met het [type volgende hop](/azure/virtual-network/virtual-networks-udr-overview) naar VirtualNetwork/Internet heeft. Dit is vereist om problemen met asymmetrische routes te voor komen.

### <a name="ingestion-issues"></a>Opname problemen

Als u problemen ondervindt met de opname en u vermoedt dat deze zijn gerelateerd aan de installatie van het virtuele netwerk, voert u de volgende stappen uit.

#### <a name="check-ingestion-health"></a>Status van opname controleren

    Check that the [cluster ingestion metrics](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) indicate a healthy state.

#### <a name="check-security-rules-on-data-source-resources"></a>Controleer de beveiligings regels op de gegevens bron resources

Als bij de metrische gegevens wordt aangegeven dat er geen gebeurtenissen zijn verwerkt vanuit de data source (*verwerkte gebeurtenissen* (voor gebeurtenis/IOT hubs), moet u ervoor zorgen dat de gegevens bron bronnen (Event hub of Storage) toegang toestaan vanuit het subnet van het cluster in de firewall regels of service-eind punten.

#### <a name="check-security-rules-configured-on-clusters-subnet"></a>Controleer de beveiligings regels die zijn geconfigureerd op het subnet van het cluster

Zorg ervoor dat het subnet van het cluster NSG, UDR en firewall regels correct zijn geconfigureerd. Test bovendien de netwerk connectiviteit voor alle afhankelijke eind punten. 

### <a name="cluster-creation-and-operations-issues"></a>Problemen met het maken van clusters en bewerkingen

Voer de volgende stappen uit om het probleem op te lossen als u problemen ondervindt met het maken van clusters of als u vermoedt dat deze zijn gerelateerd aan de configuratie van het virtuele netwerk.

#### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Het virtuele netwerk met de REST API diagnosticeren

De [ARMClient](https://chocolatey.org/packages/ARMClient) wordt gebruikt om de rest API aan te roepen met behulp van Power shell. 

1. Aanmelden met ARMClient

   ```powerShell
   armclient login
   ```

1. Diagnose bewerking aanroepen

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Het antwoord controleren

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Wachten tot de bewerking is voltooid

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
    
   Wacht tot de *status* eigenschap *is voltooid*. vervolgens wordt het veld *Eigenschappen* weer gegeven:

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

Als voor de eigenschap *bevindingen* een leeg resultaat wordt weer gegeven, betekent dit dat alle netwerk tests zijn door gegeven en dat er geen verbindingen worden verbroken. Als de volgende fout wordt weer gegeven: de *uitgaande afhankelijkheid {dependency naam}: {Port} is mogelijk niet voldaan aan (uitgaand)* , het cluster kan de afhankelijke service-eind punten niet bereiken. Ga door met de volgende stappen om problemen op te lossen.

#### <a name="check-network-security-group-nsg"></a>Controleer de netwerk beveiligings groep (NSG)

Controleer of de [netwerk beveiligings groep](/azure/virtual-network/security-overview) juist is geconfigureerd volgens de instructies in [afhankelijkheden voor VNet-implementaties](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

#### <a name="check-route-table"></a>Route tabel controleren

Als voor het subnet van het cluster geforceerde tunneling is ingesteld op Firewall (subnet met [een route tabel](/azure/virtual-network/virtual-networks-udr-overview) met de standaard route ' 0.0.0.0/0 '), moet u ervoor zorgen dat de IP-adressen van de [beheer-IP-adressen](#azure-data-explorer-management-ip-addresses) en de [status controle](#health-monitoring-addresses) worden weer geven met het [volgende hop-type](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*en het [adres voorvoegsel](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) *voor de* *status controle-IP/32*. Dit is vereist om problemen met asymmetrische routes te voor komen.

#### <a name="check-firewall-rules"></a>Firewall regels controleren

Als u uitgaand verkeer van het tunnel-subnet afdwingt naar een firewall, moet u ervoor zorgen dat alle afhankelijkheden van de FQDN-(bijvoorbeeld *. blob.core.Windows.net*) zijn toegestaan in de firewall configuratie, zoals beschreven in het [beveiligen van uitgaand verkeer met firewall](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
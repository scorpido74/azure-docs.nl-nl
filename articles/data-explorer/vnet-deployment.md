---
title: Azure Data Explorer implementeren in uw virtuele netwerk
description: Meer informatie over het implementeren van Azure Data Explorer in uw virtuele netwerk
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/31/2019
ms.openlocfilehash: b0530ddada68cc9d07753a3b8ab30bff642e26dd
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618665"
---
# <a name="deploy-azure-data-explorer-cluster-into-your-virtual-network"></a>Azure Data Explorer-cluster implementeren in uw virtuele netwerk

In dit artikel worden de resources uitgelegd die aanwezig zijn wanneer u een Azure Data Explorer-cluster implementeert in een aangepast Azure Virtual Network. Met deze informatie u een cluster implementeren in een subnet in uw Virtuele Netwerk (VNet). Zie Wat is Azure Virtual Network voor meer informatie over Azure Virtual [Networks?](/azure/virtual-network/virtual-networks-overview)

   ![vnet-diagram](media/vnet-deployment/vnet-diagram.png)

Azure Data Explorer ondersteunt het implementeren van een cluster in een subnet in uw Virtuele Netwerk (VNet). Met deze mogelijkheid u:

* [NSG-regels (Network Security Group)](/azure/virtual-network/security-overview) afdwingen voor uw Azure Data Explorer-clusterverkeer.
* Verbind uw on-premises netwerk met het subnet van het Azure Data Explorer-cluster.
* Beveilig uw gegevensverbindingsbronnen[(Gebeurtenishub](/azure/event-hubs/event-hubs-about) en [gebeurtenisraster)](/azure/event-grid/overview)met [serviceeindpunten](/azure/virtual-network/virtual-network-service-endpoints-overview).

## <a name="access-your-azure-data-explorer-cluster-in-your-vnet"></a>Toegang tot uw Azure Data Explorer-cluster in uw VNet

U hebt toegang tot uw Azure Data Explorer-cluster met de volgende IP-adressen voor elke service (engine- en datamanagementservices):

* **Privé-IP:** wordt gebruikt voor toegang tot het cluster in het VNet.
* **Openbaar IP:** gebruikt voor toegang tot het cluster van buiten het VNet voor beheer en monitoring en als bronadres voor uitgaande verbindingen die vanuit het cluster zijn gestart.

De volgende DNS-records worden gemaakt om toegang te krijgen tot de service: 

* `[clustername].[geo-region].kusto.windows.net`(motor) `ingest-[clustername].[geo-region].kusto.windows.net` (datamanagement) worden voor elke dienst toegewezen aan het openbare IP.(data management) are mapped to the public IP for each service. 

* `private-[clustername].[geo-region].kusto.windows.net`(motor) `private-ingest-[clustername].[geo-region].kusto.windows.net` (datamanagement) worden voor elke dienst toegewezen aan het privé-IP.

## <a name="plan-subnet-size-in-your-vnet"></a>Subnetgrootte plannen in uw VNet

De grootte van het subnet dat wordt gebruikt om een Azure Data Explorer-cluster te hosten, kan niet worden gewijzigd nadat het subnet is geïmplementeerd. In uw VNet gebruikt Azure Data Explorer één privé-IP-adres voor elke VM en twee privé-IP-adressen voor de interne load balancers (engine en datamanagement). Azure networking maakt ook gebruik van vijf IP-adressen voor elk subnet. Azure Data Explorer voorziet in twee VM's voor de gegevensbeheerservice. Engine service VM's zijn ingericht per gebruiker configuratie schaal capaciteit.

Het totale aantal IP-adressen:

| Gebruiken | Aantal adressen |
| --- | --- |
| Motorservice | 1 per exemplaar |
| Gegevensbeheerservice | 2 |
| Interne load balancers | 2 |
| Gereserveerde Azure-adressen | 5 |
| **Totaal** | **#engine_instances + 9** |

> [!IMPORTANT]
> Subnetgrootte moet vooraf worden gepland, omdat deze niet kan worden gewijzigd nadat Azure Data Explorer is geïmplementeerd. Daarom had de reserve subnetgrootte dienovereenkomstig nodig.

## <a name="service-endpoints-for-connecting-to-azure-data-explorer"></a>Serviceeindpunten voor verbinding maken met Azure Data Explorer

[Azure Service Endpoints](/azure/virtual-network/virtual-network-service-endpoints-overview) stelt u in staat om uw Azure multi-tenant resources te beveiligen op uw virtuele netwerk.
Als u azure data explorer-cluster implementeert in uw subnet, u gegevensverbindingen instellen met [gebeurtenishub](/azure/event-hubs/event-hubs-about) of [gebeurtenisraster](/azure/event-grid/overview) terwijl u de onderliggende bronnen voor subnet Azure Data Explorer beperkt.

> [!NOTE]
> Wanneer u EventGrid-installatie met [opslag](/azure/storage/common/storage-introduction) en [Gebeurtenishub] gebruikt, kan het opslagaccount dat in het abonnement wordt gebruikt, worden vergrendeld met serviceeindpunten op het subnet van Azure Data Explorer, terwijl vertrouwde Azure-platformservices in de [firewallconfiguratie](/azure/storage/common/storage-network-security)worden toegestaan, maar de Gebeurtenishub kan Service Endpoint niet inschakelen omdat het geen ondersteuning biedt voor vertrouwde [Azure-platformservices.](/azure/event-hubs/event-hubs-service-endpoints)

## <a name="dependencies-for-vnet-deployment"></a>Afhankelijkheden voor VNet-implementatie

### <a name="network-security-groups-configuration"></a>Configuratie van netwerkbeveiligingsgroepen

[Network Security Groups (NSG)](/azure/virtual-network/security-overview) bieden de mogelijkheid om de toegang tot het netwerk binnen een VNet te beheren. Azure Data Explorer is toegankelijk via twee eindpunten: HTTP's (443) en TDS (1433). De volgende NSG-regels moeten zijn geconfigureerd om toegang te krijgen tot deze eindpunten voor beheer, bewaking en goede werking van uw cluster.

#### <a name="inbound-nsg-configuration"></a>Binnenkomende NSG-configuratie

| **Gebruiken**   | **Van**   | **Aan**   | **Protocol**   |
| --- | --- | --- | --- |
| Beheer  |[ADX-beheeradressen](#azure-data-explorer-management-ip-addresses)/AzureDataExplorerManagement(ServiceTag) | ADX-subnet:443  | TCP  |
| Statuscontrole  | [ADX-statuscontroleadressen](#health-monitoring-addresses)  | ADX-subnet:443  | TCP  |
| Interne communicatie van ADX  | ADX-subnet: Alle poorten  | ADX-subnet:Alle poorten  | Alle  |
| Azure load balancer binnenkomend toestaan (statussonde)  | AzureLoadBalancer  | ADX subnet:80,443  | TCP  |

#### <a name="outbound-nsg-configuration"></a>Uitgaande NSG-configuratie

| **Gebruiken**   | **Van**   | **Aan**   | **Protocol**   |
| --- | --- | --- | --- |
| Afhankelijkheid van Azure Storage  | ADX-subnet  | Opslag:443  | TCP  |
| Afhankelijkheid van Azure Data Lake  | ADX-subnet  | AzureDataLake:443  | TCP  |
| EventHub-opname en servicebewaking  | ADX-subnet  | EventHub: 443,5671  | TCP  |
| Statistieken publiceren  | ADX-subnet  | AzureMonitor:443 | TCP  |
| Upload van Azure Monitor-configuratie  | ADX-subnet  | [Eindpuntadressen configureren](#azure-monitor-configuration-endpoint-addresses)azure monitor :443 | TCP  |
| Active Directory (indien van toepassing) | ADX-subnet | AzureActiveDirectory:443 | TCP |
| Certificeringsinstantie | ADX-subnet | Internet:80 | TCP |
| Interne communicatie  | ADX-subnet  | ADX-subnet:Alle poorten  | Alle  |
| Poorten die worden `sql\_request` `http\_request` gebruikt voor en plug-ins  | ADX-subnet  | Internet:Aangepast  | TCP  |

### <a name="relevant-ip-addresses"></a>Relevante IP-adressen

#### <a name="azure-data-explorer-management-ip-addresses"></a>IP-adressen azure Data Explorer-beheer

| Regio | Adressen |
| --- | --- |
| Australië - centraal | 20.37.26.134 |
| Australië Centraal2 | 20.39.99.177 |
| Australië - oost | 40.82.217.84 |
| Australië - zuidoost | 20.40.161.39 |
| BraziliëZuid | 191.233.25.183 |
| Canada - midden | 40.82.188.208 |
| Canada - oost | 40.80.255.12 |
| India - centraal | 40.81.249.251, 104.211.98.159 |
| VS - centraal | 40.67.188.68 |
| Centrale EUAP van de VS | 40.89.56.69 |
| Azië - oost | 20.189.74.103 |
| VS - oost | 52.224.146.56 |
| VS - oost 2 | 52.232.230.201 |
| Oost-US2 EUAP | 52.253.226.110 |
| Frankrijk - centraal | 40.66.57.91 |
| Frankrijk - zuid | 40.82.236.24 |
| Japan - oost | 20.43.89.90 |
| Japan - west | 40.81.184.86 |
| Korea - centraal | 40.82.156.149 |
| Korea - zuid | 40.80.234.9 |
| VS - noord-centraal | 40.81.45.254 |
| Europa - noord | 52.142.91.221 |
| Zuid-Afrika Noord | 102.133.129.138 |
| Zuid-Afrika West | 102.133.0.97 |
| VS - zuid-centraal | 20.45.3.60 |
| Azië - zuidoost | 40.119.203.252 |
| India - zuid | 40.81.72.110, 104.211.224.189 |
| Verenigd Koninkrijk Zuid | 40.81.154.254 |
| Verenigd Koninkrijk West | 40.81.122.39 |
| VS - west-centraal | 52.159.55.120 |
| Europa -west | 51.145.176.215 |
| India - west | 40.81.88.112, 104.211.160.120 |
| VS - west | 13.64.38.225 |
| VS - west 2 | 40.90.219.23 |

#### <a name="health-monitoring-addresses"></a>Adressen voor statusbewaking

| Regio | Adressen |
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
| Centrale EUAP van de VS | 168.61.212.201 |
| Azië - oost | 168.63.212.33 |
| VS - oost | 137.116.81.189 |
| VS - oost 2 | 137.116.81.189 |
| Oost-VS 2 EUAP | 137.116.81.189 |
| Frankrijk - centraal | 23.97.212.5 |
| Frankrijk - zuid | 23.97.212.5 |
| Japan - oost | 138.91.19.129 |
| Japan - west | 138.91.19.129 |
| Korea - centraal | 138.91.19.129 |
| Korea - zuid | 138.91.19.129 |
| VS - noord-centraal | 23.96.212.108 |
| Europa - noord | 191.235.212.69 
| Zuid-Afrika Noord | 104.211.224.189 |
| Zuid-Afrika West | 104.211.224.189 |
| VS - zuid-centraal | 23.98.145.105 |
| India - zuid | 23.99.5.162 |
| Azië - zuidoost | 168.63.173.234 |
| Verenigd Koninkrijk Zuid | 23.97.212.5 |
| Verenigd Koninkrijk West | 23.97.212.5 |
| VS - west-centraal | 168.61.212.201 |
| Europa -west | 23.97.212.5 |
| India - west | 23.99.5.162 |
| VS - west | 23.99.5.162 |
| VS - west 2 | 23.99.5.162, 104.210.32.14 |    

#### <a name="azure-monitor-configuration-endpoint-addresses"></a>Eindpuntadressen voor azure-monitor-configuratie

| Regio | Adressen |
| --- | --- |
| Australië - centraal | 52.148.86.165 |
| Australië - centraal 2 | 52.148.86.165 |
| Australië - oost | 52.148.86.165 |
| Australië Zuidoost | 52.148.86.165 |
| Brazilië - zuid | 13.68.89.19 |
| Canada - midden | 13.90.43.231 |
| Canada - oost | 13.90.43.231 |
| India - centraal | 13.71.25.187 |
| VS - centraal | 52.173.95.68 |
| Centrale EUAP van de VS | 13.90.43.231 |
| Azië - oost | 13.75.117.221 |
| VS - oost | 13.90.43.231 |
| VS - oost 2 | 13.68.89.19 |    
| Oost-VS 2 EUAP | 13.68.89.19 |
| Frankrijk - centraal | 52.174.4.112 |
| Frankrijk - zuid | 52.174.4.112 |
| Japan - oost | 13.75.117.221 |
| Japan - west | 13.75.117.221 |
| Korea - centraal | 13.75.117.221 |
| Korea - zuid | 13.75.117.221 |
| VS - noord-centraal | 52.162.240.236 |
| Europa - noord | 52.169.237.246 |
| Zuid-Afrika Noord | 13.71.25.187 |
| Zuid-Afrika West | 13.71.25.187 |
| VS - zuid-centraal | 13.84.173.99 |
| India - zuid | 13.71.25.187 |
| Azië - zuidoost | 52.148.86.165 |
| Verenigd Koninkrijk Zuid | 52.174.4.112 |
| Verenigd Koninkrijk West | 52.169.237.246 |
| VS - west-centraal | 52.161.31.69 |
| Europa -west | 52.174.4.112 |
| India - west | 13.71.25.187 |
| West-VS | 40.78.70.148 |
| VS - west 2 | 52.151.20.103 |

## <a name="expressroute-setup"></a>ExpressRoute-installatie

Gebruik ExpressRoute om on-premises verbinding te maken met het Azure Virtual Network. Een veelgebruikte instelling is om de standaardroute (0.0.0.0/0) te adverteren via de BGP-sessie (Border Gateway Protocol). Dit dwingt verkeer dat uit het Virtuele Netwerk komt om naar het premissenetwerk van de klant te worden doorgestuurd dat het verkeer kan dalen, veroorzakend uitgaande stromen om te breken. Om deze standaardinstelling te overwinnen, kan UDR (0.0.0.0/0) van [Gebruiker Defined Route](/azure/virtual-network/virtual-networks-udr-overview#user-defined) (0.0.0.0/0) worden geconfigureerd en wordt de volgende hop *internet.* Aangezien de UDR voorrang heeft op BGP, zal het verkeer bestemd zijn voor het internet.

## <a name="securing-outbound-traffic-with-firewall"></a>Uitgaand verkeer beveiligen met firewall

Als u uitgaand verkeer wilt beveiligen met [Azure Firewall](/azure/firewall/overview) of een virtueel toestel om domeinnamen te beperken, moeten de volgende volledig gekwalificeerde domeinnamen (FQDN) in de firewall worden toegestaan.

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

U moet ook de [routetabel](/azure/virtual-network/virtual-networks-udr-overview) op het subnet definiëren met de [beheeradressen](#azure-data-explorer-management-ip-addresses) en [adressen voor statusbewaking](#health-monitoring-addresses) met het volgende *hopinternet* om asymmetrische routesproblemen te voorkomen.

Voor de regio **West-VS** moeten bijvoorbeeld de volgende UDR's worden gedefinieerd:

| Name | Adresvoorvoegsel | Volgende hop |
| --- | --- | --- |
| ADX_Management | 13.64.38.225/32 | Internet |
| ADX_Monitoring | 23.99.5.162/32 | Internet |

## <a name="deploy-azure-data-explorer-cluster-into-your-vnet-using-an-azure-resource-manager-template"></a>Azure Data Explorer-cluster implementeren in uw VNet met behulp van een Azure Resource Manager-sjabloon

Als u Azure Data Explorer-cluster wilt implementeren in uw virtuele netwerk, gebruikt u het [cluster Azure Data Explorer implementeren in uw VNet](https://azure.microsoft.com/resources/templates/101-kusto-vnet/) Azure Resource Manager-sjabloon.

Met deze sjabloon worden het cluster, het virtuele netwerk, het subnet, de netwerkbeveiligingsgroep en openbare IP-adressen gemaakt.

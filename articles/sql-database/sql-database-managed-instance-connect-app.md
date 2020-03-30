---
title: Beheerde instantie connect-toepassing
description: In dit artikel wordt besproken hoe u uw toepassing verbinden met Azure SQL Database Managed Instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 9f592c345b7cfcf5f21d816fde1fae6b8e6b98c7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73823389"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Uw toepassing verbinden met het beheerde exemplaar van Azure SQL Database

Vandaag heb je meerdere keuzes bij de beslissing hoe en waar u uw toepassing host.

U ervoor kiezen om toepassing in de cloud te hosten door Azure App Service te gebruiken of een aantal van de geïntegreerde opties voor het virtuele netwerk (VNet) van Azure, zoals Azure App Service Environment, Virtual Machine, Virtual Machine Scale Set. U ook een hybride cloudbenadering hanteren en uw toepassingen on-premises houden.

Welke keuze je ook hebt gemaakt, je het aansluiten op een beheerde instantie.  

![hoge beschikbaarheid](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Een toepassing aansluiten binnen dezelfde VNet

Dit scenario is het eenvoudigste. Virtuele machines in het VNet kunnen direct verbinding met elkaar maken, zelfs als ze zich in verschillende subnetten bevinden. Dat betekent dat u alleen de verbindingstekenreeks in een Azure-toepassingsomgeving of virtuele machine hoeft aan te sluiten, is de verbindingstekenreeks op de juiste manier in te stellen.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Een toepassing aansluiten binnen een andere VNet

Dit scenario is iets complexer omdat Managed Instance een eigen IP-adres heeft in zijn eigen VNet. Om verbinding te maken, heeft een toepassing toegang nodig tot het VNet waar Managed Instance wordt geïmplementeerd. Dus eerst moet u een verbinding maken tussen de toepassing en de Managed Instance VNet. De VNets hoeven niet in hetzelfde abonnement te zitten om dit scenario te laten werken.

Er zijn twee opties voor het aansluiten van VNets:

- [Peering van virtuele Azure-netwerken](../virtual-network/virtual-network-peering-overview.md)
- VNet-naar-VNet VPN-gateway[(Azure-portal,](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) [PowerShell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

De peering optie is de voorkeur, omdat peering maakt gebruik van de Microsoft backbone netwerk, zodat, vanuit het perspectief van de connectiviteit, is er geen merkbaar verschil in latentie tussen virtuele machines in peered VNet en in dezelfde VNet. VNet peering is beperkt tot de netwerken in dezelfde regio.  

> [!IMPORTANT]
> VNet-peeringscenario voor Beheerde instantie is beperkt tot de netwerken in dezelfde regio vanwege [beperkingen van het global virtual network-peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zie ook het relevante gedeelte van het artikel over [veelgestelde vragen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) van Azure Virtual Networks voor meer informatie. 

## <a name="connect-an-on-premises-application"></a>Een on-premises toepassing verbinden

Beheerde instantie is alleen toegankelijk via een privé-IP-adres. Om toegang te krijgen vanaf on-premises, moet u een Site-to-Site-verbinding maken tussen de toepassing en de Managed Instance VNet.

Er zijn twee opties om on-premises verbinding te maken met Azure VNet:

- Vpn-verbinding van site naar site[(Azure-portal,](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) [PowerShell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute-verbinding](../expressroute/expressroute-introduction.md)  

Als u on-premises verbinding met Azure hebt gemaakt en u geen verbinding maken met Beheerde instantie, controleert u of uw firewall een uitgaande verbinding heeft geopend op SQL-poort 1433 en een reeks poorten van 11000-11999 voor omleiding.

## <a name="connect-an-application-on-the-developers-box"></a>Een toepassing verbinden op het systeem waarop ontwikkelaars werken

Beheerde instantie kan alleen worden geopend via een privé-IP-adres, zodat u het eerst vanuit uw ontwikkelaarsvak openen, moet u eerst een verbinding maken tussen uw ontwikkelaarsvak en het VNet voor beheerde instantie. Configureer hiervoor een Point-to-Site-verbinding met een VNet met native Azure-certificaatverificatie. Zie [Een point-to-site-verbinding configureren om verbinding te maken met een Azure SQL Database Managed Instance vanaf een on-premises computer](sql-database-managed-instance-configure-p2s.md).

## <a name="connect-from-on-premises-with-vnet-peering"></a>Verbinding maken vanuit on-premises met VNet-peering

Een ander scenario geïmplementeerd door klanten is waar VPN-gateway is geïnstalleerd in een apart virtueel netwerk en een abonnement van de ene hosting Managed Instance. De twee virtuele netwerken worden vervolgens peered. In het volgende voorbeeldarchitectuurdiagram ziet u hoe dit kan worden geïmplementeerd.

![VNet-peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Zodra u de basisinfrastructuur hebt ingesteld, moet u een instelling wijzigen, zodat de VPN-gateway de IP-adressen in het virtuele netwerk kan zien die het beheerde exemplaar host. Breng hiervoor de volgende zeer specifieke wijzigingen aan onder de **peering-instellingen**.

1. Ga in het VNet dat de VPN-gateway host, naar **Peerings,** vervolgens naar de VNet-verbinding met beheerde instantie en klik vervolgens op **Gateway-doorvoer toestaan**.
2. Ga in het VNet dat de beheerde instantie host, naar **Peerings,** vervolgens naar de VNet-verbinding met peered-gateway van VPN-gateway en klik vervolgens op **Externe gateways gebruiken**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Een door Azure App Service gehoste toepassing verbinden

Beheerde instantie kan alleen worden geopend via een privé-IP-adres, zodat u het eerst vanuit Azure App Service openen, moet u eerst een verbinding maken tussen de toepassing en het VNet voor beheerde instantie. Zie [Uw app integreren met een Azure Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  

Zie [VNets en toepassingen oplossen](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)voor probleemoplossing. Als er geen verbinding kan worden gemaakt, probeert u [de netwerkconfiguratie te synchroniseren.](sql-database-managed-instance-sync-network-configuration.md)

Een speciaal geval van het verbinden van Azure App Service met Managed Instance is wanneer u Azure App Service hebt geïntegreerd in een netwerk dat is gekeken naar Managed Instance VNet. In dat geval moet de volgende configuratie worden ingesteld:

- Beheerde instantie VNet mag GEEN gateway hebben  
- Managed Instance VNet moet optieset Externe gateways gebruiken hebben ingesteld
- Peered VNet moet de optie Gateway Transit toestaan hebben ingesteld

Dit scenario wordt geïllustreerd in het volgende diagram:

![geïntegreerd app-peering](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>De VNet-integratiefunctie integreert een app niet met een VNet met een ExpressRoute Gateway. Zelfs als de ExpressRoute Gateway is geconfigureerd in coëxistentiemodus, werkt de VNet-integratie niet. Als u toegang nodig hebt tot bronnen via een ExpressRoute-verbinding, u een App-serviceomgeving gebruiken die in uw VNet wordt uitgevoerd.

## <a name="troubleshooting-connectivity-issues"></a>Connectiviteitsproblemen oplossen

Controleer het volgende voor het oplossen van verbindingsproblemen:

- Als u geen verbinding maken met Beheerde instantie vanuit een virtuele Azure-machine binnen hetzelfde VNet, maar een ander subnet, controleert u of u een netwerkbeveiligingsgroep hebt ingesteld op vm-subnet dat de toegang mogelijk blokkeert. Houd er bovendien rekening mee dat u uitgaande verbinding op SQL-poort 1433 en poorten in bereik 11000-11999 moet openen, omdat deze nodig zijn voor verbinding via omleiding binnen de Azure-grens.
- Controleer of BGP Propagatie is ingesteld op **Ingeschakeld** voor de routetabel die is gekoppeld aan het VNet.
- Als u P2S VPN gebruikt, controleert u de configuratie in de Azure-portal om te zien of **u Ingress/Egress-nummers** ziet. Niet-nulnummers geven aan dat Azure verkeer van/naar on-premises routert.

   ![inbinnendringen/uitgangsnummers](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Controleer of de clientmachine (die de VPN-client uitvoert) routevermeldingen heeft voor alle VNets die u moet openen. De routes worden `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`opgeslagen in .

   ![route.txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Zoals in deze afbeelding wordt weergegeven, zijn er twee vermeldingen voor elke betrokken VNet en een derde vermelding voor het VPN-eindpunt dat is geconfigureerd in de Portal.

   Een andere manier om de routes te controleren is via de volgende opdracht. De output toont de routes naar de verschillende subnetten:

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- Als u VNet-peering gebruikt, moet u ervoor zorgen dat u de instructies hebt opgevolgd voor het instellen [van Gateway Transit toestaan en Externe gateways gebruiken.](#connect-from-on-premises-with-vnet-peering)

## <a name="required-versions-of-drivers-and-tools"></a>Vereiste versies van stuurprogramma's en gereedschappen

De volgende minimale versies van de hulpprogramma's en stuurprogramma's worden aanbevolen als u verbinding wilt maken met Beheerde instantie:

| Stuurprogramma/gereedschap | Versie |
| --- | --- |
|.NET Framework | 4.6.1 (of .NET-kern) |
|ODBC-stuurprogramma| v17 |
|PHP-stuurprogramma| 5.2.0 |
|JDBC-stuurprogramma| 6.4.0 |
|Node.js-stuurprogramma| 2.1.1 |
|OLEDB-stuurprogramma| 18.0.2.0 |
|SSMS| 18,0 of [hoger](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) of hoger |

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is een beheerde instantie voor](sql-database-managed-instance.md)informatie over beheerde instantie .
- Zie [Een beheerde instantie maken](sql-database-managed-instance-get-started.md)voor een zelfstudie waarin u laat zien hoe u een nieuw beheerde instantie maakt.

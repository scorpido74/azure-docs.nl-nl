---
title: Toepassing verbinden met een beheerd exemplaar
titleSuffix: Azure SQL Managed Instance
description: In dit artikel wordt beschreven hoe u uw toepassing verbindt met Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab, vanto
ms.date: 11/09/2018
ms.openlocfilehash: 051d589ec13c1fa8642701fe94a361e1dfbe4aab
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84044386"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>Uw toepassing verbinden met Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

U hebt nu meerdere keuzes wanneer u bepaalt hoe en waar u uw toepassing host.

U kunt ervoor kiezen om de toepassing te hosten in de Cloud door gebruik te maken van Azure App Service of een aantal geïntegreerde opties van het virtuele netwerk (virtueel netwerk) van Azure, zoals Azure App Service Environment, virtuele machine, Schaalset voor virtuele machines. U kunt ook een hybride Cloud aanpak maken en uw toepassingen on-premises houden.

Welke keuze u ook maakt, u kunt verbinding maken met een SQL Managed instance. 

![hoge Beschik baarheid](./media/connect-application-instance/application-deployment-topologies.png)

In dit artikel wordt beschreven hoe u een toepassing verbindt met Azure SQL Managed instance in een aantal verschillende toepassings scenario's. 

## <a name="connect-inside-the-same-vnet"></a>Verbinding maken binnen hetzelfde VNet

Het maken van een toepassing binnen hetzelfde virtuele netwerk als de SQL Managed instance is het eenvoudigste scenario. Virtuele machines in het virtuele netwerk kunnen rechtstreeks verbinding maken met elkaar, zelfs als ze zich in verschillende subnetten bevinden. Dat betekent dat u de toepassing in een Azure-toepassing omgeving of virtuele machine moet aansluiten om de connection string op de juiste manier in te stellen.  

## <a name="connect-inside-a-different-vnet"></a>Verbinding maken binnen een ander VNet

Het verbinden van een toepassing wanneer deze zich in een ander virtueel netwerk bevindt als het SQL Managed instance is een beetje complexer omdat het SQL Managed instance privé-IP-adressen heeft in het eigen virtuele netwerk. Als u verbinding wilt maken, moet een toepassing toegang hebben tot het virtuele netwerk waarin SQL Managed instance wordt geïmplementeerd. Daarom moet u een verbinding maken tussen de toepassing en het virtuele netwerk van het SQL-beheerde exemplaar. Dit scenario werkt alleen als de virtuele netwerken zich in hetzelfde abonnement behoeven.

Er zijn twee opties voor het verbinden van virtuele netwerken:

- [Azure VPN-peering](../../virtual-network/virtual-network-peering-overview.md)
- VNet-naar-VNet VPN-gateway: ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [Power shell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure cli](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

Peering verdient de voor keur omdat peering gebruikmaakt van het micro soft backbone-netwerk, zodat er geen merkbaar verschil is in de latentie tussen virtuele machines in een gekoppeld virtueel netwerk en in hetzelfde virtuele netwerk. Peering van virtuele netwerken is beperkt tot de netwerken in dezelfde regio.  

> [!IMPORTANT]
> Het scenario voor peering van het virtuele netwerk voor een SQL Managed instance is beperkt tot de netwerken in dezelfde regio vanwege de [beperkingen ofGlobal Virtual Network peering](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zie ook de relevante sectie van het artikel Veelgestelde [vragen over virtuele netwerken van Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. 

## <a name="connect-from-on-premises"></a>Verbinding maken vanaf on-premises 

U kunt uw on-premises toepassing ook aansluiten op uw door SQL beheerde exemplaar. Een door SQL beheerd exemplaar kan alleen worden gebruikt via een privé-IP-adres. U moet een site-naar-site-verbinding maken tussen de toepassing en het virtuele netwerk van het SQL Managed instance om deze van on-premises te kunnen openen.

Er zijn twee opties om on-premises verbinding te maken met een virtueel Azure-netwerk:

- Site-naar-site-VPN-verbinding ([Azure Portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [Power shell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure cli](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../../expressroute/expressroute-introduction.md) -verbinding  

Als u on-premises naar de Azure-verbinding hebt gemaakt en u geen verbinding kunt maken met een SQL-beheerd exemplaar, controleert u of uw firewall een open bare verbinding heeft met de SQL-poort 11000-11999 1433 en een poort bereik voor omleiding.

## <a name="connect-the-developers-box"></a>Het dialoog venster ontwikkel aars verbinden

Het is ook mogelijk om uw ontwikkel aars aan te sluiten op het SQL Managed instance. SQL Managed instance is alleen toegankelijk via een privé-IP-adres, zodat u het kunt openen vanuit het vak voor ontwikkel aars. u moet eerst een verbinding maken tussen het vak voor ontwikkel aars en het virtuele netwerk van SQL Managed instance. Hiervoor configureert u een punt-naar-site-verbinding met een virtueel netwerk met behulp van systeem eigen Azure-certificaat verificatie. Zie [een punt-naar-site-verbinding configureren om verbinding te maken met Azure SQL Managed instance van on-premises computer](point-to-site-p2s-configure.md)voor meer informatie.

## <a name="connect-with-vnet-peering"></a>Verbinding maken met VNet-peering

Een ander scenario dat door klanten wordt geïmplementeerd, is de plek waar een VPN-gateway wordt geïnstalleerd in een afzonderlijk virtueel netwerk en een abonnement op een host die wordt gehost door SQL Managed instance. De twee virtuele netwerken worden vervolgens gelijkwaardig. In het volgende voor beeld van een architectuur diagram ziet u hoe dit kan worden geïmplementeerd.

![peering op virtueel netwerk](./media/connect-application-instance/vnet-peering.png)

Zodra u de basis infrastructuur hebt ingesteld, moet u een aantal instellingen wijzigen zodat de VPN Gateway de IP-adressen in het virtuele netwerk die als host fungeert voor het SQL Managed instance kunnen zien. Hiervoor moet u de volgende zeer specifieke wijzigingen aanbrengen in de **instellingen voor peering**.

1. Ga in het virtuele netwerk dat als host fungeert voor de VPN-gateway naar **peerings**en vervolgens naar de door SQL beheerde instantie gepeerde virtuele netwerk verbinding en klik vervolgens op **Gateway doorvoer toestaan**.
2. Ga in het virtuele netwerk dat als host fungeert voor het SQL Managed instance naar **peerings**en vervolgens naar de VPN gateway peered Virtual Network-verbinding en klik vervolgens op **externe gateways gebruiken**.

## <a name="connect-azure-app-service"></a>Azure App Service verbinden 

U kunt ook verbinding maken met een toepassing die wordt gehost door de Azure App Service. SQL Managed instance kan alleen worden geopend via een privé-IP-adres, zodat u er eerst verbinding mee moet maken tussen de toepassing en het virtuele netwerk Azure App Service van het SQL-beheerde exemplaar. Zie [uw app integreren met een Azure-Virtual Network](../../app-service/web-sites-integrate-with-vnet.md).  

Zie [problemen met virtuele netwerken en toepassingen oplossen](../../app-service/web-sites-integrate-with-vnet.md#troubleshooting)voor probleem oplossing. Als er geen verbinding kan worden gemaakt, probeert u [de netwerk configuratie te synchroniseren](azure-app-sync-network-configuration.md).

Een speciaal geval van het verbinden van Azure App Service met een SQL Managed instance is wanneer u Azure App Service integreert in een netwerk dat is gekoppeld aan een virtueel netwerk voor SQL Managed instance. Voor dat geval moet de volgende configuratie worden ingesteld:

- Virtueel netwerk van SQL Managed instance mag geen gateway hebben  
- Voor het virtuele netwerk van SQL Managed instance moet de `Use remote gateways` optie set zijn ingesteld
- Voor een gekoppeld virtueel netwerk moet de optie voor het door geven van gateways zijn ingesteld

Dit scenario wordt geïllustreerd in het volgende diagram:

![geïntegreerde app-peering](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>De functie voor integratie van virtuele netwerken integreert geen app met een virtueel netwerk met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in de modus voor samen werking, werkt de integratie van het virtuele netwerk niet. Als u toegang nodig hebt tot bronnen via een ExpressRoute-verbinding, kunt u een App Service Environment gebruiken, dat wordt uitgevoerd in uw virtuele netwerk.

## <a name="troubleshooting-connectivity-issues"></a>Connectiviteitsproblemen oplossen

Raadpleeg het volgende voor meer informatie over het oplossen van verbindings problemen:

- Als u geen verbinding kunt maken met een SQL Managed instance vanaf een virtuele Azure-machine binnen hetzelfde virtuele netwerk, maar met een ander subnet, controleert u of er een netwerk beveiligings groep is ingesteld op het VM-subnet dat de toegang blokkeert. Daarnaast kunt u de uitgaande verbinding openen voor SQL-poort 1433 en poorten in bereik 11000-11999, omdat deze nodig zijn om verbinding te maken via omleiding binnen de Azure-grens.
- Zorg ervoor dat BGP-doorgifte is ingesteld op **ingeschakeld** voor de route tabel die is gekoppeld aan het virtuele netwerk.
- Als u P2S VPN gebruikt, controleert u de configuratie in de Azure Portal om te zien of er **ingangs-en uitgangs** nummers worden weer geven. Niet-nul getallen geven aan dat Azure verkeer routeert naar/van on-premises.

   ![ingangs-en uitgangs nummers](./media/connect-application-instance/ingress-egress-numbers.png)

- Controleer of de client computer (waarop de VPN-client wordt uitgevoerd) route vermeldingen heeft voor alle virtuele netwerken waartoe u toegang wilt. De routes worden opgeslagen in `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt` .

   ![route. txt](./media/connect-application-instance/route-txt.png)

   Zoals in deze afbeelding wordt weer gegeven, zijn er twee vermeldingen voor elk betrokken virtuele netwerk en een derde vermelding voor het VPN-eind punt dat is geconfigureerd in de portal.

   Een andere manier om de routes te controleren, is via de volgende opdracht. De uitvoer toont de routes naar de verschillende subnetten:

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

- Als u virtuele netwerk peering gebruikt, moet u ervoor zorgen dat u de instructies voor het instellen van [Gateway doorvoer toestaan en externe gateways gebruiken](#connect-from-on-premises)hebt gevolgd.

- Als u de peering van een virtueel netwerk gebruikt om verbinding te maken met een Azure App Service gehoste toepassing en het virtuele SQL Managed instance-netwerk een openbaar IP-adres bereik heeft, moet u ervoor zorgen dat uw gehoste toepassings instellingen uw uitgaand verkeer naar open bare IP-netwerken kunnen routeren. Volg de instructies in [regionale virtuele netwerk integratie](../../app-service/web-sites-integrate-with-vnet.md#regional-vnet-integration).

## <a name="required-versions-of-drivers-and-tools"></a>Vereiste versies van Stuur Programma's en hulpprogram ma's

De volgende minimale versies van de hulpprogram ma's en stuur Programma's worden aanbevolen als u verbinding wilt maken met een SQL Managed instance:

| Stuur programma/hulp programma | Versie |
| --- | --- |
|.NET Framework | 4.6.1 (of .NET core) |
|ODBC-stuurprogramma| v17 |
|PHP-stuurprogramma| 5.2.0 |
|JDBC-stuurprogramma| 6.4.0 |
|Node.js-stuurprogramma| 2.1.1 |
|OLEDB-stuurprogramma| 18.0.2.0 |
|SSMS| 18,0 of [hoger](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](https://docs.microsoft.com/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) of hoger |

## <a name="next-steps"></a>Volgende stappen

- Zie [Wat is SQL Managed instance?](sql-managed-instance-paas-overview.md)voor meer informatie over SQL Managed instance.
- Zie [een SQL Managed instance maken](instance-create-quickstart.md)voor een zelf studie waarin wordt getoond hoe u een nieuw exemplaar van SQL Managed maakt.

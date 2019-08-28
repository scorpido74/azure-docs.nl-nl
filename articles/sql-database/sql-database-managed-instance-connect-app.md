---
title: Azure SQL Database Managed instance Connect-toepassing | Microsoft Docs
description: In dit artikel wordt beschreven hoe u uw toepassing verbindt met Azure SQL Database beheerde instantie.
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
ms.openlocfilehash: 133110d015ac7a26f18f14f6ff957729a4f079b5
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060656"
---
# <a name="connect-your-application-to-azure-sql-database-managed-instance"></a>Uw toepassing verbinden met Azure SQL Database beheerde instantie

U hebt nu meerdere keuzes wanneer u bepaalt hoe en waar u uw toepassing host.

U kunt ervoor kiezen om de toepassing in de cloud te hosten door gebruik te maken van Azure App Service of een aantal van de geïntegreerde VNet-opties (virtuele netwerken) van Azure, zoals Azure App Service Environment, virtuele machine, Schaalset voor virtuele machines. U kunt ook een hybride Cloud aanpak maken en uw toepassingen on-premises houden.

Welke keuze u hebt gemaakt, kunt u verbinding laten maken met een beheerd exemplaar.  

![hoge Beschik baarheid](./media/sql-database-managed-instance/application-deployment-topologies.png)

## <a name="connect-an-application-inside-the-same-vnet"></a>Een toepassing verbinden binnen hetzelfde VNet

Dit scenario is het eenvoudigste. Virtuele machines binnen het VNet kunnen rechtstreeks verbinding maken met elkaar, zelfs als ze zich in verschillende subnetten bevinden. Dat betekent dat u de toepassing in een Azure-toepassing omgeving of virtuele machine moet aansluiten om de connection string op de juiste manier in te stellen.  

## <a name="connect-an-application-inside-a-different-vnet"></a>Een toepassing verbinden binnen een ander VNet

Dit scenario is iets ingewik kelder omdat een beheerd exemplaar een persoonlijk IP-adres heeft in zijn eigen VNet. Een toepassing moet toegang hebben tot het VNet waar het beheerde exemplaar is geïmplementeerd om verbinding te maken. Daarom moet u eerst verbinding maken tussen de toepassing en het VNet van het beheerde exemplaar. Dit scenario werkt alleen als de VNets zich in hetzelfde abonnement behoeven.

Er zijn twee opties voor het verbinden van VNets:

- [Azure Virtual Network peering](../virtual-network/virtual-network-peering-overview.md)
- VNet-naar-VNet VPN-gateway ([Azure Portal](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [Power shell](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure cli](../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

De peering-optie is de voor keur. omdat peering het micro soft backbone-netwerk gebruikt, is er geen merkbaar verschil in latentie tussen virtuele machines in peered VNet en in hetzelfde VNet. VNet-peering is beperkt tot de netwerken in dezelfde regio.  

> [!IMPORTANT]
> Het VNet-peering scenario voor het beheerde exemplaar is beperkt tot de netwerken in dezelfde regio vanwege [beperkingen van de globale Virtual Network peering](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints). Zie ook de relevante sectie van het artikel Veelgestelde [vragen over virtuele netwerken van Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) voor meer informatie. 

## <a name="connect-an-on-premises-application"></a>Verbinding maken met een on-premises toepassing

Een beheerd exemplaar kan alleen worden geopend via een privé-IP-adres. U moet een site-naar-site-verbinding maken tussen de toepassing en het VNet van het beheerde exemplaar om deze van on-premises te kunnen openen.

Er zijn twee opties om on-premises verbinding te maken met Azure VNet:

- Site-naar-site-VPN-verbinding ([Azure Portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), [Power shell](../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure cli](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [ExpressRoute](../expressroute/expressroute-introduction.md) -verbinding  

Als u on-premises naar de Azure-verbinding hebt gemaakt en u geen verbinding kunt maken met een beheerd exemplaar, controleert u of uw firewall een open bare verbinding heeft met de SQL-poort 11000-11999 1433 en het bereik van poorten voor omleiding.

## <a name="connect-an-application-on-the-developers-box"></a>Een toepassing verbinden in het vak ontwikkel aars

Een beheerd exemplaar kan alleen worden geopend via een privé-IP-adres, zodat u het kunt openen vanuit het vak voor ontwikkel aars. u moet eerst een verbinding maken tussen het vak voor ontwikkel aars en het VNet van het beheerde exemplaar. Hiervoor configureert u een punt-naar-site-verbinding met een VNet met behulp van systeem eigen Azure-certificaat verificatie. Zie [een punt-naar-site-verbinding configureren om verbinding te maken met een Azure SQL database beheerde instantie van on-premises computer](sql-database-managed-instance-configure-p2s.md)voor meer informatie.

## <a name="connect-from-on-premises-with-vnet-peering"></a>Verbinding maken vanaf on-premises met VNet-peering

Een ander scenario dat door klanten wordt geïmplementeerd, is de plek waar VPN-gateway wordt geïnstalleerd in een afzonderlijk virtueel netwerk en een abonnement van de ene beheerde instantie die als host fungeert. De twee virtuele netwerken worden vervolgens gelijkwaardig. In het volgende voor beeld van een architectuur diagram ziet u hoe dit kan worden geïmplementeerd.

![VNet-peering](./media/sql-database-managed-instance-connect-app/vnet-peering.png)

Zodra u de basis infrastructuur hebt ingesteld, moet u een aantal instellingen wijzigen zodat de VPN Gateway de IP-adressen in het virtuele netwerk die als host voor het beheerde exemplaar worden weer geven. Hiervoor moet u de volgende zeer specifieke wijzigingen aanbrengen in de **instellingen**voor peering.

1. Ga in het VNet dat als host fungeert voor de VPN-gateway naar peerings, vervolgens naar de beheerde VNet-verbinding van het Managed instance en klik vervolgens op **Gateway doorvoer toestaan**.
2. Ga in het VNet dat als host fungeert voor het beheerdeexemplaar naar peerings, vervolgens naar de VPN gateway peered VNet-verbinding en klik vervolgens op **externe gateways gebruiken**.

## <a name="connect-an-azure-app-service-hosted-application"></a>Een Azure App Service gehoste toepassing verbinden

Beheerde exemplaren kunnen alleen worden geopend via een privé-IP-adres, zodat u deze kunt openen vanaf Azure App Service u eerst verbinding moet maken tussen de toepassing en het VNet van het beheerde exemplaar. Zie [uw app integreren met een Azure-Virtual Network](../app-service/web-sites-integrate-with-vnet.md).  

Zie [Troubleshooting VNets and Applications](../app-service/web-sites-integrate-with-vnet.md#troubleshooting)(Engelstalig) voor probleem oplossing. Als er geen verbinding kan worden gemaakt, probeert u [de netwerk configuratie te synchroniseren](sql-database-managed-instance-sync-network-configuration.md).

Een speciaal geval van het verbinden van Azure App Service naar een beheerd exemplaar is wanneer u Azure App Service hebt geïntegreerd met een netwerk dat is gekoppeld aan het beheerde exemplaar VNet. Voor dat geval moet de volgende configuratie worden ingesteld:

- VNet van beheerd exemplaar mag geen gateway hebben  
- Voor het beheerde exemplaar-VNet moet de optie externe gateways gebruiken zijn ingesteld
- Voor peered VNet moet de optie voor het door geven van Gateway-transit zijn ingeschakeld

Dit scenario wordt geïllustreerd in het volgende diagram:

![geïntegreerde app-peering](./media/sql-database-managed-instance/integrated-app-peering.png)

>[!NOTE]
>De VNet-integratie functie integreert geen app met een VNet met een ExpressRoute-gateway. Zelfs als de ExpressRoute-gateway is geconfigureerd in de modus voor samen werking, werkt de VNet-integratie niet. Als u toegang nodig hebt tot resources via een ExpressRoute-verbinding, kunt u een App Service Environment gebruiken, dat wordt uitgevoerd in uw VNet.

## <a name="troubleshooting-connectivity-issues"></a>Verbindings problemen oplossen

Raadpleeg het volgende voor meer informatie over het oplossen van verbindings problemen:

- Als u geen verbinding kunt maken met een beheerd exemplaar van een virtuele Azure-machine binnen hetzelfde VNet maar een ander subnet, controleert u of er een netwerk beveiligings groep is ingesteld op het VM-subnet dat de toegang mogelijk blokkeert. Daarnaast moet u de uitgaande verbinding openen op SQL-poort 1433 en poorten in bereik 11000-11999, omdat deze nodig zijn om verbinding te maken via omleiding binnen de Azure-grens.
- Zorg ervoor dat BGP-doorgifte is ingesteld op **ingeschakeld** voor de route tabel die is gekoppeld aan het VNet.
- Als u P2S VPN gebruikt, controleert u de configuratie in de Azure Portal om te zien of er **ingangs-en uitgangs** nummers worden weer geven. Niet-nul getallen geven aan dat Azure verkeer routeert naar/van on-premises.

   ![ingangs-en uitgangs nummers](./media/sql-database-managed-instance-connect-app/ingress-egress-numbers.png)

- Controleer of de client computer (waarop de VPN-client wordt uitgevoerd) route vermeldingen bevat voor alle VNets waartoe u toegang wilt. De routes worden opgeslagen in `%AppData%\ Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`.

   ![route. txt](./media/sql-database-managed-instance-connect-app/route-txt.png)

   Zoals in deze afbeelding wordt weer gegeven, zijn er twee vermeldingen voor elk betreffende VNet en een derde vermelding voor het VPN-eind punt dat is geconfigureerd in de portal.

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

- Als u VNet-peering gebruikt, moet u ervoor zorgen dat u de instructies voor het instellen van [Gateway doorvoer toestaan en externe gateways gebruiken](#connect-from-on-premises-with-vnet-peering)hebt gevolgd.

## <a name="required-versions-of-drivers-and-tools"></a>Vereiste versies van Stuur Programma's en hulpprogram ma's

De volgende minimale versies van de hulpprogram ma's en stuur Programma's worden aanbevolen als u verbinding wilt maken met een beheerd exemplaar:

| Stuur programma/hulp programma | Version |
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

- Zie [Wat is een beheerd exemplaar (preview)?](sql-database-managed-instance.md) voor meer informatie over beheerde exemplaren.
- Zie [een beheerd exemplaar maken](sql-database-managed-instance-get-started.md)voor een zelf studie waarin wordt getoond hoe u een nieuw beheerd exemplaar maakt.

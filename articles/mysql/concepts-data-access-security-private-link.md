---
title: Private Link - Azure Database voor MySQL
description: Meer informatie over hoe private koppeling werkt voor Azure Database voor MySQL.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: c2cc4986542404281424286882c046dec39f5daf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371287"
---
# <a name="private-link-for-azure-database-for-mysql"></a>Private Link voor Azure Database voor MySQL

Met Private Link u verbinding maken met verschillende PaaS-services in Azure via een privéeindpunt. Azure Private Link brengt in wezen Azure-services binnen uw privé-virtuele netwerk (VNet). De PaaS-bronnen zijn toegankelijk via het privé-IP-adres, net als elke andere bron in het VNet.

Voor een lijst met PaaS-services die de private link-functionaliteit ondersteunen, controleert u de documentatie voor [privékoppelingen](https://docs.microsoft.com/azure/private-link/index). Een privéeindpunt is een privé-IP-adres binnen een specifiek [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) en Subnet.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database for MySQL prijsniveaus voor algemeen gebruik en geheugengeoptimaliseerd ondersteunt.

## <a name="data-exfiltration-prevention"></a>Preventie van gegevensexfiltratie

Gegevens ex-filtratie in Azure Database voor MySQL is wanneer een geautoriseerde gebruiker, zoals een databasebeheerder, in staat is om gegevens uit het ene systeem te extraheren en naar een andere locatie of systeem buiten de organisatie te verplaatsen. De gebruiker verplaatst de gegevens bijvoorbeeld naar een opslagaccount dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker waarop MySQL Workbench wordt uitgevoerd in een Azure Virtual Machine (VM) die verbinding maakt met een Azure Database voor MySQL-server die is ingericht in West US. In het onderstaande voorbeeld ziet u hoe u de toegang met openbare eindpunten in Azure Database voor MySQL beperken met behulp van netwerktoegangsbesturingselementen.

* Schakel al het Azure-serviceverkeer naar Azure Database voor MySQL uit via het openbare eindpunt door *Azure Services toestaan* in te stellen om uit te gaan. Zorg ervoor dat ip-adressen of bereiken geen toegang hebben tot de server via [firewallregels](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules) of [eindpunten voor virtuele netwerkservice.](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)

* Geef alleen verkeer toe aan de Azure Database voor MySQL met behulp van het privé-IP-adres van de VM. Zie voor meer informatie de artikelen over [serviceeindpunt-](concepts-data-access-and-security-vnet.md) en [VNet-firewallregels](howto-manage-vnet-using-portal.md).

* Verklein op de Azure VM het bereik van uitgaande verbinding met behulp van Network Security Groups (NSGs) en Service Tags als volgt

    * Geef een NSG-regel op om verkeer toe te staan voor *Service Tag = SQL. WestUs* - alleen verbinding met Azure Database toestaan voor MySQL in West US
    * Een NSG-regel opgeven (met een hogere prioriteit) om verkeer voor *Service Tag = SQL* te weigeren - het weigeren van verbindingen met Update naar Azure Database voor MySQL in alle regio's</br></br>

Aan het einde van deze installatie kan de Azure VM alleen verbinding maken met Azure Database voor MySQL in de regio West-VS. De connectiviteit is echter niet beperkt tot één Azure-database voor MySQL. De VM kan nog steeds verbinding maken met een Azure Database voor MySQL in de regio West-VS, inclusief de databases die geen deel uitmaken van het abonnement. Hoewel we het bereik van gegevensexfiltratie in het bovenstaande scenario hebben teruggebracht tot een specifieke regio, hebben we het niet helemaal geëlimineerd.</br>

Met Private Link u nu netwerktoegangsbesturingselementen zoals NSG's instellen om de toegang tot het privéeindpunt te beperken. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privéeindpunten. Een kwaadwillende insider heeft alleen toegang tot de toegewezen PaaS-bron (bijvoorbeeld een Azure Database voor MySQL) en geen andere bron.

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via private peering

Wanneer u verbinding maakt met het openbare eindpunt van on-premises machines, moet uw IP-adres worden toegevoegd aan de IP-firewall met behulp van een firewallregel op serverniveau. Hoewel dit model goed werkt voor het toestaan van toegang tot individuele machines voor dev of testworkloads, is het moeilijk te beheren in een productieomgeving.

Met Private Link u toegang tot het privéeindpunt vooraf gaan met [expressroute](https://azure.microsoft.com/services/expressroute/) (ER), privépeering of [VPN-tunnel.](https://docs.microsoft.com/azure/vpn-gateway/) Ze kunnen vervolgens alle toegang via het openbare eindpunt uitschakelen en geen gebruik maken van de IP-gebaseerde firewall.

## <a name="configure-private-link-for-azure-database-for-mysql"></a>Private Link voor Azure Database voor MySQL configureren

### <a name="creation-process"></a>Creatieproces

Privéeindpunten zijn vereist om Private Link in te schakelen. Dit kan worden gedaan met behulp van de volgende how-to gidsen.

* [Azure-portal](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/mysql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Goedkeuringsproces
Zodra de netwerkbeheerder het privéeindpunt (PE) heeft gemaakt, kan de MySQL-beheerder de private endpoint Connection (PEC) naar Azure Database voor MySQL beheren. Deze scheiding van taken tussen de netwerkbeheerder en de DBA is handig voor het beheer van de Azure Database for MySQL-connectiviteit. 

* Navigeer naar de Azure Database for MySQL-serverbron in de Azure-portal. 
    * De privéeindpuntverbindingen in het linkerdeelvenster selecteren
    * Toont een lijst met alle privé-eindpuntverbindingen (PECs)
    * Overeenkomstige privéeindpunt (PE) gemaakt

![de privé-eindpuntportal selecteren](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecteer een individuele PEC in de lijst door deze te selecteren.

![het privéeindpunt selecteren in afwachting van goedkeuring](media/concepts-data-access-and-security-private-link/select-private-link.png)

* De MySQL-serverbeheerder kan ervoor kiezen een PEC goed te keuren of af te wijzen en optioneel een kort tekstantwoord toe te voegen.

![het privéeindpuntbericht selecteren](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Na goedkeuring of afwijzing geeft de lijst de juiste status weer, samen met de antwoordtekst

![de uiteindelijke status van het privéeindpunt selecteren](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-mysql"></a>Voorbeelden van Private Link voor Azure Database voor MySQL gebruiken

Clients kunnen verbinding maken met het privéeindpunt vanaf hetzelfde VNet, vnet peered in dezelfde regio of via VNet-naar-VNet-verbinding tussen regio's. Daarnaast kunnen clients verbinding maken vanuit on-premises via ExpressRoute, private peering of VPN-tunneling. Hieronder vindt u een vereenvoudigd diagram met de algemene use cases.

![het privéeindpuntoverzicht selecteren](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Verbinding maken vanaf een Azure VM in Peered Virtual Network (VNet)
[VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) configureren om verbinding te maken met de Azure Database voor MySQL vanuit een Azure VM in een peered VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Verbinding maken vanuit een Azure VM in vnet-naar-VNet-omgeving
Configureer [VNet-to-VNet VPN-gatewayverbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) om verbinding te maken met een Azure Database voor MySQL vanuit een Azure VM in een andere regio of abonnement.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN
Als u verbinding wilt maken met een on-premises omgeving met de Azure Database voor MySQL, kiest en implementeert u een van de opties:

* [Point-to-Site-verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Site-naar-site-VPN-verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Private Link gecombineerd met firewallregels

De volgende situaties en resultaten zijn mogelijk wanneer u Private Link gebruikt in combinatie met firewallregels:

* Als u geen firewallregels configureert, heeft er standaard geen toegang tot de Azure Database voor MySQL.

* Als u openbaar verkeer of een serviceeindpunt configureert en privéeindpunten maakt, worden verschillende typen binnenkomend verkeer geautoriseerd door het bijbehorende type firewallregel.

* Als u geen openbaar verkeer of serviceeindpunt configureert en privéeindpunten maakt, is de Azure Database voor MySQL alleen toegankelijk via de privéeindpunten. Als u geen openbaar verkeer of serviceeindpunt configureert, nadat alle goedgekeurde privéeindpunten zijn geweigerd of verwijderd, heeft geen verkeer toegang tot de Azure Database voor MySQL.

## <a name="deny-public-access-for-azure-database-for-mysql"></a>Openbare toegang voor Azure Database voor MySQL weigeren

Als u alleen wilt vertrouwen op privéeindpunten voor toegang tot hun Azure Database voor MySQL, u het instellen van alle openbare eindpunten (d.w.z. [firewallregels](concepts-firewall-rules.md) en [VNet-serviceeindpunten)](concepts-data-access-and-security-vnet.md)uitschakelen door de configuratie **Openbare netwerktoegang weigeren** in te stellen op de databaseserver. 

Wanneer deze instelling is ingesteld op *JA,* zijn alleen verbindingen via privéeindpunten toegestaan met uw Azure Database voor MySQL. Wanneer deze instelling is ingesteld op *NEE,* kunnen clients verbinding maken met uw Azure Database for MySQL op basis van uw firewall- of VNet-serviceeindpuntinstellingen. Bovendien u, zodra de waarde van de privénetwerktoegang is ingesteld, bestaande firewall- en VNet-serviceeindpuntregels niet meer toevoegen en/of bijwerken.

> [!Note]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database voor PostgreSQL - Single server general purpose- en geheugengeoptimaliseerde prijsniveaus ondersteunt.
>
> Deze instelling heeft geen invloed op de SSL- en TLS-configuraties voor uw Azure Database voor MySQL.

Zie De toegang tot **openbare netwerk weigeren** voor uw Azure Database voor MySQL vanuit Azure-portal voor meer informatie over het configureren van Openbare [netwerktoegang weigeren.](howto-deny-public-network-access.md)

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure Database voor MySQL-beveiligingsfuncties:

* Zie [Firewall-ondersteuning](https://docs.microsoft.com/azure/mysql/concepts-firewall-rules)voor het configureren van een firewall voor Azure Database voor MySQL.

* Zie Toegang configureren [vanuit virtuele netwerken](https://docs.microsoft.com/azure/mysql/concepts-data-access-and-security-vnet)voor meer informatie over het configureren van een eindpunt voor een virtuele netwerkservice voor uw Azure Database voor MySQL.

* Zie [Azure Database for MySQL Connectivity Architecture voor](https://docs.microsoft.com/azure/mysql/concepts-connectivity-architecture) een overzicht van Azure Database for MySQL Connectivity Architecture

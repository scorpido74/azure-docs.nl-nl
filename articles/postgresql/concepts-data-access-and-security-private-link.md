---
title: Private Link - Azure Database voor PostgreSQL - Enkele server
description: Meer informatie over hoe private koppeling werkt voor Azure Database voor PostgreSQL - Enkele server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4216abdf8cc8aae00e3ba0c57961c4b8b7403672
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371678"
---
# <a name="private-link-for-azure-database-for-postgresql-single-server"></a>Private Link voor Azure Database voor PostgreSQL-Single server

Met Private Link u privéeindpunten maken voor Azure Database voor PostgreSQL - Enkele server en zo Azure-services binnen uw privé-virtuele netwerk (VNet) brengen. Het privéeindpunt legt een privé-IP bloot die u gebruiken om verbinding te maken met uw databaseserver, net als elke andere bron in het VNet.

Voor een lijst met PaaS-services die de private link-functionaliteit ondersteunen, controleert u de documentatie voor [privékoppelingen](https://docs.microsoft.com/azure/private-link/index). Een privéeindpunt is een privé-IP-adres binnen een specifiek [VNet](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview) en Subnet.

> [!NOTE]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database voor PostgreSQL Single-server prijsniveaus voor algemeen gebruik en geheugengeoptimaliseerd ondersteunt.

## <a name="data-exfiltration-prevention"></a>Preventie van gegevensexfiltratie

Gegevens ex-filtratie in Azure Database voor PostgreSQL Single server is wanneer een geautoriseerde gebruiker, zoals een databasebeheerder, in staat is om gegevens uit het ene systeem te extraheren en naar een andere locatie of systeem buiten de organisatie te verplaatsen. De gebruiker verplaatst de gegevens bijvoorbeeld naar een opslagaccount dat eigendom is van een derde partij.

Overweeg een scenario met een gebruiker waarop PGAdmin wordt uitgevoerd in een Azure Virtual Machine (VM) die verbinding maakt met een Azure Database voor PostgreSQL Single-server die is ingericht in West-US. In het onderstaande voorbeeld ziet u hoe u de toegang met openbare eindpunten in Azure Database voor PostgreSQL Single-server beperken met behulp van netwerktoegangsbesturingselementen.

* Schakel al het Azure-serviceverkeer naar Azure Database voor PostgreSQL Single-server uit via het openbare eindpunt door *Azure Services toestaan* in te stellen om uit te schakelen. Zorg ervoor dat ip-adressen of bereiken geen toegang hebben tot de server via [firewallregels](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules) of [eindpunten voor virtuele netwerkservice.](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)

* Geef alleen verkeer toe aan de Azure Database voor PostgreSQL Single-server met het privé-IP-adres van de VM. Zie voor meer informatie de artikelen over [Service Endpoint-](concepts-data-access-and-security-vnet.md) en [VNet-firewallregels.](howto-manage-vnet-using-portal.md)

* Verklein op de Azure VM het bereik van uitgaande verbinding met behulp van Network Security Groups (NSGs) en Service Tags als volgt

    * Geef een NSG-regel op om verkeer toe te staan voor *Service Tag = SQL. WestUS* - alleen verbinding met Azure Database toestaan voor PostgreSQL Single-server in West US
    * Een NSG-regel opgeven (met een hogere prioriteit) om verkeer voor *Service Tag = SQL* te weigeren - het weigeren van verbindingen met PostgreSQL-database in alle regio's</br></br>

Aan het einde van deze installatie kan de Azure VM alleen verbinding maken met Azure Database voor PostgreSQL Single-server in de regio West-VS. De connectiviteit is echter niet beperkt tot één Azure-database voor PostgreSQL Single-server. De VM kan nog steeds verbinding maken met een Azure Database voor PostgreSQL Single-server in de regio West-VS, inclusief de databases die geen deel uitmaken van het abonnement. Hoewel we het bereik van gegevensexfiltratie in het bovenstaande scenario hebben teruggebracht tot een specifieke regio, hebben we het niet helemaal geëlimineerd.</br>

Met Private Link u nu netwerktoegangsbesturingselementen zoals NSG's instellen om de toegang tot het privéeindpunt te beperken. Afzonderlijke Azure PaaS-resources worden vervolgens toegewezen aan specifieke privéeindpunten. Een kwaadwillende insider heeft alleen toegang tot de toegewezen PaaS-bron (bijvoorbeeld een Azure Database voor PostgreSQL Single-server) en geen andere bron.

## <a name="on-premises-connectivity-over-private-peering"></a>On-premises connectiviteit via private peering

Wanneer u verbinding maakt met het openbare eindpunt van on-premises machines, moet uw IP-adres worden toegevoegd aan de IP-firewall met behulp van een firewallregel op serverniveau. Hoewel dit model goed werkt voor het toestaan van toegang tot individuele machines voor dev of testworkloads, is het moeilijk te beheren in een productieomgeving.

Met Private Link u toegang tot het privéeindpunt vooraf gaan met [expressroute](https://azure.microsoft.com/services/expressroute/) (ER), privépeering of [VPN-tunnel.](https://docs.microsoft.com/azure/vpn-gateway/) Ze kunnen vervolgens alle toegang via het openbare eindpunt uitschakelen en geen gebruik maken van de IP-gebaseerde firewall.

## <a name="configure-private-link-for-azure-database-for-postgresql-single-server"></a>Private Link voor Azure Database configureren voor PostgreSQL Single server

### <a name="creation-process"></a>Creatieproces

Privéeindpunten zijn vereist om Private Link in te schakelen. Dit kan worden gedaan met behulp van de volgende how-to gidsen.

* [Azure-portal](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-portal)
* [CLI](https://docs.microsoft.com/azure/postgresql/howto-configure-privatelink-cli)

### <a name="approval-process"></a>Goedkeuringsproces
Zodra de netwerkbeheerder het privéeindpunt (PE) heeft gemaakt, kan de PostgreSQL-beheerder de private endpoint Connection (PEC) naar Azure Database voor PostgreSQL beheren. Deze scheiding van taken tussen de netwerkbeheerder en de DBA is handig voor het beheer van de Azure Database for PostgreSQL-connectiviteit. 

* Navigeer naar de Azure Database for PostgreSQL-serverbron in de Azure-portal. 
    * De privéeindpuntverbindingen in het linkerdeelvenster selecteren
    * Toont een lijst met alle privé-eindpuntverbindingen (PECs)
    * Overeenkomstige privéeindpunt (PE) gemaakt

![de privé-eindpuntportal selecteren](media/concepts-data-access-and-security-private-link/select-private-link-portal.png)

* Selecteer een individuele PEC in de lijst door deze te selecteren.

![het privéeindpunt selecteren in afwachting van goedkeuring](media/concepts-data-access-and-security-private-link/select-private-link.png)

* De PostgreSQL-serverbeheerder kan ervoor kiezen een PEC goed te keuren of af te wijzen en optioneel een kort tekstantwoord toe te voegen.

![het privéeindpuntbericht selecteren](media/concepts-data-access-and-security-private-link/select-private-link-message.png)

* Na goedkeuring of afwijzing geeft de lijst de juiste status weer, samen met de antwoordtekst

![de uiteindelijke status van het privéeindpunt selecteren](media/concepts-data-access-and-security-private-link/show-private-link-approved-connection.png)

## <a name="use-cases-of-private-link-for-azure-database-for-postgresql"></a>Voorbeelden van Private Link voor Azure Database voor PostgreSQL gebruiken

Clients kunnen verbinding maken met het privéeindpunt vanaf hetzelfde VNet, vnet peered in dezelfde regio of via VNet-naar-VNet-verbinding tussen regio's. Daarnaast kunnen clients verbinding maken vanuit on-premises via ExpressRoute, private peering of VPN-tunneling. Hieronder vindt u een vereenvoudigd diagram met de algemene use cases.

![het privéeindpuntoverzicht selecteren](media/concepts-data-access-and-security-private-link/show-private-link-overview.png)

### <a name="connecting-from-an-azure-vm-in-peered-virtual-network-vnet"></a>Verbinding maken vanaf een Azure VM in Peered Virtual Network (VNet)
[VNet-peering](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-powershell) configureren om verbinding te maken met de Azure Database voor PostgreSQL - Enkele server van een Azure VM in een peered VNet.

### <a name="connecting-from-an-azure-vm-in-vnet-to-vnet-environment"></a>Verbinding maken vanuit een Azure VM in vnet-naar-VNet-omgeving
Configureer [VNet-to-VNet VPN-gatewayverbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal) om verbinding te maken met een Azure Database voor PostgreSQL - Enkele server van een Azure VM in een andere regio of abonnement.

### <a name="connecting-from-an-on-premises-environment-over-vpn"></a>Verbinding maken vanuit een on-premises omgeving via VPN
Als u verbinding wilt maken met een on-premises omgeving met de Azure Database voor PostgreSQL - Enkele server, kiest en implementeert u een van de opties:

* [Point-to-Site-verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps)
* [Site-naar-site-VPN-verbinding](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell)
* [ExpressRoute-circuit](https://docs.microsoft.com/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager)

## <a name="private-link-combined-with-firewall-rules"></a>Private Link gecombineerd met firewallregels

De volgende situaties en resultaten zijn mogelijk wanneer u Private Link gebruikt in combinatie met firewallregels:

* Als u geen firewallregels configureert, heeft er standaard geen verkeer toegang tot de Azure Database voor PostgreSQL Single-server.

* Als u openbaar verkeer of een serviceeindpunt configureert en privéeindpunten maakt, worden verschillende typen binnenkomend verkeer geautoriseerd door het bijbehorende type firewallregel.

* Als u geen openbaar verkeer of serviceeindpunt configureert en privéeindpunten maakt, is de Azure Database voor PostgreSQL Single-server alleen toegankelijk via de privéeindpunten. Als u geen openbaar verkeer of serviceeindpunt configureert, nadat alle goedgekeurde privéeindpunten zijn geweigerd of verwijderd, heeft geen verkeer toegang tot de Azure Database voor PostgreSQL Single-server.

## <a name="deny-public-access-for-azure-database-for-postgresql-single-server"></a>Openbare toegang voor Azure Database voor PostgreSQL Single-server weigeren

Als u alleen wilt vertrouwen op privéeindpunten voor toegang tot hun Azure Database voor PostgreSQL Single-server, u het instellen van alle openbare eindpunten([firewallregels](concepts-firewall-rules.md) en [VNet-serviceeindpunten)](concepts-data-access-and-security-vnet.md)uitschakelen door de configuratie **Openbare netwerktoegangs weigeren** in te stellen op de databaseserver. 

Wanneer deze instelling is ingesteld op *JA* zijn alleen verbindingen via privéeindpunten toegestaan in uw Azure Database voor PostgreSQL. Wanneer deze instelling is ingesteld op *NO,* kunnen clients verbinding maken met uw Azure Database voor PostgreSQL op basis van uw firewall- of VNet-serviceeindpuntinstelling. Bovendien kan de waarde van de privénetwerktoegang voor klanten niet meer bestaande 'Firewall-regels' en 'VNet-serviceeindpuntregel' toevoegen en/of bijwerken

> [!Note]
> Deze functie is beschikbaar in alle Azure-regio's waar Azure Database voor PostgreSQL - Single server general purpose- en geheugengeoptimaliseerde prijsniveaus ondersteunt.
>
> Deze instelling heeft geen invloed op de SSL- en TLS-configuraties voor uw Azure Database voor PostgreSQL Single-server.

Raadpleeg de toegang tot **openbare netwerk weigeren** voor uw Azure-database voor PostgreSQL Single-server vanuit Azure-portal voor meer informatie over het configureren van Openbare [netwerktoegang weigeren](howto-deny-public-network-access.md).

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over Azure Database voor de beveiligingsfuncties van PostgreSQL Single server:

* Zie [Firewall-ondersteuning](https://docs.microsoft.com/azure/postgresql/concepts-firewall-rules)voor het configureren van een firewall voor Azure Database voor PostgreSQL Single server.

* Zie Toegang configureren [vanuit virtuele netwerken](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-vnet)voor meer informatie over het configureren van een eindpunt voor een virtuele netwerkservice voor uw Azure Database voor PostgreSQL Single server.

* Zie [Azure Database voor PostgreSQL Connectivity Architecture voor](https://docs.microsoft.com/azure/postgresql/concepts-connectivity-architecture) een overzicht van Azure Database voor PostgreSQL-connectiviteit
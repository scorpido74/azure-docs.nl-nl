---
title: Service-eindpunten voor een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Ontdek hoe u directe toegang inschakelt voor Azure-resources via een virtueel netwerk met behulp van service-eindpunten.
services: virtual-network
documentationcenter: na
author: sumeetmittal
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2019
ms.author: sumi
ms.custom: ''
ms.openlocfilehash: 72c2c90f7a71bd9bf251adb492168fa5d2baa60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244782"
---
# <a name="virtual-network-service-endpoints"></a>Service-eindpunten voor virtueel netwerk

VNet-serviceeindpunten (Virtual Network) breiden de privéadresruimte van uw virtuele netwerk uit. De eindpunten breiden ook de identiteit van uw VNet uit tot de Azure-services via een directe verbinding. Met eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar uw virtuele netwerken. Verkeer van uw VNet naar de Azure-service blijft altijd in het Microsoft Azure-backbonenetwerk.

Deze functie is beschikbaar voor de volgende Azure-services en -regio's. De *Microsoft.-bron\* * is tussen haakjes. Schakel deze bron in vanaf de subnetzijde tijdens het configureren van serviceeindpunten voor uw service:

**Algemeen verkrijgbaar**

- **[Azure Storage](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json#grant-access-from-a-virtual-network)** (*Microsoft.Storage):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure SQL Database](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure SQL Data Warehouse](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure Database for PostgreSQL server](../postgresql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Algemeen beschikbaar in Azure-regio's waar databaseservice beschikbaar is.
- **[Azure Database for MySQL-server](../mysql/howto-manage-vnet-using-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.Sql):* Algemeen beschikbaar in Azure-regio's waar databaseservice beschikbaar is.
- **[Azure Database for MariaDB](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-vnet)** (*Microsoft.Sql):* Algemeen beschikbaar in Azure-regio's waar databaseservice beschikbaar is.
- **[Azure Cosmos DB](../cosmos-db/vnet-service-endpoint.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.AzureCosmosDB):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure Key Vault](../key-vault/key-vault-overview-vnet-service-endpoints.md)** *(Microsoft.KeyVault):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure Service Bus](../service-bus-messaging/service-bus-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.ServiceBus):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure Event Hubs](../event-hubs/event-hubs-service-endpoints.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** (*Microsoft.EventHub):* Algemeen beschikbaar in alle Azure-regio's.
- **[Azure Data Lake Store Gen 1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)** *(Microsoft.AzureActiveDirectory):* Algemeen beschikbaar in alle Azure-regio's waar ADLS Gen1 beschikbaar is.
- **[Azure App Service:](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions)** algemeen beschikbaar in alle Azure-regio's waar de App-service beschikbaar is.

**Openbare preview**

- **[Azure Container Registry](../container-registry/container-registry-vnet.md)** *(Microsoft.ContainerRegistry):* Voorbeeld beschikbaar in alle Azure-regio's waar Azure Container Registry beschikbaar is.

Voor recente updates kijkt u op de pagina [Azure Virtual Network Updates](https://azure.microsoft.com/updates/?product=virtual-network) (Updates voor Azure Virtual Network).

## <a name="key-benefits"></a>Belangrijkste voordelen

Service-eindpunten bieden de volgende voordelen:

- **Verbeterde beveiliging voor uw Azure-serviceresources:** vnet-privéadresruimten kunnen elkaar overlappen. U overlappende spaties niet gebruiken om verkeer dat afkomstig is van uw VNet, uniek te identificeren. Serviceeindpunten bieden de mogelijkheid om Azure-serviceresources te beveiligen voor uw virtuele netwerk door vnet-identiteit uit te breiden naar de service. Zodra u serviceeindpunten in uw virtuele netwerk inschakelt, u een virtuele netwerkregel toevoegen om de Azure-serviceresources aan uw virtuele netwerk te beveiligen. De regeltoevoeging biedt verbeterde beveiliging door openbare internettoegang tot bronnen volledig te verwijderen en alleen verkeer van uw virtuele netwerk toe te staan.
- **Optimale routering voor Azure-serviceverkeer vanuit uw virtuele netwerk:** Tegenwoordig dwingen alle routes in uw virtuele netwerk internetverkeer naar uw on-premises en/of virtuele apparaten ook Azure-serviceverkeer om dezelfde route te nemen als het internetverkeer. Service-eindpunten bieden een optimale routering voor Azure-verkeer. 

  Eindpunten zorgen er altijd voor dat serviceverkeer rechtstreeks van uw virtuele netwerk naar de service op het Microsoft Azure-backbone-netwerk gaat. Door het verkeer op het Azure-backbone-netwerk in stand te houden, kunt u het uitgaande internetverkeer vanuit uw virtuele netwerk blijven controleren en bewaken via geforceerd tunnelen, zonder dat dit van invloed is op het serviceverkeer. Zie Azure virtual network traffic routing voor meer informatie over door de gebruiker gedefinieerde routes en geforceerde [tunneling.](virtual-networks-udr-overview.md)
- **Eenvoudig te installeren met minder beheeroverhead**: u hebt geen gereserveerde, openbare IP-adressen meer nodig in uw virtuele netwerken om Azure-resources via een IP-firewall te beveiligen. Er zijn geen NAT-adresvertalingen (Network Address Translation) of gateway-apparaten vereist om de serviceeindpunten in te stellen. U serviceeindpunten configureren door simpelweg op een subnet te klikken. Er is geen extra overhead aan het onderhoud van de eindpunten.

## <a name="limitations"></a>Beperkingen

- De functie is alleen beschikbaar voor virtuele netwerken die zijn geïmplementeerd met behulp van het Azure Resource Manager-implementatiemodel.
- Eindpunten worden ingeschakeld in subnetten die zijn geconfigureerd in virtuele Azure-netwerken. Eindpunten kunnen niet worden gebruikt voor verkeer van uw lokalen naar Azure-services. Zie [Toegang tot Azure-services beveiligen vanaf on-premises](#secure-azure-services-to-virtual-networks) voor meer informatie
- Voor Azure SQL geldt een service-eindpunt alleen voor Azure-serviceverkeer binnen de regio van een virtueel netwerk. Voor Azure Storage worden eindpunten ook uitgebreid tot gekoppelde regio's waar u het virtuele netwerk implementeert ter ondersteuning van RA-GRS-verkeer (Read-Access Geo-Redundant Storage) en Geo Redundant Storage (GRS). Zie [Azure-gekoppelde regio's](../best-practices-availability-paired-regions.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-paired-regions)voor meer informatie .
- Voor ADLS Gen 1 (Azure Data Lake Storage) is de VNet-integratiemogelijkheid alleen beschikbaar voor virtuele netwerken binnen dezelfde regio. Houd er ook rekening mee dat virtuele netwerkintegratie voor ADLS Gen1 de endpoint-beveiliging van de virtuele netwerkservice tussen uw virtuele netwerk en Azure AD (Azure AD) gebruikt om extra beveiligingsclaims in het toegangstoken te genereren. Deze claims worden vervolgens gebruikt om het virtuele netwerk te verifiëren bij het Data Lake Storage Gen1-account en toegang toe te staan. De *Microsoft.AzureActiveDirectory-tag* die wordt vermeld onder serviceeindpunten voor services, wordt alleen gebruikt voor het ondersteunen van serviceeindpunten voor ADLS Gen 1. Azure AD ondersteunt serviceeindpunten niet native. Zie [Netwerkbeveiliging in Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over Azure Data Lake Store Gen 1 VNet-integratie.

## <a name="secure-azure-services-to-virtual-networks"></a>Azure-services beveiligen voor virtuele netwerken

- Een service-eindpunt voor een virtueel netwerk biedt de identiteit van het virtuele netwerk voor de Azure-service. Zodra u serviceeindpunten in uw virtuele netwerk inschakelt, u een virtuele netwerkregel toevoegen om de Azure-serviceresources aan uw virtuele netwerk te beveiligen.
- Tegenwoordig maakt Azure-serviceverkeer vanaf een virtueel netwerk gebruik van openbare IP-adressen als IP-bronadressen. Bij gebruik van service-eindpunten schakelt serviceverkeer over op het gebruik van privéadressen van virtuele netwerken als bron-IP-adressen bij het toegang krijgen tot de Azure-service vanuit een virtueel netwerk. Hierdoor hebt u toegang tot de services zonder dat u gereserveerde openbare IP-adressen nodig hebt die worden gebruikt in IP-firewalls.

   >[!NOTE]
   > Met service-eindpunten worden de IP-bronadressen van de virtuele machines in het subnet voor serviceverkeer overgeschakeld van het gebruik van openbare IPv4-adressen naar IPv4-privéadressen. Bestaande firewallregels voor Azure-service die gebruikmaken van openbare IP-adressen werken na deze overschakeling niet meer. Zorg ervoor dat deze overschakeling is toegestaan op basis van de firewallregels voor Azure-service vóórdat u de service-eindpunten instelt. Er kan ook een tijdelijke onderbreking in het serviceverkeer van dit subnet optreden tijdens het configureren van de service-eindpunten. 
 
## <a name="secure-azure-service-access-from-on-premises"></a>Azure-servicetoegang beveiligen vanuit on-premises

  Azure-serviceresources die zijn beveiligd met virtuele netwerken, zijn standaard niet bereikbaar via on-premises netwerken. Als u verkeer van on-premises wilt toestaan, moet u ook openbare (meestal NAT)IP-adressen toestaan vanuit uw on-premises of ExpressRoute. U deze IP-adressen toevoegen via de IP-firewallconfiguratie voor Azure-servicebronnen.

  ExpressRoute: Als u [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruikt voor openbare peering of Microsoft-peering vanuit uw locatie, moet u de NAT IP-adressen identificeren die u gebruikt. Voor openbaar peering gebruikt elk ExpressRoute-circuit standaard twee NAT-IP-adressen die worden toegepast op Azure-serviceverkeer wanneer het verkeer de Microsoft Azure-netwerkbackbone binnenkomt. Voor Microsoft-peering zijn de NAT-IP-adressen de klant die door de serviceprovider wordt verstrekt of verstrekt.Voor toegang tot uw serviceresources moet u deze openbare IP-adressen toestaan in de instelling voor IP-firewall voor de resource.Wanneer u op zoek bent naar de IP-adressen van uw ExpressRoute-circuit voor openbare peering, opent u [een ondersteuningsticket met ExpressRoute](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) via de Azure-portal. Zie [ExpressRoute NAT-vereisten](../expressroute/expressroute-nat.md?toc=%2fazure%2fvirtual-network%2ftoc.json#nat-requirements-for-azure-public-peering)voor meer informatie over NAT voor Openbaar ExpressRoute en Microsoft-peering.

![Azure-services aan virtuele netwerken koppelen](./media/virtual-network-service-endpoints-overview/VNet_Service_Endpoints_Overview.png)

### <a name="configuration"></a>Configuratie

- Serviceeindpunten configureren op een subnet in een virtueel netwerk. Eindpunten werken met alle soorten rekenprocessen die worden uitgevoerd in dat subnet.
- U meerdere serviceeindpunten configureren voor alle ondersteunde Azure-services (bijvoorbeeld Azure Storage of Azure SQL Database) op een subnet.
- Voor Azure SQL Database moeten virtuele netwerken zich in dezelfde regio bevinden als de Azure-serviceresource. Als u Azure Storage-accounts voor GRS en RA-GRS gebruikt, moet het hoofdaccount zich in dezelfde regio bevinden als het virtuele netwerk. Voor alle andere services u Azure-servicebronnen beveiligen voor virtuele netwerken in elke regio. 
- Het virtuele netwerk waar het eindpunt is geconfigureerd, kan zich in hetzelfde abonnement bevinden als de Azure-serviceresource, maar ook in een ander abonnement. Zie [Inrichten](#provisioning) voor meer informatie over de benodigde machtigingen voor het instellen van eindpunten en het koppelen van Azure-services.
- Voor ondersteunde services kunt u nieuwe of bestaande resources koppelen aan virtuele netwerken met behulp van service-eindpunten.

### <a name="considerations"></a>Overwegingen

- Nadat u een serviceeindpunt hebt inschakelt, worden de bron-IP-adressen van virtuele machines in de subnetschakelaar inschakelt. De bron-IP-adressen schakelen over van het gebruik van openbare IPv4-adressen naar het gebruik van hun privé-IPv4-adres bij het communiceren met de service vanaf dat subnet. Bestaande open TCP-verbindingen met de service worden hierbij gesloten. Zorg ervoor dat er geen kritieke taken worden uitgevoerd wanneer u een service-eindpunt voor een service voor een subnet in- of uitschakelt. Zorg er ook voor dat uw toepassingen automatisch verbinding kunnen maken met Azure-services nadat de wisseling van IP-adres heeft plaatsgevonden.

  Het wisselen van IP-adres heeft alleen gevolgen voor het serviceverkeer vanuit uw virtuele netwerk. Er is geen invloed op ander verkeer dat is gericht op of van de openbare IPv4-adressen die zijn toegewezen aan uw virtuele machines. Als u voor Azure-services bestaande firewallregels hebt waarin gebruik wordt gemaakt van openbare IP-adressen voor Azure, werken deze regels niet meer nadat is overgeschakeld op privéadressen van virtuele netwerken.
- Met serviceeindpunten blijven DNS-vermeldingen voor Azure-services zoals het nu is en blijven ze oplossen voor openbare IP-adressen die zijn toegewezen aan de Azure-service.

- Netwerkbeveiligingsgroepen (NSG's) met de service-eindpunten:
  - Standaard staan NSG's uitgaand internetverkeer toe en staan ze ook verkeer van uw VNet naar Azure-services toe. Dit verkeer blijft werken met service eindpunten zoals het is. 
  - Als u al het uitgaande internetverkeer wilt weigeren en alleen verkeer naar specifieke Azure-services wilt toestaan, u dit doen met behulp van [servicetags](security-overview.md#service-tags) in uw NMG's. U ondersteunde Azure-services opgeven als bestemming in uw NSG-regels en Azure biedt ook het onderhoud van IP-adressen die aan elke tag ten grondslag liggen. Zie [Azure-servicelabels voor NSG's](security-overview.md#service-tags) voor meer informatie. 

### <a name="scenarios"></a>Scenario's

- **Virtuele netwerken met peers, verbonden of meerdere virtuele netwerken**: als u Azure-services wilt koppelen aan meerdere subnetten binnen een virtueel netwerk of aan meerdere virtuele netwerken, kunt u in elk van de subnetten service-eindpunten inschakelen en de Azure-serviceresources vervolgens aan al deze subnetten koppelen.
- **Uitgaand verkeer filteren van een virtueel netwerk naar Azure-services:** Als u het verkeer dat naar een Azure-service wordt verzonden vanuit een virtueel netwerk wilt inspecteren of filteren, u een virtueel netwerktoestel implementeren in het virtuele netwerk. U kunt dan service-eindpunten toepassen op het subnet waarop het virtueel-netwerkapparaat is geïmplementeerd en Azure-serviceresources alleen koppelen aan dit subnet. Dit scenario kan handig zijn als u netwerkfiltering voor virtuele apparaten wilt gebruiken om azure-servicetoegang vanuit uw virtuele netwerk alleen te beperken tot specifieke Azure-bronnen. Zie [Egress with network virtual appliances](/azure/architecture/reference-architectures/dmz/nva-ha) (Uitgaand verkeer met virtueel-netwerkapparaten) voor meer informatie.
- **Azure-bronnen beveiligen voor services die rechtstreeks in virtuele netwerken**worden geïmplementeerd: u verschillende Azure-services rechtstreeks implementeren in specifieke subnetten in een virtueel netwerk. U kunt Azure-serviceresources koppelen aan subnetten voor [beheerde services](virtual-network-for-azure-services.md) door een service-eindpunt in te stellen in het subnet van deze beheerde services.
- **Schijfverkeer vanaf een virtuele Azure-machine:** Virtual Machine Disk-verkeer voor beheerde en onbeheerde schijven wordt niet beïnvloed door wijzigingen in de routeringslocatie van serviceeindpunten voor Azure Storage. Dit verkeer omvat diskIO evenals monteren en ontkoppelen. U resttoegang beperken tot paginablobs om netwerken te selecteren via serviceeindpunten en [Azure Storage-netwerkregels.](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 

### <a name="logging-and-troubleshooting"></a>Logboekregistratie en problemen oplossen

Zodra u serviceeindpunten configureert voor een specifieke service, valideert u dat de eindpuntroute van de service van kracht is door: 
 
- Valideer het IP-bronadres van een serviceaanvraag in servicediagnose. In alle nieuwe aanvragen met service-eindpunten wordt het IP-bronadres voor de aanvraag weergegeven als het privéadres van het virtuele netwerk, toegewezen aan de client die de aanvraag vanuit uw virtuele netwerk uitvoert. Zonder het eindpunt is het adres een openbaar IP-adres van Azure.
- Het weergeven van de effectieve routes in een netwerkinterface in een subnet. De route naar de service:
  - Toont een meer specifieke standaardroute naar de adresvoorvoegselbereiken van elke service
  - Heeft een nextHopType van *VirtualNetworkServiceEndpoint*
  - Geeft aan dat er een directere verbinding met de service van kracht is in vergelijking met alle geforceerde tunnelroutes

>[!NOTE]
> Service-eindpuntroute overschrijft BGP- of UDR-routes voor de overeenkomst met het adresvoorvoegsel van een Azure-service. Zie [probleemoplossing met effectieve routes](diagnose-network-routing-problem.md)voor meer informatie.

## <a name="provisioning"></a>Inrichten

Serviceeindpunten kunnen onafhankelijk van elkaar op virtuele netwerken worden geconfigureerd door een gebruiker met schrijftoegang tot een virtueel netwerk. Als u Azure-serviceresources wilt beveiligen voor een VNet, moet de gebruiker toestemming hebben voor *Microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/action* voor de toegevoegde subnetten. De ingebouwde functierollen voor servicebeheerders bevatten deze standaardmachtiging. U de machtiging wijzigen door aangepaste rollen te maken.

Zie [Ingebouwde rollen voor Azure-resources voor](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over ingebouwde rollen. Zie [Aangepaste rollen voor Azure-resources voor](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie over het toewijzen van specifieke machtigingen aan aangepaste rollen.

Virtuele netwerken en Azure-serviceresources kunnen binnen hetzelfde abonnement of in verschillende abonnementen aanwezig zijn. Als de virtuele netwerken en Azure-serviceresources in verschillende abonnementen aanwezig zijn, moeten de resources onder dezelfde Active Directory-tenant (AD) vallen. 

## <a name="pricing-and-limits"></a>Prijzen en beperkingen

Er zijn geen extra kosten verbonden aan het gebruik van serviceeindpunten. Het huidige prijsmodel voor Azure-services (Azure Storage, Azure SQL Database, enz.) is van toepassing zoals het nu is.

Er is geen limiet voor het totale aantal serviceeindpunten in een virtueel netwerk.

Bepaalde Azure-services, zoals Azure Storage Accounts, kunnen limieten afdwingen voor het aantal subnetten dat wordt gebruikt voor het beveiligen van de bron. Raadpleeg de documentatie voor verschillende services in de sectie [Volgende stappen](#next-steps) voor meer informatie.

## <a name="vnet-service-endpoint-policies"></a>VNet-serviceeindpuntbeleid 

Met het eindpuntbeleid van vNet-service u virtueel netwerkverkeer filteren op Azure-services. Met dit filter staan alleen specifieke Azure-serviceresources toe voor serviceeindpunten. Beleid voor service-eindpunten zorgt voor nauwkeurig toegangsbeheer voor verkeer van Virtual Network naar Azure-services. Zie [Eindpuntbeleid voor virtual network service voor](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)meer informatie .

## <a name="faqs"></a>Veelgestelde vragen

Zie Veelgestelde vragen voor veelgestelde vragen voor veelgestelde vragen over het eindpunt van de service voor [veelgestelde vragen.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-faq#virtual-network-service-endpoints)

## <a name="next-steps"></a>Volgende stappen

- [Eindpunten voor virtuele netwerkservice configureren](tutorial-restrict-network-access-to-resources.md)
- [Een Azure Storage-account beveiligen naar een virtueel netwerk](../storage/common/storage-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Een Azure SQL-database beveiligen naar een virtueel netwerk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Een Azure SQL Data Warehouse beveiligen naar een virtueel netwerk](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md?toc=%2fazure%2fsql-data-warehouse%2ftoc.json)
- [Azure-serviceintegratie in virtuele netwerken](virtual-network-for-azure-services.md)
- [Eindpuntbeleid voor virtuele netwerkservice](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoint-policies-overview)
- [Azure Resource Manager-sjabloon](https://azure.microsoft.com/resources/templates/201-vnet-2subnets-service-endpoints-storage-integration)


---
title: VNet-serviceeindpunten - Azure Database voor MariaDB
description: Beschrijft hoe VNet-serviceeindpunten werken voor uw Azure Database voor MariaDB-server.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 54379c65850fa210e5523b53a64fe89705ed1f15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532107"
---
# <a name="use-virtual-network-service-endpoints-and-rules-for-azure-database-for-mariadb"></a>Service-eindpunten en -regels voor virtuele netwerken gebruiken voor Azure Database for MariaDB

*Virtuele netwerkregels* zijn een beveiligingsfunctie voor firewalls die bepaalt of uw Azure Database voor MariaDB-server communicatie accepteert die wordt verzonden vanuit bepaalde subnetten in virtuele netwerken. In dit artikel wordt uitgelegd waarom de functie voor virtuele netwerkregel soms de beste optie is om veilig communicatie toe te staan naar uw Azure Database voor MariaDB-server.

Als u een virtuele netwerkregel wilt maken, moet er eerst een [virtual network][vm-virtual-network-overview] (VNet) en een eindpunt voor [virtuele netwerkservice][vm-virtual-network-service-endpoints-overview-649d] zijn waar naar verwezen moet worden. In de volgende afbeelding ziet u hoe een eindpunt van een virtual network-service werkt met Azure Database voor MariaDB:

![Voorbeeld van hoe een VNet-serviceeindpunt werkt](media/concepts-data-access-security-vnet/vnet-concept.png)

> [!NOTE]
> Deze functie is beschikbaar in alle regio's van Azure waar Azure Database voor MariaDB is geïmplementeerd voor servers voor algemeen gebruik en geheugengeoptimaliseerd.

<a name="anch-terminology-and-description-82f" />

## <a name="terminology-and-description"></a>Terminologie en beschrijving

**Virtueel netwerk:** U virtuele netwerken aan uw Azure-abonnement laten gekoppeld.

**Subnet:** Een virtueel netwerk bevat **subnetten.** Alle Azure virtuele machines (VM's) die u hebt toegewezen aan subnetten. Eén subnet kan meerdere VM's of andere compute nodes bevatten. Compute-knooppunten die zich buiten uw virtuele netwerk bevinden, hebben geen toegang tot uw virtuele netwerk, tenzij u uw beveiliging configureert om toegang toe te staan.

**Eindpunt van de virtuele netwerkservice:** Een [eindpunt van de service voor virtuele netwerken][vm-virtual-network-service-endpoints-overview-649d] is een subnet waarvan de eigenschapswaarden een of meer formele namen van azure-servicetypen bevatten. In dit artikel zijn we geïnteresseerd in de typenaam van **Microsoft.Sql**, die verwijst naar de Azure-service met de naam SQL Database. Deze servicetag is ook van toepassing op de Azure Database voor MariaDB-, MySQL- en PostgreSQL-services. Het is belangrijk op te merken dat bij het toepassen van de **Microsoft.Sql-servicetag** op een VNet-serviceeindpunt serviceeindpunt wordt geconfigureerd voor alle Azure SQL Database, Azure Database voor MariaDB, Azure Database voor MySQL en Azure Database voor PostgreSQL-servers op het subnet.

**Virtuele netwerkregel:** Een virtuele netwerkregel voor uw Azure Database voor MariaDB-server is een subnet dat wordt vermeld in de lijst met toegangscontrole (ACL) van uw Azure Database voor MariaDB-server. Als u zich in de ACL voor uw Azure-database voor MariaDB-server wilt bevinden, moet het subnet de **typenaam Microsoft.Sql** bevatten.

Een virtuele netwerkregel vertelt uw Azure Database voor MariaDB-server om communicatie te accepteren van elk knooppunt dat zich op het subnet bevindt.







<a name="anch-benefits-of-a-vnet-rule-68b" />

## <a name="benefits-of-a-virtual-network-rule"></a>Voordelen van een virtuele netwerkregel

Totdat u actie onderneemt, kunnen de VM's op uw subnetten niet communiceren met uw Azure Database voor MariaDB-server. Een actie die de communicatie vaststelt is het creëren van een virtuele netwerkregel. De reden voor het kiezen van de VNet-regelbenadering vereist een vergelijkingsdiscussie met betrekking tot de concurrerende beveiligingsopties die door de firewall worden aangeboden.

### <a name="a-allow-access-to-azure-services"></a>A. Toegang tot Azure-services toestaan

Het beveiligingsdeelvenster Verbinding heeft een **aan/uit-knop** met het label **Toegang tot Azure-services toestaan.** Met de instelling **AAN** u communicatie van alle Azure IP-adressen en alle Azure-subnetten gebruiken. Deze Azure IP's of subnetten zijn mogelijk niet eigendom van u. Deze **ON-instelling** is waarschijnlijk beter geopend dan u wilt dat uw Azure Database voor MariaDB-database wordt. De functie voor virtuele netwerkregel biedt veel fijnere gedetailleerde controle.

### <a name="b-ip-rules"></a>B. IP-regels

Met de Azure Database for MariaDB-firewall u IP-adresbereiken opgeven waaruit communicatie wordt geaccepteerd in de Azure-database voor MariaDB-server. Deze aanpak is prima voor stabiele IP-adressen die zich buiten het Azure-privénetwerk bevinden. Maar veel knooppunten binnen het Azure-privénetwerk zijn geconfigureerd met *dynamische* IP-adressen. Dynamische IP-adressen kunnen veranderen, bijvoorbeeld wanneer uw vm opnieuw wordt opgestart. Het zou dwaasheid zijn om een dynamisch IP-adres op te geven in een firewallregel, in een productieomgeving.

U de IP-optie redden door een *statisch* IP-adres voor uw VM te verkrijgen. Zie [Privé-IP-adressen configureren voor een virtuele machine voor][vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]meer informatie met behulp van de Azure-portal.

Echter, de statische IP-aanpak kan moeilijk te beheren, en het is duur wanneer gedaan op schaal. Virtuele netwerkregels zijn eenvoudiger vast te stellen en te beheren.

### <a name="c-cannot-yet-have-azure-database-for-mariadb-on-a-subnet-without-defining-a-service-endpoint"></a>C. Kan Azure Database voor MariaDB nog niet op een subnet hebben zonder een serviceeindpunt te definiëren

Als uw **Microsoft.Sql-server** een knooppunt was op een subnet in uw virtuele netwerk, kunnen alle knooppunten binnen het virtuele netwerk communiceren met uw Azure Database voor MariaDB-server. In dit geval kunnen uw VM's communiceren met Azure Database voor MariaDB zonder dat er virtuele netwerkregels of IP-regels nodig zijn.

Vanaf augustus 2018 behoort de Azure Database for MariaDB-service echter nog niet tot de services die rechtstreeks aan een subnet kunnen worden toegewezen.

<a name="anch-details-about-vnet-rules-38q" />

## <a name="details-about-virtual-network-rules"></a>Details over virtuele netwerkregels

In deze sectie worden verschillende details over virtuele netwerkregels beschreven.

### <a name="only-one-geographic-region"></a>Slechts één geografische regio

Elk eindpunt van de service voor virtuele netwerken is van toepassing op slechts één Azure-regio. Het eindpunt stelt andere regio's niet in staat om communicatie van het subnet te accepteren.

Elke virtuele netwerkregel is beperkt tot de regio waarop het onderliggende eindpunt van toepassing is.

### <a name="server-level-not-database-level"></a>Serverniveau, niet databaseniveau

Elke virtuele netwerkregel is van toepassing op uw hele Azure Database voor MariaDB-server, niet alleen op één bepaalde database op de server. Met andere woorden, virtuele netwerkregel is van toepassing op serverniveau, niet op databaseniveau.

### <a name="security-administration-roles"></a>Rollen beveiligingsbeheer

Er is een scheiding van beveiligingsrollen in het beheer van eindpunten van de Virtual Network-service. Actie is vereist vanuit elk van de volgende rollen:

- **Netwerkbeheerder:** &nbsp; schakel het eindpunt in.
- **Databasebeheerder:** &nbsp; werk de lijst met toegangscontrole (ACL) bij om het opgegeven subnet toe te voegen aan de Azure Database voor MariaDB-server.

*RBAC-alternatief:*

De rollen van netwerkbeheerder en databasebeheerder hebben meer mogelijkheden dan nodig zijn om virtuele netwerkregels te beheren. Slechts een subset van hun mogelijkheden is nodig.

U hebt de mogelijkheid om [RBAC (Role-based access control)][rbac-what-is-813s] in Azure te gebruiken om één aangepaste rol te maken die alleen de benodigde subset van mogelijkheden heeft. De aangepaste rol kan worden gebruikt in plaats van de netwerkbeheerder of de databasebeheerder. Het oppervlak van uw beveiligingsbelichting is lager als u een gebruiker toevoegt aan een aangepaste rol, versus de gebruiker toe te voegen aan de andere twee belangrijke beheerdersrollen.

> [!NOTE]
> In sommige gevallen bevinden de Azure Database voor MariaDB en het VNet-subnet zich in verschillende abonnementen. In deze gevallen moet u zorgen voor de volgende configuraties:
> - Beide abonnementen moeten zich in dezelfde Azure Active Directory-tenant bevinden.
> - De gebruiker heeft de vereiste machtigingen om bewerkingen te starten, zoals het inschakelen van serviceeindpunten en het toevoegen van een VNet-subnet aan de opgegeven server.
> - Zorg ervoor dat zowel het abonnement de **Microsoft.Sql-resourceprovider** heeft geregistreerd. Voor meer informatie verwijzen [resource-manager-registratie][resource-manager-portal]

## <a name="limitations"></a>Beperkingen

Voor Azure Database voor MariaDB heeft de functie virtuele netwerkregels de volgende beperkingen:

- Een web-app kan worden toegewezen aan een privé-IP in een VNet/subnet. Zelfs als serviceeindpunten zijn ingeschakeld vanaf het opgegeven VNet/subnet, hebben verbindingen van de web-app naar de server een openbare IP-bron van Azure, geen VNet/subnetbron. Als u de verbinding wilt inschakelen van een web-app met een server met VNet-firewallregels, moet u Azure-services toegang geven tot de server op de server.

- In de firewall voor uw Azure Database voor MariaDB verwijst elke virtuele netwerkregel naar een subnet. Al deze subnetten waarnaar wordt verwezen, moeten worden gehost in dezelfde geografische regio als de Azure-database voor MariaDB.

- Elke Azure-database voor MariaDB-server kan maximaal 128 ACL-vermeldingen hebben voor een bepaald virtueel netwerk.

- Virtuele netwerkregels zijn alleen van toepassing op virtuele azure-netwerken voor Azure Resource Manager; en niet aan [klassieke implementatiemodelnetwerken.][resource-manager-deployment-model-568f]

- Als u eindpunten voor virtuele netwerkservices inschakelt in Azure Database voor MariaDB met behulp van de **Microsoft.Sql-servicetag,** worden ook de eindpunten voor alle Azure Database-services ingeschakeld: Azure Database for MariaDB, Azure Database for MySQL, Azure Database en Azure SQL Data Warehouse.

- Ondersteuning voor VNet-serviceeindpunten is alleen voor servers met algemeen gebruik en geheugengeoptimaliseerd.

- Op de firewall zijn IP-adresbereiken wel van toepassing op de volgende netwerkitems, maar virtuele netwerkregels niet:
    - [Site-to-Site (S2S) virtual private network (VPN)][vpn-gateway-indexmd-608y]
    - On-premises via [ExpressRoute][expressroute-indexmd-744v]

## <a name="expressroute"></a>ExpressRoute

Als uw netwerk is verbonden met het Azure-netwerk via het gebruik van [ExpressRoute,][expressroute-indexmd-744v]is elk circuit geconfigureerd met twee openbare IP-adressen op de Microsoft Edge. De twee IP-adressen worden gebruikt om verbinding te maken met Microsoft Services, zoals azure storage, met azure public peering.

Als u communicatie van uw circuit naar Azure Database voor MariaDB wilt toestaan, moet u IP-netwerkregels maken voor de openbare IP-adressen van uw circuits. Open een ondersteuningsticket met ExpressRoute via de Azure-portal om de openbare IP-adressen van uw ExpressRoute-circuit te vinden.

## <a name="adding-a-vnet-firewall-rule-to-your-server-without-turning-on-vnet-service-endpoints"></a>Een VNET Firewall-regel toevoegen aan uw server zonder VNET-serviceeindpunten in te schakelen

Alleen het instellen van een Firewall-regel helpt niet om de server te beveiligen naar het VNet. U moet ook VNet-serviceeindpunten **inschakelen** om de beveiliging van kracht te laten worden. Wanneer u serviceeindpunten **inschakelt,** ervaart uw VNet-subnet downtime totdat de overgang van **Aan** naar **Aan**is voltooid. Dit geldt vooral in de context van grote VNets. U de **ignoremissingserviceendpuntvlag** gebruiken om de downtime tijdens de overgang te verminderen of te elimineren.

U de **ignoremissingserviceendpuntvlag** instellen met behulp van de Azure CLI of -portal.

## <a name="related-articles"></a>Verwante artikelen:
- [Virtuele Azure-netwerken][vm-virtual-network-overview]
- [Service-eindpunten voor een virtueel Azure-netwerk][vm-virtual-network-service-endpoints-overview-649d]

## <a name="next-steps"></a>Volgende stappen
Zie voor artikelen over het maken van VNet-regels:
- [Azure Database voor MariaDB VNet-regels maken en beheren met behulp van de Azure-portal](howto-manage-vnet-portal.md)
 
<!--
- [Create and manage Azure Database for MariaDB VNet rules using Azure CLI](howto-manage-vnet-using-cli.md)
-->

<!-- Link references, to text, Within this same GitHub repo. -->
[resource-manager-deployment-model-568f]: ../azure-resource-manager/management/deployment-models.md

[vm-virtual-network-overview]: ../virtual-network/virtual-networks-overview.md

[vm-virtual-network-service-endpoints-overview-649d]: ../virtual-network/virtual-network-service-endpoints-overview.md

[vm-configure-private-ip-addresses-for-a-virtual-machine-using-the-azure-portal-321w]: ../virtual-network/virtual-networks-static-private-ip-arm-pportal.md

[rbac-what-is-813s]: ../active-directory/role-based-access-control-what-is.md

[vpn-gateway-indexmd-608y]: ../vpn-gateway/index.yml

[expressroute-indexmd-744v]: ../expressroute/index.yml

[resource-manager-portal]: ../azure-resource-manager/management/resource-providers-and-types.md

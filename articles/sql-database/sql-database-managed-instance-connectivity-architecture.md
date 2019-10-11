---
title: Connectiviteits architectuur voor een beheerd exemplaar in Azure SQL Database | Microsoft Docs
description: Meer informatie over Azure SQL Database beheerde instantie communicatie en connectiviteits architectuur, evenals hoe de onderdelen direct verkeer naar het beheerde exemplaar.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 04/16/2019
ms.openlocfilehash: 7e32cb302322f7a80154a3f2a246d7d4f1743c09
ms.sourcegitcommit: 961468fa0cfe650dc1bec87e032e648486f67651
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/10/2019
ms.locfileid: "72249374"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Connectiviteits architectuur voor een beheerd exemplaar in Azure SQL Database

In dit artikel wordt de communicatie in een Azure SQL Database beheerd exemplaar uitgelegd. Ook wordt de connectiviteits architectuur beschreven en hoe de onderdelen direct verkeer naar het beheerde exemplaar.  

Het beheerde exemplaar van SQL Database wordt in het virtuele Azure-netwerk geplaatst en het subnet dat is toegewezen aan beheerde exemplaren. Deze implementatie biedt:

- Een beveiligd privé-IP-adres.
- De mogelijkheid om een on-premises netwerk te verbinden met een beheerd exemplaar.
- De mogelijkheid om een beheerd exemplaar te verbinden met een gekoppelde server of een andere on-premises gegevens opslag.
- De mogelijkheid om een beheerd exemplaar te verbinden met Azure-resources.

## <a name="communication-overview"></a>Communicatie overzicht

In het volgende diagram ziet u entiteiten die verbinding maken met een beheerd exemplaar. Ook worden de bronnen weer gegeven die moeten communiceren met het beheerde exemplaar. Het communicatie proces aan de onderkant van het diagram vertegenwoordigt klant toepassingen en hulpprogram ma's die verbinding maken met het beheerde exemplaar als gegevens bronnen.  

![Entiteiten in connectiviteits architectuur](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Een beheerd exemplaar is een PaaS-aanbieding (platform as a Service). Micro soft maakt gebruik van geautomatiseerde agents (beheer, implementatie en onderhoud) voor het beheren van deze service op basis van telemetriegegevens. Omdat micro soft verantwoordelijk is voor het beheer, hebben klanten geen toegang tot de virtuele cluster machines van het beheerde exemplaar via Remote Desktop Protocol (RDP).

Voor sommige SQL Server bewerkingen die door eind gebruikers of toepassingen worden gestart, is het mogelijk dat beheerde exemplaren moeten communiceren met het platform. Een voor beeld is het maken van een Data Base met beheerde exemplaren. Deze resource wordt weer gegeven via de Azure Portal, Power shell, Azure CLI en de REST API.

Beheerde exemplaren zijn afhankelijk van Azure-Services, zoals Azure Storage voor back-ups, Azure Event Hubs voor telemetrie, Azure Active Directory voor verificatie Azure Key Vault voor Transparent Data Encryption (TDE) en een aantal Azure-platform services die bieden functies voor beveiliging en ondersteuning. Met de beheerde instanties worden verbindingen met deze services gemaakt.

Alle communicaties zijn versleuteld en ondertekend met behulp van certificaten. Voor het controleren van de betrouw baarheid van communicerende partijen verifiëren beheerde instanties deze certificaten voortdurend via certificaatintrekkingslijsten. Als de certificaten zijn ingetrokken, worden de verbindingen met het beheerde exemplaar gesloten om de gegevens te beveiligen.

## <a name="high-level-connectivity-architecture"></a>Connectiviteits architectuur op hoog niveau

Op hoog niveau is een beheerd exemplaar een set service onderdelen. Deze onderdelen worden gehost op een speciale set geïsoleerde virtuele machines die worden uitgevoerd in het subnet van het virtuele netwerk van de klant. Deze machines vormen een virtueel cluster.

Een virtueel cluster kan meerdere beheerde instanties hosten. Als dat het geval is, wordt het cluster automatisch uitgebreid of gecontracteerd wanneer de klant het aantal ingerichte instanties in het subnet wijzigt.

Klant toepassingen kunnen verbinding maken met beheerde instanties en data bases in het virtuele netwerk, het gekoppelde virtuele netwerk of het netwerk dat is verbonden via VPN of Azure ExpressRoute kunnen opvragen en bijwerken. Dit netwerk moet een eind punt en een privé-IP-adres gebruiken.  

![Diagram van connectiviteits architectuur](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Micro soft Management and Deployment Services worden uitgevoerd buiten het virtuele netwerk. Een beheerd exemplaar en micro soft-Services maken verbinding via de eind punten die een openbaar IP-adres hebben. Wanneer een beheerd exemplaar een uitgaande verbinding maakt, ziet de verbinding bij het ontvangen van het eind netwerk adres omzetting (NAT) dat deze is afkomstig van dit open bare IP-adres.

Beheer verkeer loopt door het virtuele netwerk van de klant. Dit betekent dat elementen van de infra structuur van het virtuele netwerk het beheer verkeer kunnen schaden door te zorgen dat het exemplaar mislukt en niet meer beschikbaar is.

> [!IMPORTANT]
> Ter verbetering van de gebruikers ervaring en de beschik baarheid van de service, past micro soft een netwerk intentie beleid toe op infra structuur-elementen van Azure Virtual Network. Het beleid kan invloed hebben op de werking van het beheerde exemplaar. Dit platform mechanisme doorloopt transparante netwerk vereisten voor gebruikers. Het belangrijkste doel van het beleid is om te voor komen dat de netwerk configuratie niet juist is geconfigureerd en om te zorgen voor normale beheerde instantie bewerkingen. Wanneer u een beheerd exemplaar verwijdert, wordt het beleid voor netwerk intentie ook verwijderd.

## <a name="virtual-cluster-connectivity-architecture"></a>Architectuur van Virtual cluster-connectiviteit

Laten we een diep gaande van de connectiviteits architectuur voor beheerde instanties volgen. Het volgende diagram toont de conceptuele indeling van het virtuele cluster.

![Connectiviteits architectuur van het virtuele cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Clients maken verbinding met een beheerd exemplaar met behulp van een hostnaam met de notatie `<mi_name>.<dns_zone>.database.windows.net`. Deze hostnaam wordt omgezet in een privé-IP-adres, maar is geregistreerd in een open bare Domain Name System (DNS)-zone en kan openbaar worden omgezet. De `zone-id` wordt automatisch gegenereerd wanneer u het cluster maakt. Als een nieuw cluster als host fungeert voor een secundaire beheerde instantie, wordt de zone-ID gedeeld met het primaire cluster. Zie voor meer informatie [automatische failover-groepen gebruiken om transparante en gecoördineerde failover van meerdere data bases in te scha kelen](sql-database-auto-failover-group.md##enabling-geo-replication-between-managed-instances-and-their-vnets).

Dit privé-IP-adres maakt deel uit van de interne load balancer van het beheerde exemplaar. De load balancer stuurt verkeer naar de gateway van het beheerde exemplaar. Omdat meerdere beheerde instanties binnen hetzelfde cluster kunnen worden uitgevoerd, gebruikt de gateway de hostnaam van het beheerde exemplaar om verkeer om te leiden naar de juiste SQL engine-service.

Beheer-en implementatie services maken verbinding met een beheerd exemplaar met behulp van een [beheer eindpunt](#management-endpoint) dat is toegewezen aan een externe Load Balancer. Verkeer wordt alleen gerouteerd naar de knoop punten als het wordt ontvangen op een vooraf gedefinieerde set poorten die alleen worden gebruikt door de beheer onderdelen van het beheerde exemplaar. Een ingebouwde firewall op de knoop punten is ingesteld om alleen verkeer vanaf micro soft IP-bereiken toe te staan. Certificaten verifiëren wederzijds alle communicatie tussen beheer onderdelen en het beheer vlak.

## <a name="management-endpoint"></a>Beheereindpunt

Micro soft beheert het beheerde exemplaar met behulp van een beheer eindpunt. Dit eind punt bevindt zich in het virtuele cluster van het exemplaar. Het beheer eindpunt wordt beveiligd door een ingebouwde firewall op het netwerk niveau. Op toepassings niveau wordt het beveiligd door wederzijdse verificatie van certificaten. Zie [het IP-adres van het beheer eindpunt bepalen](sql-database-managed-instance-find-management-endpoint-ip-address.md)om het IP-adres van het eind punt te vinden.

Wanneer verbindingen worden gestart binnen het beheerde exemplaar (net als bij back-ups en audit Logboeken), lijkt het verkeer te starten vanuit het open bare IP-adres van het beheer eindpunt. U kunt de toegang tot open bare Services vanuit een beheerd exemplaar beperken door firewall regels in te stellen zodat alleen het IP-adres van het beheerde exemplaar is toegestaan. Zie [de ingebouwde firewall van het beheerde exemplaar controleren](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)voor meer informatie.

> [!NOTE]
> Verkeer dat wordt verplaatst naar Azure-Services die zich binnen de regio van het beheerde exemplaar bevinden, is geoptimaliseerd en daarom niet is genateeerd naar een beheerd exemplaar beheer eindpunt openbaar IP-adres. Om die reden als u op IP gebaseerde firewall regels wilt gebruiken, moet de service meestal worden gebruikt in een andere regio dan een beheerd exemplaar.

## <a name="network-requirements"></a>Netwerkvereisten

Implementeer een beheerd exemplaar in een toegewezen subnet in het virtuele netwerk. Het subnet moet de volgende eigenschappen hebben:

- **Toegewezen subnet:** Het subnet van het beheerde exemplaar kan geen andere Cloud service bevatten die eraan is gekoppeld en kan geen gateway-subnet zijn. Het subnet kan geen resource bevatten, maar het beheerde exemplaar, en u kunt later geen andere typen resources toevoegen in het subnet.
- **Netwerk beveiligings groep (NSG):** Een NSG die aan het virtuele netwerk is gekoppeld, moet regels voor [inkomende beveiliging](#mandatory-inbound-security-rules) en [uitgaande beveiligings regels](#mandatory-outbound-security-rules) vóór andere regels definiëren. U kunt een NSG gebruiken om de toegang tot het gegevens eindpunt van het beheerde exemplaar te beheren door verkeer te filteren op poort 1433 en poorten 11000-11999 wanneer het beheerde exemplaar is geconfigureerd voor omleidings verbindingen.
- Door de **gebruiker gedefinieerde route tabel (UDR):** Een UDR-tabel die aan het virtuele netwerk is gekoppeld, moet specifieke [vermeldingen](#user-defined-routes)bevatten.
- **Geen service-eind punten:** Er moet geen service-eind punt zijn gekoppeld aan het subnet van het beheerde exemplaar. Zorg ervoor dat de optie service-eind punten is uitgeschakeld tijdens het maken van het virtuele netwerk.
- **Voldoende IP-adressen:** Het subnet van het beheerde exemplaar moet ten minste 16 IP-adressen hebben. De aanbevolen minimum waarde is 32 IP-adressen. Zie [de grootte van het subnet voor beheerde instanties bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md)voor meer informatie. U kunt beheerde exemplaren in [het bestaande netwerk](sql-database-managed-instance-configure-vnet-subnet.md) implementeren nadat u deze hebt geconfigureerd om te voldoen aan [de netwerk vereisten voor beheerde exemplaren](#network-requirements). Als dat niet het geval is, maakt u een [nieuw netwerk en subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> U kunt geen nieuw beheerd exemplaar implementeren als het doel-subnet deze kenmerken niet heeft. Wanneer u een beheerd exemplaar maakt, wordt een netwerk intentie beleid toegepast op het subnet om niet-compatibele wijzigingen in de netwerk installatie te voor komen. Nadat het laatste exemplaar van het subnet is verwijderd, wordt het netwerkintentiebeleid ook verwijderd.

### <a name="mandatory-inbound-security-rules"></a>Verplichte regels voor binnenkomende beveiliging

| Naam       |Port                        |Protocol|Bron           |Bestemming|Bewerking|
|------------|----------------------------|--------|-----------------|-----------|------|
|beheer  |9000, 9003, 1438, 1440, 1452|TCP     |Alle              |MI-SUBNET  |Toestaan |
|mi_subnet   |Alle                         |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |
|health_probe|Alle                         |Alle     |AzureLoadBalancer|MI-SUBNET  |Toestaan |

### <a name="mandatory-outbound-security-rules"></a>Verplichte uitgaande beveiligings regels

| Naam       |Port          |Protocol|Bron           |Bestemming|Bewerking|
|------------|--------------|--------|-----------------|-----------|------|
|beheer  |443, 12000    |TCP     |MI-SUBNET        |AzureCloud |Toestaan |
|mi_subnet   |Alle           |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |

> [!IMPORTANT]
> Zorg ervoor dat er slechts één binnenkomende regel is voor poorten 9000, 9003, 1438, 1440, 1452 en één uitgaande regel voor poorten 80, 443, 12000. Het inrichten van beheerde instanties via Azure Resource Manager implementaties mislukt als de regels voor binnenkomend en uitgaand verkeer voor elke poort afzonderlijk zijn geconfigureerd. Als deze poorten zich in afzonderlijke regels bevinden, mislukt de implementatie met fout code `VnetSubnetConflictWithIntendedPolicy`

\* MI-SUBNET verwijst naar het IP-adres bereik voor het subnet in de vorm 10. x. x. x/y. U kunt deze informatie vinden in het Azure Portal, in eigenschappen van subnet.

> [!IMPORTANT]
> Hoewel vereiste binnenkomende beveiligings regels verkeer toestaan van _een_ bron op poort 9000, 9003, 1438, 1440 en 1452, worden deze poorten beveiligd door een ingebouwde firewall. Zie [het adres van het beheer eindpunt bepalen](sql-database-managed-instance-find-management-endpoint-ip-address.md)voor meer informatie.
> [!NOTE]
> Als u transactionele replicatie in een beheerd exemplaar gebruikt en als u een exemplaar database als een uitgever of een distributeur gebruikt, opent u poort 445 (TCP uitgaand) in de beveiligings regels van het subnet. Deze poort biedt toegang tot de Azure-bestands share.

### <a name="user-defined-routes"></a>Door de gebruiker gedefinieerde routes

|Naam|Adresvoorvoegsel|Volgende hop|
|----|--------------|-------|
|subnet_to_vnetlocal|MI-SUBNET|Virtueel netwerk|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-96-13-nexthop-Internet|13.96.0.0/13|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|Mi-20-8-nexthop-Internet|20.0.0.0/8|Internet|
|Mi-23-96-13-nexthop-Internet|23.96.0.0/13|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-42-159-16-nexthop-Internet|42.159.0.0/16|Internet|
|Mi-51-8-nexthop-Internet|51.0.0.0/8|Internet|
|Mi-52-8-nexthop-Internet|52.0.0.0/8|Internet|
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|Mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|Mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|Mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-22-nexthop-Internet|103.25.156.0/22|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|Mi-131-253-16-nexthop-Internet|131.253.0.0/16|Internet|
|Mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|Mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|Mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|Mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|Mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|Mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|Mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|Mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|Mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|Mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|Mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|Mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|Mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|Mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|Mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|Mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|Mi-192-100-102-24-nexthop-Internet|192.100.102.0/24|Internet|
|Mi-192-100-103-24-nexthop-Internet|192.100.103.0/24|Internet|
|Mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|Mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|Mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|Mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|Mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|Mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|Mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|Mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|Mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|Mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|Mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|Mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|Mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|Mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|Mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|Mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|Mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|Mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|Mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|Mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|Mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|Mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|Mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|Mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|Mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|Mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|Mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|Mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|Mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
||||

Daarnaast kunt u vermeldingen aan de route tabel toevoegen om verkeer met on-premises privé-IP-adresbereiken als bestemming te routeren via de gateway van het virtuele netwerk of het virtuele netwerk apparaat (NVA).

Als het virtuele netwerk een aangepaste DNS bevat, moet de aangepaste DNS-server open bare DNS-records kunnen omzetten. Voor het gebruik van aanvullende functies, zoals Azure AD-verificatie, moet u mogelijk aanvullende FQDN-kenmerken oplossen. Zie [een aangepaste DNS instellen](sql-database-managed-instance-custom-dns.md)voor meer informatie.

## <a name="service-aided-subnet-configuration-public-preview-in-east-us-and-west-us"></a>Configuratie van geaidede subnetten (open bare preview-versie in VS-Oost en VS-West)

Om de beveiliging en beheer baarheid vereisten van de klant op te lossen, wordt het beheerde exemplaar overgezet van hand matig naar service-aided subnet-configuratie.

Met Service-aided subnet Configuration gebruiker bevindt zich volledig beheer van gegevens in TDS-verkeer, terwijl het beheerde exemplaar verantwoordelijk is voor een ononderbroken stroom van beheer verkeer om te voldoen aan de SLA.

### <a name="network-requirements-with-service-aided-subnet-configuration"></a>Netwerk vereisten met de service-aided subnet-configuratie 

Implementeer een beheerd exemplaar in een toegewezen subnet in het virtuele netwerk. Het subnet moet de volgende eigenschappen hebben:

- **Toegewezen subnet:** Het subnet van het beheerde exemplaar kan geen andere Cloud service bevatten die eraan is gekoppeld en kan geen gateway-subnet zijn. Het subnet kan geen resource bevatten, maar het beheerde exemplaar, en u kunt later geen andere typen resources toevoegen in het subnet.
- **Subnet-overdracht:** Het subnet van het beheerde exemplaar moet worden gedelegeerd naar `Microsoft.Sql/managedInstances`-resource provider.
- **Netwerk beveiligings groep (NSG):** Een NSG moet worden gekoppeld aan het subnet van het beheerde exemplaar. U kunt een NSG gebruiken om de toegang tot het gegevens eindpunt van het beheerde exemplaar te beheren door verkeer te filteren op poort 1433 en poorten 11000-11999 wanneer het beheerde exemplaar is geconfigureerd voor omleidings verbindingen. De service voegt automatisch [regels](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) toe die vereist zijn om een ononderbroken stroom van beheer verkeer toe te staan.
- Door de **gebruiker gedefinieerde route tabel (UDR):** Een UDR-tabel moet worden gekoppeld aan het subnet van het beheerde exemplaar. U kunt vermeldingen toevoegen aan de route tabel om verkeer met on-premises privé-IP-adresbereiken als bestemming te routeren via de gateway van het virtuele netwerk of het virtuele netwerk apparaat (NVA). Service voegt automatisch [vermeldingen](#user-defined-routes-with-service-aided-subnet-configuration) toe die vereist zijn voor het toestaan van een ononderbroken stroom van beheer verkeer.
- **Service-eind punten:** Service-eind punten kunnen worden gebruikt voor het configureren van virtuele-netwerk regels voor opslag accounts die back-ups/audit logboeken bewaren.
- **Voldoende IP-adressen:** Het subnet van het beheerde exemplaar moet ten minste 16 IP-adressen hebben. De aanbevolen minimum waarde is 32 IP-adressen. Zie [de grootte van het subnet voor beheerde instanties bepalen](sql-database-managed-instance-determine-size-vnet-subnet.md)voor meer informatie. U kunt beheerde exemplaren in [het bestaande netwerk](sql-database-managed-instance-configure-vnet-subnet.md) implementeren nadat u deze hebt geconfigureerd om te voldoen aan [de netwerk vereisten voor beheerde exemplaren](#network-requirements). Als dat niet het geval is, maakt u een [nieuw netwerk en subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Wanneer u een beheerd exemplaar maakt, wordt een netwerk intentie beleid toegepast op het subnet om niet-compatibele wijzigingen in de netwerk installatie te voor komen. Nadat het laatste exemplaar van het subnet is verwijderd, wordt het netwerkintentiebeleid ook verwijderd.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Verplichte regels voor binnenkomende beveiliging met configuratie van geaidede subnetten 

| Naam       |Port                        |Protocol|Bron           |Bestemming|Bewerking|
|------------|----------------------------|--------|-----------------|-----------|------|
|beheer  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement    |MI-SUBNET  |Toestaan |
|            |9000, 9003                  |TCP     |CorpnetSaw       |MI-SUBNET  |Toestaan |
|            |9000, 9003                  |TCP     |65.55.188.0/24, 167.220.0.0/16, 131.107.0.0/16|MI-SUBNET  |Toestaan |
|mi_subnet   |Alle                         |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |
|health_probe|Alle                         |Alle     |AzureLoadBalancer|MI-SUBNET  |Toestaan |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Verplichte uitgaande beveiligings regels met een service-aided subnet-configuratie 

| Naam       |Port          |Protocol|Bron           |Bestemming|Bewerking|
|------------|--------------|--------|-----------------|-----------|------|
|beheer  |443, 12000    |TCP     |MI-SUBNET        |AzureCloud |Toestaan |
|mi_subnet   |Alle           |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Door de gebruiker gedefinieerde routes met de service-aided subnet-configuratie 

|Naam|Adresvoorvoegsel|Volgende hop|
|----|--------------|-------|
|subnet-to-vnetlocal|MI-SUBNET|Virtueel netwerk|
|Mi-13-64-11-nexthop-Internet|13.64.0.0/11|Internet|
|Mi-13-104-14-nexthop-Internet|13.104.0.0/14|Internet|
|Mi-20-34-15-nexthop-Internet|20.34.0.0/15|Internet|
|Mi-20-36-14-nexthop-Internet|20.36.0.0/14|Internet|
|Mi-20-40-13-nexthop-Internet|20.40.0.0/13|Internet|
|Mi-20-128-16-nexthop-Internet|20.128.0.0/16|Internet|
|Mi-20-140-15-nexthop-Internet|20.140.0.0/15|Internet|
|Mi-20-144-14-nexthop-Internet|20.144.0.0/14|Internet|
|Mi-20-150-15-nexthop-Internet|20.150.0.0/15|Internet|
|Mi-20-160-12-nexthop-Internet|20.160.0.0/12|Internet|
|Mi-20-176-14-nexthop-Internet|20.176.0.0/14|Internet|
|Mi-20-180-14-nexthop-Internet|20.180.0.0/14|Internet|
|Mi-20-184-13-nexthop-Internet|20.184.0.0/13|Internet|
|Mi-40-64-10-nexthop-Internet|40.64.0.0/10|Internet|
|Mi-51-4-15-nexthop-Internet|51.4.0.0/15|Internet|
|Mi-51-8-16-nexthop-Internet|51.8.0.0/16|Internet|
|Mi-51-10-15-nexthop-Internet|51.10.0.0/15|Internet|
|Mi-51-12-15-nexthop-Internet|51.12.0.0/15|Internet|
|Mi-51-18-16-nexthop-Internet|51.18.0.0/16|Internet|
|Mi-51-51-16-nexthop-Internet|51.51.0.0/16|Internet|
|Mi-51-53-16-nexthop-Internet|51.53.0.0/16|Internet|
|Mi-51-103-16-nexthop-Internet|51.103.0.0/16|Internet|
|Mi-51-104-15-nexthop-Internet|51.104.0.0/15|Internet|
|Mi-51-107-16-nexthop-Internet|51.107.0.0/16|Internet|
|Mi-51-116-16-nexthop-Internet|51.116.0.0/16|Internet|
|Mi-51-120-16-nexthop-Internet|51.120.0.0/16|Internet|
|Mi-51-124-16-nexthop-Internet|51.124.0.0/16|Internet|
|Mi-51-132-16-nexthop-Internet|51.132.0.0/16|Internet|
|Mi-51-136-15-nexthop-Internet|51.136.0.0/15|Internet|
|Mi-51-138-16-nexthop-Internet|51.138.0.0/16|Internet|
|Mi-51-140-14-nexthop-Internet|51.140.0.0/14|Internet|
|Mi-51-144-15-nexthop-Internet|51.144.0.0/15|Internet|
|Mi-52-96-12-nexthop-Internet|52.96.0.0/12|Internet|
|Mi-52-112-14-nexthop-Internet|52.112.0.0/14|Internet|
|Mi-52-125-16-nexthop-Internet|52.125.0.0/16|Internet|
|Mi-52-126-15-nexthop-Internet|52.126.0.0/15|Internet|
|Mi-52-130-15-nexthop-Internet|52.130.0.0/15|Internet|
|Mi-52-132-14-nexthop-Internet|52.132.0.0/14|Internet|
|Mi-52-136-13-nexthop-Internet|52.136.0.0/13|Internet|
|Mi-52-145-16-nexthop-Internet|52.145.0.0/16|Internet|
|Mi-52-146-15-nexthop-Internet|52.146.0.0/15|Internet|
|Mi-52-148-14-nexthop-Internet|52.148.0.0/14|Internet|
|Mi-52-152-13-nexthop-Internet|52.152.0.0/13|Internet|
|Mi-52-160-11-nexthop-Internet|52.160.0.0/11|Internet|
|Mi-52-224-11-nexthop-Internet|52.224.0.0/11|Internet|
|Mi-64-4-18-nexthop-Internet|64.4.0.0/18|Internet|
|Mi-65-52-14-nexthop-Internet|65.52.0.0/14|Internet|
|Mi-66-119-144-20-nexthop-Internet|66.119.144.0/20|Internet|
|Mi-70-37-17-nexthop-Internet|70.37.0.0/17|Internet|
|Mi-70-37-128-18-nexthop-Internet|70.37.128.0/18|Internet|
|Mi-91-190-216-21-nexthop-Internet|91.190.216.0/21|Internet|
|Mi-94-245-64-18-nexthop-Internet|94.245.64.0/18|Internet|
|Mi-103-9-8-22-nexthop-Internet|103.9.8.0/22|Internet|
|Mi-103-25-156-24-nexthop-Internet|103.25.156.0/24|Internet|
|Mi-103-25-157-24-nexthop-Internet|103.25.157.0/24|Internet|
|Mi-103-25-158-23-nexthop-Internet|103.25.158.0/23|Internet|
|Mi-103-36-96-22-nexthop-Internet|103.36.96.0/22|Internet|
|Mi-103-255-140-22-nexthop-Internet|103.255.140.0/22|Internet|
|Mi-104-40-13-nexthop-Internet|104.40.0.0/13|Internet|
|Mi-104-146-15-nexthop-Internet|104.146.0.0/15|Internet|
|Mi-104-208-13-nexthop-Internet|104.208.0.0/13|Internet|
|Mi-111-221-16-20-nexthop-Internet|111.221.16.0/20|Internet|
|Mi-111-221-64-18-nexthop-Internet|111.221.64.0/18|Internet|
|Mi-129-75-16-nexthop-Internet|129.75.0.0/16|Internet|
|Mi-131-253-1-24-nexthop-Internet|131.253.1.0/24|Internet|
|Mi-131-253-3-24-nexthop-Internet|131.253.3.0/24|Internet|
|Mi-131-253-5-24-nexthop-Internet|131.253.5.0/24|Internet|
|Mi-131-253-6-24-nexthop-Internet|131.253.6.0/24|Internet|
|Mi-131-253-8-24-nexthop-Internet|131.253.8.0/24|Internet|
|Mi-131-253-12-22-nexthop-Internet|131.253.12.0/22|Internet|
|Mi-131-253-16-23-nexthop-Internet|131.253.16.0/23|Internet|
|Mi-131-253-18-24-nexthop-Internet|131.253.18.0/24|Internet|
|Mi-131-253-21-24-nexthop-Internet|131.253.21.0/24|Internet|
|Mi-131-253-22-23-nexthop-Internet|131.253.22.0/23|Internet|
|Mi-131-253-24-21-nexthop-Internet|131.253.24.0/21|Internet|
|Mi-131-253-32-20-nexthop-Internet|131.253.32.0/20|Internet|
|Mi-131-253-61-24-nexthop-Internet|131.253.61.0/24|Internet|
|Mi-131-253-62-23-nexthop-Internet|131.253.62.0/23|Internet|
|Mi-131-253-64-18-nexthop-Internet|131.253.64.0/18|Internet|
|Mi-131-253-128-17-nexthop-Internet|131.253.128.0/17|Internet|
|Mi-132-245-16-nexthop-Internet|132.245.0.0/16|Internet|
|Mi-134-170-16-nexthop-Internet|134.170.0.0/16|Internet|
|Mi-134-177-16-nexthop-Internet|134.177.0.0/16|Internet|
|Mi-137-116-15-nexthop-Internet|137.116.0.0/15|Internet|
|Mi-137-135-16-nexthop-Internet|137.135.0.0/16|Internet|
|Mi-138-91-16-nexthop-Internet|138.91.0.0/16|Internet|
|Mi-138-196-16-nexthop-Internet|138.196.0.0/16|Internet|
|Mi-139-217-16-nexthop-Internet|139.217.0.0/16|Internet|
|Mi-139-219-16-nexthop-Internet|139.219.0.0/16|Internet|
|Mi-141-251-16-nexthop-Internet|141.251.0.0/16|Internet|
|Mi-146-147-16-nexthop-Internet|146.147.0.0/16|Internet|
|Mi-147-243-16-nexthop-Internet|147.243.0.0/16|Internet|
|Mi-150-171-16-nexthop-Internet|150.171.0.0/16|Internet|
|Mi-150-242-48-22-nexthop-Internet|150.242.48.0/22|Internet|
|Mi-157-54-15-nexthop-Internet|157.54.0.0/15|Internet|
|Mi-157-56-14-nexthop-Internet|157.56.0.0/14|Internet|
|Mi-157-60-16-nexthop-Internet|157.60.0.0/16|Internet|
|Mi-167-220-16-nexthop-Internet|167.220.0.0/16|Internet|
|Mi-168-61-16-nexthop-Internet|168.61.0.0/16|Internet|
|Mi-168-62-15-nexthop-Internet|168.62.0.0/15|Internet|
|Mi-191-232-13-nexthop-Internet|191.232.0.0/13|Internet|
|Mi-192-32-16-nexthop-Internet|192.32.0.0/16|Internet|
|Mi-192-48-225-24-nexthop-Internet|192.48.225.0/24|Internet|
|Mi-192-84-159-24-nexthop-Internet|192.84.159.0/24|Internet|
|Mi-192-84-160-23-nexthop-Internet|192.84.160.0/23|Internet|
|Mi-192-100-102-24-nexthop-Internet|192.100.102.0/24|Internet|
|Mi-192-100-103-24-nexthop-Internet|192.100.103.0/24|Internet|
|Mi-192-197-157-24-nexthop-Internet|192.197.157.0/24|Internet|
|Mi-193-149-64-19-nexthop-Internet|193.149.64.0/19|Internet|
|Mi-193-221-113-24-nexthop-Internet|193.221.113.0/24|Internet|
|Mi-194-69-96-19-nexthop-Internet|194.69.96.0/19|Internet|
|Mi-194-110-197-24-nexthop-Internet|194.110.197.0/24|Internet|
|Mi-198-105-232-22-nexthop-Internet|198.105.232.0/22|Internet|
|Mi-198-200-130-24-nexthop-Internet|198.200.130.0/24|Internet|
|Mi-198-206-164-24-nexthop-Internet|198.206.164.0/24|Internet|
|Mi-199-60-28-24-nexthop-Internet|199.60.28.0/24|Internet|
|Mi-199-74-210-24-nexthop-Internet|199.74.210.0/24|Internet|
|Mi-199-103-90-23-nexthop-Internet|199.103.90.0/23|Internet|
|Mi-199-103-122-24-nexthop-Internet|199.103.122.0/24|Internet|
|Mi-199-242-32-20-nexthop-Internet|199.242.32.0/20|Internet|
|Mi-199-242-48-21-nexthop-Internet|199.242.48.0/21|Internet|
|Mi-202-89-224-20-nexthop-Internet|202.89.224.0/20|Internet|
|Mi-204-13-120-21-nexthop-Internet|204.13.120.0/21|Internet|
|Mi-204-14-180-22-nexthop-Internet|204.14.180.0/22|Internet|
|Mi-204-79-135-24-nexthop-Internet|204.79.135.0/24|Internet|
|Mi-204-79-179-24-nexthop-Internet|204.79.179.0/24|Internet|
|Mi-204-79-181-24-nexthop-Internet|204.79.181.0/24|Internet|
|Mi-204-79-188-24-nexthop-Internet|204.79.188.0/24|Internet|
|Mi-204-79-195-24-nexthop-Internet|204.79.195.0/24|Internet|
|Mi-204-79-196-23-nexthop-Internet|204.79.196.0/23|Internet|
|Mi-204-79-252-24-nexthop-Internet|204.79.252.0/24|Internet|
|Mi-204-152-18-23-nexthop-Internet|204.152.18.0/23|Internet|
|Mi-204-152-140-23-nexthop-Internet|204.152.140.0/23|Internet|
|Mi-204-231-192-24-nexthop-Internet|204.231.192.0/24|Internet|
|Mi-204-231-194-23-nexthop-Internet|204.231.194.0/23|Internet|
|Mi-204-231-197-24-nexthop-Internet|204.231.197.0/24|Internet|
|Mi-204-231-198-23-nexthop-Internet|204.231.198.0/23|Internet|
|Mi-204-231-200-21-nexthop-Internet|204.231.200.0/21|Internet|
|Mi-204-231-208-20-nexthop-Internet|204.231.208.0/20|Internet|
|Mi-204-231-236-24-nexthop-Internet|204.231.236.0/24|Internet|
|Mi-205-174-224-20-nexthop-Internet|205.174.224.0/20|Internet|
|Mi-206-138-168-21-nexthop-Internet|206.138.168.0/21|Internet|
|Mi-206-191-224-19-nexthop-Internet|206.191.224.0/19|Internet|
|Mi-207-46-16-nexthop-Internet|207.46.0.0/16|Internet|
|Mi-207-68-128-18-nexthop-Internet|207.68.128.0/18|Internet|
|Mi-208-68-136-21-nexthop-Internet|208.68.136.0/21|Internet|
|Mi-208-76-44-22-nexthop-Internet|208.76.44.0/22|Internet|
|Mi-208-84-21-nexthop-Internet|208.84.0.0/21|Internet|
|Mi-209-240-192-19-nexthop-Internet|209.240.192.0/19|Internet|
|Mi-213-199-128-18-nexthop-Internet|213.199.128.0/18|Internet|
|Mi-216-32-180-22-nexthop-Internet|216.32.180.0/22|Internet|
|Mi-216-220-208-20-nexthop-Internet|216.220.208.0/20|Internet|
||||

\* MI-SUBNET verwijst naar het IP-adres bereik voor het subnet in de vorm 10. x. x. x/y. U kunt deze informatie vinden in het Azure Portal, in eigenschappen van subnet.

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL database Advanced Data Security](sql-database-managed-instance.md)(Engelstalig) voor een overzicht.
- Meer informatie over het [instellen van een nieuw virtueel Azure-netwerk](sql-database-managed-instance-create-vnet-subnet.md) of een [bestaand virtueel Azure-netwerk](sql-database-managed-instance-configure-vnet-subnet.md) waar u beheerde exemplaren kunt implementeren.
- [Bereken de grootte van het subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) waar u de beheerde exemplaren wilt implementeren.
- Meer informatie over het maken van een beheerd exemplaar:
  - Van de [Azure Portal](sql-database-managed-instance-get-started.md).
  - Met behulp van [Power shell](scripts/sql-database-create-configure-managed-instance-powershell.md).
  - Met behulp van [een Azure Resource Manager sjabloon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Met behulp van [een Azure Resource Manager sjabloon (met behulp van JumpBox, met SSMS inbegrepen)](https://azure.microsoft.com/en-us/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 

---
title: Connectiviteitsarchitectuur voor een beheerd exemplaar
description: Meer informatie over azure SQL Database beheerde instantiecommunicatie- en connectiviteitsarchitectuur en hoe de componenten verkeer naar de beheerde instantie leiden.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: fasttrack-edit
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: sstein, bonova, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: f30ccd498b79c36c8892ae38a3e26d169249621a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481096"
---
# <a name="connectivity-architecture-for-a-managed-instance-in-azure-sql-database"></a>Connectiviteitsarchitectuur voor een beheerde instantie in Azure SQL Database

In dit artikel wordt de communicatie in een azure SQL-databasebeheerd exemplaar uitgelegd. Het beschrijft ook de connectiviteitsarchitectuur en hoe de componenten verkeer naar de beheerde instantie leiden.  

De sql-databasebeheerde instantie wordt geplaatst in het virtuele Azure-netwerk en het subnet dat is toegewezen aan beheerde exemplaren. Deze implementatie biedt:

- Een veilig privé IP-adres.
- De mogelijkheid om een on-premises netwerk aan te sluiten op een beheerde instantie.
- De mogelijkheid om een beheerde instantie te verbinden met een gekoppelde server of een ander on-premises gegevensarchief.
- De mogelijkheid om een beheerde instantie te verbinden met Azure-resources.

## <a name="communication-overview"></a>Communicatieoverzicht

In het volgende diagram worden entiteiten weergegeven die verbinding maken met een beheerde instantie. Het toont ook de resources die moeten communiceren met de beheerde instantie. Het communicatieproces onder aan het diagram vertegenwoordigt klanttoepassingen en -hulpprogramma's die verbinding maken met de beheerde instantie als gegevensbronnen.  

![Entiteiten in connectiviteitsarchitectuur](./media/managed-instance-connectivity-architecture/connectivityarch001.png)

Een beheerde instantie is een platform as a service (PaaS) aanbod. Microsoft gebruikt geautomatiseerde agents (beheer, implementatie en onderhoud) om deze service te beheren op basis van telemetriegegevensstromen. Omdat Microsoft verantwoordelijk is voor het beheer, hebben klanten geen toegang tot de beheerde virtuele clustermachines via Extern bureaublad-protocol (RDP).

Voor sommige SQL Server-bewerkingen die zijn gestart door eindgebruikers of toepassingen, moeten beheerde exemplaren mogelijk met het platform worden uitgevoerd. Een geval is het maken van een beheerde instantie database. Deze bron wordt weergegeven via de Azure-portal, PowerShell, Azure CLI en de REST API.

Beheerde exemplaren zijn afhankelijk van Azure-services, zoals Azure Storage voor back-ups, Azure Event Hubs voor telemetrie, Azure Active Directory voor verificatie, Azure Key Vault for Transparent Data Encryption (TDE) en een paar Azure-platformservices die bieden beveiligings- en ondersteuningsfuncties. De beheerde instanties maken verbindingen met deze services.

Alle communicatie wordt versleuteld en ondertekend met certificaten. Om de betrouwbaarheid van communicerende partijen te controleren, controleren beheerde instanties deze certificaten voortdurend via certificaatintrekkingslijsten. Als de certificaten worden ingetrokken, worden de verbindingen met beheerde instantie gesloten om de gegevens te beschermen.

## <a name="high-level-connectivity-architecture"></a>Connectiviteitsarchitectuur op hoog niveau

Op een hoog niveau is een beheerde instantie een set servicecomponenten. Deze componenten worden gehost op een speciale set van geïsoleerde virtuele machines die worden uitgevoerd in het virtuele netwerk subnet van de klant. Deze machines vormen een virtueel cluster.

Een virtueel cluster kan meerdere beheerde exemplaren hosten. Indien nodig wordt het cluster automatisch uitgebreid of contracten uitgevoerd wanneer de klant het aantal ingerichte exemplaren in het subnet wijzigt.

Klanttoepassingen kunnen verbinding maken met beheerde instanties en kunnen databases binnen het virtuele netwerk, peered virtueel netwerk of netwerk dat is verbonden via VPN of Azure ExpressRoute, opvragen en bijwerken. Dit netwerk moet een eindpunt en een privé-IP-adres gebruiken.  

![Diagram connectiviteitsarchitectuur](./media/managed-instance-connectivity-architecture/connectivityarch002.png)

Microsoft-beheer- en implementatieservices worden buiten het virtuele netwerk uitgevoerd. Een beheerde instantie en Microsoft-services maken verbinding via de eindpunten met openbare IP-adressen. Wanneer een beheerde instantie een uitgaande verbinding maakt, laat nat bij ontvangst van end Network Address Translation (NAT) de verbinding eruit zien alsof deze afkomstig is van dit openbare IP-adres.

Het beheer van het verkeer stroomt door het virtuele netwerk van de klant. Dat betekent dat elementen van de infrastructuur van het virtuele netwerk het beheerverkeer kunnen schaden door de instantie te laten mislukken en niet meer beschikbaar te worden.

> [!IMPORTANT]
> Om de klantervaring en de beschikbaarheid van services te verbeteren, past Microsoft een beleid voor netwerkintentie toe op azure-elementen voor virtuele netwerkinfrastructuur. Het beleid kan van invloed zijn op de werking van de beheerde instantie. Dit platformmechanisme communiceert op transparante wijze netwerkvereisten aan gebruikers. Het belangrijkste doel van het beleid is om foutieve configuratie van het netwerk te voorkomen en normale beheerde instantiebewerkingen te garanderen. Wanneer u een beheerde instantie verwijdert, wordt ook het beleid voor netwerkintentie verwijderd.

## <a name="virtual-cluster-connectivity-architecture"></a>Architectuur voor virtuele clusterconnectiviteit

Laten we een diepere duik nemen in de connectiviteitsarchitectuur voor beheerde instanties. In het volgende diagram ziet u de conceptuele indeling van het virtuele cluster.

![Connectiviteitsarchitectuur van het virtuele cluster](./media/managed-instance-connectivity-architecture/connectivityarch003.png)

Clients maken verbinding met een beheerde instantie `<mi_name>.<dns_zone>.database.windows.net`met behulp van een hostnaam die het formulier heeft. Deze hostnaam wordt opgelost op een privé-IP-adres, hoewel deze is geregistreerd in een DNS-zone (public Domain Name System) en openbaar oplosbaar is. Het `zone-id` wordt automatisch gegenereerd wanneer u het cluster maakt. Als een nieuw gemaakt cluster een secundairbeheerde instantie host, deelt het zijn zone-id met het primaire cluster. Zie [Automatische failovergroepen gebruiken om een transparante en gecoördineerde failover van meerdere databases mogelijk te maken](sql-database-auto-failover-group.md#enabling-geo-replication-between-managed-instances-and-their-vnets)voor meer informatie.

Dit privé-IP-adres behoort tot de interne load balancer van de beheerde instantie. De load balancer leidt het verkeer naar de gateway van de beheerde instantie. Omdat meerdere beheerde exemplaren in hetzelfde cluster kunnen worden uitgevoerd, gebruikt de gateway de hostnaam van de beheerde instantie om verkeer om te leiden naar de juiste SQL-engineservice.

Beheer- en implementatieservices maken verbinding met een beheerexemplaar met behulp van een [beheereindpunt](#management-endpoint) dat wordt toegewezen aan een externe load balancer. Verkeer wordt alleen naar de knooppunten geleid als het wordt ontvangen op een vooraf gedefinieerde set poorten die alleen de beheercomponenten van de beheerde instantie gebruiken. Een ingebouwde firewall op de knooppunten is ingesteld om alleen verkeer van Microsoft IP-bereiken mogelijk te maken. Certificaten verifiëren wederzijds alle communicatie tussen beheeronderdelen en het managementvlak.

## <a name="management-endpoint"></a>Beheereindpunt

Microsoft beheert de beheerde instantie met behulp van een beheereindpunt. Dit eindpunt bevindt zich in het virtuele cluster van de instantie. Het beheereindpunt wordt beschermd door een ingebouwde firewall op netwerkniveau. Op aanvraagniveau wordt het beschermd door verificatie van wederzijdse certificaten. Zie Het [IP-adres](sql-database-managed-instance-find-management-endpoint-ip-address.md)van het eindpunt bepalen als u het IP-adres van het eindpunt wilt vinden.

Wanneer verbindingen starten in de beheerde instantie (zoals bij back-ups en controlelogboeken), lijkt het verkeer te beginnen vanaf het openbare IP-adres van het beheereindpunt. U de toegang tot openbare services vanuit een beheerde instantie beperken door firewallregels in te stellen om alleen het IP-adres van de beheerde instantie toe te staan. Zie [De ingebouwde firewall van de beheerde instantie verifiëren](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md)voor meer informatie.

> [!NOTE]
> Verkeer dat naar Azure-services gaat die zich binnen de regio van de beheerde instantie bevinden, is geoptimaliseerd en om die reden niet nated naar beheerde instantiebeheereindpuntopenbaar IP-adres. Om die reden als u IP-gebaseerde firewallregels moet gebruiken, moet de service meestal in een andere regio zijn dan de beheerde instantie.

## <a name="service-aided-subnet-configuration"></a>Subnetconfiguratie met servicehulp

Om de vereisten voor klantbeveiliging en beheerbaarheid aan te pakken, schakelt Managed Instance over van handmatige naar service-ondersteunde subnetconfiguratie.

Met service-aided subnet configuratie gebruiker is in volledige controle van de gegevens (TDS) verkeer, terwijl Managed Instance neemt de verantwoordelijkheid om een ononderbroken stroom van beheerverkeer te waarborgen om SLA te voldoen.

Subnetconfiguratie met servicehulpprogramma's bouwt voort op de functie voor [subnetdelegatie](../virtual-network/subnet-delegation-overview.md) van virtuele netwerken om automatisch netwerkconfiguratiebeheer te bieden en serviceeindpunten in te schakelen. Serviceeindpunten kunnen worden gebruikt om virtuele netwerkfirewallregels voor opslagaccounts te configureren die back-ups/ controlelogboeken bijhouden.

### <a name="network-requirements"></a>Netwerkvereisten 

Een beheerde instantie implementeren in een speciaal subnet binnen het virtuele netwerk. Het subnet moet de volgende kenmerken hebben:

- **Speciaal subnet:** Het subnet van de beheerde instantie kan geen andere cloudservice bevatten die eraan is gekoppeld en kan geen gateway-subnet zijn. Het subnet kan geen bron bevatten, behalve de beheerde instantie en u later geen andere typen resources in het subnet toevoegen.
- **Subnetdelegatie:** Het subnet van de beheerde instantie `Microsoft.Sql/managedInstances` moet worden gedelegeerd aan resourceprovider.
- **Netwerkbeveiligingsgroep (NSG):** Een NSG moet worden gekoppeld aan het subnet van de beheerde instantie. U een NSG gebruiken om de toegang tot het eindpunt van de beheerde instantie te beheren door verkeer op poort 1433 en poorten 11000-11999 te filteren wanneer de beheerde instantie is geconfigureerd voor omleidingsverbindingen. Service zal automatisch provisionen en houden de huidige [regels](#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) die nodig zijn om een ononderbroken stroom van het beheer verkeer mogelijk te maken.
- **UDR-tabel (User defined route):** Een UDR-tabel moet worden gekoppeld aan het subnet van de beheerde instantie. U items toevoegen aan de routetabel om verkeer te routeren met on-premises privé-IP-bereiken als bestemming via de virtuele netwerkgateway of het virtuele netwerktoestel (NVA). Service zal automatisch in- en [weergeven](#user-defined-routes-with-service-aided-subnet-configuration) de huidige items die nodig zijn om een ononderbroken stroom van beheerverkeer mogelijk te maken.
- **Voldoende IP-adressen:** Het beheerde instantiesubnet moet ten minste 16 IP-adressen hebben. Het aanbevolen minimum is 32 IP-adressen. Zie [De grootte van het subnet voor beheerde instanties bepalen voor](sql-database-managed-instance-determine-size-vnet-subnet.md)meer informatie . U beheerde exemplaren implementeren in [het bestaande netwerk](sql-database-managed-instance-configure-vnet-subnet.md) nadat u deze hebt geconfigureerd om te voldoen aan de [netwerkvereisten voor beheerde exemplaren.](#network-requirements) Maak anders een [nieuw netwerk en subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> Wanneer u een beheerde instantie maakt, wordt op het subnet een netwerkintentiebeleid toegepast om niet-compatibele wijzigingen in de netwerkinstelling te voorkomen. Nadat de laatste instantie uit het subnet is verwijderd, wordt ook het netwerkintentiebeleid verwijderd.

### <a name="mandatory-inbound-security-rules-with-service-aided-subnet-configuration"></a>Verplichte inkomende beveiligingsregels met subnetconfiguratie met service 

| Name       |Poort                        |Protocol|Bron           |Doel|Actie|
|------------|----------------------------|--------|-----------------|-----------|------|
|beheer  |9000, 9003, 1438, 1440, 1452|TCP     |SqlManagement SqlManagement    |MI-SUBNET  |Toestaan |
|            |9000, 9003                  |TCP     |CorpnetSaw (CorpnetSaw)       |MI-SUBNET  |Toestaan |
|            |9000, 9003                  |TCP     |CorpnetPublic (CorpnetPublic)    |MI-SUBNET  |Toestaan |
|mi_subnet   |Alle                         |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |
|health_probe|Alle                         |Alle     |AzureLoadBalancer|MI-SUBNET  |Toestaan |

### <a name="mandatory-outbound-security-rules-with-service-aided-subnet-configuration"></a>Verplichte uitgaande beveiligingsregels met subnetconfiguratie met servicehulp 

| Name       |Poort          |Protocol|Bron           |Doel|Actie|
|------------|--------------|--------|-----------------|-----------|------|
|beheer  |443, 12000    |TCP     |MI-SUBNET        |AzureCloud |Toestaan |
|mi_subnet   |Alle           |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |

### <a name="user-defined-routes-with-service-aided-subnet-configuration"></a>Door de gebruiker gedefinieerde routes met subnetconfiguratie met servicehulp 

|Name|Adresvoorvoegsel|Volgende hop|
|----|--------------|-------|
|subnet-to-vnetlocal|MI-SUBNET|Virtueel netwerk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

\*MI SUBNET verwijst naar het IP-adresbereik voor het subnet in de vorm x.x.x.x/y. U deze informatie vinden in de Azure-portal, in subneteigenschappen.

Daarnaast u items toevoegen aan de routetabel om verkeer te routeren met on-premises privé-IP-bereiken als bestemming via de virtuele netwerkgateway of het virtuele netwerktoestel (NVA).

Als het virtuele netwerk een aangepaste DNS bevat, moet de aangepaste DNS-server openbare dns-records kunnen oplossen. Voor het gebruik van extra functies, zoals Azure AD-verificatie, moet mogelijk extra FQDN's worden opgelost. Zie [Een aangepaste DNS instellen](sql-database-managed-instance-custom-dns.md)voor meer informatie.

### <a name="networking-constraints"></a>Netwerkbeperkingen

**TLS 1.2 wordt afgedwongen op uitgaande verbindingen**: In januari 2020 heeft Microsoft TLS 1.2 afgedwongen voor intraserviceverkeer in alle Azure-services. Voor azure SQL Database-beheerde instantie resulteerde dit in een afgedwongen TLS 1.2 op uitgaande verbindingen die worden gebruikt voor replicatie en gekoppelde serververbindingen met SQL Server. Als u versies van SQL Server gebruikt die ouder zijn dan 2016 met Beheerde instantie, moet u ervoor zorgen dat [specifieke UPDATES van TLS 1.2](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server) zijn toegepast.

Volgende virtuele netwerkfuncties worden momenteel niet ondersteund met Beheerde instantie:
- **Microsoft-peering:** [Microsoft-peering](../expressroute/expressroute-faqs.md#microsoft-peering) op expresroutecircuits die rechtstreeks of transitief worden gekeken met virtueel netwerk waar Beheerde instantie zich bevindt, heeft invloed op de verkeersstroom tussen beheerde instantiecomponenten binnen het virtuele netwerk en services, afhankelijk van het veroorzaken van beschikbaarheidsproblemen. Beheerde instantieimplementaties naar virtueel netwerk waarbij Microsoft-peering al is ingeschakeld, zullen naar verwachting mislukken.
- **Wereldwijde virtuele netwerkpeering:** [virtuele netwerkpeerconnectiviteit](../virtual-network/virtual-network-peering-overview.md) in Azure-regio's werkt niet voor Beheerde instantie vanwege [gedocumenteerde beperkingen op de load balancer.](../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- **AzurePlatformDNS**: Als u [AzurePlatformDNS-servicetag](../virtual-network/service-tags-overview.md) gebruikt om de DNS-resolutie van het platform te blokkeren, wordt Managed Instance niet beschikbaar. Hoewel Managed Instance door de klant gedefinieerde DNS voor DNS-resolutie in de engine ondersteunt, is er een afhankelijkheid van platform DNS voor platformbewerkingen.

### <a name="deprecated-network-requirements-without-service-aided-subnet-configuration"></a>[Afgeschaft] Netwerkvereisten zonder subnetconfiguratie met servicehulp

Een beheerde instantie implementeren in een speciaal subnet binnen het virtuele netwerk. Het subnet moet de volgende kenmerken hebben:

- **Speciaal subnet:** Het subnet van de beheerde instantie kan geen andere cloudservice bevatten die eraan is gekoppeld en kan geen gateway-subnet zijn. Het subnet kan geen bron bevatten, behalve de beheerde instantie en u later geen andere typen resources in het subnet toevoegen.
- **Netwerkbeveiligingsgroep (NSG):** Een NSG die is gekoppeld aan het virtuele netwerk, moet [binnenkomende beveiligingsregels](#mandatory-inbound-security-rules) en [uitgaande beveiligingsregels](#mandatory-outbound-security-rules) definiëren voordat andere regels gelden. U een NSG gebruiken om de toegang tot het eindpunt van de beheerde instantie te beheren door verkeer op poort 1433 en poorten 11000-11999 te filteren wanneer de beheerde instantie is geconfigureerd voor omleidingsverbindingen.
- **UDR-tabel (User defined route):** Een UDR-tabel die is gekoppeld aan het virtuele netwerk moet specifieke [vermeldingen](#user-defined-routes)bevatten.
- **Geen serviceeindpunten:** Er mag geen serviceeindpunt worden gekoppeld aan het subnet van de beheerde instantie. Zorg ervoor dat de optie serviceeindpunten is uitgeschakeld wanneer u het virtuele netwerk maakt.
- **Voldoende IP-adressen:** Het beheerde instantiesubnet moet ten minste 16 IP-adressen hebben. Het aanbevolen minimum is 32 IP-adressen. Zie [De grootte van het subnet voor beheerde instanties bepalen voor](sql-database-managed-instance-determine-size-vnet-subnet.md)meer informatie . U beheerde exemplaren implementeren in [het bestaande netwerk](sql-database-managed-instance-configure-vnet-subnet.md) nadat u deze hebt geconfigureerd om te voldoen aan de [netwerkvereisten voor beheerde exemplaren.](#network-requirements) Maak anders een [nieuw netwerk en subnet](sql-database-managed-instance-create-vnet-subnet.md).

> [!IMPORTANT]
> U een nieuw beheerde instantie niet implementeren als het doelsubnet deze kenmerken mist. Wanneer u een beheerde instantie maakt, wordt op het subnet een netwerkintentiebeleid toegepast om niet-compatibele wijzigingen in de netwerkinstelling te voorkomen. Nadat de laatste instantie uit het subnet is verwijderd, wordt ook het netwerkintentiebeleid verwijderd.

### <a name="mandatory-inbound-security-rules"></a>Verplichte inkomende beveiligingsregels

| Name       |Poort                        |Protocol|Bron           |Doel|Actie|
|------------|----------------------------|--------|-----------------|-----------|------|
|beheer  |9000, 9003, 1438, 1440, 1452|TCP     |Alle              |MI-SUBNET  |Toestaan |
|mi_subnet   |Alle                         |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |
|health_probe|Alle                         |Alle     |AzureLoadBalancer|MI-SUBNET  |Toestaan |

### <a name="mandatory-outbound-security-rules"></a>Verplichte uitgaande beveiligingsregels

| Name       |Poort          |Protocol|Bron           |Doel|Actie|
|------------|--------------|--------|-----------------|-----------|------|
|beheer  |443, 12000    |TCP     |MI-SUBNET        |AzureCloud |Toestaan |
|mi_subnet   |Alle           |Alle     |MI-SUBNET        |MI-SUBNET  |Toestaan |

> [!IMPORTANT]
> Zorg ervoor dat er slechts één inkomende regel is voor poorten 9000, 9003, 1438, 1440, 1452 en één uitgaande regel voor poorten 443, 12000. Beheerde instantiedie wordt ingericht via Azure Resource Manager-implementaties, mislukt als binnenkomende en uitgaande regels afzonderlijk voor elke poort worden geconfigureerd. Als deze poorten zich in afzonderlijke regels bevinden, mislukt de implementatie met foutcode`VnetSubnetConflictWithIntendedPolicy`

\*MI SUBNET verwijst naar het IP-adresbereik voor het subnet in de vorm x.x.x.x/y. U deze informatie vinden in de Azure-portal, in subneteigenschappen.

> [!IMPORTANT]
> Hoewel de vereiste binnenkomende beveiligingsregels verkeer van _elke_ bron op poorten 9000, 9003, 1438, 1440 en 1452 toestaan, worden deze poorten beschermd door een ingebouwde firewall. Zie Het adres [van het beheereindpunt bepalen](sql-database-managed-instance-find-management-endpoint-ip-address.md)voor meer informatie.

> [!NOTE]
> Als u transactionele replicatie gebruikt in een beheerde instantie en als u een instantiedatabase gebruikt als uitgever of distributeur, opent u poort 445 (TCP-uitgaande) in de beveiligingsregels van het subnet. Deze poort geeft toegang tot het Azure-bestandsaandeel.

### <a name="user-defined-routes"></a>Door de gebruiker gedefinieerde routes

|Name|Adresvoorvoegsel|Volgende hop|
|----|--------------|-------|
|subnet_to_vnetlocal|MI-SUBNET|Virtueel netwerk|
|mi-13-64-11-nexthop-internet|13.64.0.0/11|Internet|
|mi-13-104-14-nexthop-internet|13.104.0.0/14|Internet|
|mi-20-33-16-nexthop-internet|20.33.0.0/16|Internet|
|mi-20-34-15-nexthop-internet|20.34.0.0/15|Internet|
|mi-20-36-14-nexthop-internet|20.36.0.0/14|Internet|
|mi-20-40-13-nexthop-internet|20.40.0.0/13|Internet|
|mi-20-48-12-nexthop-internet|20.48.0.0/12|Internet|
|mi-20-64-10-nexthop-internet|20.64.0.0/10|Internet|
|mi-20-128-16-nexthop-internet|20.128.0.0/16|Internet|
|mi-20-135-16-nexthop-internet|20.135.0.0/16|Internet|
|mi-20-136-16-nexthop-internet|20.136.0.0/16|Internet|
|mi-20-140-15-nexthop-internet|20.140.0.0/15|Internet|
|mi-20-143-16-nexthop-internet|20.143.0.0/16|Internet|
|mi-20-144-14-nexthop-internet|20.144.0.0/14|Internet|
|mi-20-150-15-nexthop-internet|20.150.0.0/15|Internet|
|mi-20-160-12-nexthop-internet|20.160.0.0/12|Internet|
|mi-20-176-14-nexthop-internet|20.176.0.0/14|Internet|
|mi-20-180-14-nexthop-internet|20.180.0.0/14|Internet|
|mi-20-184-13-nexthop-internet|20.184.0.0/13|Internet|
|mi-20-192-10-nexthop-internet|20.192.0.0/10|Internet|
|mi-40-64-10-nexthop-internet|40.64.0.0/10|Internet|
|mi-51-4-15-nexthop-internet|51.4.0.0/15|Internet|
|mi-51-8-16-nexthop-internet|51.8.0.0/16|Internet|
|mi-51-10-15-nexthop-internet|51.10.0.0/15|Internet|
|mi-51-18-16-nexthop-internet|51.18.0.0/16|Internet|
|mi-51-51-16-nexthop-internet|51.51.0.0/16|Internet|
|mi-51-53-16-nexthop-internet|51.53.0.0/16|Internet|
|mi-51-103-16-nexthop-internet|51.103.0.0/16|Internet|
|mi-51-104-15-nexthop-internet|51.104.0.0/15|Internet|
|mi-51-132-16-nexthop-internet|51.132.0.0/16|Internet|
|mi-51-136-15-nexthop-internet|51.136.0.0/15|Internet|
|mi-51-138-16-nexthop-internet|51.138.0.0/16|Internet|
|mi-51-140-14-nexthop-internet|51.140.0.0/14|Internet|
|mi-51-144-15-nexthop-internet|51.144.0.0/15|Internet|
|mi-52-96-12-nexthop-internet|52.96.0.0/12|Internet|
|mi-52-112-14-nexthop-internet|52.112.0.0/14|Internet|
|mi-52-125-16-nexthop-internet|52.125.0.0/16|Internet|
|mi-52-126-15-nexthop-internet|52.126.0.0/15|Internet|
|mi-52-130-15-nexthop-internet|52.130.0.0/15|Internet|
|mi-52-132-14-nexthop-internet|52.132.0.0/14|Internet|
|mi-52-136-13-nexthop-internet|52.136.0.0/13|Internet|
|mi-52-145-16-nexthop-internet|52.145.0.0/16|Internet|
|mi-52-146-15-nexthop-internet|52.146.0.0/15|Internet|
|mi-52-148-14-nexthop-internet|52.148.0.0/14|Internet|
|mi-52-152-13-nexthop-internet|52.152.0.0/13|Internet|
|mi-52-160-11-nexthop-internet|52.160.0.0/11|Internet|
|mi-52-224-11-nexthop-internet|52.224.0.0/11|Internet|
|mi-64-4-18-nexthop-internet|64.4.0.0/18|Internet|
|mi-65-52-14-nexthop-internet|65.52.0.0/14|Internet|
|mi-66-119-144-20-nexthop-internet|66.119.144.0/20|Internet|
|mi-70-37-17-nexthop-internet|70.37.0.0/17|Internet|
|mi-70-37-128-18-nexthop-internet|70.37.128.0/18|Internet|
|mi-91-190-216-21-nexthop-internet|91.190.216.0/21|Internet|
|mi-94-245-64-18-nexthop-internet|94.245.64.0/18|Internet|
|mi-103-9-8-22-nexthop-internet|103.9.8.0/22|Internet|
|mi-103-25-156-24-nexthop-internet|103.25.156.0/24|Internet|
|mi-103-25-157-24-nexthop-internet|103.25.157.0/24|Internet|
|mi-103-25-158-23-nexthop-internet|103.25.158.0/23|Internet|
|mi-103-36-96-22-nexthop-internet|103.36.96.0/22|Internet|
|mi-103-255-140-22-nexthop-internet|103.255.140.0/22|Internet|
|mi-104-40-13-nexthop-internet|104.40.0.0/13|Internet|
|mi-104-146-15-nexthop-internet|104.146.0.0/15|Internet|
|mi-104-208-13-nexthop-internet|104.208.0.0/13|Internet|
|mi-111-221-16-20-nexthop-internet|111.221.16.0/20|Internet|
|mi-111-221-64-18-nexthop-internet|111.221.64.0/18|Internet|
|mi-129-75-16-nexthop-internet|129.75.0.0/16|Internet|
|mi-131-107-16-nexthop-internet|131.107.0.0/16|Internet|
|mi-131-253-1-24-nexthop-internet|131.253.1.0/24|Internet|
|mi-131-253-3-24-nexthop-internet|131.253.3.0/24|Internet|
|mi-131-253-5-24-nexthop-internet|131.253.5.0/24|Internet|
|mi-131-253-6-24-nexthop-internet|131.253.6.0/24|Internet|
|mi-131-253-8-24-nexthop-internet|131.253.8.0/24|Internet|
|mi-131-253-12-22-nexthop-internet|131.253.12.0/22|Internet|
|mi-131-253-16-23-nexthop-internet|131.253.16.0/23|Internet|
|mi-131-253-18-24-nexthop-internet|131.253.18.0/24|Internet|
|mi-131-253-21-24-nexthop-internet|131.253.21.0/24|Internet|
|mi-131-253-22-23-nexthop-internet|131.253.22.0/23|Internet|
|mi-131-253-24-21-nexthop-internet|131.253.24.0/21|Internet|
|mi-131-253-32-20-nexthop-internet|131.253.32.0/20|Internet|
|mi-131-253-61-24-nexthop-internet|131.253.61.0/24|Internet|
|mi-131-253-62-23-nexthop-internet|131.253.62.0/23|Internet|
|mi-131-253-64-18-nexthop-internet|131.253.64.0/18|Internet|
|mi-131-253-128-17-nexthop-internet|131.253.128.0/17|Internet|
|mi-132-245-16-nexthop-internet|132.245.0.0/16|Internet|
|mi-134-170-16-nexthop-internet|134.170.0.0/16|Internet|
|mi-134-177-16-nexthop-internet|134.177.0.0/16|Internet|
|mi-137-116-15-nexthop-internet|137.116.0.0/15|Internet|
|mi-137-135-16-nexthop-internet|137.135.0.0/16|Internet|
|mi-138-91-16-nexthop-internet|138.91.0.0/16|Internet|
|mi-138-196-16-nexthop-internet|138.196.0.0/16|Internet|
|mi-139-217-16-nexthop-internet|139.217.0.0/16|Internet|
|mi-139-219-16-nexthop-internet|139.219.0.0/16|Internet|
|mi-141-251-16-nexthop-internet|141.251.0.0/16|Internet|
|mi-146-147-16-nexthop-internet|146.147.0.0/16|Internet|
|mi-147-243-16-nexthop-internet|147.243.0.0/16|Internet|
|mi-150-171-16-nexthop-internet|150.171.0.0/16|Internet|
|mi-150-242-48-22-nexthop-internet|150.242.48.0/22|Internet|
|mi-157-54-15-nexthop-internet|157.54.0.0/15|Internet|
|mi-157-56-14-nexthop-internet|157.56.0.0/14|Internet|
|mi-157-60-16-nexthop-internet|157.60.0.0/16|Internet|
|mi-167-105-16-nexthop-internet|167.105.0.0/16|Internet|
|mi-167-220-16-nexthop-internet|167.220.0.0/16|Internet|
|mi-168-61-16-nexthop-internet|168.61.0.0/16|Internet|
|mi-168-62-15-nexthop-internet|168.62.0.0/15|Internet|
|mi-191-232-13-nexthop-internet|191.232.0.0/13|Internet|
|mi-192-32-16-nexthop-internet|192.32.0.0/16|Internet|
|mi-192-48-225-24-nexthop-internet|192.48.225.0/24|Internet|
|mi-192-84-159-24-nexthop-internet|192.84.159.0/24|Internet|
|mi-192-84-160-23-nexthop-internet|192.84.160.0/23|Internet|
|mi-192-197-157-24-nexthop-internet|192.197.157.0/24|Internet|
|mi-193-149-64-19-nexthop-internet|193.149.64.0/19|Internet|
|mi-193-221-113-24-nexthop-internet|193.221.113.0/24|Internet|
|mi-194-69-96-19-nexthop-internet|194.69.96.0/19|Internet|
|mi-194-110-197-24-nexthop-internet|194.110.197.0/24|Internet|
|mi-198-105-232-22-nexthop-internet|198.105.232.0/22|Internet|
|mi-198-200-130-24-nexthop-internet|198.200.130.0/24|Internet|
|mi-198-206-164-24-nexthop-internet|198.206.164.0/24|Internet|
|mi-199-60-28-24-nexthop-internet|199.60.28.0/24|Internet|
|mi-199-74-210-24-nexthop-internet|199.74.210.0/24|Internet|
|mi-199-103-90-23-nexthop-internet|199.103.90.0/23|Internet|
|mi-199-103-122-24-nexthop-internet|199.103.122.0/24|Internet|
|mi-199-242-32-20-nexthop-internet|199.242.32.0/20|Internet|
|mi-199-242-48-21-nexthop-internet|199.242.48.0/21|Internet|
|mi-202-89-224-20-nexthop-internet|202.89.224.0/20|Internet|
|mi-204-13-120-21-nexthop-internet|204.13.120.0/21|Internet|
|mi-204-14-180-22-nexthop-internet|204.14.180.0/22|Internet|
|mi-204-79-135-24-nexthop-internet|204.79.135.0/24|Internet|
|mi-204-79-179-24-nexthop-internet|204.79.179.0/24|Internet|
|mi-204-79-181-24-nexthop-internet|204.79.181.0/24|Internet|
|mi-204-79-188-24-nexthop-internet|204.79.188.0/24|Internet|
|mi-204-79-195-24-nexthop-internet|204.79.195.0/24|Internet|
|mi-204-79-196-23-nexthop-internet|204.79.196.0/23|Internet|
|mi-204-79-252-24-nexthop-internet|204.79.252.0/24|Internet|
|mi-204-152-18-23-nexthop-internet|204.152.18.0/23|Internet|
|mi-204-152-140-23-nexthop-internet|204.152.140.0/23|Internet|
|mi-204-231-192-24-nexthop-internet|204.231.192.0/24|Internet|
|mi-204-231-194-23-nexthop-internet|204.231.194.0/23|Internet|
|mi-204-231-197-24-nexthop-internet|204.231.197.0/24|Internet|
|mi-204-231-198-23-nexthop-internet|204.231.198.0/23|Internet|
|mi-204-231-200-21-nexthop-internet|204.231.200.0/21|Internet|
|mi-204-231-208-20-nexthop-internet|204.231.208.0/20|Internet|
|mi-204-231-236-24-nexthop-internet|204.231.236.0/24|Internet|
|mi-205-174-224-20-nexthop-internet|205.174.224.0/20|Internet|
|mi-206-138-168-21-nexthop-internet|206.138.168.0/21|Internet|
|mi-206-191-224-19-nexthop-internet|206.191.224.0/19|Internet|
|mi-207-46-16-nexthop-internet|207.46.0.0/16|Internet|
|mi-207-68-128-18-nexthop-internet|207.68.128.0/18|Internet|
|mi-208-68-136-21-nexthop-internet|208.68.136.0/21|Internet|
|mi-208-76-44-22-nexthop-internet|208.76.44.0/22|Internet|
|mi-208-84-21-nexthop-internet|208.84.0.0/21|Internet|
|mi-209-240-192-19-nexthop-internet|209.240.192.0/19|Internet|
|mi-213-199-128-18-nexthop-internet|213.199.128.0/18|Internet|
|mi-216-32-180-22-nexthop-internet|216.32.180.0/22|Internet|
|mi-216-220-208-20-nexthop-internet|216.220.208.0/20|Internet|
|mi-23-96-13-nexthop-internet|23.96.0.0/13|Internet|
|mi-42-159-16-nexthop-internet|42.159.0.0/16|Internet|
|mi-51-13-17-nexthop-internet|51.13.0.0/17|Internet|
|mi-51-107-16-nexthop-internet|51.107.0.0/16|Internet|
|mi-51-116-16-nexthop-internet|51.116.0.0/16|Internet|
|mi-51-120-16-nexthop-internet|51.120.0.0/16|Internet|
|mi-51-120-128-17-nexthop-internet|51.120.128.0/17|Internet|
|mi-51-124-16-nexthop-internet|51.124.0.0/16|Internet|
|mi-102-37-18-nexthop-internet|102.37.0.0/18|Internet|
|mi-102-133-16-nexthop-internet|102.133.0.0/16|Internet|
|mi-199-30-16-20-nexthop-internet|199.30.16.0/20|Internet|
|mi-204-79-180-24-nexthop-internet|204.79.180.0/24|Internet|
||||

## <a name="next-steps"></a>Volgende stappen

- Zie [SQL Database Advanced Data Security](sql-database-managed-instance.md)voor een overzicht.
- Meer informatie over het [instellen van een nieuw virtueel Azure-netwerk](sql-database-managed-instance-create-vnet-subnet.md) of een bestaand Virtueel [Azure-netwerk](sql-database-managed-instance-configure-vnet-subnet.md) waar u beheerde exemplaren implementeren.
- [Bereken de grootte van het subnet](sql-database-managed-instance-determine-size-vnet-subnet.md) waar u de beheerde instanties wilt implementeren.
- Meer informatie over het maken van een beheerde instantie:
  - Vanuit de [Azure-portal](sql-database-managed-instance-get-started.md).
  - Door [PowerShell](scripts/sql-database-create-configure-managed-instance-powershell.md)te gebruiken.
  - Met behulp van [een Azure Resource Manager-sjabloon](https://azure.microsoft.com/resources/templates/101-sqlmi-new-vnet/).
  - Door [een Azure Resource Manager-sjabloon te gebruiken (met JumpBox, inclusief SSMS)](https://azure.microsoft.com/resources/templates/201-sqlmi-new-vnet-w-jumpbox/). 

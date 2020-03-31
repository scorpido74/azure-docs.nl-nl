---
title: Veelgestelde vragen - Azure Database Migration Service
description: Veelgestelde vragen over het gebruik van Azure Database Migration Service om databasemigraties uit te voeren.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: a664f12843585ac7524cf8d51aef156d15d32504
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650978"
---
# <a name="faq-about-using-azure-database-migration-service"></a>Veelgestelde vragen over het gebruik van Azure Database Migration Service

In dit artikel worden veelgestelde vragen over het gebruik van Azure Database Migration Service weergegeven, samen met gerelateerde antwoorden.

## <a name="overview"></a>Overzicht

**V. Wat is Azure Database Migration Service?**
Azure Database Migration Service is een volledig beheerde service die is ontworpen om naadloze migraties van meerdere databasebronnen naar Azure Data-platforms mogelijk te maken met minimale downtime. De service bevindt zich momenteel in algemene beschikbaarheid, met voortdurende ontwikkelingsinspanningen gericht op:

* Betrouwbaarheid en prestaties.
* Iteratieve toevoeging van bron-doelparen.
* Blijveninvesteren in frictievrije migraties.

**V. Welke bron-/doelparen ondersteunt Azure Database Migration Service momenteel?**
De service ondersteunt momenteel verschillende bron-/doelparen of migratiescenario's. Zie het artikel [Status van migratiescenario's dat wordt ondersteund door de Azure Database Migration Service](https://docs.microsoft.com/azure/dms/resource-scenario-status)voor een volledige lijst met de status van elk beschikbaar migratiescenario.

Andere migratiescenario's zijn in preview en vereisen het indienen van een nominatie via de DMS Preview-site. Zie de [DMS Preview-site](https://aka.ms/dms-preview/)voor een volledige lijst van de scenario's in preview en om u aan te melden om deel te nemen aan een van deze aanbiedingen.

**V. Welke versies van SQL Server ondersteunt Azure Database Migration Service als bron?**
Bij het migreren van SQL Server zijn ondersteunde bronnen voor Azure Database Migration Service SQL Server 2005 tot en met SQL Server 2019.

**V: Wat is het verschil tussen een offline- en een onlinemigratie bij het gebruik van Azure Database Migration Service?**
U Azure Database Migration Service gebruiken om offline en online migraties uit te voeren. Bij een *offlinemigratie* begint de uitvaltijd van de toepassing wanneer de migratie wordt gestart. Met een *online* migratie is downtime beperkt tot de tijd om te stoppen aan het einde van de migratie. We raden u aan eerst een offlinemigratie te testen om te bepalen of de downtime aanvaardbaar is. Zo niet, voer dan een onlinemigratie uit.

> [!NOTE]
> Als u Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, moet u een instantie maken op basis van de prijscategorie Premium. Zie de pagina met [prijzen](https://azure.microsoft.com/pricing/details/database-migration/) van Azure Database Migration Service voor meer informatie.

**V. Hoe verhoudt Azure Database Migration Service zich tot andere hulpprogramma's voor databasemigratie van Microsoft, zoals de Database Migration Assistant (DMA) of SQL Server Migration Assistant (SSMA)?**
Azure Database Migration Service is de voorkeursmethode voor databasemigratie naar Microsoft Azure op schaal. Zie voor meer informatie over hoe azure database migration service zich verhoudt tot andere hulpprogramma's voor databasemigratie van Microsoft en voor aanbevelingen over het gebruik van de service voor verschillende scenario's, de blogposting [Waarin microsoft's Database Migration Tools and Services worden onderscheiden.](https://techcommunity.microsoft.com/t5/microsoft-data-migration/differentiating-microsoft-s-database-migration-tools-and/ba-p/368529)

**V. Hoe verhoudt Azure Database Migration Service zich tot de Azure Migrate-aanbieding?**
Azure Migrate helpt bij de migratie van on-premises virtuele machines naar Azure IaaS. De service beoordeelt de geschiktheid van migratie en prestatiegebaseerde grootte en biedt kostenramingen voor het uitvoeren van uw on-premises virtuele machines in Azure. Azure Migreren is handig voor lift-and-shift migraties van on-premises VM-gebaseerde workloads naar Azure IaaS VM's. In tegenstelling tot Azure Database Migration Service is Azure Migrate echter geen gespecialiseerde databasemigratieservicedie wordt aangeboden voor relationele databaseplatforms van Azure PaaS, zoals Azure SQL Database of Azure SQL Database Managed Instance.

## <a name="setup"></a>Instellen

**V. Wat zijn de vereisten voor het gebruik van Azure Database Migration Service?**
Er zijn verschillende vereisten vereist om ervoor te zorgen dat azure databasemigratieservice probleemloos wordt uitgevoerd bij het uitvoeren van databasemigraties. Sommige van de vereisten zijn van toepassing op alle scenario's (brondoelparen) die door de service worden ondersteund, terwijl andere vereisten uniek zijn voor een specifiek scenario.

Vereisten voor Azure Database Migration Service die veel voorkomen in alle ondersteunde migratiescenario's, zijn de noodzaak om:

* Maak een Microsoft Azure Virtual Network for Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel, dat site-to-site-connectiviteit biedt met uw on-premises bronservers met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)
* Zorg ervoor dat de regels van uw netwerknetwerkbeveiligingsgroep de volgende communicatiepoorten 443, 53, 9354, 445, 12000 niet blokkeren. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)voor meer informatie over het filteren van het virtuele netwerk.
* Wanneer u een firewalltoestel gebruikt voor uw brondatabase(s), moet u mogelijk firewallregels toevoegen om Azure Database Migration Service toegang te geven tot de brondatabase(s) voor migratie.

Zie de gerelateerde zelfstudies in de [documentatie](https://docs.microsoft.com/azure/dms/dms-overview) van azure databasemigration service op docs.microsoft.com voor een lijst met alle vereisten die nodig zijn om te concurreren met specifieke migratiescenario's met Azure Database Migration Service.

**V. Hoe vind ik het IP-adres voor Azure Database Migration Service, zodat ik een lijst met toegestane kan maken voor de firewallregels die worden gebruikt om toegang te krijgen tot mijn brondatabase voor migratie?**
Mogelijk moet u firewallregels toevoegen waarmee Azure Database Migration Service toegang heeft tot uw brondatabase voor migratie. Het IP-adres voor de service is dynamisch, maar als u ExpressRoute gebruikt, wordt dit adres privé toegewezen door uw bedrijfsnetwerk. De eenvoudigste manier om het juiste IP-adres te identificeren, is door in dezelfde brongroep te zoeken als uw ingerichte Azure Database Migration Service-bron om de bijbehorende netwerkinterface te vinden. Meestal begint de naam van de netwerkinterfacebron met het NIC-voorvoegsel en wordt gevolgd door een uniek teken en getalreeks, bijvoorbeeld NIC-jj6tnztnmarpsskr82rbndyp. Als u deze netwerkinterfacebron selecteert, ziet u het IP-adres dat moet worden opgenomen in de lijst toestaan op de Azure-portalpagina voor resourceoverzicht.

Mogelijk moet u ook de poortbron opnemen die SQL Server luistert in de lijst met toegestane gegevens. Standaard is het poort 1433, maar de bron SQL Server kan worden geconfigureerd om te luisteren op andere poorten ook. In dit geval moet u deze poorten ook op de lijst met toegestane plaatsen opnemen. U de poort bepalen waarop SQL Server luistert met behulp van een dynamic management view-query:

```sql
    SELECT DISTINCT
        local_tcp_port
    FROM sys.dm_exec_connections
    WHERE local_tcp_port IS NOT NULL
```

U ook de poort bepalen die SQL Server luistert door het SQL Server-foutlogboek op te vragen:

```sql
    USE master
    GO
    xp_readerrorlog 0, 1, N'Server is listening on'
    GO
```

**V. Hoe stel ik een Microsoft Azure Virtual Network in?**
Terwijl meerdere Microsoft-zelfstudies die u kunnen leiden door het proces van het opzetten van een virtueel netwerk, de officiële documentatie wordt weergegeven in het artikel [Azure Virtual Network](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview).

## <a name="usage"></a>Gebruik

**V. Wat is een overzicht van de stappen die nodig zijn om Azure Database Migration Service te gebruiken om een databasemigratie uit te voeren?**
Tijdens een typische, eenvoudige databasemigratie u:

1. Een doeldatabase(en) maken.
2. Beoordeel uw brondatabase(s).
    * Voor homogene migraties beoordeelt u uw bestaande database(en) met behulp van [DMA.](https://www.microsoft.com/download/details.aspx?id=53595)
    * Voor heterogene migraties (uit concurrentiebronnen) beoordeel je bestaande database(en) met [SSMA.](https://aka.ms/get-ssma) U gebruikt SSMA ook om databaseobjecten te converteren en het schema te migreren naar uw doelplatform.
3. Maak een exemplaar van de Azure Database Migration Service.
4. Maak een migratieproject met de brondatabase(s), doeldatabase(s) en de tabellen die moeten worden gemigreerd.
5. Start de volledige belasting.
6. Kies de volgende validatie.
7. Voer een handmatige omschakeling van uw productieomgeving uit naar de nieuwe clouddatabase.

## <a name="troubleshooting-and-optimization"></a>Probleemoplossing en optimalisatie

**V. Ik ben het opzetten van een migratieproject in DMS, en ik heb moeite met verbinding maken met mijn bron database. Wat moet ik doen?**
Als u tijdens het werken aan migratie geen verbinding maken met uw brondatabasesysteem, maakt u een virtuele machine in het virtuele netwerk waarmee u uw DMS-exemplaar instelt. In de virtuele machine moet u een connecttest kunnen uitvoeren, zoals het gebruik van een UDL-bestand om een verbinding met SQL Server te testen of robo 3T downloaden om MongoDB-verbindingen te testen. Als de verbindingstest slaagt, hebt u geen probleem met het maken van verbinding met uw brondatabase. Als de verbindingstest niet slaagt, neemt u contact op met de netwerkbeheerder.

**V. Waarom is mijn Azure Database Migration Service niet beschikbaar of gestopt?**
Als de gebruiker Azure Database Migration Service (DMS) expliciet stopt of als de service gedurende 24 uur inactief is, wordt de service in een gestopte of automatisch onderbroken status. In elk geval is de service niet beschikbaar en in een gestopte status.  Als u actieve migraties wilt hervatten, start u de service opnieuw.

**V. Zijn er aanbevelingen voor het optimaliseren van de prestaties van Azure Database Migration Service?**
U een paar dingen doen om de migratie van uw database te versnellen met behulp van de service:

* Gebruik de multi CPU General Purpose Pricing Tier wanneer u uw service-instantie maakt, zodat de service gebruik kan maken van meerdere vCPU's voor parallellen en snellere gegevensoverdracht.
* Schaal uw Azure SQL Database-doelinstantie tijdelijk op naar de SKU-niveau Premium tijdens de gegevensmigratiebewerking om azure SQL Database-beperking te minimaliseren die van invloed kan zijn op gegevensoverdrachtsactiviteiten bij het gebruik van SKU's op een lager niveau.

## <a name="next-steps"></a>Volgende stappen

Zie het artikel [Wat is de Azure Database Migration Service](dms-overview.md)voor een overzicht van de Azure Database Migration Service en regionale beschikbaarheid.

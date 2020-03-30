---
title: Uitschalen
description: Software as a Service (SaaS) ontwikkelaars kunnen eenvoudig elastische, schaalbare databases in de cloud maken met behulp van deze tools
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.date: 01/25/2019
ms.openlocfilehash: 44c6147402cbe05e62c091863cb0bd4f9235bfab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80061647"
---
# <a name="scaling-out-with-azure-sql-database"></a>Uitbreiden met Azure SQL Database
U Azure SQL-databases eenvoudig uitschalen met de tools **voor Elastic Database.** Met deze hulpprogramma's en functies u de databasebronnen van **Azure SQL Database** gebruiken om oplossingen te maken voor transactionele workloads, en met name SaaS-toepassingen (Software as a Service). Elastic Database-functies bestaan uit:

* [Elastic Database-clientbibliotheek](sql-database-elastic-database-client-library.md): De clientbibliotheek is een functie waarmee u geshard databases maken en onderhouden.  Zie [Aan de slag met hulpmiddelen voor elastische database.](sql-database-elastic-scale-get-started.md)
* [Elastic Database split-merge tool:](sql-database-elastic-scale-overview-split-and-merge.md)verplaatst gegevens tussen geshard databases. Deze tool is handig voor het verplaatsen van gegevens van een multi-tenant database naar een single-tenant database (of vice versa). Zie Zelfstudie van het [hulpprogramma Onderdelensamenvoegen elastische database](sql-database-elastic-scale-configure-deploy-split-and-merge.md).
* [Elastische databasetaken:](elastic-jobs-overview.md)gebruik taken om grote aantallen Azure SQL-databases te beheren. Voer eenvoudig administratieve bewerkingen uit, zoals schemawijzigingen, referentiesbeheer, referentiegegevensupdates, prestatiegegevensverzameling of tenant (klant) telemetrieverzameling met behulp van taken.
* [Elastic Database-query](sql-database-elastic-query-overview.md) (voorbeeld): hiermee u een Transact-SQL-query uitvoeren die meerdere databases omvat. Dit maakt verbinding met rapportagetools zoals Excel, Power BI, Tableau, enz.
* [Elastische transacties:](sql-database-elastic-transactions-overview.md)met deze functie u transacties uitvoeren die meerdere databases in Azure SQL-database omvatten. Elastische databasetransacties zijn beschikbaar voor .NET-toepassingen met ADO .NET en integreren met de vertrouwde programmeerervaring met behulp van de [klassen System.Transaction](https://msdn.microsoft.com/library/system.transactions.aspx).

De volgende afbeelding toont een architectuur die de **eigenschappen van** elastic database bevat in relatie tot een verzameling databases.

In deze afbeelding vertegenwoordigen kleuren van de database schema's. Databases met dezelfde kleur delen hetzelfde schema.

1. Een set **Azure SQL-databases** wordt gehost op Azure met behulp van sharding-architectuur.
2. De **elastic database-clientbibliotheek** wordt gebruikt om een shardset te beheren.
3. Een subset van de databases wordt in een **elastische pool**geplaatst. (Zie [Wat is een zwembad?](sql-database-elastic-pool.md)).
4. Een **Elastic Database-taak** voert geplande of ad hoc T-SQL-scripts uit op alle databases.
5. Het **gereedschap splitsen wordt** gebruikt om gegevens van de ene scherf naar de andere te verplaatsen.
6. Met de **query Elastic Database** u een query schrijven die alle databases in de shardset omvat.
7. **Met elastische transacties** u transacties uitvoeren die meerdere databases omvatten. 

![Hulpprogramma's voor elastische databases][1]

## <a name="why-use-the-tools"></a>Waarom de tools gebruiken?
Het bereiken van elasticiteit en schaal voor cloudtoepassingen is eenvoudig geweest voor VM's en blob-opslag - voeg eenheden eenvoudig toe of trek af of verhoog het vermogen. Maar het is een uitdaging gebleven voor stateful data processing in relationele databases. Uitdagingen ontstonden in deze scenario's:

* Groeiende en krimpende capaciteit voor de relationele database deel van uw werklast.
* Hotspots beheren die van invloed kunnen zijn op een specifieke subset van gegevens, zoals een drukke eindklant (tenant).

Traditioneel, scenario's als deze zijn aangepakt door te investeren in grootschaligere database servers ter ondersteuning van de toepassing. Deze optie is echter beperkt in de cloud waar alle verwerking plaatsvindt op vooraf gedefinieerde grondstoffenhardware. In plaats daarvan biedt het distribueren van gegevens en verwerking over veel identiek gestructureerde databases (een scale-outpatroon dat bekend staat als "sharding") een alternatief voor traditionele scale-upbenaderingen, zowel in termen van kosten als elasticiteit.

## <a name="horizontal-and-vertical-scaling"></a>Horizontaal en verticaal schalen
De volgende afbeelding toont de horizontale en verticale dimensies van schalen, die de basismanieren zijn waarop de elastische databases kunnen worden geschaald.

![Horizontale versus verticale scale-out][2]

Horizontale schaling verwijst naar het toevoegen of verwijderen van databases om de capaciteit of algehele prestaties aan te passen, ook wel "scaling out" genoemd. Sharding, waarbij gegevens worden verdeeld over een verzameling identiek gestructureerde databases, is een veelvoorkomende manier om horizontale schaling te implementeren.  

Verticale schaling verwijst naar het vergroten of verkleinen van de rekengrootte van een afzonderlijke database, ook wel 'opschalen' genoemd.

De meeste databasetoepassingen op cloudschaal maken gebruik van een combinatie van deze twee strategieën. Een Software as a Service-toepassing kan bijvoorbeeld horizontale schaling gebruiken om nieuwe eindklanten en verticale schaling in te richten, zodat de database van elke eindgebruiker resources kan groeien of verkleinen als dat nodig is voor de werkbelasting.

* Horizontale schaling wordt beheerd met behulp van de [elastic database-clientbibliotheek](sql-database-elastic-database-client-library.md).
* Verticale schaling wordt uitgevoerd met Azure PowerShell-cmdlets om de servicelaag te wijzigen of door databases in een elastische groep te plaatsen.

## <a name="sharding"></a>Sharding
*Sharding* is een techniek waarbij grote hoeveelheden identiek gestructureerde gegevens worden gedistribueerd over een aantal onafhankelijke databases. Het is vooral populair bij cloudontwikkelaars die Software as a Service (SAAS)-aanbiedingen maken voor eindklanten of bedrijven. Deze eindklanten worden vaak aangeduid als "huurders". Om verschillende redenen kan sharding nodig zijn:  

* De totale hoeveelheid gegevens is te groot om binnen de beperkingen van een individuele database te passen
* De transactiedoorvoer van de totale werkbelasting overtreft de mogelijkheden van een afzonderlijke database
* Huurders kunnen fysieke isolatie van elkaar vereisen, zodat afzonderlijke databases nodig zijn voor elke tenant
* Verschillende secties van een database moeten mogelijk in verschillende regio's verblijven om naleving, prestaties of geopolitieke redenen.

In andere scenario's, zoals het innemen van gegevens van gedistribueerde apparaten, kan sharding worden gebruikt om een set databases te vullen die tijdelijk zijn georganiseerd. Een aparte database kan bijvoorbeeld worden toegewezen aan elke dag of week. In dat geval kan de shardingssleutel een geheel getal zijn dat de datum vertegenwoordigt (aanwezig in alle rijen van de geshardtabellen) en query's die informatie voor een datumbereik ophalen, moeten door de toepassing worden doorgestuurd naar de subset van databases die het bereik in kwestie bestrijken.

Sharding werkt het beste wanneer elke transactie in een toepassing kan worden beperkt tot één waarde van een shardingsleutel. Dat zorgt ervoor dat alle transacties lokaal zijn voor een specifieke database.

## <a name="multi-tenant-and-single-tenant"></a>Multi-tenant en single-tenant
Sommige toepassingen gebruiken de eenvoudigste benadering van het maken van een aparte database voor elke tenant. Deze benadering is het **shardingspatroon voor één tenant** dat isolatie, back-up/herstelmogelijkheid en resourceschaling biedt op basis van de granulariteit van de tenant. Bij één tenantsharding is elke database gekoppeld aan een specifieke tenant-id-waarde (of klantsleutelwaarde), maar die sleutel hoeft niet altijd aanwezig te zijn in de gegevens zelf. Het is de verantwoordelijkheid van de toepassing om elk verzoek door te sturen naar de juiste database - en de clientbibliotheek kan dit vereenvoudigen.

![Eén tenant versus multitenant][4]

Andere scenario's verpakken meerdere tenants samen in databases, in plaats van ze te isoleren in afzonderlijke databases. Dit patroon is een typisch **multi-tenant sharding patroon** - en het kan worden aangedreven door het feit dat een toepassing grote aantallen kleine tenants beheert. Bij sharding met meerdere tenant's zijn de rijen in de databasetabellen allemaal ontworpen om een sleutel te dragen die de tenant-id of de shardingsleutel identificeert. Nogmaals, de toepassingslaag is verantwoordelijk voor het routeren van het verzoek van een tenant naar de juiste database, en dit kan worden ondersteund door de elastische databaseclientbibliotheek. Bovendien kan rijbeveiliging worden gebruikt om te filteren tot welke rijen elke tenant toegang heeft - zie [Multi-tenanttoepassingen met elastische databasetools en beveiliging op rijniveau](sql-database-elastic-tools-multi-tenant-row-level-security.md). Het opnieuw distribueren van gegevens tussen databases kan nodig zijn met het shardingpatroon voor meerdere tenant's en wordt vergemakkelijkt door het hulpprogramma voor het splitsen van de elastische database. Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md) voor meer informatie over ontwerppatronen voor SaaS-toepassingen met elastische pools.

### <a name="move-data-from-multiple-to-single-tenancy-databases"></a>Gegevens verplaatsen van meerdere naar databases met één huurovereenkomst
Bij het maken van een SaaS-toepassing is het typisch om potentiële klanten een proefversie van de software aan te bieden. In dit geval is het kosteneffectief om een multi-tenant database te gebruiken voor de gegevens. Wanneer een prospect echter klant wordt, is een database met één tenant beter omdat deze betere prestaties biedt. Als de klant tijdens de proefperiode gegevens heeft gemaakt, gebruikt u het [hulpprogramma voor samenvoeging](sql-database-elastic-scale-overview-split-and-merge.md) om de gegevens van de multi-tenant naar de nieuwe database met één tenant te verplaatsen.

## <a name="next-steps"></a>Volgende stappen
Zie Aan de slag met hulpmiddelen [voor elastische database](sql-database-elastic-scale-get-started.md)voor een voorbeeld-app die de clientbibliotheek demonstreert.

Zie Bestaande databases migreren om bestaande databases te converteren om bestaande databases te converteren [om deze uit te schalen.](sql-database-elastic-convert-to-use-elastic-tools.md)

Zie [Prijs- en prestatieoverwegingen voor een elastische pool](sql-database-elastic-pool.md)of maak een nieuwe pool met [elastische pool](sql-database-elastic-pool-manage-portal.md)om de specifieke kenmerken van de elastische pool te bekijken.  

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/sql-database-elastic-scale-introduction/tools.png
[2]:./media/sql-database-elastic-scale-introduction/h_versus_vert.png
[3]:./media/sql-database-elastic-scale-introduction/overview.png
[4]:./media/sql-database-elastic-scale-introduction/single_v_multi_tenant.png


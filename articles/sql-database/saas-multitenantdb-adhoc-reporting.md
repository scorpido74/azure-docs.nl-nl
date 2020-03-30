---
title: Ad hoc rapportagequery's in meerdere databases
description: Voer ad-hocrapportagequery's uit in meerdere SQL-databases in een voorbeeld van een multi-tenant-app.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: AyoOlubeko
ms.author: craigg
ms.reviewer: sstein
ms.date: 10/30/2018
ms.openlocfilehash: c0d1829c52041446b4feb43d8af262265e2680fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822175"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Ad hoc analytics-query's uitvoeren in meerdere Azure SQL-databases

In deze zelfstudie voert u gedistribueerde query's uit in de hele set tenantdatabases om ad hoc interactieve rapportage mogelijk te maken. Deze query's kunnen inzichten extraheren die zijn begraven in de dagelijkse operationele gegevens van de Wingtip Tickets SaaS-app. Als u deze extracties wilt uitvoeren, implementeert u een extra analysedatabase naar de catalogusserver en gebruikt u Elastic Query om gedistribueerde query's in te schakelen.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> 
> * Een ad hoc rapportagedatabase implementeren
> * Gedistribueerde query's uitvoeren in alle tenantdatabases


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip Tickets SaaS Multi-tenant Database app wordt geïmplementeerd. Zie [De Wingtip Tickets SaaS Multi-tenant Database-toepassing implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) als u deze in minder dan vijf minuten wilt implementeren.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* SQL Server Management Studio (SSMS) is geïnstalleerd. Zie SQL Server Management [Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)downloaden en installeren om SSMS te downloaden en te installeren.


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc rapportagepatroon

![adhoc rapportagepatroon](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-toepassingen kunnen de enorme hoeveelheid tenantgegevens analyseren die centraal in de cloud wordt opgeslagen. De analyses geven inzicht in de werking en het gebruik van uw applicatie. Deze inzichten kunnen de ontwikkeling van functies, bruikbaarheidsverbeteringen en andere investeringen in uw apps en services begeleiden.

U kunt gemakkelijk toegang tot deze gegevens krijgen in een database met meerdere tenants, maar dit is niet zo gemakkelijk als de gegevens op schaal zijn verdeeld over misschien wel duizenden databases. Een benadering is het gebruik [van Elastic Query](sql-database-elastic-query-overview.md), waarmee query's in een gedistribueerde set databases met gemeenschappelijk schema. Deze databases kunnen worden verdeeld over verschillende resourcegroepen en abonnementen. Toch moet één gemeenschappelijke login toegang hebben tot het extraheren van gegevens uit alle databases. Elastic Query maakt gebruik van een database met één *hoofd* waarin externe tabellen worden gedefinieerd die tabellen of weergaven in de gedistribueerde (tenant)databases spiegelen. Query's die naar deze hoofddatabase worden verzonden, worden gecompileerd tot een gedistribueerd queryplan, waarbij delen van de query, wanneer nodig, naar de onderliggende tenantdatabases worden gepusht. Elastic Query gebruikt de shardkaart in de catalogusdatabase om de locatie van alle tenantdatabases te bepalen. Setup en query zijn eenvoudig met behulp van standaard [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)en ondersteunen ad hoc query's vanuit hulpprogramma's zoals Power BI en Excel.

Door query's te distribueren over de tenantdatabases, biedt Elastic Query direct inzicht in live productiegegevens. Als Elastic Query echter gegevens uit mogelijk veel databases haalt, kan de querylatentie soms hoger zijn dan bij gelijkwaardige query's die worden ingediend bij één database met meerdere tenants. Zorg ervoor dat u query's ontwerpt om de geretourneerde gegevens te minimaliseren. Elastic Query is vaak het meest geschikt voor het opvragen van kleine hoeveelheden realtime gegevens, in tegenstelling tot het bouwen van veelgebruikte of complexe analysequery's of rapporten. Als query's niet goed presteren, bekijkt u het [uitvoeringsplan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) om te zien welk deel van de query naar de externe database is geduwd. En beoordeel hoeveel gegevens worden geretourneerd. Query's die complexe analytische verwerking vereisen, kunnen beter worden weergegeven door de geëxtraheerde tenantgegevens op te slaan in een database die is geoptimaliseerd voor analysequery's. SQL Database en SQL Data Warehouse kunnen de analytics-database hosten.

Dit patroon voor analytics wordt uitgelegd in de [tenant analytics tutorial](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Download de broncode en scripts van de Wingtip Tickets SaaS Multi-tenant Database-toepassing

De Wingtip Tickets SaaS Multi-tenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub repo. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.

## <a name="create-ticket-sales-data"></a>Gegevens over kaartverkoop maken

Als u query's wilt uitvoeren tegen een interessantere gegevensset, maakt u gegevens over de kaartverkoop door de ticketgenerator uit te voeren.

1. In de *PowerShell ISE,* open de ... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReporting.ps1* script en stel de volgende waarden in:
   * **$DemoScenario** = 1, **Koop tickets voor evenementen op alle locaties.**
2. Druk op **F5** om het script uit te voeren en de kaartverkoop te genereren. Terwijl het script wordt uitgevoerd, gaat u verder met de stappen in deze zelfstudie. De ticketgegevens worden opgevraagd in de sectie *Ad hoc gedistribueerde query's uitvoeren,* dus wacht tot de ticketgenerator is voltooid.

## <a name="explore-the-tenant-tables"></a>De tenanttabellen verkennen 

In de Wingtip Tickets SaaS Multi-tenant Database-toepassing worden tenants opgeslagen in een hybride tenantbeheermodel - waarbij tenantgegevens worden opgeslagen in een database met meerdere tenants of in één tenantdatabase en tussen de twee kunnen worden verplaatst. Bij het opvragen in alle tenantdatabases is het belangrijk dat Elastic Query de gegevens kan behandelen alsof ze deel uitmaken van één logische database die wordt geshard door tenant. 

Om dit patroon te bereiken, bevatten alle tenanttabellen een *VenueId-kolom* die aangeeft tot welke tenant de gegevens behoren. De *VenueId* wordt berekend als een hash van de naam Venue, maar elke aanpak kan worden gebruikt om een unieke waarde voor deze kolom te introduceren. Deze benadering is vergelijkbaar met de manier waarop de tenantsleutel wordt berekend voor gebruik in de catalogus. Tabellen met *VenueId* worden door Elastic Query gebruikt om query's te parallellopen en naar de juiste externe tenantdatabase te duwen. Dit vermindert drastisch de hoeveelheid gegevens die wordt geretourneerd en resulteert in een toename van de prestaties, vooral wanneer er meerdere tenants waarvan de gegevens worden opgeslagen in eenmalige tenant databases.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>De database implementeren die wordt gebruikt voor ad-hoc gedistribueerde query's

Met deze oefening wordt de *adhocrapportagedatabase* geïmplementeerd. Dit is de hoofddatabase die het schema bevat dat wordt gebruikt voor het opvragen in alle tenantdatabases. De database wordt geïmplementeerd op de bestaande catalogusserver, de server die wordt gebruikt voor alle beheergerelateerde databases in de voorbeeld-app.

1. Open... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReporting.ps1* in de *PowerShell ISE* en stel de volgende waarden in:
   * **$DemoScenario** = 2, **Ad hoc-analysedatabase implementeren**.

2. Druk op **F5** om het script uit te voeren en de *adhocrapportagedatabase* te maken.

In de volgende sectie voegt u schema toe aan de database, zodat het kan worden gebruikt om gedistribueerde query's uit te voeren.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>De 'hoofd'-database configureren voor het uitvoeren van gedistribueerde query's

Met deze oefening wordt schema (de externe gegevensbron en externe tabeldefinities) toegevoegd aan de ad-hocrapportagedatabase waarmee query's in alle tenantdatabases kunnen worden opgevraagd.

1. Open SQL Server Management Studio en maak verbinding met de Adhoc-rapportagedatabase die u in de vorige stap hebt gemaakt. De naam van de database is *adhocreporting*.
2. Open ...\Learning Modules\Operational Analytics\Adhoc Reporting\ *Initialize-AdhocReportingDB.sql* in SSMS.
3. Controleer het SQL-script en noteer het volgende:

   Elastic Query gebruikt een database-scoped referentie om toegang te krijgen tot elk van de tenantdatabases. Deze referentie moet beschikbaar zijn in alle databases en moet normaal gesproken worden verleend aan de minimale rechten die nodig zijn om deze ad hoc query's in te schakelen.

    ![referentie maken](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Door de catalogusdatabase als externe gegevensbron te gebruiken, worden query's gedistribueerd naar alle databases die in de catalogus zijn geregistreerd wanneer de query wordt uitgevoerd. Omdat servernamen voor elke implementatie verschillend zijn, krijgt dit initialisatiescript de locatie@servernamevan de catalogusdatabase door de huidige server (@) op te halen waar het script wordt uitgevoerd.

    ![externe gegevensbron maken](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   De externe tabellen die verwijzen naar tenanttabellen worden gedefinieerd met **DISTRIBUTION = SHARDED(VenueId).** Hiermee wordt een query voor een bepaalde *VenueId* naar de juiste database geleid en worden de prestaties voor veel scenario's verbeterd, zoals in de volgende sectie wordt weergegeven.

    ![externe tabellen maken](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   De lokale tabel *VenueTypes* die is gemaakt en ingevuld. Deze tabel met verwijzingsgegevens is gebruikelijk in alle tenantdatabases, zodat deze hier kan worden weergegeven als een lokale tabel en kan worden gevuld met de algemene gegevens. Voor sommige query's kan dit de hoeveelheid gegevens die tussen de tenantdatabases en de *adhocrapportagedatabase* worden verplaatst, verminderen.

    ![tabel maken](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Als u op deze manier referentietabellen opneemt, moet u het tabelschema en de gegevens bijwerken wanneer u de tenantdatabases bijwerkt.

4. Druk op **F5** om het script uit te voeren en de *adhocrapportagedatabase te initialiseren.* 

Nu u gedistribueerde query's uitvoeren en inzichten verzamelen over alle tenants!

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc gedistribueerde query's uitvoeren

Nu de *adhocrapportagedatabase* is ingesteld, u een aantal gedistribueerde query's uitvoeren. Neem het uitvoeringsplan op voor een beter inzicht in waar de queryverwerking plaatsvindt. 

Wanneer u het uitvoeringsplan inspecteert, zweeft u over de planpictogrammen voor details. 

1. In *SSMS*, open ... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReportingQueries.sql*.
2. Zorg ervoor dat u bent verbonden met de **adhocrapportagedatabase.**
3. Selecteer het menu **Query** en klik op **Uitvoeringsplan werkelijke uitvoering opnemen**
4. Markeer de *welke locaties momenteel zijn geregistreerd?* query, en druk op **F5**.

   De query retourneert de volledige locatielijst en laat zien hoe snel en eenvoudig het is om alle tenants te bevragen en gegevens van elke tenant te retourneren.

   Controleer het plan en zie dat de volledige kosten de externe query zijn, omdat we gewoon naar elke tenantdatabase gaan en de locatiegegevens selecteren.

   ![SELECTEER * UIT dbo. Locaties](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecteer de volgende query en druk op **F5**.

   Met deze query worden gegevens uit de tenantdatabases en de lokale *locatietypen-tabel* (lokaal, omdat het een tabel in de *adhocrapportagedatabase* is) samenvoegt.

   Controleer het plan en zie dat de meeste kosten de externe query is omdat we de locatiegegevens van elke tenant (dbo. Locaties), en doe vervolgens een snelle lokale join met de lokale *VenueTypes* tafel om de vriendelijke naam weer te geven.

   ![Deelnemen aan externe en lokale gegevens](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Selecteer nu de *op welke dag waren de meeste tickets verkocht?* query, en druk op **F5**.

   Deze query doet een beetje meer complexe samenvoegen en aggregatie. Wat belangrijk is om op te merken is dat het grootste deel van de verwerking op afstand wordt gedaan, en nogmaals, we brengen alleen de rijen die we nodig hebben, terug slechts een enkele rij voor de totale ticketverkoop van elke locatie tellen per dag.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Gedistribueerde query's uitvoeren voor alle databases van de tenant
> * Implementeer een ad-hocrapportagedatabase en voeg er schema aan toe om gedistribueerde query's uit te voeren.

Probeer nu de [tenantanalytics-zelfstudie](saas-multitenantdb-tenant-analytics.md) om gegevens te extraheren naar een afzonderlijke analysedatabase voor complexere analyseverwerking.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastische query](sql-database-elastic-query-overview.md)

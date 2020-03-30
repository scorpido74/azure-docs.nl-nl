---
title: Query's rapporteren in meerdere databases
description: Rapportage met meerdere tenant's met gedistribueerde query's.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewers: billgib,ayolubek
ms.date: 01/25/2019
ms.openlocfilehash: c863946934df9990c14e49ef1a0a82bbc55b27c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822084"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Rapportage met meerdere tenant's met gedistribueerde query's

In deze zelfstudie voert u gedistribueerde query's uit in de hele set tenantdatabases voor rapportage. Deze query's kunnen inzichten extraheren die zijn begraven in de dagelijkse operationele gegevens van de Wingtip Tickets SaaS-huurders. Hiervoor implementeert u een extra rapportagedatabase naar de catalogusserver en gebruikt u Elastic Query om gedistribueerde query's in te schakelen.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> 
> * Een rapportagedatabase implementeren
> * Gedistribueerde query's uitvoeren in alle tenantdatabases
> * Hoe globale weergaven in elke database efficiënt query's voor tenants mogelijk kunnen maken


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:


* De Wingtip Tickets SaaS Database Per Tenant app wordt geïmplementeerd. Zie [De SaaS-database van Wingtip Tickets per tenant](saas-dbpertenant-get-started-deploy.md) implementeren en verkennen in minder dan vijf minuten.
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* SQL Server Management Studio (SSMS) is geïnstalleerd. Zie SQL Server Management [Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)downloaden en installeren om SSMS te downloaden en te installeren.


## <a name="cross-tenant-reporting-pattern"></a>Rapportagepatroon voor meerdere huurders

![gedistribueerd querypatroon voor meerdere tenant](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Een van de mogelijkheden met SaaS-toepassingen is om de enorme hoeveelheid tenantgegevens die in de cloud zijn opgeslagen te gebruiken om inzicht te krijgen in de werking en het gebruik van uw toepassing. Deze inzichten kunnen de ontwikkeling van functies, bruikbaarheidsverbeteringen en andere investeringen in uw apps en services begeleiden.

U kunt gemakkelijk toegang tot deze gegevens krijgen in een database met meerdere tenants, maar dit is niet zo gemakkelijk als de gegevens op schaal zijn verdeeld over misschien wel duizenden databases. Een benadering is het gebruik [van Elastic Query](sql-database-elastic-query-overview.md), waarmee query's in een gedistribueerde set databases met gemeenschappelijk schema. Deze databases kunnen worden verdeeld over verschillende brongroepen en abonnementen, maar moeten een algemene aanmelding delen. Elastic Query maakt gebruik van een database met één *hoofd* waarin externe tabellen worden gedefinieerd die tabellen of weergaven in de gedistribueerde (tenant)databases spiegelen. Query's die naar deze hoofddatabase worden verzonden, worden gecompileerd tot een gedistribueerd queryplan, waarbij delen van de query, wanneer nodig, naar de onderliggende tenantdatabases worden gepusht. Elastic Query gebruikt de shardkaart in de catalogusdatabase om de locatie van alle tenantdatabases te bepalen. Het instellen en opvragen van de hoofddatabase is eenvoudig met behulp van standaard [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)en ondersteuning van query's vanuit hulpprogramma's zoals Power BI en Excel.

Door query's te distribueren over de tenantdatabases, biedt Elastic Query direct inzicht in live productiegegevens. Aangezien Elastic Query gegevens uit mogelijk veel databases haalt, kan de querylatentie hoger zijn dan gelijkwaardige query's die zijn ingediend bij één database met meerdere tenants. Ontwerp query's om de gegevens die naar de hoofddatabase worden geretourneerd tot een minimum te beperken. Elastic Query is vaak het meest geschikt voor het opvragen van kleine hoeveelheden realtime gegevens, in tegenstelling tot het bouwen van veelgebruikte of complexe analysequery's of rapporten. Als query's niet goed presteren, bekijkt u het [uitvoeringsplan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) om te zien welk deel van de query naar de externe database wordt geduwd en hoeveel gegevens worden geretourneerd. Query's die complexe aggregatie of analytische verwerking vereisen, kunnen beter worden verwerkt door tenantgegevens te extraheren in een database of gegevensmagazijn die is geoptimaliseerd voor analysequery's. Dit patroon wordt uitgelegd in de [tenant analytics tutorial](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Download de Wingtip Tickets SaaS Database Per Tenant applicatie scripts

De Wingtip Tickets SaaS Multi-tenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.

## <a name="create-ticket-sales-data"></a>Gegevens over kaartverkoop maken

Als u query's wilt uitvoeren tegen een interessantere gegevensset, maakt u gegevens over de kaartverkoop door de ticketgenerator uit te voeren.

1. In de *PowerShell ISE,* open de ... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReporting.ps1* script en stel de volgende waarde in:
   * **$DemoScenario** = 1, **Koop tickets voor evenementen op alle locaties.**
2. Druk op **F5** om het script uit te voeren en de kaartverkoop te genereren. Terwijl het script wordt uitgevoerd, gaat u verder met de stappen in deze zelfstudie. De ticketgegevens worden opgevraagd in de sectie *Ad hoc gedistribueerde query's uitvoeren,* dus wacht tot de ticketgenerator is voltooid.

## <a name="explore-the-global-views"></a>Ontdek de wereldwijde opvattingen

In de Wingtip Tickets SaaS Database Per Tenant applicatie krijgt elke tenant een database. De gegevens in de databasetabellen worden dus beperkt tot het perspectief van één tenant. Wanneer u echter in alle databases wordt opgevraagd, is het belangrijk dat Elastic Query de gegevens kan behandelen alsof deze deel uitmaken van één logische database die wordt geshard door tenant. 

Om dit patroon te simuleren, wordt een set 'globale' weergaven toegevoegd aan de tenantdatabase die een tenant-id projecteert in elk van de tabellen die wereldwijd worden opgevraagd. In de weergave *VenueEvents* wordt bijvoorbeeld een berekende *VenueId* toegevoegd aan de kolommen die vanuit de tabel *Gebeurtenissen* worden geprojecteerd. Op dezelfde manier voegen de weergaven *VenueTicketAankopen* en *VenueTickets* een berekende *VenueId-kolom* toe die wordt geprojecteerd op basis van hun respectievelijke tabellen. Deze weergaven worden door Elastic Query gebruikt om query's te parallelliseren en naar de juiste externe tenantdatabase te duwen wanneer er een *VenueId-kolom* aanwezig is. Dit vermindert de hoeveelheid geretourneerde gegevens drastisch en resulteert in een aanzienlijke toename van de prestaties voor veel query's. Deze globale weergaven zijn vooraf gemaakt in alle tenantdatabases.

1. Open SSMS en [maak verbinding&lt;&gt; met de tenants1- USER-server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. **Databases**uitvouwen, met de rechtermuisknop _op contosoconcerthall_klikken en **Nieuwe query selecteren**.
1. Voer de volgende query's uit om het verschil tussen de tabellen met één tenant en de globale weergaven te verkennen:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

In deze weergaven wordt de *VenueId* berekend als een hash van de naam Venue, maar elke benadering kan worden gebruikt om een unieke waarde te introduceren. Deze benadering is vergelijkbaar met de manier waarop de tenantsleutel wordt berekend voor gebruik in de catalogus.

Ga als lid van het onderzoek naar de definitie van de weergave *Locaties:*

1. Vouw **in Object Explorer** **contosoconcerthall-weergaven** > **Views**uit:

   ![Weergaven](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klik met de rechtermuisknop op **dbo. Locaties.**
3. **Scriptweergave selecteren als** > **MAKEN naar** > **nieuw queryeditorvenster**

Script een van de andere *venue* weergaven om te zien hoe ze de *VenueId*toevoegen.

## <a name="deploy-the-database-used-for-distributed-queries"></a>De database implementeren die wordt gebruikt voor gedistribueerde query's

Met deze oefening wordt de _adhocrapportagedatabase_ geïmplementeerd. Dit is de hoofddatabase die het schema bevat dat wordt gebruikt voor het opvragen in alle tenantdatabases. De database wordt geïmplementeerd op de bestaande catalogusserver, de server die wordt gebruikt voor alle beheergerelateerde databases in de voorbeeld-app.

1. in *PowerShell ISE*, open ... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReporting.ps1*. 

1. Stel **$DemoScenario = 2**, Ad _hoc-rapportagedatabase implementeren_.

1. Druk op **F5** om het script uit te voeren en de *adhocrapportagedatabase* te maken.

In de volgende sectie voegt u schema toe aan de database, zodat het kan worden gebruikt om gedistribueerde query's uit te voeren.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>De 'hoofd'-database configureren voor het uitvoeren van gedistribueerde query's

Met deze oefening wordt schema (de externe gegevensbron en externe tabeldefinities) toegevoegd aan de _adhocrapportagedatabase_ om query's in alle tenantdatabases mogelijk te maken.

1. Open SQL Server Management Studio en maak verbinding met de Adhoc-rapportagedatabase die u in de vorige stap hebt gemaakt. De naam van de database is *adhocreporting*.
2. Open ...\Learning Modules\Operational Analytics\Adhoc Reporting\ _Initialize-AdhocReportingDB.sql_ in SSMS.
3. Controleer het SQL-script en opmerking:

   Elastic Query gebruikt een database-scoped referentie om toegang te krijgen tot elk van de tenantdatabases. Deze referentie moet beschikbaar zijn in alle databases en moet normaal gesproken worden verleend aan de minimale rechten die nodig zijn om deze query's in te schakelen.

    ![referentie maken](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Met de catalogusdatabase als externe gegevensbron worden query's gedistribueerd naar alle databases die in de catalogus zijn geregistreerd op het moment dat de query wordt uitgevoerd. Aangezien servernamen voor elke implementatie verschillend zijn, krijgt dit script de@servernamelocatie van de catalogusdatabase van de huidige server (@) waar het script wordt uitgevoerd.

    ![externe gegevensbron maken](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   De externe tabellen die verwijzen naar de globale weergaven die in de vorige sectie zijn beschreven en gedefinieerd met **DISTRIBUTION = SHARDED(VenueId)**. Omdat elke *VenueId* naar een afzonderlijke database wordt toegewezen, verbetert dit de prestaties voor veel scenario's, zoals in de volgende sectie wordt weergegeven.

    ![externe tabellen maken](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   De lokale tabel _VenueTypes_ die is gemaakt en ingevuld. Deze tabel met verwijzingsgegevens is gebruikelijk in alle tenantdatabases, zodat deze hier kan worden weergegeven als een lokale tabel en kan worden gevuld met de algemene gegevens. Voor sommige query's kan het hebben van deze tabel die in de hoofddatabase is gedefinieerd, de hoeveelheid gegevens verminderen die naar de hoofddatabase moet worden verplaatst.

    ![tabel maken](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Als u op deze manier referentietabellen opneemt, moet u het tabelschema en de gegevens bijwerken wanneer u de tenantdatabases bijwerkt.

4. Druk op **F5** om het script uit te voeren en de *adhocrapportagedatabase te initialiseren.* 

Nu u gedistribueerde query's uitvoeren en inzichten verzamelen over alle tenants!

## <a name="run-distributed-queries"></a>Gedistribueerde query's uitvoeren

Nu de *adhocrapportagedatabase* is ingesteld, u een aantal gedistribueerde query's uitvoeren. Neem het uitvoeringsplan op voor een beter inzicht in waar de queryverwerking plaatsvindt. 

Wanneer u het uitvoeringsplan inspecteert, zweeft u over de planpictogrammen voor details. 

Belangrijk om op te merken, is dat het instellen **van DISTRIBUTIE = SHARDED (VenueId)** wanneer de externe gegevensbron is gedefinieerd verbetert de prestaties voor veel scenario's. Zoals elke *VenueId* kaarten naar een individuele database, filtering is gemakkelijk op afstand gedaan, het retourneren van alleen de benodigde gegevens.

1. Open... \\Learning\\Modules\\Operational Analytics\\Adhoc Reporting*Demo-AdhocReportingQueries.sql* in SSMS.
2. Zorg ervoor dat u bent verbonden met de **adhocrapportagedatabase.**
3. Selecteer het menu **Query** en klik op **Uitvoeringsplan werkelijke uitvoering opnemen**
4. Markeer de *welke locaties momenteel zijn geregistreerd?* query, en druk op **F5**.

   De query retourneert de volledige locatielijst, die laat zien hoe snel en eenvoudig het is om alle tenants te bevragen en gegevens van elke tenant te retourneren.

   Controleer het plan en zie dat de volledige kosten in de externe query zijn. Elke tenantdatabase voert de query op afstand uit en retourneert de locatiegegevens naar de hoofddatabase.

   ![SELECTEER * UIT dbo. Locaties](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecteer de volgende query en druk op **F5**.

   Met deze query worden gegevens uit de tenantdatabases en de lokale *locatietypen-tabel* (lokaal, omdat het een tabel in de *adhocrapportagedatabase* is) samenvoegt.

   Controleer het plan en zie dat de meeste kosten de externe query zijn. Elke tenantdatabase retourneert de locatiegegevens en voert een lokale join uit met de lokale *VenueTypes-tabel* om de vriendelijke naam weer te geven.

   ![Deelnemen aan externe en lokale gegevens](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Selecteer nu de *op welke dag waren de meeste tickets verkocht?* query, en druk op **F5**.

   Deze query doet een beetje meer complexe samenvoegen en aggregatie. Het grootste deel van de verwerking vindt op afstand plaats.  Alleen enkele rijen, met het dagelijkse aantal tickets per dag van elke locatie, worden teruggestuurd naar de hoofddatabase.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Gedistribueerde query's uitvoeren voor alle databases van de tenant
> * Implementeer een rapportagedatabase en definieer het schema dat nodig is om gedistribueerde query's uit te voeren.


Probeer nu de [tenantanalytics-zelfstudie](saas-tenancy-tenant-analytics.md) om gegevens te extraheren naar een afzonderlijke analysedatabase voor complexere analyseverwerking.

## <a name="additional-resources"></a>Aanvullende bronnen

* Aanvullende [tutorials die voortbouwen op de Wingtip Tickets SaaS Database Per Tenant-toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastische query](sql-database-elastic-query-overview.md)

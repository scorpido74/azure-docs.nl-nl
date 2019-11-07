---
title: Ad hoc-rapportage query's uitvoeren in meerdere Azure SQL-data bases
description: Ad hoc-rapportage query's uitvoeren voor meerdere SQL-data bases in een multi tenant-app-voor beeld.
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
ms.openlocfilehash: 3d345e222dac98a63400dd2661ce92674f2534f6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73692081"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-azure-sql-databases"></a>Ad hoc Analytics-query's uitvoeren voor meerdere Azure SQL-data bases

In deze zelf studie voert u gedistribueerde query's uit op de hele set Tenant databases om ad hoc-interactieve rapportage in te scha kelen. Deze query's kunnen inzicht verkrijgen in de dagelijkse operationele gegevens van de SaaS-app van de Wingtip tickets. Als u deze uittreksels wilt uitvoeren, implementeert u een extra Analytics-Data Base op de catalogus server en gebruikt u elastische query's om gedistribueerde queries in te scha kelen.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> 
> * Een AD-hocrapportage-rapportage database implementeren
> * Gedistribueerde query's uitvoeren in alle Tenant databases


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:

* De Wingtip tickets SaaS multi-tenant-data base-app wordt geïmplementeerd. Zie [de toepassing Wingtip tickets SaaS multi-tenant data base implementeren en verkennen](saas-multitenantdb-get-started-deploy.md) voor meer informatie over de implementatie in minder dan vijf minuten
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* SQL Server Management Studio (SSMS) is geïnstalleerd. Zie [down load SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)als u SSMS wilt downloaden en installeren.


## <a name="ad-hoc-reporting-pattern"></a>Ad hoc-rapportage patroon

![AD-verslaggevings patroon](media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

SaaS-toepassingen kunnen de grote hoeveelheid Tenant gegevens die centraal worden opgeslagen, analyseren in de Cloud. De analyses tonen inzicht in de werking en het gebruik van uw toepassing. Met deze inzichten kunt u de ontwikkeling van functies, verbetering van de bruikbaarheid en andere investeringen in uw apps en services begeleiden.

U kunt gemakkelijk toegang tot deze gegevens krijgen in een database met meerdere tenants, maar dit is niet zo gemakkelijk als de gegevens op schaal zijn verdeeld over misschien wel duizenden databases. Een aanpak is het gebruik van [elastische query's](sql-database-elastic-query-overview.md), waarmee u query's kunt uitvoeren op een gedistribueerde set data bases met een gemeen schappelijk schema. Deze data bases kunnen worden gedistribueerd over verschillende resource groepen en-abonnementen. Maar een veelvoorkomende aanmelding moet toegang hebben tot gegevens uit de data bases ophalen. Elastische Query's maken gebruik van één *Head* -Data Base waarin externe tabellen worden gedefinieerd die spie gelen tabellen of weer gaven in de gedistribueerde (Tenant) data bases. Query's die naar deze hoofddatabase worden verzonden, worden gecompileerd tot een gedistribueerd queryplan, waarbij delen van de query, wanneer nodig, naar de onderliggende tenantdatabases worden gepusht. Elastische Query's gebruiken de Shard-toewijzing in de catalogus database om de locatie van alle Tenant databases te bepalen. Setup en query zijn eenvoudig te gebruiken met behulp van standaard [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)en ondersteunen ad-hoc query's van hulpprogram ma's zoals Power bi en Excel.

Door query's over de Tenant databases te distribueren, biedt elastische query's onmiddellijk inzicht in live productie gegevens. Omdat elastische Query's echter gegevens uit mogelijk veel data bases ophaalt, kan de query latentie soms hoger zijn dan voor gelijkwaardige query's die worden verzonden naar één multi tenant-data base. Zorg ervoor dat u query's ontwerpt om de geretourneerde gegevens te minimaliseren. Elastische Query's zijn vaak het meest geschikt voor het uitvoeren van query's in kleine hoeveel heden gegevens in realtime, in plaats van het maken van vaak gebruikte of complexe analyse query's of-rapporten. Als query's niet goed werken, kijkt u naar het [uitvoerings plan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) om te zien welk gedeelte van de query naar de externe data base is gepusht. En beoordelen hoeveel gegevens er worden geretourneerd. Query's waarvoor complexe analytische verwerking is vereist, kunnen beter worden bediend door de geëxtraheerde Tenant gegevens op te slaan in een Data Base die is geoptimaliseerd voor analyse query's. SQL Database en SQL Data Warehouse kunnen de Analytics-Data Base hosten.

Dit patroon voor analyses wordt uitgelegd in de [zelf studie over Tenant analyse](saas-multitenantdb-tenant-analytics.md).

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>De Wingtip tickets SaaS multi-tenant database toepassings bron code en scripts ophalen

De Wingtip tickets SaaS multi-tenant database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) github opslag plaats. Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.

## <a name="create-ticket-sales-data"></a>Verkoop gegevens van tickets maken

Als u query's wilt uitvoeren op een interessantere gegevensset, maakt u verkoop gegevens van tickets door de ticket Generator uit te voeren.

1. Open in de *Power shell-ISE*de trainings modules...\\\\Operational Analytics\\Adhoc Reporting\\*demo-AdhocReporting. ps1* script en stel de volgende waarden in:
   * **$DemoScenario** = 1, **Koop tickets voor gebeurtenissen op alle locaties**.
2. Druk op **F5** om het script uit te voeren en de verkoop van tickets te genereren. Wanneer het script wordt uitgevoerd, gaat u door met de stappen in deze zelf studie. De ticket gegevens worden in de sectie *ad hoc gedistribueerde Query's uitvoeren* opgevraagd en wachten op het volt ooien van de ticket generator.

## <a name="explore-the-tenant-tables"></a>De Tenant tabellen verkennen 

In de Wingtip tickets SaaS multi-tenant database toepassing worden tenants opgeslagen in een hybride Tenant beheer model: de Tenant gegevens worden opgeslagen in een Data Base met meerdere tenants of in één Tenant database en kunnen tussen de twee worden verplaatst. Bij het uitvoeren van query's op alle Tenant databases is het belang rijk dat elastische Query's de gegevens kunnen behandelen alsof deze deel uitmaken van één logische data base Shard per Tenant. 

Om dit patroon te krijgen, bevatten alle Tenant tabellen een kolom *VenueId* die identificeert met welke Tenant de gegevens horen. De *VenueId* wordt berekend als een hash van de naam van de locatie, maar elke benadering kan worden gebruikt om een unieke waarde voor deze kolom in te voeren. Deze methode is vergelijkbaar met de manier waarop de Tenant sleutel wordt berekend voor gebruik in de catalogus. Tabellen die *VenueId* bevatten, worden door Elastic query gebruikt voor het parallelliseren van query's en om ze naar de juiste externe Tenant database te pushen. Dit reduceert aanzienlijk de hoeveelheid gegevens die wordt geretourneerd en resulteert in een toename van de prestaties, met name wanneer er meerdere tenants zijn waarvan de gegevens worden opgeslagen in afzonderlijke Tenant databases.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>De data base implementeren die wordt gebruikt voor ad-hoc gedistribueerde query's

In deze oefening wordt de *adhocreporting* -data base geïmplementeerd. Dit is de hoofd database die het schema bevat dat wordt gebruikt voor het uitvoeren van query's in alle Tenant databases. De data base wordt geïmplementeerd op de bestaande catalogus server. Dit is de server die wordt gebruikt voor alle aan beheer gerelateerde data bases in de voor beeld-app.

1. Open...\\learning modules\\Operational Analytics\\adhoc Reporting\\*demo-AdhocReporting. ps1* in de *Power shell ISE* en stel de volgende waarden in:
   * **$DemoScenario** = 2, **ad hoc Analytics-Data Base implementeren**.

2. Druk op **F5** om het script uit te voeren en de *adhocreporting* -data base te maken.

In de volgende sectie voegt u een schema toe aan de data base zodat het kan worden gebruikt voor het uitvoeren van gedistribueerde query's.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>De Head-Data Base configureren voor het uitvoeren van gedistribueerde query's

Deze oefening voegt schema (de externe gegevens bron en externe tabel definities) toe aan de AD-hocrapportage waarmee query's kunnen worden doorzocht op alle Tenant databases.

1. Open SQL Server Management Studio en maak verbinding met de ad hoc-rapportage database die u in de vorige stap hebt gemaakt. De naam van de data base is *adhocreporting*.
2. Open. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ *Initialize-AdhocReportingDB. SQL* in SSMS.
3. Controleer het SQL-script en Let op het volgende:

   Elastische Query's gebruiken een Data Base-scoped referentie voor toegang tot elk van de Tenant databases. Deze referentie moet beschikbaar zijn in alle data bases en moet normaal gesp roken de minimale rechten krijgen die nodig zijn om deze ad hoc-query's in te scha kelen.

    ![referentie maken](media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Door de catalogus database als externe gegevens bron te gebruiken, worden query's gedistribueerd naar alle data bases die in de catalogus zijn geregistreerd wanneer de query wordt uitgevoerd. Omdat Server namen verschillend zijn voor elke implementatie, haalt dit initialisatie script de locatie van de catalogus database op door de huidige server (@@servername) op te halen waarin het script wordt uitgevoerd.

    ![externe gegevens bron maken](media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   De externe tabellen die verwijzen naar Tenant tabellen worden gedefinieerd met **distributie = Shard (VenueId)** . Hiermee wordt een query naar een bepaalde *VenueId* gerouteerd naar de juiste data base en worden de prestaties verbeterd voor veel scenario's, zoals wordt weer gegeven in de volgende sectie.

    ![externe tabellen maken](media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   De lokale tabel *VenueTypes* die is gemaakt en ingevuld. Deze referentie gegevens tabel is gebruikelijk in alle Tenant databases, zodat deze kan worden weer gegeven als een lokale tabel en gevuld met de algemene gegevens. Voor sommige query's kan dit de hoeveelheid gegevens die tussen de Tenant databases en de *adhocreporting* -Data Base worden verplaatst, verminderen.

    ![tabel maken](media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Als u verwijzings tabellen op deze manier opneemt, moet u ervoor zorgen dat u het tabel schema en de gegevens bijwerkt wanneer u de Tenant databases bijwerkt.

4. Druk op **F5** om het script uit te voeren en Initialiseer de *adhocreporting* -data base. 

U kunt nu gedistribueerde query's uitvoeren en inzichten verzamelen over alle tenants.

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc gedistribueerde query's uitvoeren

Nu de *adhocreporting* -data base is ingesteld, gaat u naar een aantal gedistribueerde query's en voert u deze uit. Neem het uitvoerings plan op voor een beter begrip van waar de query verwerking plaatsvindt. 

Als u het uitvoerings plan wilt inspecteren, houdt u de muis aanwijzer boven de pictogrammen plannen voor meer informatie. 

1. Open in *SSMS*\\Learning modules\\Operational Analytics\\Adhoc Reporting\\*demo-AdhocReportingQueries. SQL*.
2. Zorg ervoor dat u bent verbonden met de **adhocreporting** -data base.
3. Selecteer het **query** menu en klik op **werkelijke uitvoerings plan toevoegen**
4. Markeer de *locaties die momenteel zijn geregistreerd?* query en druk op **F5**.

   De query retourneert de volledige lijst met locaties en illustreert hoe snel en eenvoudig het is om een query uit te geven op alle tenants en gegevens van elke Tenant te retour neren.

   Inspecteer het plan en controleer of de volledige kosten de externe query zijn omdat we eenvoudigweg naar elke Tenant database gaan en de locatie gegevens selecteren.

   ![SELECT * FROM dbo. Venues](media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Selecteer de volgende query en druk op **F5**.

   Met deze query worden gegevens uit de Tenant databases en de lokale *VenueTypes* -tabel (Local, weer gegeven als een tabel in de *adhocreporting* -data base).

   Inspecteer het plan en controleer of het meren deel van de kosten de externe query is, omdat we de locatie gegevens van elke Tenant opvragen (dbo. Locaties) en vervolgens een snelle lokale samen voeging met de lokale *VenueTypes* -tabel om de beschrijvende naam weer te geven.

   ![Verbinding maken met externe en lokale gegevens](media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Selecteer nu de optie *op welke dag zijn de meeste tickets verkocht?* query en druk op **F5**.

   Deze query heeft een beetje complexere join en aggregatie. Het is belang rijk te weten dat het grootste deel van de verwerking op afstand wordt uitgevoerd en dat er weer meer rijen worden teruggestuurd en dat er slechts één rij wordt geretourneerd voor het totale aantal verkochte tickets per dag van elke locatie.

   ![query](media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Gedistribueerde query's uitvoeren voor alle databases van de tenant
> * Implementeer een AD-hocrapportage en voeg schema toe om gedistribueerde query's uit te voeren.

Probeer nu de [zelf studie voor Tenant analyse](saas-multitenantdb-tenant-analytics.md) om het extra heren van gegevens te verkennen naar een afzonderlijke Analytics-Data Base voor complexere analyse verwerking.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Elastische query](sql-database-elastic-query-overview.md)

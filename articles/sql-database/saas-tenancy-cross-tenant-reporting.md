---
title: Rapportage query's uitvoeren in meerdere Azure SQL-data bases | Microsoft Docs
description: Meerdere Tenant rapporten met behulp van gedistribueerde query's.
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
ms.openlocfilehash: fa8dbbbb09fbdc14049e168afe6eb4810ccc8254
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/26/2019
ms.locfileid: "68570238"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Rapportage over meerdere tenants met behulp van gedistribueerde query's

In deze zelf studie voert u gedistribueerde query's uit op de hele set Tenant databases voor rapportage. Deze query's kunnen inzicht verkrijgen in de dagelijkse operationele gegevens van de Wingtip tickets SaaS-tenants. Om dit te doen, implementeert u een extra rapportage database op de catalogus server en gebruikt u elastische query's om gedistribueerde queries in te scha kelen.


In deze zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> 
> * Een rapportage database implementeren
> * Gedistribueerde query's uitvoeren in alle Tenant databases
> * Hoe globale weer gaven in elke Data Base efficiënte query's mogelijk kunnen maken voor meerdere tenants


U kunt deze zelfstudie alleen voltooien als aan de volgende vereisten wordt voldaan:


* De Wingtip tickets SaaS-data base per Tenant-app wordt geïmplementeerd. Zie [de SaaS-data base van Wingtip tickets implementeren en verkennen per Tenant toepassing](saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* SQL Server Management Studio (SSMS) is geïnstalleerd. Zie [down load SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)als u SSMS wilt downloaden en installeren.


## <a name="cross-tenant-reporting-pattern"></a>Het rapport patroon voor meerdere tenants

![gedistribueerd query patroon voor meerdere tenants](media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Een kans met SaaS-toepassingen is het gebruik van de enorme hoeveelheid Tenant gegevens die in de Cloud is opgeslagen om inzicht te krijgen in de werking en het gebruik van uw toepassing. Met deze inzichten kunt u de ontwikkeling van functies, verbetering van de bruikbaarheid en andere investeringen in uw apps en services begeleiden.

U kunt gemakkelijk toegang tot deze gegevens krijgen in een database met meerdere tenants, maar dit is niet zo gemakkelijk als de gegevens op schaal zijn verdeeld over misschien wel duizenden databases. Een aanpak is het gebruik van [elastische query's](sql-database-elastic-query-overview.md), waarmee u query's kunt uitvoeren op een gedistribueerde set data bases met een gemeen schappelijk schema. Deze data bases kunnen worden gedistribueerd over verschillende resource groepen en-abonnementen, maar moeten een gemeen schappelijke aanmelding delen. Elastische Query's maken gebruik van één *Head* -Data Base waarin externe tabellen worden gedefinieerd die spie gelen tabellen of weer gaven in de gedistribueerde (Tenant) data bases. Query's die naar deze hoofddatabase worden verzonden, worden gecompileerd tot een gedistribueerd queryplan, waarbij delen van de query, wanneer nodig, naar de onderliggende tenantdatabases worden gepusht. Elastische Query's gebruiken de Shard-toewijzing in de catalogus database om de locatie van alle Tenant databases te bepalen. Het instellen en uitvoeren van een query voor de hoofd database is eenvoudig met behulp van standaard [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference)en biedt ondersteuning voor het uitvoeren van query's via hulpprogram ma's als Power bi en Excel.

Door query's over de Tenant databases te distribueren, biedt elastische query's onmiddellijk inzicht in live productie gegevens. Als elastische query gegevens ophaalt uit mogelijk veel data bases, kan de query latentie hoger zijn dan gelijkwaardige query's die worden verzonden naar één multi tenant-data base. Ontwerp query's om de gegevens die worden geretourneerd naar de hoofd database te minimaliseren. Elastische Query's zijn vaak het meest geschikt voor het uitvoeren van query's in kleine hoeveel heden gegevens in realtime, in plaats van het maken van vaak gebruikte of complexe analyse query's of-rapporten. Als query's niet goed pres teren, bekijkt u het [uitvoerings plan](https://docs.microsoft.com/sql/relational-databases/performance/display-an-actual-execution-plan) om te zien welk gedeelte van de query naar de externe data base wordt gepusht en hoeveel gegevens er worden geretourneerd. Query's waarvoor complexe aggregatie of analytische verwerking vereist is, kunnen beter worden afgehandeld door Tenant gegevens te extra heren naar een Data Base of Data Warehouse dat is geoptimaliseerd voor analyse query's. Dit patroon wordt uitgelegd in de [zelf studie over Tenant analyse](saas-tenancy-tenant-analytics.md). 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>De Wingtip tickets SaaS-data base ophalen per Tenant toepassings scripts

De Wingtip tickets SaaS multi-tenant database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) github opslag plaats. Bekijk de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.

## <a name="create-ticket-sales-data"></a>Verkoop gegevens van tickets maken

Als u query's wilt uitvoeren op een interessantere gegevensset, maakt u verkoop gegevens van tickets door de ticket Generator uit te voeren.

1. Open in de *Power shell-ISE*de... \\Trainings modules\\Operational\\Reporting\\AD*demo-AdhocReporting. ps1* script en stel de volgende waarde in:
   * **$DemoScenario** = 1, **Koop tickets voor gebeurtenissen op alle locaties**.
2. Druk op **F5** om het script uit te voeren en de verkoop van tickets te genereren. Wanneer het script wordt uitgevoerd, gaat u door met de stappen in deze zelf studie. De ticket gegevens worden in de sectie *ad hoc gedistribueerde Query's uitvoeren* opgevraagd en wachten op het volt ooien van de ticket generator.

## <a name="explore-the-global-views"></a>De globale weer gaven verkennen

In de Wingtip tickets SaaS-data base per Tenant toepassing krijgt elke Tenant een Data Base. De gegevens in de database tabellen zijn dus gericht op het perspectief van één Tenant. Bij het uitvoeren van query's voor alle data bases is het echter belang rijk dat elastische Query's de gegevens kunnen behandelen alsof deze deel uitmaken van één logische data base Shard per Tenant. 

Als u dit patroon wilt simuleren, wordt er een set globale weer gaven toegevoegd aan de Tenant database waarmee een Tenant-ID wordt gedefinieerd in elk van de tabellen die wereld wijd worden opgevraagd. In de weer gave *VenueEvents* wordt bijvoorbeeld een berekende *VenueId* toegevoegd aan de kolommen die in de tabel *gebeurtenissen* zijn geprojecteerd. Op dezelfde manier voegt de weer gaven *VenueTicketPurchases* en *VenueTickets* een berekende *VenueId* -kolom toe die is geprojecteerd uit de bijbehorende tabellen. Deze weer gaven worden door elastische query's gebruikt voor het parallelliseren van query's en worden naar de juiste externe Tenant database gepusht als er een *VenueId* -kolom aanwezig is. Dit reduceert de hoeveelheid gegevens die wordt geretourneerd aanzienlijk en resulteert in een aanzienlijke toename van de prestaties voor veel query's. Deze globale weer gaven zijn vooraf gemaakt in alle Tenant databases.

1. Open SSMS en [Maak verbinding met de tenants1&lt;-&gt; gebruikers server](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Vouw **data bases**uit, klik met de rechter muisknop op _Contosoconcerthall_en selecteer **nieuwe query**.
1. Voer de volgende query's uit om het verschil tussen de tabellen met één Tenant en de globale weer gaven te verkennen:

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

In deze weer gaven wordt de *VenueId* berekend als een hash van de naam van de locatie, maar elke benadering kan worden gebruikt om een unieke waarde in te voeren. Deze methode is vergelijkbaar met de manier waarop de Tenant sleutel wordt berekend voor gebruik in de catalogus.

De definitie van de weer gave *evenementen* bekijken:

1. Vouw in **objectverkenner** **contosoconcerthall** > **weer gaven**uit:

   ![weergaven](media/saas-tenancy-cross-tenant-reporting/views.png)

2. Klik met de rechter muisknop op **dbo. Locaties**.
3. Selecteer de **script weergave als** > **maken voor het** > **nieuwe query editor-venster**

Scripteer een van de andere *locaties* weergaven om te zien hoe ze de *VenueId*toevoegen.

## <a name="deploy-the-database-used-for-distributed-queries"></a>De data base implementeren die wordt gebruikt voor gedistribueerde query's

In deze oefening wordt de _adhocreporting_ -data base geïmplementeerd. Dit is de hoofd database die het schema bevat dat wordt gebruikt voor het uitvoeren van query's in alle Tenant databases. De data base wordt geïmplementeerd op de bestaande catalogus server. Dit is de server die wordt gebruikt voor alle aan beheer gerelateerde data bases in de voor beeld-app.

1. Open in *Power shell ISE*... \\Trainings modules\\Operational\\Reporting\\AD*demo-AdhocReporting. ps1*. 

1. Stel **$DemoScenario = 2**in, _Implementeer een ad-hoc rapportage database_.

1. Druk op **F5** om het script uit te voeren en de *adhocreporting* -data base te maken.

In de volgende sectie voegt u een schema toe aan de data base zodat het kan worden gebruikt voor het uitvoeren van gedistribueerde query's.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>De Head-Data Base configureren voor het uitvoeren van gedistribueerde query's

Deze oefening voegt schema (de externe gegevens bron en externe tabel definities) toe aan de _adhocreporting_ -data base om query's in te scha kelen voor alle Tenant databases.

1. Open SQL Server Management Studio en maak verbinding met de ad hoc-rapportage database die u in de vorige stap hebt gemaakt. De naam van de data base is *adhocreporting*.
2. Open. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ _Initialize-AdhocReportingDB. SQL_ in SSMS.
3. Bekijk het SQL-script en Let op:

   Elastische Query's gebruiken een Data Base-scoped referentie voor toegang tot elk van de Tenant databases. Deze referentie moet beschikbaar zijn in alle data bases en moet normaal gesp roken de minimale rechten worden verleend die nodig zijn om deze query's in te scha kelen.

    ![referentie maken](media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   Met de catalogus database als externe gegevens bron worden query's gedistribueerd naar alle data bases die in de catalogus zijn geregistreerd op het moment dat de query wordt uitgevoerd. Als server namen verschillend zijn voor elke implementatie, wordt met dit script de locatie van de catalogus database opgehaald van de huidige server@servername(@) waar het script wordt uitgevoerd.

    ![externe gegevens bron maken](media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   De externe tabellen die verwijzen naar de algemene weer gaven die in de vorige sectie zijn beschreven, en die zijn gedefinieerd met **Distribution = Shard (VenueId)** . Omdat elke *VenueId* wordt toegewezen aan een afzonderlijke Data Base, verbetert dit de prestaties voor veel scenario's, zoals wordt weer gegeven in de volgende sectie.

    ![externe tabellen maken](media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   De lokale tabel _VenueTypes_ die is gemaakt en ingevuld. Deze referentie gegevens tabel is gebruikelijk in alle Tenant databases, zodat deze kan worden weer gegeven als een lokale tabel en gevuld met de algemene gegevens. Voor sommige query's kan de hoeveelheid gegevens die moet worden verplaatst naar de hoofd database worden verminderd als deze tabel is gedefinieerd in de hoofd database.

    ![tabel maken](media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Als u verwijzings tabellen op deze manier opneemt, moet u ervoor zorgen dat u het tabel schema en de gegevens bijwerkt wanneer u de Tenant databases bijwerkt.

4. Druk op **F5** om het script uit te voeren en Initialiseer de *adhocreporting* -data base. 

U kunt nu gedistribueerde query's uitvoeren en inzichten verzamelen over alle tenants.

## <a name="run-distributed-queries"></a>Gedistribueerde query's uitvoeren

Nu de *adhocreporting* -data base is ingesteld, gaat u naar een aantal gedistribueerde query's en voert u deze uit. Neem het uitvoerings plan op voor een beter begrip van waar de query verwerking plaatsvindt. 

Als u het uitvoerings plan wilt inspecteren, houdt u de muis aanwijzer boven de pictogrammen plannen voor meer informatie. 

Belang rijk om te weten dat het instellen van **distributie = Shard (VenueId)** wanneer de externe gegevens bron is gedefinieerd, verbetert de prestaties voor veel scenario's. Wanneer elke *VenueId* wordt toegewezen aan een afzonderlijke Data Base, kan het filteren eenvoudig op afstand worden uitgevoerd, waarbij alleen de benodigde gegevens worden geretourneerd.

1. Openen... \\Trainings modules\\Operational\\Reporting\\AD*demo-AdhocReportingQueries. SQL* in SSMS.
2. Zorg ervoor dat u bent verbonden met de **adhocreporting** -data base.
3. Selecteer het **query** menu en klik op **werkelijke uitvoerings plan toevoegen**
4. Markeer de *locaties die momenteel zijn geregistreerd?* query en druk op **F5**.

   De query retourneert de volledige lijst met locaties en illustreert hoe snel en eenvoudig het is om een query uit te geven voor alle tenants en om gegevens van elke Tenant te retour neren.

   Inspecteer het plan en controleer of de volledige kosten zich in de externe query bevinden. Elke Tenant database voert de query op afstand uit en retourneert de locatie-informatie naar de hoofd database.

   ![SELECT * FROM dbo. Venues](media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Selecteer de volgende query en druk op **F5**.

   Met deze query worden gegevens uit de Tenant databases en de lokale *VenueTypes* -tabel (Local, weer gegeven als een tabel in de *adhocreporting* -data base).

   Inspecteer het plan en controleer of het meren deel van de kosten de externe query is. Elke Tenant database retourneert de locatie gegevens en voert een lokale samen voeging uit met de lokale *VenueTypes* -tabel om de beschrijvende naam weer te geven.

   ![Verbinding maken met externe en lokale gegevens](media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Selecteer nu de optie *op welke dag zijn de meeste tickets verkocht?* query en druk op **F5**.

   Deze query heeft een beetje complexere join en aggregatie. De meeste verwerking vindt op afstand plaats.  Alleen enkele rijen met het aantal dagelijkse ticket verkopen per dag worden geretourneerd naar de hoofd database.

   ![query](media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Gedistribueerde query's uitvoeren voor alle databases van de tenant
> * Implementeer een rapportage database en definieer het schema dat vereist is voor het uitvoeren van gedistribueerde query's.


Probeer nu de [zelf studie voor Tenant analyse](saas-tenancy-tenant-analytics.md) om het extra heren van gegevens te verkennen naar een afzonderlijke Analytics-Data Base voor complexere analyse verwerking.

## <a name="additional-resources"></a>Aanvullende resources

* Aanvullende [zelf studies die voortbouwen op de SaaS-data base van Wingtip tickets per Tenant toepassing](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastische query](sql-database-elastic-query-overview.md)

---
title: Schema in een app met meerdere tenants beheren
description: Het schema voor meerdere tenants beheren in een toepassing met meerdere tenants die gebruikmaakt van Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: billgib, sstein
ms.date: 12/18/2018
ms.openlocfilehash: 6f660426c41b37dd27438c28cbf603bdbf1e58b3
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78359140"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Schema beheren in een SaaS-toepassing die gebruikmaakt van Shard multi tenant SQL-data bases

In deze zelf studie worden de uitdagingen besproken voor het onderhouden van een vloot van data bases in een SaaS-toepassing (Software as a Service). Oplossingen worden gedemonstreerd voor fanninge schema wijzigingen in de vloot van data bases.

Net als bij elke toepassing zal de SaaS-app van de Wingtip tickets in de loop van de tijd worden ontwikkeld en moeten wijzigingen worden aangebracht in de data base. Wijzigingen kunnen van invloed zijn op schema-of referentie gegevens of om database onderhouds taken toe te passen. Met een SaaS-toepassing die gebruikmaakt van een Data Base per Tenant patroon, moeten de wijzigingen worden gecoördineerd in een potentieel enorme vloot van Tenant-data bases. Daarnaast moet u deze wijzigingen opnemen in het data base-inrichtings proces om ervoor te zorgen dat ze worden opgenomen in nieuwe data bases wanneer ze worden gemaakt.

#### <a name="two-scenarios"></a>Twee scenario's

In deze zelf studie worden de volgende twee scenario's besproken:
- Updates voor referentie gegevens implementeren voor alle tenants.
- Een index opnieuw samen stellen voor de tabel die de referentie gegevens bevat.

De functie [elastische taken](elastic-jobs-overview.md) van Azure SQL database wordt gebruikt om deze bewerkingen uit te voeren in de Tenant-data bases. De taken worden ook toegepast op de Tenant database van de sjabloon. In de voor beeld-app Wingtip tickets wordt deze sjabloon database gekopieerd om een nieuwe Tenant database in te richten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een taak agent maken.
> * Een T-SQL-query uitvoeren op meerdere Tenant databases.
> * Referentie gegevens bijwerken in alle Tenant databases.
> * Een index maken voor een tabel in alle Tenant databases.

## <a name="prerequisites"></a>Vereisten

- De data base-app met meerdere tenants van Wingtip tickets moet al zijn geïmplementeerd:
    - Zie voor instructies de eerste zelf studie, waarmee de Wingtip tickets SaaS multi-tenant data base-app wordt geïntroduceerd:<br />[Implementeer en verken een Shard multi tenant-toepassing die gebruikmaakt van Azure SQL database](saas-multitenantdb-get-started-deploy.md).
        - Het implementatie proces wordt minder dan vijf minuten uitgevoerd.
    - U moet de *Shard multi tenant* -versie van Wingtip hebben geïnstalleerd. De versies voor *zelfstandige* en *Data Base per Tenant* bieden geen ondersteuning voor deze zelf studie.

- De nieuwste versie van SQL Server Management Studio (SSMS) moet zijn geïnstalleerd. U hebt [SSMS te downloaden en te installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell moet zijn geïnstalleerd. Zie [aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)voor meer informatie.

> [!NOTE]
> In deze zelf studie worden functies van de Azure SQL Database-service met een beperkte preview ([taak voor Elastic database](sql-database-elastic-database-client-library.md)) gebruikt. Als u deze zelf studie wilt uitvoeren, geeft u uw abonnements-ID op *SaaSFeedback\@Microsoft.com* met de preview-versie van elastische taken. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, [downloadt en installeert u de taak-cmdlets van de voorlopige versie](https://github.com/jaredmoo/azure-powershell/releases). Dit voor beeld is beperkt, dus neem contact op met *SaaSFeedback\@Microsoft.com* voor gerelateerde vragen of ondersteuning.

## <a name="introduction-to-saas-schema-management-patterns"></a>Inleiding tot SaaS-schema beheer patronen

Met het Shard multi tenant-database model dat in dit voor beeld wordt gebruikt, kan een tenants-Data Base een of meer tenants bevatten. In dit voor beeld wordt gebruikgemaakt van een combi natie van een Data Base met veel tenants en één Tenant, waardoor een *hybride* model voor Tenant beheer mogelijk is. Het beheren van wijzigingen in deze data bases kan gecompliceerd zijn. [Elastische taken](elastic-jobs-overview.md) vereenvoudigt het beheer en het beheer van grote aantallen data bases. Met taken kunt u Transact-SQL-scripts veilig en betrouwbaar uitvoeren als taken, tegen een groep Tenant databases. De taken zijn onafhankelijk van de interactie of invoer van de gebruiker. Deze methode kan worden gebruikt voor het implementeren van wijzigingen in schema of algemene referentie gegevens, in alle tenants in een toepassing. Elastische taken kunnen ook worden gebruikt voor het onderhouden van een gouden sjabloon kopie van de data base. De sjabloon wordt gebruikt voor het maken van nieuwe tenants, zodat altijd het meest recente schema en de referentie gegevens worden gebruikt.

![scherm](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Beperkte preview voor Elastische taken

Er is een nieuwe versie van elastische taken die nu een geïntegreerde functie van Azure SQL Database zijn. Van deze nieuwe versie van Elastische taken is momenteel een beperkte preview beschikbaar. De beperkte preview-versie ondersteunt momenteel het gebruik van Power shell voor het maken van een taak agent en T-SQL voor het maken en beheren van taken.
> [!NOTE]
> In deze zelf studie worden functies van de SQL Database-service met een beperkte preview (taak voor Elastic Database) gebruikt. Als u deze zelf studie wilt uitvoeren, geeft u uw abonnements-ID op SaaSFeedback@microsoft.com met de preview-versie van het onderwerp = elastische taken. Nadat u een bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, downloadt en installeert u de nieuwste cmdlets voor de voorlopige versie van de taken. Dit voor beeld is beperkt, dus neem contact op met SaaSFeedback@microsoft.com voor gerelateerde vragen of ondersteuning.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>De Wingtip tickets SaaS multi-tenant database toepassings bron code en scripts ophalen

De Wingtip tickets SaaS multi-tenant database scripts en toepassings bron code zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB-](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) opslag plaats op github. Raadpleeg de [algemene richt lijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor de stappen voor het downloaden en blok keren van de Wingtip tickets SaaS-scripts.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Een taak agent-data base en nieuwe taak agent maken

Voor deze zelf studie moet u Power shell gebruiken om de data base van de taak agent en de taak agent te maken. Net als de MSDB-data base die wordt gebruikt door SQL-Agent, gebruikt een taak agent een Azure-SQL database om taak definities, taak status en geschiedenis op te slaan. Nadat de taak agent is gemaakt, kunt u taken direct maken en bewaken.

1. Open in **Power shell ISE** *Learning modules\\\\schema beheer\\demo-SchemaManagement. ps1*.
2. Druk op **F5** om het script uit te voeren.

Het script *demo-SchemaManagement. ps1* roept het script *Deploy-SchemaManagement. ps1* aan om een Data Base met de naam _jobagent_ te maken op de catalogus server. Het script maakt vervolgens de taak agent, waarbij de _jobagent_ -Data Base als para meter wordt door gegeven.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Een taak maken om nieuwe referentiegegevens te implementeren voor alle tenants

#### <a name="prepare"></a>Voorbereiden

De data base van elke Tenant bevat een set locatie typen in de tabel **VenueTypes** . Elk type locatie definieert het soort gebeurtenissen dat kan worden gehost op locatie. Deze typen locaties komen overeen met de achtergrond afbeeldingen die u ziet in de app voor Tenant gebeurtenissen.  In deze oefening implementeert u een update voor alle data bases om twee extra typen locaties toe te voegen: *motor race* en *zwem Club*.

Controleer eerst de locatie typen die zijn opgenomen in elke Tenant database. Verbinding maken met een van de Tenant-data bases in SQL Server Management Studio (SSMS) en de VenueTypes-tabel controleren.  U kunt ook een query uitvoeren op deze tabel in de query-editor in het Azure Portal, toegankelijk via de pagina Data Base.

1. Open SSMS en maak verbinding met de Tenant server: *tenants1-dpt-&lt;gebruiker&gt;. database.Windows.net*
1. Als u wilt bevestigen dat de *race van motor rijwiel* en de *zwem Club* momenteel **niet zijn** opgenomen, bladert u naar de *contosoconcerthall* -data base op de *tenants1-dpt-&lt;gebruiker&gt;-* server en zoekt u in de *VenueTypes* -tabel.



#### <a name="steps"></a>Stappen

U maakt nu een taak om de tabel **VenueTypes** in elke Tenant-data base bij te werken door de twee nieuwe typen locaties toe te voegen.

Als u een nieuwe taak wilt maken, gebruikt u de opgeslagen procedures voor het systeem van taken die zijn gemaakt in de _jobagent_ -data base. De opgeslagen procedures zijn gemaakt toen de taak agent werd gemaakt.

1. Maak in SSMS verbinding met de Tenant server: tenants1-MT-&lt;gebruiker&gt;. database.windows.net

2. Blader naar de *tenants1* -data base.

3. Query's uitvoeren op de tabel *VenueTypes* om te bevestigen dat de *motor van rijwielen* en *zwem Club* nog niet voor komen in de lijst met resultaten.

4. Maak verbinding met de catalogus server. Dit is *Catalog-MT-&lt;user&gt;. database.Windows.net*.

5. Maak verbinding met de _jobagent_ -data base in de catalogus server.

6. Open in SSMS het bestand *...\\Learning Modules\\schema beheer\\DeployReferenceData. SQL*.

7. Wijzig de instructie: set @User = &lt;gebruiker&gt; en vervang de gebruikers waarde die wordt gebruikt bij het implementeren van de toepassing Wingtip tickets SaaS multi tenant data base.

8. Druk op **F5** om het script uit te voeren.

#### <a name="observe"></a>Nageleefd

Bekijk de volgende items in het script *DeployReferenceData. SQL* :

- **sp\_toevoegen\_doel\_groep** maakt de naam van de doel groep *DemoServerGroup*en voegt de doel leden toe aan de groep.

- **sp\_toevoegen\_doel\_groep\_lid** voegt de volgende items toe:
    - Een type lid van een *Server* doel.
        - Dit is de *tenants1-MT-&lt;gebruiker&gt;-* server die de tenants-data bases bevat.
        - Met inbegrip van de server bevat de Tenant databases die bestaan op het moment dat de taak wordt uitgevoerd.
    - Een type lid van een *database* doel voor de sjabloon database (*basetenantdb*) die zich bevindt in *catalogus-MT-&lt;gebruiker&gt;-* server.
    - Een type lid van een *database* doel om de *adhocreporting* -Data Base op te nemen die in een latere zelf studie wordt gebruikt.

- met **sp\_toevoegen\_taak** wordt een taak met de naam *referentie gegevens implementatie*gemaakt.

- met **sp\_toevoegen\_jobstep** wordt de taak stap gemaakt met de t-SQL-opdracht tekst voor het bijwerken van de referentie tabel, VenueTypes.

- De resterende weergaven in het script tonen het bestaan van de objecten en controleren de taakuitvoering. Gebruik deze query's om de status waarde in de kolom **levens cyclus** te controleren om te bepalen wanneer de taak is voltooid. Met de taak wordt de tenants-data base bijgewerkt en worden de twee aanvullende data bases bijgewerkt die de referentie tabel bevatten.

In SSMS bladert u naar de Tenant database op de *tenants1-MT-&lt;gebruiker&gt;-* server. Query's uitvoeren op de *VenueTypes* -tabel om te bevestigen dat de race van de *motor* en de *zwem Club* nu aan de tabel worden toegevoegd. Het totale aantal locatie typen moet met twee worden verhoogd.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Een taak maken voor het beheren van de referentietabelindex

In deze oefening maakt u een taak voor het opnieuw samen stellen van de index op de primaire sleutel van de referentie tabel op alle Tenant databases. Het opnieuw opbouwen van een index is een typische bewerking voor database beheer die een beheerder kan uitvoeren na het laden van een grote hoeveelheid gegevens belasting om de prestaties te verbeteren.

1. In SSMS maakt u verbinding met de _jobagent_ -data base in *Catalog-MT-&lt;gebruiker&gt;. database.Windows.net-* server.

2. Open in SSMS *\\Learning Modules\\schema beheer\\OnlineReindex. SQL*.

3. Druk op **F5** om het script uit te voeren.

#### <a name="observe"></a>Nageleefd

Bekijk de volgende items in het script *OnlineReindex. SQL* :

* met **sp\_add\_-taak** wordt een nieuwe taak gemaakt met de naam *online reindex PK\_\_VENUETYP\_\_265E44FD7FD4C885*.

* **sp\_toevoegen\_jobstep** maakt de taak stap die de t-SQL-opdracht tekst bevat om de index bij te werken.

* De resterende weer gaven van de script controle taak worden uitgevoerd. Gebruik deze query's om de status waarde in de kolom **levens cyclus** te controleren om te bepalen wanneer de taak is voltooid voor alle leden van de doel groep.

## <a name="additional-resources"></a>Aanvullende resources

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Uitgeschaalde clouddatabases beheren](elastic-jobs-overview.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een taak agent maken voor het uitvoeren van T-SQL-taken in meerdere data bases
> * Referentie gegevens bijwerken in alle Tenant databases
> * Een index in een tabel maken in alle tenantdatabases

Probeer vervolgens de [zelf studie ad hoc Reporting](saas-multitenantdb-adhoc-reporting.md) uit te voeren om gedistribueerde query's te verkennen in Tenant databases.


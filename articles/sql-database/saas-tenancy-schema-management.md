---
title: Schema beheren in een app met één tenant
description: Schema beheren voor meerdere tenants in één tenant-app die Azure SQL-database gebruikt
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/19/2018
ms.openlocfilehash: b6802d97b964b8863f6c2fce0cebfe16782b46fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269209"
---
# <a name="manage-schema-in-a-saas-application-using-the-database-per-tenant-pattern-with-azure-sql-database"></a>Schema beheren in een SaaS-toepassing met behulp van het database-per-tenant-patroon met Azure SQL Database
 
Naarmate een databasetoepassing evolueert, moeten er onvermijdelijk wijzigingen worden aangebracht in het databaseschema of de referentiegegevens.  Ook onderhoudstaken in de database zijn periodiek nodig. Voor het beheren van een toepassing die de database per tenantpatroon gebruikt, moet u deze wijzigingen of onderhoudstaken toepassen in een vloot tenantdatabases.

In deze zelfstudie worden twee scenario's verkend: het implementeren van referentiegegevensupdates voor alle tenants en het opnieuw opbouwen van een index op de tabel met de referentiegegevens. De functie [Elastische taken](elastic-jobs-overview.md) wordt gebruikt om deze acties uit te voeren op alle tenantdatabases en op de sjabloondatabase die wordt gebruikt om nieuwe tenantdatabases te maken.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> 
> * Een functieagent maken
> * Ervoor zorgen dat T-SQL-taken worden uitgevoerd op alle tenantdatabases
> * Referentiegegevens bijwerken in alle tenantdatabases
> * Een index in een tabel maken in alle tenantdatabases


Voor het voltooien van deze zelfstudie moet u ervoor zorgen dat aan de volgende vereisten is voldaan:

* De Wingtip Tickets SaaS Database Per Tenant app wordt geïmplementeerd. Zie [De SaaS-database van Wingtip Tickets implementeren en verkennen per tenanttoepassing](saas-dbpertenant-get-started-deploy.md) om in minder dan vijf minuten te implementeren, zie De SaaS-database van Wingtip Tickets implementeren en verkennen
* Azure PowerShell is geïnstalleerd. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* De laatste versie van SQL Server Management Studio (SSMS) moet zijn geïnstalleerd. [SSMS downloaden en installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms)

> [!NOTE]
> In deze zelfstudie wordt gebruikgemaakt van de functies van de SQL Database-service die deel uitmaken van een beperkte preview (elastische databasetaken). Als u deze zelfstudie wilt uitvoeren, SaaSFeedback@microsoft.com geeft u uw abonnements-ID aan met onderwerp=Elastic Jobs Preview. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, [downloadt en installeert u de taak-cmdlets van de voorlopige versie](https://github.com/jaredmoo/azure-powershell/releases). Deze preview is beperkt, dus neem contact op SaaSFeedback@microsoft.com voor gerelateerde vragen of ondersteuning.

## <a name="introduction-to-saas-schema-management-patterns"></a>Inleiding tot saas-schemabeheerpatronen

De database per tenantpatroon isoleert tenantgegevens effectief, maar verhoogt het aantal databases dat moet worden beheren en onderhouden. [Elastic Jobs](elastic-jobs-overview.md) faciliteert het beheer en beheer van SQL-databases. Met taken u taken (T-SQL-scripts) veilig en betrouwbaar uitvoeren tegen een groep databases. Taken kunnen schema- en algemene referentiegegevenswijzigingen implementeren in alle tenantdatabases in een toepassing. Elastische taken kunnen ook worden gebruikt om een *sjabloondatabase* bij te houden die wordt gebruikt om nieuwe tenants te maken, zodat deze altijd over de nieuwste schema- en referentiegegevens beschikt.

![scherm](media/saas-tenancy-schema-management/schema-management-dpt.png)


## <a name="elastic-jobs-limited-preview"></a>Beperkte preview voor Elastische taken

Er is een nieuwe versie van Elastic Jobs die nu een geïntegreerde functie van Azure SQL Database is. Van deze nieuwe versie van Elastische taken is momenteel een beperkte preview beschikbaar. Deze beperkte preview ondersteunt momenteel het gebruik van PowerShell om een jobagent te maken en T-SQL om taken te maken en te beheren.

> [!NOTE]
> In deze zelfstudie wordt gebruikgemaakt van de functies van de SQL Database-service die deel uitmaken van een beperkte preview (elastische databasetaken). Als u deze zelfstudie wilt uitvoeren, SaaSFeedback@microsoft.com geeft u uw abonnements-ID aan met onderwerp=Elastic Jobs Preview. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, [downloadt en installeert u de taak-cmdlets van de voorlopige versie](https://github.com/jaredmoo/azure-powershell/releases). Deze preview is beperkt, dus neem contact op SaaSFeedback@microsoft.com voor gerelateerde vragen of ondersteuning.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Download de Wingtip Tickets SaaS-database per tenanttoepassingsscripts

De broncode en beheerscripts van de toepassing zijn beschikbaar in de [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) GitHub repo. Bekijk de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Een database voor een functieagent en een nieuwe functie-agent maken

Deze zelfstudie vereist dat u PowerShell gebruikt om een jobagent en de database met back-ups van een functietegoed te maken. De database met taakagentbevat taakdefinities, taakstatus en geschiedenis. Zodra de jobagent en de database zijn gemaakt, u taken onmiddellijk maken en controleren.

1. **In PowerShell ISE**, open ... \\Leermodules\\Schema\\Management*Demo-SchemaManagement.ps1*.
1. Druk op **F5** om het script uit te voeren.

Het *Demo-SchemaManagement.ps1* script roept het *Script Deploy-SchemaManagement.ps1* aan om een SQL-database met de naam *osagent* op de catalogusserver te maken. Vervolgens wordt de taakagent gemaakt, met behulp van de database als parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Een taak maken om nieuwe referentiegegevens te implementeren voor alle tenants

In de Wingtip Tickets-app bevat elke tenantdatabase een reeks ondersteunde locatietypen. Elke locatie heeft een specifiek locatietype, dat het soort evenementen definieert dat kan worden gehost en de achtergrondafbeelding bepaalt die in de app wordt gebruikt. Voor de toepassing ter ondersteuning van nieuwe soorten evenementen moeten deze referentiegegevens worden bijgewerkt en nieuwe locatietypen worden toegevoegd.  In deze oefening implementeert u een update voor alle tenantdatabases waarbij twee nieuwe locatietypen worden toegevoegd: *Motorcycle Racing* en *Swimming Club*.

Bekijk eerst de locatietypen die in elke tenantdatabase zijn opgenomen. Maak verbinding met een van de tenantdatabases in SQL Server Management Studio (SSMS) en inspecteer de tabel VenueTypes.  U deze tabel ook opvragen in de queryeditor in de Azure-portal, die toegankelijk is vanaf de databasepagina. 

1. Open SSMS en maak verbinding met de tenantserver: *&lt;&gt;tenants1-dpt-gebruiker .database.windows.net*
1. Als u wilt bevestigen dat *Motorcycle Racing* en *Swimming Club* momenteel **niet zijn** opgenomen, bladert u naar de _contosoconcerthall-database_ op de server van de *tenants1-dpt-gebruiker&lt;&gt; * en bevraagt u de tabel *VenueTypes.*

Laten we nu een taak maken om de tabel *VenueTypes* in alle tenantdatabases bij te werken om de nieuwe locatietypen toe te voegen.

Als u een nieuwe taak wilt maken, gebruikt u een set van functiesysteemdat is opgeslagen in de _jobagent-database_ wanneer de taakagent is gemaakt.

1. Maak in SSMS verbinding met de catalogusserver: *de&lt;gebruiker&gt;van database.windows.net* 
1. Open in SSMS het bestand ... \\Leermodules\\Schemabeheer\\DeployReferenceData.sql
1. De instructie wijzigen: &lt;&gt; SET @wtpUser = gebruiker en vervang de gebruikerswaarde die wordt gebruikt bij het implementeren van de SaaS-database van Wingtip Tickets per tenant-app
1. Zorg ervoor dat u bent verbonden met de _jobagent-database_ en druk op **F5** om het script uit te voeren

Bekijk de volgende elementen in het *Script DeployReferenceData.sql:*
* **sp\_\_add-doelgroep\_** maakt de doelgroepnaam DemoServerGroup.
* **sp\_\_add\_\_doelgroep lid** wordt gebruikt om de set van doelgroep databases te definiëren.  Eerst wordt de _tenants1-dpt-user&lt;&gt; _ server toegevoegd.  Als u de server als doel toevoegt, worden de databases in die server op het moment van taakuitvoering opgenomen in de taak. Vervolgens worden de _basetenantdb-database_ en de *adhocreporting database* (gebruikt in een latere zelfstudie) toegevoegd als doelen.
* **sp\_\_add-job** maakt een taak met de naam _Reference Data Deployment_.
* **sp\_\_add jobstep** maakt de taakstap met T-SQL-opdrachttekst om de referentietabel VenueTypes bij te werken.
* De resterende weergaven in het script tonen het bestaan van de objecten en controleren de taakuitvoering. Gebruik deze query's om de statuswaarde in de **levenscycluskolom** te controleren om te bepalen wanneer de taak is voltooid op alle doeldatabases.

Zodra het script is voltooid, u controleren of de referentiegegevens zijn bijgewerkt.  Blader in SSMS naar de *contosoconcerthall-database* op de server van de *tenants1-dpt-gebruiker&lt;&gt; * en query de *VenueTypes-tabel.*  Controleer of *Motorcycle Racing* en *Swimming Club* nu aanwezig **zijn.**


## <a name="create-a-job-to-manage-the-reference-table-index"></a>Een taak maken voor het beheren van de referentietabelindex

Deze oefening maakt gebruik van een taak om de index op de primaire sleutel van de referentietabel opnieuw op te bouwen.  Dit is een typische databaseonderhoudsbewerking die kan worden uitgevoerd na het laden van grote hoeveelheden gegevens.

Maak een taak met dezelfde in het systeem opgeslagen procedures.

1. SSMS openen en verbinding maken met de server .database.windows.net server van de _&lt;&gt;catalogusdpt-gebruiker .database.windows.net_
1. Open het bestand _... Leermodules\\Schema\\Management OnlineReindex.sql \\_
1. Klik met de rechtermuisknop, selecteer Verbinding en maak verbinding met de _server&lt;&gt;.database.windows.net_ server van de catalogusdpt-
1. Zorg ervoor dat u bent verbonden met de _jobagent-database_ en druk op **F5** om het script uit te voeren

Bekijk de volgende elementen in het _Script OnlineReindex.sql:_
* **sp\_\_add job** creëert een nieuwe baan\_\_genaamd "Online Reindex PK VenueTyp\_\_265E4FD7FD4C885"
* **sp\_\_jobstep toevoegen** maakt de taakstap met T-SQL-opdrachttekst om de index bij te werken
* De resterende weergaven in het script bewaken de uitvoering van de taak. Gebruik deze query's om de statuswaarde in de **kolom levenscyclus** te controleren om te bepalen wanneer de taak is voltooid op alle doelgroepleden.



## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> 
> * Een functieagent maken om meerdere Databases van T-SQL-taken uit te voeren
> * Referentiegegevens bijwerken in alle tenantdatabases
> * Een index in een tabel maken in alle tenantdatabases

Probeer vervolgens de [zelfstudie Ad hoc-rapportage](saas-tenancy-cross-tenant-reporting.md) om het uitvoeren van gedistribueerde query's in tenantdatabases te verkennen.


## <a name="additional-resources"></a>Aanvullende bronnen

* [Aanvullende zelfstudies die voortbouwen op de Wingtip Tickets SaaS Database Per Tenant-toepassing implementatie](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Uitgeschaalde clouddatabases beheren](elastic-jobs-overview.md)
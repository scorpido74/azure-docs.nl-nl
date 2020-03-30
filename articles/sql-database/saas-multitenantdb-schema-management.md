---
title: Schema beheren in een app met meerdere tenants
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269196"
---
# <a name="manage-schema-in-a-saas-application-that-uses-sharded-multi-tenant-sql-databases"></a>Schema beheren in een SaaS-toepassing die gebruikmaakt van geharde SQL-databases met meerdere tenant

Deze zelfstudie onderzoekt de uitdagingen bij het onderhouden van een vloot databases in een Software as a Service (SaaS) applicatie. Oplossingen worden gedemonstreerd voor het uitwaaieren van schemawijzigingen in de vloot van databases.

Zoals elke toepassing zal de Wingtip Tickets SaaS-app in de loop van de tijd evolueren en moet de database worden gewijzigd. Wijzigingen kunnen van invloed zijn op schema- of referentiegegevens of op onderhoudstaken voor databasebestanden worden toegepast. Met een SaaS-toepassing die een database per tenantpatroon gebruikt, moeten wijzigingen worden gecoördineerd in een potentieel enorme vloot tenantdatabases. Bovendien moet u deze wijzigingen opnemen in het inrichtingsproces van de database om ervoor te zorgen dat ze worden opgenomen in nieuwe databases terwijl ze worden gemaakt.

#### <a name="two-scenarios"></a>Twee scenario's

In deze zelfstudie worden de volgende twee scenario's onderzocht:
- Werk aan de implementatie van referentiegegevensupdates voor alle tenants.
- Een index opnieuw opbouwen op de tabel die de referentiegegevens bevat.

De functie [Elastische taken](elastic-jobs-overview.md) van Azure SQL Database wordt gebruikt om deze bewerkingen in tenantdatabases uit te voeren. De taken werken ook op de 'template' tenant database. In de voorbeeldapp Wingtip Tickets wordt deze sjabloondatabase gekopieerd om een nieuwe tenantdatabase in te richten.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Maak een jobagent.
> * Voer een T-SQL-query uit op meerdere tenantdatabases.
> * Referentiegegevens in alle tenantdatabases bijwerken.
> * Maak een index op een tabel in alle tenantdatabases.

## <a name="prerequisites"></a>Vereisten

- De Wingtip Tickets multi-tenant database-app moet al worden geïmplementeerd:
    - Zie voor instructies de eerste zelfstudie, waarin de Wingtip Tickets SaaS-database-app met meerdere tenant's wordt geïntroduceerd:<br />[Een geshard multi-tenanttoepassing implementeren en verkennen die Azure SQL Database gebruikt.](saas-multitenantdb-get-started-deploy.md)
        - Het implementatieproces duurt minder dan vijf minuten.
    - U moet de *gesharde multi-tenant* versie van Wingtip geïnstalleerd hebben. De versies voor *Standalone* en *Database per tenant* ondersteunen deze zelfstudie niet.

- De nieuwste versie van SQL Server Management Studio (SSMS) moet worden geïnstalleerd. [SSMS downloaden en installeren](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms).

- Azure PowerShell moet worden geïnstalleerd. Zie Aan [de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)voor meer informatie.

> [!NOTE]
> In deze zelfstudie worden functies van de Azure SQL Database-service gebruikt die zich in een beperkte preview bevinden[(Elastic Database-taken).](sql-database-elastic-database-client-library.md) Als u deze zelfstudie wilt uitvoeren, geeft u uw abonnements-ID op *\@SaaSFeedback microsoft.com* met onderwerp=Elastic Jobs Preview. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, [downloadt en installeert u de taak-cmdlets van de voorlopige versie](https://github.com/jaredmoo/azure-powershell/releases). Deze preview is beperkt, dus neem contact op met *\@SaaSFeedback microsoft.com* voor gerelateerde vragen of ondersteuning.

## <a name="introduction-to-saas-schema-management-patterns"></a>Inleiding tot saas-schemabeheerpatronen

Met het geshard databasemodel met meerdere tenants dat in dit voorbeeld wordt gebruikt, kan een tenantdatabase een of meer tenants bevatten. In dit voorbeeld wordt onderzocht of het mogelijk is om een mix van een databases met veel tenant en één tenant te gebruiken, waardoor een *hybride* tenantbeheermodel mogelijk is. Het beheren van wijzigingen in deze databases kan ingewikkeld zijn. [Elastic Jobs](elastic-jobs-overview.md) faciliteert het beheer en beheer van grote aantallen database. Met taken u transact-SQL-scripts veilig en betrouwbaar uitvoeren als taken, tegen een groep tenantdatabases. De taken zijn onafhankelijk van gebruikersinteractie of invoer. Deze methode kan worden gebruikt om wijzigingen in het schema of gemeenschappelijke referentiegegevens te implementeren voor alle tenants in een toepassing. Elastische taken kunnen ook worden gebruikt om een gouden sjabloonkopie van de database bij te houden. De sjabloon wordt gebruikt om nieuwe tenants te maken, zodat altijd de nieuwste schema- en referentiegegevens in gebruik zijn.

![scherm](media/saas-multitenantdb-schema-management/schema-management.png)

## <a name="elastic-jobs-limited-preview"></a>Beperkte preview voor Elastische taken

Er is een nieuwe versie van Elastic Jobs die nu een geïntegreerde functie van Azure SQL Database is. Van deze nieuwe versie van Elastische taken is momenteel een beperkte preview beschikbaar. De beperkte preview ondersteunt momenteel het gebruik van PowerShell om een jobagent te maken en T-SQL om taken te maken en te beheren.
> [!NOTE]
> In deze zelfstudie wordt gebruikgemaakt van de functies van de SQL Database-service die deel uitmaken van een beperkte preview (elastische databasetaken). Als u deze zelfstudie wilt uitvoeren, SaaSFeedback@microsoft.com geeft u uw abonnements-ID aan met onderwerp=Elastic Jobs Preview. Wanneer u de bevestiging hebt ontvangen dat uw abonnement is ingeschakeld, downloadt en installeert u de taak-cmdlets van de voorlopige versie. Deze preview is beperkt, dus neem contact op SaaSFeedback@microsoft.com voor gerelateerde vragen of ondersteuning.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Download de broncode en scripts van de Wingtip Tickets SaaS Multi-tenant Database-toepassing

De Wingtip Tickets SaaS Multi-tenant Database scripts en applicatie broncode zijn beschikbaar in de [WingtipTicketsSaaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) repository op GitHub. Zie de [algemene richtlijnen](saas-tenancy-wingtip-app-guidance-tips.md) voor stappen om de Wingtip Tickets SaaS-scripts te downloaden en te deblokkeren.

## <a name="create-a-job-agent-database-and-new-job-agent"></a>Een database voor een functieagent en een nieuwe functie-agent maken

Deze zelfstudie vereist dat u PowerShell gebruikt om de database voor taakagent en taakagent te maken. Net als de MSDB-database die door SQL Agent wordt gebruikt, gebruikt een taakagent een Azure SQL-database om taakdefinities, taakstatus en geschiedenis op te slaan. Nadat de functieagent is gemaakt, u taken onmiddellijk maken en controleren.

1. In **PowerShell ISE**, open *... Leermodules\\Schema\\Management Demo-SchemaManagement.ps1 \\*.
2. Druk op **F5** om het script uit te voeren.

Het *script Demo-SchemaManagement.ps1* roept het *script Deploy-SchemaManagement.ps1* aan om een database met de naam _jobagent_ op de catalogusserver te maken. Het script maakt vervolgens de taakagent, het passeren van de _jobagent_ database als een parameter.

## <a name="create-a-job-to-deploy-new-reference-data-to-all-tenants"></a>Een taak maken om nieuwe referentiegegevens te implementeren voor alle tenants

#### <a name="prepare"></a>Voorbereiden

De database van elke tenant bevat een reeks locatietypen in de tabel **VenueTypes.** Elk locatietype definieert het soort evenementen dat op een locatie kan worden gehost. Deze locatietypen komen overeen met de achtergrondafbeeldingen die u ziet in de app tenantgebeurtenissen.  In deze oefening implementeert u een update voor alle databases om twee extra locatietypen toe te voegen: *Motorcycle Racing* en *Swimming Club.*

Bekijk eerst de locatietypen die in elke tenantdatabase zijn opgenomen. Maak verbinding met een van de tenantdatabases in SQL Server Management Studio (SSMS) en inspecteer de tabel VenueTypes.  U deze tabel ook opvragen in de queryeditor in de Azure-portal, die toegankelijk is vanaf de databasepagina.

1. Open SSMS en maak verbinding met de tenantserver: *&lt;&gt;tenants1-dpt-gebruiker .database.windows.net*
1. Als u wilt bevestigen dat *Motorcycle Racing* en *Swimming Club* momenteel **niet zijn** opgenomen, bladert u naar de *contosoconcerthall-database* op de server van de *tenants1-dpt-gebruiker&lt;&gt; * en bevraagt u de tabel *VenueTypes.*



#### <a name="steps"></a>Stappen

Nu maakt u een taak om de tabel **VenueTypes** in elke tenantsdatabase bij te werken door de twee nieuwe locatietypen toe te voegen.

Als u een nieuwe taak wilt maken, gebruikt u de set van door een functie opgeslagen procedures die zijn gemaakt in de _jobagent-database._ De opgeslagen procedures zijn gemaakt toen de functieagent werd gemaakt.

1. Maak in SSMS verbinding met de tenantserver:&lt;&gt;tenants1-mt-gebruiker .database.windows.net

2. Blader naar de *tenants1-database.*

3. Vraag de *VenueTypes-tabel* om te bevestigen dat *Motorcycle Racing* en *Swimming Club* nog niet in de resultatenlijst staan.

4. Maak verbinding met de catalogusserver, die *catalogus-mt-gebruiker&lt;&gt;is .database.windows.net*.

5. Maak verbinding met de _jobagent-database_ in de catalogusserver.

6. Open in SSMS het bestand *... Learning\\Modules\\Schema Management DeployReferenceData.sql . \\*

7. Wijzig de instructie: &lt;&gt; stel de gebruiker in @User en vervang de gebruikerswaarde die wordt gebruikt bij het implementeren van de Wingtip Tickets SaaS Multi-tenant Database-toepassing.

8. Druk op **F5** om het script uit te voeren.

#### <a name="observe"></a>Observeren

Bekijk de volgende items in het *Script DeployReferenceData.sql:*

- **sp\_\_add\_doelgroep** maakt de doelgroep naam *DemoServerGroup*, en voegt doelgroep leden aan de groep.

- **sp\_\_toevoegen\_\_doelgroeplid** voegt de volgende items:
    - Een *server* serverdoellidtype.
        - Dit is de *tenants1-mt-gebruikersserver&lt;&gt; * die de tenantsdatabases bevat.
        - Inclusief de server bevat de tenantdatabases die bestaan op het moment dat de taak wordt uitgevoerd.
    - Een *database* databasedoellidtype voor de sjabloondatabase *(basetenantdb)* die zich bevindt op *de server van de catalogusmt-user,&lt;&gt; *
    - Een *database* databasedoellidtype om de *adhocrapportagedatabase* op te nemen die in een latere zelfstudie wordt gebruikt.

- **sp\_\_add-job** maakt een taak met de naam *Reference Data Deployment*.

- **sp\_\_add jobstep** maakt de taakstap met T-SQL-opdrachttekst om de referentietabel VenueTypes bij te werken.

- De resterende weergaven in het script tonen het bestaan van de objecten en controleren de taakuitvoering. Gebruik deze query's om de statuswaarde in de **levenscycluskolom** te controleren om te bepalen wanneer de taak is voltooid. De taak werkt de tenantsdatabase bij en werkt de twee extra databases bij die de referentietabel bevatten.

Blader in SSMS naar de tenantdatabase op de server van de *tenants1-mt-&lt;gebruiker.&gt; * Vraag de tabel *VenueTypes* om te bevestigen dat *Motorcycle Racing* en *Swimming Club* nu aan de tabel zijn toegevoegd. Het totale aantal locatietypen had met twee moeten zijn toegenomen.

## <a name="create-a-job-to-manage-the-reference-table-index"></a>Een taak maken voor het beheren van de referentietabelindex

Met deze oefening wordt een taak gemaakt om de index op de primaire sleutel van de referentietabel in alle tenantdatabases opnieuw op te bouwen. Een indexreconstructie is een typische databasebeheerbewerking die een beheerder kan uitvoeren na het laden van een grote hoeveelheid gegevensbelasting, om de prestaties te verbeteren.

1. Maak in SSMS verbinding met _de jobagentdatabase_ in *de server&lt;.database.windows.net-server.&gt;*

2. In SSMS, open *... Learning\\Modules\\Schema Management OnlineReindex.sql . \\*

3. Druk op **F5** om het script uit te voeren.

#### <a name="observe"></a>Observeren

Bekijk de volgende items in het *Script OnlineReindex.sql:*

* **sp\_\_add job** creëert een nieuwe baan genaamd *Online Reindex PK\_\_\_\_VenueTyp 265E4FD7FD4C885*.

* **sp\_\_add jobstep** maakt de taakstap met T-SQL-opdrachttekst om de index bij te werken.

* De resterende weergaven in het script bewaken de uitvoering van de taak. Gebruik deze query's om de statuswaarde in de **kolom levenscyclus** te controleren om te bepalen wanneer de taak is voltooid op alle doelgroepleden.

## <a name="additional-resources"></a>Aanvullende bronnen

<!-- TODO: Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment (*Tutorial link to come*)
(saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->
* [Uitgeschaalde clouddatabases beheren](elastic-jobs-overview.md)

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u het volgende geleerd:

> [!div class="checklist"]
> * Een functieagent maken om T-SQL-taken uit te voeren in meerdere databases
> * Referentiegegevens bijwerken in alle tenantdatabases
> * Een index in een tabel maken in alle tenantdatabases

Probeer vervolgens de [zelfstudie Ad hoc-rapportage](saas-multitenantdb-adhoc-reporting.md) om het uitvoeren van gedistribueerde query's in tenantdatabases te verkennen.


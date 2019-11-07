---
title: "Multi tenant-apps met hulpprogram ma's voor beveiliging op rijniveau en elastische data base "
description: Gebruik hulpprogram ma's voor elastische data bases met beveiliging op rijniveau om een toepassing met een zeer schaal bare gegevenslaag te bouwen.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 4d3f25a6e234c3d3dfd878aaae68cf58684f2fac
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73691862"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Multi tenant-toepassingen met Elastic data base-hulpprogram ma's en beveiliging op rijniveau

[Hulpprogram ma's voor elastische data bases](sql-database-elastic-scale-get-started.md) en [beveiliging op rijniveau][rls] kunnen samen werken om de gegevenslaag van een toepassing met meerdere tenants te schalen met Azure SQL database. Deze technologieën helpen u bij het bouwen van een toepassing met een zeer schaal bare gegevenslaag. De gegevenslaag ondersteunt multi tenant Shards en maakt gebruik van **ADO.net SqlClient** of **Entity Framework**. Zie [ontwerp patronen voor SaaS-toepassingen met meerdere tenants met Azure SQL database](saas-tenancy-app-design-patterns.md)voor meer informatie.

- Met **hulpprogram ma's voor Elastic data base** kunnen ontwikkel aars de gegevenslaag uitschalen met de standaard sharding-procedures, met behulp van .net-bibliotheken en Azure-service sjablonen. Het beheren van Shards met behulp van de [Elastic database-client bibliotheek][s-d-elastic-database-client-library] helpt bij het automatiseren en stroom lijnen van veel van de infrastructuur-taken die doorgaans aan sharding zijn gekoppeld.
- Met **beveiliging op rijniveau** kunnen ontwikkel aars veilig gegevens opslaan voor meerdere tenants in dezelfde data base. Beveiligings beleid voor beveiliging op RIJNIVEAU filtert rijen die geen deel uitmaken van de Tenant die een query uitvoert. Het centraliseren van de filter logica binnen de data base vereenvoudigt het onderhoud en vermindert het risico van een beveiligings fout. Het alternatief van om te vertrouwen op alle client code voor het afdwingen van beveiliging is riskant.

Door deze functies samen te gebruiken, kan een toepassing gegevens opslaan voor meerdere tenants in dezelfde Shard-data base. De IT-kosten worden minder per Tenant, wanneer de tenants een Data Base delen. Maar dezelfde toepassing kan ook zijn Premium-tenants de mogelijkheid bieden om hun eigen, specifieke Shard voor eenmalige tenants te betalen. Een voor deel van het isoleren van één Tenant is de prestaties van een stevigere prestatie garantie. In een Data Base met één Tenant is er geen andere Tenant concurrerend voor bronnen.

Het doel is de client bibliotheek voor Elastic data base te gebruiken die [afhankelijke routerings-](sql-database-elastic-scale-data-dependent-routing.md) api's voor het automatisch verbinden van elke Tenant met de juiste Shard-data base. Slechts één Shard bevat een bepaalde TenantId-waarde voor de opgegeven Tenant. De TenantId is de *sharding-sleutel*. Nadat de verbinding tot stand is gebracht, zorgt een beveiligings beleid voor RIJNIVEAU binnen de data base ervoor dat de gegeven Tenant alleen toegang heeft tot de gegevens rijen die de TenantId bevatten.

> [!NOTE]
> De Tenant-id kan uit meer dan één kolom bestaan. Voor het gemak gaan we ervan uit dat er één kolom TenantId is.

![App-architectuur voor blogs][1]

## <a name="download-the-sample-project"></a>Het voorbeeld project downloaden

### <a name="prerequisites"></a>Vereisten

- Visual Studio (2012 of hoger) gebruiken
- Drie Azure SQL-data bases maken
- Voorbeeld project downloaden: [elastische DB-Hulpprogram ma's voor Azure SQL-Shards voor meerdere tenants](https://go.microsoft.com/?linkid=9888163)
  - Vul de gegevens in voor uw data bases aan het begin van **Program.cs**

Dit project breidt het uit dat wordt beschreven in [elastische DB-Hulpprogram ma's voor Azure SQL-Entity Framework-integratie](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) door ondersteuning toe te voegen voor multi tenant Shard-data bases. Het project bouwt een eenvoudige console toepassing voor het maken van blogs en berichten. Het project bevat vier tenants, plus twee Shard-data bases met meerdere tenants. Deze configuratie wordt in het voor gaande diagram geïllustreerd.

Bouw en voer de toepassing uit. Dit voert Boots traps uit van de Elastic data base tools Shard map Manager en voert de volgende tests uit:

1. Met Entity Framework en LINQ een nieuwe blog maken en vervolgens alle blogs voor elke Tenant weer geven
2. Alle blogs voor een Tenant weer geven met behulp van ADO.NET SqlClient
3. Probeer een blog in te voegen voor de verkeerde Tenant om te controleren of er een fout is opgetreden

U ziet dat de beveiliging op rijniveau nog niet is ingeschakeld in de Shard-data bases, maar bij elk van deze tests wordt een probleem weer gegeven: tenants kunnen de blogs zien die niet bij hen horen en de toepassing kan niet worden gebruikt om een blog voor de verkeerde Tenant in te voegen. In de rest van dit artikel wordt beschreven hoe u deze problemen oplost door Tenant isolatie af te dwingen met beveiliging op rijniveau. Er zijn twee stappen:

1. **Toepassingslaag**: Wijzig de toepassings code zodanig dat altijd de huidige TenantId wordt ingesteld in de sessie\_context na het openen van een verbinding. Het voorbeeld project stelt de TenantId op deze manier al in.
2. **Gegevenslaag**: Maak een beveiligings beleid op rijniveau in elke Shard-data base om rijen te filteren op basis van de TenantId die is opgeslagen in de sessie-\_context. Maak een beleid voor elk van uw Shard-data bases, anders worden rijen in multi tenant Shards niet gefilterd.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. gegevenslaagtoepassing: TenantId instellen in de sessie-\_CONTEXT

Eerst maakt u verbinding met een Shard-data base met behulp van de gegevens afhankelijke routerings-Api's van de client bibliotheek voor Elastic data base. De toepassing moet de data base die TenantId gebruikt de verbinding nog wel laten weten. In de TenantId wordt het beveiligings beleid voor RIJNIVEAU vermeld, welke rijen moeten worden gefilterd als onderdeel van andere tenants. Sla de huidige TenantId op in de [sessie\_context](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) van de verbinding.

Een alternatief voor sessie-\_CONTEXT is het gebruik van [context\_info](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Maar sessie\_CONTEXT is een betere optie. SESSIE-\_CONTEXT is gemakkelijker te gebruiken. het retourneert standaard NULL en sleutel-waardeparen ondersteunt.

### <a name="entity-framework"></a>Entity Framework

Voor toepassingen die gebruikmaken van Entity Framework, is de eenvoudigste manier om de sessie\_CONTEXT in te stellen binnen de ElasticScaleContext onderdrukking die wordt beschreven in [gegevens afhankelijke route ring met EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Maak en voer een SqlCommand uit waarmee TenantId in de sessie\_CONTEXT wordt ingesteld op de shardingKey die voor de verbinding is opgegeven. Ga vervolgens terug naar de Connection Broker via gegevens afhankelijke route ring. Op deze manier hoeft u slechts eenmaal code te schrijven om de sessie\_CONTEXT in te stellen.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Nu wordt de CONTEXT van de sessie-\_automatisch ingesteld met de opgegeven TenantId wanneer ElasticScaleContext wordt aangeroepen:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>ADO.NET SqlClient

Voor toepassingen die gebruikmaken van ADO.NET SqlClient maakt u een wrapper-functie rondom methode ShardMap. OpenConnectionForKey. Laat de wrapper automatisch TenantId instellen in de sessie\_CONTEXT voor de huidige TenantId voordat een verbinding wordt geretourneerd. Om ervoor te zorgen dat sessie\_CONTEXT altijd is ingesteld, moet u alleen verbindingen openen met deze wrapper-functie.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. gegevenslaag: beveiligings beleid op rijniveau maken

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Een beveiligings beleid maken voor het filteren van de rijen waartoe elke Tenant toegang heeft

Nu de toepassing sessie\_CONTEXT instelt met de huidige TenantId vóór het uitvoeren van een query, kan een beveiligings beleid voor beveiliging tegen query's worden gefilterd en rijen met een andere TenantId uitsluiten.

Beveiliging op rijniveau wordt geïmplementeerd in Transact-SQL. Een door de gebruiker gedefinieerde functie definieert de toegangs logica en een beveiligings beleid koppelt deze functie aan een wille keurig aantal tabellen. Voor dit project:

1. De functie controleert of de toepassing is verbonden met de data base en of de TenantId die is opgeslagen in de sessie\_CONTEXT overeenkomt met de TenantId van een bepaalde rij.
    - De toepassing is verbonden in plaats van een andere SQL-gebruiker.

2. Met een FILTER predicaat kunnen rijen die voldoen aan het TenantId-filter worden door gegeven voor SELECT-, UPDATE-en DELETE-query's.
    - Een BLOCK-predikaat voor komt dat rijen die het filter mislukken, worden ingevoegd of bijgewerkt.
    - Als er geen sessie\_CONTEXT is ingesteld, retourneert de functie NULL en zijn er geen rijen zichtbaar of kunnen ze worden ingevoegd.

Als u beveiliging op rijniveau op alle Shards wilt inschakelen, voert u de volgende T-SQL uit met behulp van Visual Studio (SSDT), SSMS of het Power shell-script dat is opgenomen in het project. Of als u Elastic Database- [taken](elastic-jobs-overview.md)gebruikt, kunt u de uitvoering van deze T-SQL automatiseren op alle Shards.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> In een complex project moet u mogelijk het predicaat toevoegen aan honderden tabellen. Dit kan omslachtig zijn. Er is een door de helper opgeslagen procedure waarmee automatisch een beveiligings beleid wordt gegenereerd en een predikaat wordt toegevoegd aan alle tabellen in een schema. Zie het blog bericht voor het [Toep assen van beveiliging op rijniveau op alle tabellen-hulp script (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script)voor meer informatie.

Als u de voorbeeld toepassing nu opnieuw uitvoert, zien tenants alleen rijen die er deel van uitmaken. Daarnaast kunnen de toepassingen geen rijen invoegen die horen bij andere tenants dan de toepassing die momenteel is verbonden met de Shard-data base. De TenantId kan ook niet worden bijgewerkt met de app in alle rijen die kunnen worden weer geven. Als de app hiervoor een poging doet, wordt een DbUpdateException gegenereerd.

Als u later een nieuwe tabel toevoegt, wijzigt u het beveiligings beleid om FILTER-en blok predikaten toe te voegen aan de nieuwe tabel.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Standaard beperkingen toevoegen om TenantId automatisch in te vullen voor inserts

U kunt een standaard beperking op elke tabel plaatsen om de TenantId automatisch te vullen met de waarde die momenteel is opgeslagen in de sessie\_CONTEXT bij het invoegen van rijen. Hier volgt een voor beeld.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

De toepassing hoeft nu geen TenantId op te geven bij het invoegen van rijen:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Als u de standaard beperkingen voor een Entity Framework project gebruikt, wordt het aanbevolen dat u de TenantId-kolom *niet* in uw EF-gegevens model opneemt. Deze aanbeveling is omdat Entity Framework query's automatisch standaard waarden opgeven die de standaard beperkingen overschrijven die zijn gemaakt in T-SQL die gebruikmaken van sessie\_CONTEXT.
> Als u standaard beperkingen in het voorbeeld project wilt gebruiken, moet u bijvoorbeeld TenantId verwijderen uit DataClasses.cs (en add-Migration uitvoeren in de Package Manager-console) en T-SQL gebruiken om ervoor te zorgen dat het veld alleen in de database tabellen voor komt. Op deze manier levert EF automatisch onjuiste standaard waarden op bij het invoegen van gegevens.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>Beschrijving Een *super gebruiker* in staat stellen om toegang te krijgen tot alle rijen

Sommige toepassingen willen mogelijk een *super gebruiker* maken die toegang heeft tot alle rijen. Een super gebruiker kan rapportage over alle tenants op alle Shards inschakelen. Of een super gebruiker kan Split-Merge-bewerkingen uitvoeren op Shards waarbij het verplaatsen van Tenant rijen tussen data bases is vereist.

Als u een beheerder wilt inschakelen, maakt u in elke Shard-Data Base een nieuwe SQL-gebruiker (`superuser` in dit voor beeld). Wijzig vervolgens het beveiligings beleid met een nieuwe predicaat functie waarmee deze gebruiker toegang kan krijgen tot alle rijen. Een dergelijke functie wordt nu gegeven.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Onderhoud

- **Nieuwe Shards toevoegen**: Voer het T-SQL-script uit om beveiliging op rijniveau in te scha kelen voor een nieuwe Shards, anders worden query's op deze Shards niet gefilterd.
- **Nieuwe tabellen toevoegen**: Voeg een filter en een PREDIKAAT blok keren toe aan het beveiligings beleid op alle Shards wanneer een nieuwe tabel wordt gemaakt. Anders worden query's voor de nieuwe tabel niet gefilterd. Deze toevoeging kan worden geautomatiseerd met behulp van een DDL-trigger, zoals wordt beschreven in [beveiliging op rijniveau automatisch Toep assen op nieuw gemaakte tabellen (blog)](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx).

## <a name="summary"></a>Samenvatting

Hulpprogram ma's voor elastische data bases en beveiliging op rijniveau kunnen samen worden gebruikt om de gegevenslaag van een toepassing uit te schalen met ondersteuning voor zowel multi tenant als single-tenant Shards. Multi tenant-Shards kunnen worden gebruikt om gegevens efficiënter op te slaan. Deze efficiëntie wordt uitgesp roken wanneer een groot aantal tenants slechts enkele rijen met gegevens bevat. Shards met één Tenant kunnen Premium-tenants ondersteunen die strenge prestaties en isolatie vereisten hebben. Zie [beveiliging op rijniveau][rls]voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Wat is een elastische Azure-groep?](sql-database-elastic-pool.md)
- [Uitbreiden met Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Verificatie in multitenant-apps met Azure AD en OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [De toepassing Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Vragen en functie aanvragen

Neem voor vragen contact met ons op in het [SQL database Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). En Voeg eventuele functie aanvragen toe aan het [Feedback forum van SQL database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

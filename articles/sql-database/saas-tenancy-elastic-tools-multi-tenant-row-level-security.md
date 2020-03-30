---
title: Apps met meerdere huurders met RLS- en elastische databasetools
description: Gebruik elastische databasetools met beveiliging op rijniveau om een toepassing te bouwen met een zeer schaalbare gegevenslaag.
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
ms.openlocfilehash: a5fe5d6d4076c5d82d33737d05bb95ede0a89c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822020"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Multi-tenant toepassingen met elastische databasetools en beveiliging op rijniveau

[Elastic database tools](sql-database-elastic-scale-get-started.md) en [row-level security (RLS)][rls] werken samen om het schalen van de gegevenslaag van een multi-tenant toepassing met Azure SQL Database mogelijk te maken. Samen helpen deze technologieën u bij het bouwen van een toepassing met een zeer schaalbare gegevenslaag. De gegevenslaag ondersteunt shards met meerdere tenant's en maakt gebruik **van ADO.NET SqlClient** of **Entity Framework.** Zie [Ontwerppatronen voor SaaS-toepassingen met meerdere tenant's met Azure SQL Database](saas-tenancy-app-design-patterns.md)voor meer informatie.

- **Met elastische databasehulpprogramma's** kunnen ontwikkelaars de gegevenslaag uitschalen met standaardshardingspraktijken met behulp van .NET-bibliotheken en Azure-servicesjablonen. Het beheren van shards met behulp van de [Elastic Database Client Library][s-d-elastic-database-client-library] helpt bij het automatiseren en stroomlijnen van veel van de infrastructurele taken die doorgaans gepaard gaan met sharding.
- **Met beveiliging op rijniveau** kunnen ontwikkelaars gegevens voor meerdere tenants veilig opslaan in dezelfde database. RLS-beveiligingsbeleid filtert rijen uit die niet behoren tot de tenant die een query uitvoert. Het centraliseren van de filterlogica in de database vereenvoudigt het onderhoud en vermindert het risico op een beveiligingsfout. Het alternatief van vertrouwen op alle clientcode om beveiliging af te dwingen is riskant.

Door deze functies samen te gebruiken, kan een toepassing gegevens opslaan voor meerdere tenants in dezelfde sharddatabase. Het kost minder per tenant wanneer de tenants een database delen. Maar dezelfde applicatie kan ook bieden haar premium huurders de mogelijkheid van het betalen voor hun eigen dedicated single-tenant scherf. Een voordeel van eenenkele huurder isolatie is steviger prestatiegaranties. In een database met één tenant concurreert er geen andere tenant om resources.

Het doel is om de [gegevensafhankelijke](sql-database-elastic-scale-data-dependent-routing.md) routerings-API's van de elastische databaseclientbibliotheek te gebruiken om elke tenant automatisch te verbinden met de juiste sharddatabase. Slechts één scherf bevat een bepaalde TenantId-waarde voor de opgegeven tenant. De TenantId is de *sharding sleutel.* Nadat de verbinding is ingesteld, zorgt een RLS-beveiligingsbeleid in de database ervoor dat de opgegeven tenant alleen toegang heeft tot de gegevensrijen die zijn TenantId bevatten.

> [!NOTE]
> De tenant-id kan uit meer dan één kolom bestaan. Voor het gemak is deze discussie, we informeel aannemen dat een single-column TenantId.

![App-architectuur bloggen][1]

## <a name="download-the-sample-project"></a>Het voorbeeldproject downloaden

### <a name="prerequisites"></a>Vereisten

- Visual Studio gebruiken (2012 of hoger)
- Drie Azure SQL-databases maken
- Voorbeeldproject downloaden: [Elastic DB-hulpprogramma's voor Azure SQL - Shards met meerdere tenantn](https://go.microsoft.com/?linkid=9888163)
  - Vul de gegevens voor uw databases aan het begin van **Program.cs**

Dit project breidt het project uit dat wordt beschreven in [Elastic DB Tools for Azure SQL - Entity Framework Integration](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md) door ondersteuning toe te voegen voor sharddatabases met meerdere tenantn. Het project bouwt een eenvoudige console-applicatie voor het maken van blogs en berichten. Het project omvat vier huurders, plus twee multi-tenant shard databases. Deze configuratie wordt geïllustreerd in het voorgaande diagram.

Maak de toepassing en voer deze uit. Deze run start de shardmapmanager van de elastische databasetools en voert de volgende tests uit:

1. Met Entity Framework en LINQ maakt u een nieuwe blog en geeft u alle blogs voor elke tenant weer
2. Met ADO.NET SqlClient alle blogs voor een tenant weergeven
3. Probeer een blog in te voegen voor de verkeerde tenant om te controleren of er een fout wordt gegenereerd

Merk op dat omdat RLS nog niet is ingeschakeld in de shard databases, elk van deze tests onthult een probleem: huurders zijn in staat om blogs die niet behoren tot hen te zien, en de toepassing is niet verhinderd het invoegen van een blog voor de verkeerde tenant. De rest van dit artikel beschrijft hoe u deze problemen oplossen door tenantisolatie met RLS af te dwingen. Er zijn twee stappen:

1. **Toepassingslaag:** wijzig de toepassingscode om de huidige\_tenantid altijd in de SESSIECONTEXT in te stellen na het openen van een verbinding. Het voorbeeldproject stelt de TenantId al op deze manier in.
2. **Gegevenslaag:** maak een RLS-beveiligingsbeleid in elke sharddatabase om rijen\_te filteren op basis van de tenantid die is opgeslagen in SESSIECONTEXT. Maak een beleid voor elk van uw sharddatabases, anders worden rijen in shards met meerdere tenants niet gefilterd.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Toepassingslaag: TenantId instellen\_in de sessiecontext

Eerst maakt u verbinding met een sharddatabase met behulp van de gegevensafhankelijke routerings-API's van de elastische databaseclientbibliotheek. De toepassing moet de database nog steeds vertellen welke TenantId de verbinding gebruikt. De TenantId vertelt het RLS-beveiligingsbeleid welke rijen moeten worden gefilterd als behorend tot andere tenants. Sla de huidige TenantId op in de [sessiecontext\_](https://docs.microsoft.com/sql/t-sql/functions/session-context-transact-sql) van de verbinding.

Een alternatief\_voor SESSIECONTEXT is het gebruik van [CONTEXT\_INFO](https://docs.microsoft.com/sql/t-sql/functions/context-info-transact-sql). Maar\_SESSIE CONTEXT is een betere optie. SESSIECONTEXT\_is gemakkelijker te gebruiken, het retourneert NULL standaard en ondersteunt sleutelwaardeparen.

### <a name="entity-framework"></a>Entity Framework

Voor toepassingen die Entity Framework gebruiken, is\_de eenvoudigste benadering het instellen van de SESSIECONTEXT binnen de ElasticScaleContext-overschrijving die wordt beschreven in [Gegevensafhankelijke routering met EF DbContext](sql-database-elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Maak en voer een SqlCommand uit\_waarmee TenantId in de SESSIECONTEXT wordt ingesteld op de shardingKey die is opgegeven voor de verbinding. Retourneer vervolgens de verbinding die is bemiddeld via gegevensafhankelijke routering. Op deze manier hoeft u slechts één\_keer code te schrijven om de SESSIECONTEXT in te stellen.

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

Nu wordt\_de SESSIECONTEXT automatisch ingesteld met de opgegeven TenantId wanneer ElasticScaleContext wordt aangeroepen:

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

Voor toepassingen die ADO.NET SqlClient gebruiken, maakt u een wrapper-functie rond methode ShardMap.OpenConnectionForKey. Laat de wrapper tenantid automatisch\_instellen in de SESSIECONTEXT op de huidige TenantId voordat u een verbinding retourneert. Om ervoor\_te zorgen dat SESSIE CONTEXT altijd is ingesteld, moet u alleen verbindingen openen met deze wrapper functie.

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

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Gegevenslaag: beveiligingsbeleid op rijniveau maken

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Een beveiligingsbeleid maken om de rijen te filteren waartoe elke tenant toegang heeft

Nu de toepassing sessiecontext\_instelt met de huidige TenantId voordat deze wordt opgevraagd, kan een RLS-beveiligingsbeleid query's filteren en rijen met een andere tenantid uitsluiten.

RLS is geïmplementeerd in Transact-SQL. Een door de gebruiker gedefinieerde functie definieert de toegangslogica en een beveiligingsbeleid bindt deze functie aan een willekeurig aantal tabellen. Voor dit project:

1. De functie controleert of de toepassing is verbonden met de database\_en of de TenantId die is opgeslagen in de SESSIECONTEXT overeenkomt met de TenantId van een bepaalde rij.
    - De toepassing is verbonden, in plaats van een andere SQL-gebruiker.

2. Met een filterpredicaat kunnen rijen die voldoen aan het tenantid-filter worden doorgegeven voor query's selecteren, bijwerken en verwijderen.
    - Een blokpredicaat voorkomt dat rijen die het filter niet halen, inserted of updated worden.
    - Als\_SESSIECONTEXT niet is ingesteld, retourneert de functie NULL en zijn er geen rijen zichtbaar of kunnen ze worden ingevoegd.

Als u RLS op alle scherven wilt inschakelen, voert u de volgende T-SQL uit met Behulp van Visual Studio (SSDT), SSMS of het PowerShell-script dat in het project is opgenomen. Of als u [Elastic Database Jobs](elastic-jobs-overview.md)gebruikt, u de uitvoering van deze T-SQL op alle shards automatiseren.

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
> In een complex project moet u mogelijk het predicaat op honderden tabellen toevoegen, wat vervelend kan zijn. Er is een helper-procedure die automatisch een beveiligingsbeleid genereert en een predicaat toevoegt voor alle tabellen in een schema. Zie het blogbericht op [Row-Level Security toepassen op alle tabellen - helperscript (blog).](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-to-all-tables-helper-script)

Als u de voorbeeldtoepassing opnieuw uitvoert, zien huurders alleen rijen die bij hen horen. Bovendien kan de toepassing geen rijen invoegen die behoren tot andere tenants dan die welke momenteel zijn verbonden met de sharddatabase. Ook kan de app de TenantId niet bijwerken in rijen die kan worden gezien. Als de app dit ook probeert te doen, wordt een DbUpdateException verhoogd.

Als u later een nieuwe tabel toevoegt, wijzigt u het beveiligingsbeleid om FILTER- en BLOK-predicaten op de nieuwe tabel toe te voegen.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Standaardbeperkingen toevoegen om tenantid voor INSERT's automatisch in te vullen

U een standaardbeperking op elke tabel plaatsen om de tenantid\_automatisch in te vullen met de waarde die momenteel is opgeslagen in SESSIECONTEXT bij het invoegen van rijen. Een voorbeeld volgt.

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

Nu hoeft de toepassing geen tenantid op te geven bij het invoegen van rijen:

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
> Als u standaardbeperkingen gebruikt voor een Entiteitskaderproject, wordt aanbevolen de kolom TenantId *NIET* op te nemen in uw EF-gegevensmodel. Deze aanbeveling is omdat Entiteitskaderquery's automatisch standaardwaarden leveren die de standaardbeperkingen overschrijven die in T-SQL zijn gemaakt en die SESSIECONTEXT\_gebruiken.
> Als u bijvoorbeeld standaardbeperkingen in het voorbeeldproject wilt gebruiken, moet u TenantId verwijderen uit DataClasses.cs (en Add-Migration uitvoeren in de Package Manager Console) en T-SQL gebruiken om ervoor te zorgen dat het veld alleen in de databasetabellen bestaat. Op deze manier levert EF automatisch onjuiste standaardwaarden bij het invoegen van gegevens.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Optioneel) Een *supergebruiker toegang* geven tot alle rijen

Sommige toepassingen willen mogelijk een *supergebruiker* maken die toegang heeft tot alle rijen. Een supergebruiker kan rapportage over alle tenants op alle scherven mogelijk maken. Of een supergebruiker kan split-merge-bewerkingen uitvoeren op shards waarbij tenantrijen tussen databases worden verplaatst.

Als u een supergebruiker wilt inschakelen, maakt u een nieuwe SQL-gebruiker (in`superuser` dit voorbeeld) in elke sharddatabase. Wijzig vervolgens het beveiligingsbeleid met een nieuwe predicaatfunctie waarmee deze gebruiker toegang heeft tot alle rijen. Een dergelijke functie wordt hierna gegeven.

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

- **Nieuwe shards toevoegen:** Voer het T-SQL-script uit om RLS in te schakelen op nieuwe scherven, anders worden query's op deze shards niet gefilterd.
- **Nieuwe tabellen toevoegen:** Een predicaat FILTER en BLOKKEREN toevoegen aan het beveiligingsbeleid voor alle shards wanneer een nieuwe tabel wordt gemaakt. Anders worden query's in de nieuwe tabel niet gefilterd. Deze toevoeging kan worden geautomatiseerd met behulp van een DDL-trigger, zoals beschreven in [Row-Level Security automatisch toepassen op nieuw gemaakte tabellen (blog).](https://blogs.msdn.com/b/sqlsecurity/archive/20../../apply-row-level-security-automatically-to-newly-created-tables.aspx)

## <a name="summary"></a>Samenvatting

Elastische databasetools en rijbeveiliging kunnen samen worden gebruikt om de gegevenslaag van een toepassing uit te schalen met ondersteuning voor zowel shards met meerdere tenants als met één tenant. Multi-tenant shards kunnen worden gebruikt om gegevens efficiënter op te slaan. Deze efficiëntie wordt uitgesproken wanneer een groot aantal tenants slechts een paar rijen gegevens heeft. Single-tenant shards kunnen premium huurders ondersteunen die strengere prestatie- en isolatievereisten hebben. Zie [Referentie voor beveiliging op rijniveau][rls]voor meer informatie .

## <a name="additional-resources"></a>Aanvullende bronnen

- [Wat is een elastische groep van Azure?](sql-database-elastic-pool.md)
- [Uitbreiden met Azure SQL Database](sql-database-elastic-scale-introduction.md)
- [Ontwerppatronen voor SaaS-toepassingen met meerdere tenants met behulp van Azure SQL Database](saas-tenancy-app-design-patterns.md)
- [Verificatie in multitenant-apps met Azure AD en OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [De toepassing Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)

## <a name="questions-and-feature-requests"></a>Vragen en functieverzoeken

Voor vragen u contact met ons opnemen op het [SQL Database-forum.](https://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) En voeg functieaanvragen toe aan het [SQL Database feedbackforum.](https://feedback.azure.com/forums/217321-sql-database/)

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: https://docs.microsoft.com/sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]: sql-database-elastic-database-client-library.md

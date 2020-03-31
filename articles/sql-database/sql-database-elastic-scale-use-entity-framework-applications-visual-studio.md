---
title: Elastische databaseclientbibliotheek gebruiken met Entiteitskader
description: De clientbibliotheek van Elastic Database en entiteitskader gebruiken voor het coderen van databases
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/04/2019
ms.openlocfilehash: 1653a904875964d86864c59c718603a6dacdcbda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087183"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Elastic Database-clientbibliotheek met Entiteitskader

Dit document toont de wijzigingen in een entiteitskadertoepassing die nodig zijn om te integreren met de [tools voor elastische database.](sql-database-elastic-scale-introduction.md) De focus ligt op het samenstellen van [shard mapmanagement](sql-database-elastic-scale-shard-map-management.md) en [data-afhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md) met de Entiteit Framework **Code First-benadering.** De [code eerst - nieuwe databasezelfstudie](https://msdn.microsoft.com/data/jj193542.aspx) voor EF dient als het lopende voorbeeld in dit document. De voorbeeldcode bij dit document maakt deel uit van de set voorbeelden van elastische databasetools in de Visual Studio Code Samples.

## <a name="downloading-and-running-the-sample-code"></a>De voorbeeldcode downloaden en uitvoeren

Ga als volgt te werk om de code voor dit artikel te downloaden:

* Visual Studio 2012 of hoger is vereist. 
* Download het [voorbeeld van Elastic DB Tools voor Azure SQL - Entity Framework Integration.](https://github.com/Azure/elastic-db-tools/) Rits het voorbeeld uit naar een locatie naar keuze.
* Start Visual Studio. 
* Selecteer in Visual Studio Bestand -> Project/Oplossing openen. 
* Navigeer in het dialoogvenster **Project openen** naar het voorbeeld dat u hebt gedownload en selecteer **EntityFrameworkCodeFirst.sln** om het voorbeeld te openen. 

Als u het voorbeeld wilt uitvoeren, moet u drie lege databases maken in Azure SQL Database:

* Shard Map Manager-database
* Shard 1-database
* Shard 2-database

Zodra u deze databases hebt gemaakt, vult u de plaatshouders in **Program.cs** met uw Azure SQL DB-servernaam, de databasenamen en uw referenties om verbinding te maken met de databases. Bouw de oplossing in Visual Studio. Visual Studio downloadt de vereiste NuGet-pakketten voor de elastische databaseclientbibliotheek, Entiteitsframework en Tijdelijke foutafhandeling als onderdeel van het buildproces. Zorg ervoor dat het herstellen van NuGet-pakketten is ingeschakeld voor uw oplossing. U deze instelling inschakelen door met de rechtermuisknop op het oplossingsbestand te klikken in de Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Entiteitskaderwerkstromen

Entiteitsframework-ontwikkelaars vertrouwen op een van de volgende vier workflows om toepassingen te bouwen en persistentie voor toepassingsobjecten te garanderen:

* **Code First (Nieuwe database):** De EF-ontwikkelaar maakt het model in de toepassingscode en genereert vervolgens de database ervan. 
* **Code First (Bestaande Database):** De ontwikkelaar laat EF de toepassingscode voor het model genereren vanuit een bestaande database.
* **Model First**: De ontwikkelaar maakt het model in de EF-ontwerper en vervolgens maakt EF de database van het model.
* **Database First**: De ontwikkelaar gebruikt EF-tooling om het model uit een bestaande database te leiden. 

Al deze benaderingen zijn afhankelijk van de klasse DbContext om databaseverbindingen en databaseschema voor een toepassing transparant te beheren. Verschillende constructors in de basisklasse DbContext zorgen voor verschillende niveaus van controle over het maken van verbindingen, databasebootstrapping en het maken van schema's. Uitdagingen vloeien voornamelijk voort uit het feit dat het beheer van de databaseverbinding door EF de mogelijkheden voor verbindingsbeheer van de gegevensafhankelijke routeringsinterfaces van de elastische databaseclientbibliotheek doorsnijdt. 

## <a name="elastic-database-tools-assumptions"></a>Aannames voor elastische databasetools

Zie [Woordenlijst voor elastic database-gereedschappen](sql-database-elastic-scale-glossary.md)voor termdefinities .

Met de elastische databaseclientbibliotheek definieert u partities van uw toepassingsgegevens die shardlets worden genoemd. Shardlets worden geïdentificeerd door een sharding sleutel en worden toegewezen aan specifieke databases. Een toepassing kan zoveel databases hebben als nodig is en de shardlets distribueren om voldoende capaciteit of prestaties te bieden gezien de huidige bedrijfsvereisten. Het toewijzen van shardingskernwaarden voor de databases wordt opgeslagen door een shardkaart die wordt geleverd door de API's van de elastische databaseclient. Deze mogelijkheid heet **Shard Map Management**, of SMM voor kort. De shard kaart dient ook als de makelaar van database verbindingen voor verzoeken die een sharding sleutel dragen. Deze mogelijkheid staat bekend als **gegevensafhankelijke routering.** 

De shard map manager beschermt gebruikers tegen inconsistente weergaven in shardlet gegevens die kunnen optreden wanneer gelijktijdige shardlet beheerbewerkingen (zoals het verplaatsen van gegevens van de ene scherf naar de andere) gebeuren. Om dit te doen, de shard kaarten beheerd door de client bibliotheek makelaar de database verbindingen voor een toepassing. Hierdoor kan de shardkaartfunctionaliteit automatisch een databaseverbinding verwijderen wanneer shardbeheerbewerkingen van invloed kunnen zijn op de shardlet waarvoor de verbinding is gemaakt. Deze aanpak moet worden geïntegreerd met een aantal van de functionaliteit van EF, zoals het maken van nieuwe verbindingen van een bestaande om te controleren op database bestaan. In het algemeen is onze observatie dat de standaard DbContext constructors alleen betrouwbaar werken voor gesloten databaseverbindingen die veilig kunnen worden gekloond voor EF-werk. Het ontwerp principe van elastische database in plaats daarvan is om alleen makelaar geopende verbindingen. Men zou kunnen denken dat het sluiten van een verbinding die door de clientbibliotheek wordt bemiddeld voordat deze wordt overgedragen aan de EF DbContext dit probleem kan oplossen. Door de verbinding te sluiten en te vertrouwen op EF om deze te heropenen, wordt de validatie- en consistentiecontroles die door de bibliotheek worden uitgevoerd, echter afgesloten. De migratiefunctionaliteit in EF gebruikt deze verbindingen echter om het onderliggende databaseschema te beheren op een manier die transparant is voor de toepassing. Idealiter behoudt en combineert u al deze mogelijkheden van zowel de elastische databaseclientbibliotheek als EF in dezelfde toepassing. In de volgende sectie worden deze eigenschappen en vereisten nader beschreven. 

## <a name="requirements"></a>Vereisten

Wanneer u werkt met zowel de elastische databaseclientbibliotheek als de Entiteitskader-API's, wilt u de volgende eigenschappen behouden: 

* **Uitschalen:** Databases toevoegen of verwijderen uit de gegevenslaag van de geshard toepassing indien nodig voor de capaciteitsvereisten van de toepassing. Dit betekent controle over het maken en verwijderen van databases en het gebruik van de elastic database shard map manager API's om databases te beheren, en toewijzingen van shardlets. 
* **Consistentie**: De toepassing maakt gebruik van sharding en maakt gebruik van de gegevensafhankelijke routeringsmogelijkheden van de clientbibliotheek. Om beschadigingof verkeerde queryresultaten te voorkomen, worden verbindingen bemiddeld via de shardmapmanager. Dit behoudt ook validatie en consistentie.
* **Code First**: Om het gemak van ef's code eerste paradigma te behouden. In Code First worden klassen in de toepassing transparant toegewezen aan de onderliggende databasestructuren. De toepassingscode werkt samen met DbSets die de meeste aspecten maskeren die betrokken zijn bij de onderliggende databaseverwerking.
* **Schema**: Entity Framework verwerkt de initiële databaseschema's en de daaropvolgende schema-evolutie via migraties. Door deze mogelijkheden te behouden, is het aanpassen van uw app eenvoudig naarmate de gegevens evolueren. 

De volgende richtlijnen geven instructies hoe u aan deze vereisten voor Code First-toepassingen voldoen met behulp van elastische databasetools. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Gegevensafhankelijke routering met EF DbContext

Databaseverbindingen met Entity Framework worden doorgaans beheerd via subklassen van **DbContext**. Maak deze subklassen door te afleiden uit **DbContext**. Hier definieert u uw **DbSets** die de door de database gesteunde verzamelingen CLR-objecten voor uw toepassing implementeren. In de context van gegevensafhankelijke routering u verschillende nuttige eigenschappen identificeren die niet noodzakelijkerwijs gelden voor andere ef-code eersttoepassingsscenario's: 

* De database bestaat al en is geregistreerd in de elastische database shard kaart. 
* Het schema van de toepassing is al geïmplementeerd in de database (hieronder uitgelegd). 
* Gegevensafhankelijke routeringsverbindingen naar de database worden bemiddeld door de shardkaart. 

**DbContexts** integreren met gegevensafhankelijke routering voor scale-out:

1. Maak fysieke databaseverbindingen via de elastische databaseclientinterfaces van de shardmapbeheerder, 
2. De verbinding met de subklasse **DbContext** inpakken
3. Geef de verbinding door in de **basisklassen van DbContext** om ervoor te zorgen dat alle verwerkingen aan de EF-kant ook gebeuren. 

In het volgende codevoorbeeld wordt deze benadering geïllustreerd. (Deze code zit ook in het bijbehorende Visual Studio project)

```csharp
public class ElasticScaleContext<T> : DbContext
{
public DbSet<Blog> Blogs { get; set; }
...

    // C'tor for data-dependent routing. This call opens a validated connection 
    // routed to the proper shard by the shard map manager. 
    // Note that the base class c'tor call fails for an open connection
    // if migrations need to be done and SQL credentials are used. This is the reason for the 
    // separation of c'tors into the data-dependent routing case (this c'tor) and the internal c'tor for new shards.
    public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
        : base(CreateDDRConnection(shardMap, shardingKey, connectionStr), 
        true /* contextOwnsConnection */)
    {
    }

    // Only static methods are allowed in calls into base class c'tors.
    private static DbConnection CreateDDRConnection(
    ShardMap shardMap, 
    T shardingKey, 
    string connectionStr)
    {
        // No initialization
        Database.SetInitializer<ElasticScaleContext<T>>(null);

        // Ask shard map to broker a validated connection for the given key
        SqlConnection conn = shardMap.OpenConnectionForKey<T>
                            (shardingKey, connectionStr, ConnectionOptions.Validate);
        return conn;
    }
```

## <a name="main-points"></a>Belangrijkste punten

* Een nieuwe constructor vervangt de standaardconstructor in de subklasse DbContext 
* De nieuwe constructor neemt de argumenten die nodig zijn voor gegevensafhankelijke routering via de elastische databaseclientbibliotheek:
  
  * de shardkaart om toegang te krijgen tot de gegevensafhankelijke routeringsinterfaces;
  * de shardingsleutel om de shardlet te identificeren;
  * een verbindingstekenreeks met de referenties voor de gegevensafhankelijke routeringsverbinding met de shard. 
* De aanroep naar de constructor van de basisklasse neemt een omweg naar een statische methode die alle stappen uitvoert die nodig zijn voor gegevensafhankelijke routering. 
  
  * Het maakt gebruik van de OpenConnectionForKey-aanroep van de elastische databaseclientinterfaces op de shardkaart om een open verbinding tot stand te brengen.
  * De shardkaart maakt de open verbinding met de scherf die de shardlet voor de gegeven shardingsleutel vasthoudt.
  * Deze open verbinding wordt doorgegeven aan de constructor basisklasse van DbContext om aan te geven dat deze verbinding door EF moet worden gebruikt in plaats van EF automatisch een nieuwe verbinding te laten maken. Op deze manier is de verbinding getagd door de API voor elastische databaseclient, zodat deze consistentie kan garanderen onder shardmapbeheerbewerkingen.

Gebruik de nieuwe constructor voor de subklasse DbContext in plaats van de standaardconstructor in uw code. Hier volgt een voorbeeld: 

```csharp
// Create and save a new blog.

Console.Write("Enter a name for a new blog: "); 
var name = Console.ReadLine(); 

using (var db = new ElasticScaleContext<int>( 
                        sharding.ShardMap,  
                        tenantId1,  
                        connStrBldr.ConnectionString)) 
{ 
    var blog = new Blog { Name = name }; 
    db.Blogs.Add(blog); 
    db.SaveChanges(); 

    // Display all Blogs for tenant 1 
    var query = from b in db.Blogs 
                orderby b.Name 
                select b; 
    … 
}
```

De nieuwe constructeur opent de verbinding met de scherf die de gegevens bevat voor de shardlet die wordt geïdentificeerd door de waarde van **tenantid1**. De code in **het blok met gebruik** blijft ongewijzigd om toegang te krijgen tot de **DbSet** voor blogs met BEHULP VAN EF op de shard voor **tenantid1**. Hiermee wordt de semantiek voor de code in het gebruiksblok zodanig gewijzigd dat alle databasebewerkingen nu worden ondergebracht bij de ene shard waar **tenantid1** wordt bewaard. Bijvoorbeeld, een LINQ query over de blogs **DbSet** zou alleen terug blogs opgeslagen op de huidige scherf, maar niet degenen die zijn opgeslagen op andere scherven.  

#### <a name="transient-faults-handling"></a>Afhandeling van tijdelijke fouten

Het team Microsoft Patterns & Practices heeft het [the Transient Fault Handling Application Block](https://msdn.microsoft.com/library/dn440719.aspx)gepubliceerd. De bibliotheek wordt gebruikt met elastische schaal clientbibliotheek in combinatie met EF. Zorg er echter voor dat elke tijdelijke uitzondering terugkeert naar een plaats waar u ervoor zorgen dat de nieuwe constructor wordt gebruikt na een tijdelijke fout, zodat elke nieuwe verbindingspoging wordt uitgevoerd met behulp van de constructors die u hebt getweakt. Anders is een verbinding met de juiste shard niet gegarandeerd en zijn er geen garanties dat de verbinding wordt onderhouden wanneer er wijzigingen in de shardkaart optreden. 

In het volgende codevoorbeeld wordt geïllustreerd hoe een SQL-hertrybeleid kan worden gebruikt rond de nieuwe dbcontext-subklasseconstructors: **DbContext** 

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() => 
{ 
    using (var db = new ElasticScaleContext<int>( 
                            sharding.ShardMap,  
                            tenantId1,  
                            connStrBldr.ConnectionString)) 
        { 
                var blog = new Blog { Name = name }; 
                db.Blogs.Add(blog); 
                db.SaveChanges(); 
        … 
        } 
    }); 
```

**SqlDatabaseUtils.SqlRetryPolicy** in de bovenstaande code wordt gedefinieerd als een **SqlDatabaseTransientErrorDetectionStrategy** met een hernieuwingsaantal van 10 en 5 seconden wachttijd tussen nieuwe pogingen. Deze aanpak is vergelijkbaar met de richtlijnen voor EF- en door de gebruiker geïnitieerde transacties (zie [Beperkingen met Uitvoeringsstrategieën opnieuw proberen (EF6 verder)](https://msdn.microsoft.com/data/dn307226). In beide situaties moet het toepassingsprogramma het bereik regelen waarop de tijdelijke uitzondering terugkeert: de transactie opnieuw openen of (zoals weergegeven) de context opnieuw maken vanuit de juiste constructor die de elastische databaseclientbibliotheek gebruikt.

De noodzaak om te bepalen waar tijdelijke uitzonderingen ons terug in het bereik brengen, sluit ook het gebruik uit van de ingebouwde **SqlAzureExecutionStrategy** die wordt geleverd met EF. **SqlAzureExecutionStrategy** zou een verbinding heropenen, maar geen Gebruik maken van **OpenConnectionForKey** en dus alle validatie omzeilen die wordt uitgevoerd als onderdeel van de **OpenConnectionForKey-aanroep.** In plaats daarvan maakt het codevoorbeeld gebruik van de ingebouwde **DefaultExecutionStrategy** die ook wordt geleverd met EF. In tegenstelling tot **SqlAzureExecutionStrategy**werkt het correct in combinatie met het retry-beleid van Transient Fault Handling. Het uitvoeringsbeleid is ingesteld in de klasse **ElasticScaleDbConfiguration.** Houd er rekening mee dat we hebben besloten **om DefaultSqlExecutionStrategy** niet te gebruiken, omdat het suggereert **sqlAzureExecutionStrategy** te gebruiken als er tijdelijke uitzonderingen optreden - wat zou leiden tot verkeerd gedrag zoals besproken. Zie [Verbindingstolerantie in EF](https://msdn.microsoft.com/data/dn456835.aspx)voor meer informatie over de verschillende retry-beleidsregels en EF.     

#### <a name="constructor-rewrites"></a>Constructor herschrijft

De bovenstaande codevoorbeelden illustreren de standaardconstructor die nodig is voor uw toepassing om gegevensafhankelijke routering te gebruiken met het Entiteitskader. De volgende tabel generaliseert deze benadering voor andere constructeurs. 

| Huidige constructeur | Constructor herschreven voor gegevens | Basisconstructor | Opmerkingen |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |De verbinding moet een functie zijn van de shardkaart en de gegevensafhankelijke routesleutel. U moet de automatische verbinding maken door EF omzeilen en in plaats daarvan de shardkaart gebruiken om de verbinding te bemiddelen. |
| MyContext(tekenreeks) |ElasticScaleContext(ShardMap, TKey) |DbContext(DbConnection, bool) |De verbinding is een functie van de shardkaart en de gegevensafhankelijke routesleutel. Een vaste databasenaam of verbindingstekenreeks werkt niet omdat ze door validatie door de shardkaart worden gevalideerd. |
| Mycontext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |De verbinding wordt gemaakt voor de gegeven shard kaart en sharding sleutel met het model verstrekt. Het gecompileerde model wordt doorgegeven aan de basis c'tor. |
| MyContext(DbConnection, bool) |ElasticScaleContext(ShardMap, TKey, bool) |DbContext(DbConnection, bool) |De verbinding moet worden afgeleid uit de shardkaart en de sleutel. Het kan niet worden geleverd als een input (tenzij die ingang was al met behulp van de shard kaart en de sleutel). De Booleaan wordt doorgegeven. |
| MyContext(tekenreeks, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext(DbConnection, DbCompiledModel, bool) |De verbinding moet worden afgeleid uit de shardkaart en de sleutel. Het kan niet worden geleverd als een input (tenzij die ingang was met behulp van de shard kaart en de sleutel). Het gecompileerde model wordt doorgegeven. |
| MyContext(ObjectContext, bool) |ElasticScaleContext(ShardMap, TKey, ObjectContext, bool) |DbContext(ObjectContext, bool) |De nieuwe constructor moet ervoor zorgen dat elke verbinding in de ObjectContext die als invoer wordt doorgegeven, wordt omgeleid naar een verbinding die wordt beheerd door Elastic Scale. Een gedetailleerde bespreking van ObjectContexts valt buiten het bereik van dit document. |
| MyContext(DbConnection, DbCompiledModel, bool) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel, bool) |DbContext(DbConnection, DbCompiledModel, bool); |De verbinding moet worden afgeleid uit de shardkaart en de sleutel. De verbinding kan niet worden geleverd als een invoer (tenzij die invoer al de shardkaart en de sleutel gebruikte). Model en Boolean worden doorgegeven aan de constructeur van de basisklasse. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementatie van shardschema's via EF-migraties

Automatisch schemabeheer is een gemak dat wordt geboden door het Entiteitskader. In de context van toepassingen die elastische databasegereedschappen gebruiken, wilt u deze mogelijkheid behouden om het schema automatisch in te richten op nieuw gemaakte shards wanneer databases worden toegevoegd aan de geshard toepassing. De primaire use case is om de capaciteit op de gegevenslaag voor geshardtoepassingen te verhogen met BEHULP van EF. Vertrouwen op ef's mogelijkheden voor schemabeheer vermindert de database beheer inspanning met een geshard applicatie gebouwd op EF. 

Schema-implementatie via EF-migraties werkt het beste op **ongeopende verbindingen**. Dit in tegenstelling tot het scenario voor gegevensafhankelijke routering dat is gebaseerd op de geopende verbinding die wordt geleverd door de API voor elastische databaseclient. Een ander verschil is de consistentievereiste: Hoewel het wenselijk is om consistentie te garanderen voor alle gegevensafhankelijke routeringsverbindingen om te beschermen tegen gelijktijdige shardkaartmanipulatie, is het geen probleem met de eerste schema-implementatie naar een nieuwe database die is nog niet geregistreerd in de shardkaart en nog niet toegewezen aan shardlets. U dus vertrouwen op regelmatige databaseverbindingen voor dit scenario, in tegenstelling tot gegevensafhankelijke routering.  

Dit leidt tot een aanpak waarbij schema-implementatie via EF-migraties nauw is gekoppeld aan de registratie van de nieuwe database als een scherf in de shardkaart van de toepassing. Dit is afhankelijk van de volgende voorwaarden: 

* De database is al gemaakt. 
* De database is leeg - er is geen gebruikersschema en geen gebruikersgegevens.
* De database is nog niet toegankelijk via de api's van de elastische databaseclient voor gegevensafhankelijke routering. 

Met deze vereisten u een reguliere niet-geopende **SqlConnection** maken om EF-migraties voor schema-implementatie af te starten. In het volgende voorbeeld van de code wordt deze benadering geïllustreerd. 

```csharp
// Enter a new shard - i.e. an empty database - to the shard map, allocate a first tenant to it  
// and kick off EF initialization of the database to deploy schema 

public void RegisterNewShard(string server, string database, string connStr, int key) 
{ 

    Shard shard = this.ShardMap.CreateShard(new ShardLocation(server, database)); 

    SqlConnectionStringBuilder connStrBldr = new SqlConnectionStringBuilder(connStr); 
    connStrBldr.DataSource = server; 
    connStrBldr.InitialCatalog = database; 

    // Go into a DbContext to trigger migrations and schema deployment for the new shard. 
    // This requires an un-opened connection. 
    using (var db = new ElasticScaleContext<int>(connStrBldr.ConnectionString)) 
    { 
        // Run a query to engage EF migrations 
        (from b in db.Blogs 
            select b).Count(); 
    } 

    // Register the mapping of the tenant to the shard in the shard map. 
    // After this step, data-dependent routing on the shard map can be used 

    this.ShardMap.CreatePointMapping(key, shard); 
} 
```

In dit voorbeeld wordt de methode **RegisterNewShard** weergegeven die de shard registreert in de shardkaart, het schema implementeert via EF-migraties en een toewijzing van een shardingsleutel op de shardopslaat. Het is gebaseerd op een constructor van de **Subklasse DbContext** **(ElasticScaleContext** in het voorbeeld) die een SQL-verbindingstekenreeks als invoer neemt. De code van deze constructor is rechttoe rechtaan, zoals het volgende voorbeeld laat zien: 

```csharp
// C'tor to deploy schema and migrations to a new shard 
protected internal ElasticScaleContext(string connectionString) 
    : base(SetInitializerForConnection(connectionString)) 
{ 
} 

// Only static methods are allowed in calls into base class c'tors 
private static string SetInitializerForConnection(string connectionString) 
{ 
    // You want existence checks so that the schema can get deployed 
    Database.SetInitializer<ElasticScaleContext<T>>( 
new CreateDatabaseIfNotExists<ElasticScaleContext<T>>()); 

    return connectionString; 
} 
```

Men zou de versie van de constructor kunnen hebben gebruikt die van de basisklasse wordt geërfd. Maar de code moet ervoor zorgen dat de standaard initialisator voor EF wordt gebruikt bij het aansluiten. Vandaar de korte omweg naar de statische methode voordat u met de verbindingstekenreeks de constructor van de basisklasse aanroept. Houd er rekening mee dat de registratie van shards moet worden uitgevoerd in een ander app-domein of -proces om ervoor te zorgen dat de initialisator-instellingen voor EF niet in conflict komen. 

## <a name="limitations"></a>Beperkingen

De in dit document beschreven benaderingen brengen een aantal beperkingen met zich mee: 

* EF-toepassingen die **LocalDb** gebruiken, moeten eerst migreren naar een reguliere SQL Server-database voordat u de elastische databaseclientbibliotheek gebruikt. Het uitschalen van een toepassing door middel van sharding met Elastic Scale is niet mogelijk met **LocalDb.** Houd er rekening mee dat ontwikkeling **LocalDb**nog steeds kan gebruiken. 
* Wijzigingen in de toepassing die wijzigingen in het databaseschema impliceren, moeten door EF-migraties op alle shards gaan. De voorbeeldcode voor dit document laat niet zien hoe u dit moet doen. Overweeg Update-Database te gebruiken met een parameter ConnectionString om alle shards te herhalen. of haal het T-SQL-script voor de in behandeling zijnde migratie met Update-Database met de optie -Script en pas het T-SQL-script toe op uw shards.  
* Bij een verzoek wordt ervan uitgegaan dat al de verwerking van de database is opgenomen in een enkele scherf, zoals geïdentificeerd door de shardingsleutel die door het verzoek wordt verstrekt. Deze veronderstelling geldt echter niet altijd. Bijvoorbeeld wanneer het niet mogelijk is om een sharding sleutel beschikbaar te stellen. Om dit aan te pakken, biedt de clientbibliotheek de klasse **MultiShardQuery** die een verbindingsabstractie implementeert voor het opvragen over verschillende shards. Leren om de **MultiShardQuery** te gebruiken in combinatie met EF valt buiten het bereik van dit document

## <a name="conclusion"></a>Conclusie

Via de stappen die in dit document worden beschreven, kunnen EF-toepassingen de mogelijkheid van de elastische databaseclientbibliotheek gebruiken voor gegevensafhankelijke routering door constructors van de **Subklassen DbContext** die in de EF-toepassing worden gebruikt, te refactoringen. Dit beperkt de wijzigingen die nodig zijn voor de plaatsen waar **dbcontext-klassen** al bestaan. Bovendien kunnen EF-toepassingen blijven profiteren van automatische schema-implementatie door de stappen die de nodige EF-migraties aanroepen te combineren met de registratie van nieuwe scherven en toewijzingen in de shardkaart. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png

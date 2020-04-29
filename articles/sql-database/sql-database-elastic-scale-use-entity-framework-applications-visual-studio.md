---
title: Client bibliotheek voor Elastic Data Base gebruiken met Entity Framework
description: Elastic Database-client bibliotheek en-Entity Framework gebruiken voor het coderen van data bases
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77087183"
---
# <a name="elastic-database-client-library-with-entity-framework"></a>Client bibliotheek Elastic Database met Entity Framework

Dit document toont de wijzigingen in een Entity Framework toepassing die nodig zijn om te integreren met de [Elastic database-hulpprogram ma's](sql-database-elastic-scale-introduction.md). De focus ligt op het samen stellen van [Shard-kaart beheer](sql-database-elastic-scale-shard-map-management.md) en [gegevens afhankelijke route ring](sql-database-elastic-scale-data-dependent-routing.md) met de Entity Framework **code First** aanpak. De [code First-nieuwe data base-](https://msdn.microsoft.com/data/jj193542.aspx) zelf studie voor EF fungeert als het actieve voor beeld in dit document. De voorbeeld code van dit document maakt deel uit van de verzameling voor beelden van elastische database hulpprogramma's in de Visual Studio code-voor beelden.

## <a name="downloading-and-running-the-sample-code"></a>De voorbeeld code downloaden en uitvoeren

Voor het downloaden van de code voor dit artikel:

* Visual Studio 2012 of hoger is vereist. 
* Down load de [elastische DB-Hulpprogram ma's voor Azure SQL-Entity Framework Integration-voor beeld](https://github.com/Azure/elastic-db-tools/). Pak het voor beeld uit naar een locatie van uw keuze.
* Start Visual Studio. 
* Selecteer in Visual Studio file-> open project/Solution. 
* In het dialoog venster **project openen** gaat u naar het voor beeld dat u hebt gedownload en selecteert u **EntityFrameworkCodeFirst. SLN** om het voor beeld te openen. 

Als u het voor beeld wilt uitvoeren, moet u drie lege data bases maken in Azure SQL Database:

* Shard map manager-data base
* Shard 1-data base
* Shard 2-data base

Wanneer u deze data bases hebt gemaakt, vult u de locatie houders in **Program.cs** in met de naam van uw Azure SQL DB-server, de database namen en uw referenties om verbinding te maken met de data bases. Bouw de oplossing in Visual Studio. Visual Studio downloadt de vereiste NuGet-pakketten voor de client bibliotheek voor Elastic data base, Entity Framework en tijdelijke fout afhandeling als onderdeel van het bouw proces. Zorg ervoor dat het herstellen van NuGet-pakketten is ingeschakeld voor uw oplossing. U kunt deze instelling inschakelen door met de rechter muisknop te klikken op het oplossings bestand in Visual Studio Solution Explorer. 

## <a name="entity-framework-workflows"></a>Entity Framework werk stromen

Entity Framework ontwikkel aars zijn afhankelijk van een van de volgende vier werk stromen om toepassingen te bouwen en om persistentie te garanderen voor toepassings objecten:

* **Code First (nieuwe data base)**: de EF-ontwikkelaar maakt het model in de toepassings code en vervolgens wordt de data base door EF gegenereerd. 
* **Code First (bestaande data base)**: met de ontwikkelaar kan EF de toepassings code voor het model uit een bestaande data base genereren.
* **Model eerst**: de ontwikkelaar maakt het model in de EF Designer en vervolgens wordt de data base gemaakt op basis van het model.
* **Data Base**: de ontwikkelaar gebruikt het hulp programma EF om het model van een bestaande data base af te leiden. 

Al deze benaderingen zijn afhankelijk van de DbContext-klasse om database verbindingen en database schema op transparante wijze te beheren voor een toepassing. Verschillende constructors in de DbContext-basis klasse bieden verschillende niveaus van controle over het maken van verbindingen, data base-Boots traps en het maken van schema's. Problemen ontstaan voornamelijk uit het feit dat het database verbindings beheer dat door EF werd geboden door de verbindings beheer mogelijkheden van de gegevens afhankelijke routerings interfaces die worden geboden door de client bibliotheek voor Elastic data base. 

## <a name="elastic-database-tools-assumptions"></a>Veronderstellingen voor Elastic data base-hulpprogram ma's

Zie [Elastic database extra woorden lijst](sql-database-elastic-scale-glossary.md)voor termen definities.

Met Elastic data base-client bibliotheek definieert u de partities van uw toepassings gegevens, met de naam shardlets. Shardlets worden geïdentificeerd aan de hand van een sharding-sleutel en worden toegewezen aan specifieke data bases. Een toepassing kan zoveel data bases bevatten als nodig is en de shardlets distribueren om voldoende capaciteit of prestaties te bieden aan de huidige bedrijfs vereisten. De toewijzing van sharding-sleutel waarden aan de data bases wordt opgeslagen door een Shard-kaart die wordt verschaft door de client-Api's voor Elastic data base. Deze mogelijkheid heet **Shard-toewijzings beheer**of smm voor short. De Shard-toewijzing fungeert ook als de Broker van database verbindingen voor aanvragen die een sharding-sleutel bevatten. Deze mogelijkheid wordt ook wel **gegevens afhankelijke route ring**genoemd. 

De Shard-toewijzings Manager beveiligt gebruikers van inconsistente weer gaven in shardlet-gegevens die kunnen optreden wanneer gelijktijdige shardlet-beheer bewerkingen (zoals het verplaatsen van gegevens van de ene Shard naar een andere) plaatsvinden. Hiertoe worden de Shard-kaarten die worden beheerd door de client bibliotheek Broker de database verbindingen voor een toepassing. Op deze manier kan de Shard-toewijzings functionaliteit automatisch een database verbinding afbreken wanneer Shard-beheer bewerkingen van invloed kunnen zijn op de shardlet waarmee de verbinding is gemaakt. Deze aanpak moet worden geïntegreerd met een aantal functies van EF, zoals het maken van nieuwe verbindingen van een bestaande verbinding om te controleren of de data base bestaat. Over het algemeen is onze waarneming dat de standaard DbContext-constructors alleen betrouwbaar werken voor gesloten database verbindingen die veilig kunnen worden gekloond voor EF-werk. In plaats daarvan is het ontwerp principe van elastische data base alleen het aantal geopende Broker-verbindingen. Het kan ook voor komen dat een verbinding die is gesloten door de client bibliotheek wordt afgesloten voordat deze naar de EF DbContext kan worden opgelost. Door de verbinding te sluiten en te vertrouwen op EF om deze opnieuw te openen, wordt een voor gaande gecontroleerd op de validatie-en consistentie controles die door de bibliotheek worden uitgevoerd. De migratie functionaliteit in EF maakt echter gebruik van deze verbindingen om het onderliggende database schema te beheren op een manier die transparant is voor de toepassing. In het ideale geval moet u al deze mogelijkheden van de client bibliotheek voor Elastic data base en EF in dezelfde toepassing bewaren en combi neren. In de volgende sectie vindt u meer informatie over deze eigenschappen en vereisten. 

## <a name="requirements"></a>Vereisten

Wanneer u werkt met de client bibliotheek voor Elastic data base en Entity Framework Api's, wilt u de volgende eigenschappen behouden: 

* **Uitschalen**: om data bases toe te voegen aan of te verwijderen uit de gegevenslaag van de Shard-toepassing, indien nodig voor de capaciteits vereisten van de toepassing. Dit betekent het beheren van het maken en verwijderen van data bases en het gebruik van de elastische data base Shard-toewijzings beheer Api's voor het beheren van data bases en toewijzingen van shardlets. 
* **Consistentie**: de toepassing maakt gebruik van sharding en gebruikt de gegevens afhankelijke routerings mogelijkheden van de client bibliotheek. Om beschadigingen of verkeerde query resultaten te voor komen, worden verbindingen brokert via het Shard-toewijzings beheer. Hiermee behoudt u ook validatie en consistentie.
* **Code eerst**: om het gemak van het eerste paradigma van de code van EF te bewaren. In de eerste code worden klassen in de toepassing transparant toegewezen aan de onderliggende database structuren. De toepassings code communiceert met DbSets waarmee de meeste aspecten van de onderliggende database verwerking worden gemaskeerd.
* **Schema**: Entity Framework verwerkt initiële database schema's en verdere schema-evolutie via migraties. Door deze mogelijkheden te behouden, is het aanpassen van uw app eenvoudig omdat de gegevens worden gegroeid. 

De volgende richt lijnen geven aan hoe aan deze vereisten wordt voldaan voor het eerste gebruik van code toepassingen met Elastic data base-hulpprogram ma's. 

## <a name="data-dependent-routing-using-ef-dbcontext"></a>Gegevens afhankelijke route ring met EF DbContext

Database verbindingen met Entity Framework worden doorgaans beheerd via subklassen van **DbContext**. Maak deze subklassen door af te leiden van **DbContext**. Hier definieert u uw **DbSets** voor het implementeren van de door data bases gemaakte verzamelingen van CLR-objecten voor uw toepassing. In de context van gegevens afhankelijke route ring kunt u verschillende nuttige eigenschappen identificeren die niet noodzakelijkerwijs van toepassing zijn op de eerste toepassings scenario's van andere EF-code: 

* De data base bestaat al en is geregistreerd in de Shard-kaart voor Elastic data base. 
* Het schema van de toepassing is al geïmplementeerd voor de data base (zie hieronder). 
* Gegevens afhankelijke routerings verbindingen met de Data Base zijn brokered door de Shard-kaart. 

**DbContexts** integreren met gegevens afhankelijke route ring voor uitschalen:

1. Fysieke database verbindingen maken via de client interfaces voor Elastic data base van de Shard-kaart beheer, 
2. De verbinding met de **DbContext** -subklasse verpakken
3. Geef de verbinding op in de basis klassen **DbContext** om ervoor te zorgen dat alle verwerkingen op de EF-zijde ook worden uitgevoerd. 

In het volgende code voorbeeld ziet u deze aanpak. (Deze code bevindt zich ook in het bijbehorende Visual Studio-project)

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

## <a name="main-points"></a>Hoofd punten

* Een nieuwe constructor vervangt de standaardconstructor in de DbContext-subklasse 
* De nieuwe constructor accepteert de argumenten die vereist zijn voor gegevens afhankelijke route ring via Elastic data base-client bibliotheek:
  
  * de Shard-kaart voor toegang tot de gegevens afhankelijke routerings interfaces,
  * de sharding-sleutel voor het identificeren van de shardlet,
  * een connection string met de referenties voor de gegevens afhankelijke routerings verbinding met de Shard. 
* Het aanroepen van de basis klasse-constructor neemt een omleiding naar een statische methode waarmee alle stappen worden uitgevoerd die nodig zijn voor gegevens afhankelijke route ring. 
  
  * Er wordt gebruikgemaakt van de OpenConnectionForKey-aanroep van de client interfaces voor Elastic Data Base op de Shard-kaart om een open verbinding tot stand te brengen.
  * De Shard-kaart maakt de open verbinding met de Shard die de shardlet voor de opgegeven sharding sleutel bevat.
  * Deze open verbinding wordt teruggestuurd naar de basis klasse-constructor van DbContext om aan te geven dat deze verbinding moet worden gebruikt door EF in plaats van EF een nieuwe verbinding automatisch maken. Op deze manier is de verbinding gelabeld door de client-API voor Elastic data base, zodat deze consistentie kan garanderen onder Shard-toewijzings beheer bewerkingen.

Gebruik de nieuwe constructor voor uw DbContext-subklasse in plaats van de standaardconstructor in uw code. Hier volgt een voorbeeld: 

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

De nieuwe constructor opent de verbinding met de Shard die de gegevens bevat voor de shardlet die wordt geïdentificeerd door de waarde van **tenantid1**. De code in het blok **using** blijft ongewijzigd voor toegang tot de **DbSet** voor blogs met EF op de Shard voor **tenantid1**. Deze wijziging is van invloed op de code in het gebruik van blok keren, zodat alle database bewerkingen nu worden afgestemd op de ene Shard waar **tenantid1** wordt bewaard. Zo zou een LINQ-query via de blogs **DbSet** alleen blogs retour neren die zijn opgeslagen op de huidige Shard, maar niet op de bestanden die zijn opgeslagen op andere Shards.  

#### <a name="transient-faults-handling"></a>Afhandeling van tijdelijke fouten

Het micro soft-programma voor patronen & practices heeft het [toepassings blok voor tijdelijke fout afhandeling](https://msdn.microsoft.com/library/dn440719.aspx)gepubliceerd. De tape wisselaar wordt gebruikt met een elastische Scale-client bibliotheek in combi natie met EF. Zorg echter dat elke tijdelijke uitzonde ring terugkeert naar een locatie waar u ervoor kunt zorgen dat de nieuwe constructor wordt gebruikt na een tijdelijke fout, zodat er een nieuwe verbindings poging wordt gedaan met de constructors die u hebt verfijnd. Anders wordt een verbinding met de juiste Shard niet gegarandeerd en is er geen garantie dat de verbinding wordt behouden als er wijzigingen in de Shard-toewijzing optreden. 

In het volgende code voorbeeld ziet u hoe een SQL-beleid voor opnieuw proberen kan worden gebruikt rond de nieuwe **DbContext** -subklassen: 

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

**SqlDatabaseUtils. SqlRetryPolicy** in de bovenstaande code wordt gedefinieerd als een **SqlDatabaseTransientErrorDetectionStrategy** met een aantal nieuwe pogingen van 10 en een wacht tijd van vijf seconden tussen nieuwe pogingen. Deze benadering is vergelijkbaar met de richt lijnen voor EF en door de gebruiker gestarte trans acties (Zie de [beperkingen bij het opnieuw proberen van de uitvoerings strategie (EF6)](https://msdn.microsoft.com/data/dn307226). Beide situaties vereisen dat het toepassings programma het bereik bepaalt waarnaar de tijdelijke uitzonde ring wordt geretourneerd: als u de trans actie opnieuw wilt openen of (zoals weer gegeven), maakt u de context opnieuw van de juiste constructor die gebruikmaakt van de client bibliotheek voor Elastic data base.

De nood zaak om te bepalen waar tijdelijke uitzonde ringen in het bereik terugkomen, is ook van toepassing op het gebruik van de ingebouwde **SqlAzureExecutionStrategy** die bij EF wordt geleverd. **SqlAzureExecutionStrategy** zou een verbinding opnieuw kunnen openen, maar **OpenConnectionForKey** niet gebruiken en omzeilt daarom alle validaties die worden uitgevoerd als onderdeel van de **OpenConnectionForKey** -aanroep. In plaats daarvan gebruikt het code voorbeeld de ingebouwde **DefaultExecutionStrategy** die ook bij EF worden geleverd. In tegens telling tot **SqlAzureExecutionStrategy**werkt het goed in combi natie met het beleid voor opnieuw proberen van tijdelijke fout afhandeling. Het uitvoerings beleid wordt ingesteld in de **ElasticScaleDbConfiguration** -klasse. Houd er rekening mee dat we **DefaultSqlExecutionStrategy** niet gebruiken omdat wordt voorgesteld het gebruik van **SqlAzureExecutionStrategy** als tijdelijke uitzonde ringen optreden. Dit zou leiden tot een onjuist gedrag zoals besproken. Zie [verbindings tolerantie in EF](https://msdn.microsoft.com/data/dn456835.aspx)voor meer informatie over de verschillende beleids regels voor opnieuw proberen en EF.     

#### <a name="constructor-rewrites"></a>Constructor reschrijft

De code voorbeelden hierboven illustreren de standaard herschrijf bewerkingen van de constructor die vereist zijn voor uw toepassing om gegevens afhankelijke route ring te kunnen gebruiken met de Entity Framework. In de volgende tabel wordt deze aanpak gegeneraliseerd met andere constructors. 

| Huidige constructor | Reschreven constructor voor gegevens | Basis-constructor | Opmerkingen |
| --- | --- | --- | --- |
| MyContext() |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, BOOL) |De verbinding moet een functie zijn van de Shard-kaart en de gegevens afhankelijke routerings sleutel. U moet door gaan met het maken van automatische verbinding door EF en in plaats daarvan de Shard-toewijzing gebruiken om de verbinding te Broker. |
| MyContext (teken reeks) |ElasticScaleContext(ShardMap, TKey) |DbContext (DbConnection, BOOL) |De verbinding is een functie van de Shard-kaart en de gegevens afhankelijke routerings sleutel. De naam van een vaste data base of connection string werkt niet als door gegeven validatie door de Shard-kaart. |
| MyContext(DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, BOOL) |De verbinding wordt gemaakt voor de opgegeven Shard-map en de sharding-sleutel met het opgegeven model. Het gecompileerde model wordt door gegeven aan de basis-c'tor. |
| MyContext (DbConnection, BOOL) |ElasticScaleContext (ShardMap, TKey, BOOL) |DbContext (DbConnection, BOOL) |De verbinding moet worden afgeleid van de Shard-kaart en de sleutel. Het kan niet worden opgegeven als invoer (tenzij die invoer al gebruikmaakt van de Shard-kaart en de sleutel). De Booleaanse waarde wordt door gegeven aan. |
| MyContext (teken reeks, DbCompiledModel) |ElasticScaleContext(ShardMap, TKey, DbCompiledModel) |DbContext (DbConnection, DbCompiledModel, BOOL) |De verbinding moet worden afgeleid van de Shard-kaart en de sleutel. Het kan niet worden opgegeven als invoer (tenzij die invoer de Shard-kaart en de sleutel gebruikt.) Het gecompileerde model is door gegeven aan. |
| MyContext (object context, BOOL) |ElasticScaleContext (ShardMap, TKey, object context, BOOL) |DbContext (object context, BOOL) |De nieuwe constructor moet ervoor zorgen dat een verbinding in de object context die is door gegeven als invoer, opnieuw wordt gerouteerd naar een verbinding die wordt beheerd door Elastic scale. Een gedetailleerde bespreking van ObjectContexts valt buiten het bereik van dit document. |
| MyContext (DbConnection, DbCompiledModel, BOOL) |ElasticScaleContext (ShardMap, TKey, DbCompiledModel, BOOL) |DbContext (DbConnection, DbCompiledModel, BOOL); |De verbinding moet worden afgeleid van de Shard-kaart en de sleutel. De verbinding kan niet worden opgegeven als invoer (tenzij die invoer al gebruikmaakt van de Shard-kaart en de sleutel). Het model en de Booleaanse waarde worden door gegeven aan de constructor van de basis klasse. |

## <a name="shard-schema-deployment-through-ef-migrations"></a>Implementatie van Shard-schema via EF-migraties

Automatische schema beheer is een gebruiks gemak van de Entity Framework. In de context van toepassingen die gebruikmaken van elastische database hulpprogramma's, wilt u deze mogelijkheid behouden om het schema automatisch in te richten op nieuw gemaakte Shards wanneer data bases worden toegevoegd aan de Shard-toepassing. De primaire use-case is het verg Roten van de capaciteit van de gegevenslaag voor Shard-toepassingen met EF. Het gebruik van de mogelijkheden van EF voor schema beheer vermindert de inspanning van het database beheer met een Shard-toepassing die is gebouwd op EF. 

Schema-implementatie via EF-migraties werkt het beste bij niet- **opende verbindingen**. Dit is in tegens telling tot het scenario voor gegevens afhankelijke route ring, afhankelijk van de geopende verbinding die is gemaakt door de client-API voor Elastic data base. Een ander verschil is de consistentie vereisten: Hoewel het wenselijk is om consistentie te garanderen voor alle gegevens afhankelijke routerings verbindingen om te beschermen tegen gelijktijdige Shard toewijzings bewerkingen, is het geen probleem met de initiële schema-implementatie naar een nieuwe Data Base die nog niet is geregistreerd in de Shard-kaart en nog niet is toegewezen aan shardlets. U kunt daarom gebruikmaken van normale database verbindingen voor dit scenario, in tegens telling tot gegevens afhankelijke route ring.  

Dit leidt tot een benadering waarbij schema-implementatie via EF-migraties nauw samen worden gekoppeld aan de registratie van de nieuwe Data Base als een Shard in de Shard-toewijzing van de toepassing. Dit is afhankelijk van de volgende vereisten: 

* De data base is al gemaakt. 
* De data base is leeg. het bevat geen gebruikers schema en geen gebruikers gegevens.
* De data base is nog niet toegankelijk via de client-Api's van de Elastic Data Base voor gegevens afhankelijke route ring. 

Als aan deze vereisten is voldaan, kunt u een gewone ongeopende **SqlConnection** maken om te starten met EF-migraties voor de implementatie van het schema. In het volgende code voorbeeld ziet u deze aanpak. 

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

Dit voor beeld toont de methode **RegisterNewShard** die de Shard registreert in de Shard-toewijzing, implementeert het schema via EF-migraties en slaat een toewijzing van een sharding-sleutel op in de Shard. Dit is afhankelijk van een constructor van de **DbContext** -subklasse (**ElasticScaleContext** in het voor beeld) waarmee een SQL-Connection String als invoer wordt gebruikt. De code van deze constructor is direct-voorwaarts, zoals in het volgende voor beeld wordt getoond: 

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

Een van de versies van de constructor die is overgenomen van de basis klasse, is mogelijk al gebruikt. Maar de code moet ervoor zorgen dat de standaard initialisatie functie voor EF wordt gebruikt bij het maken van verbinding. Daarom wordt de korte afronding van de statische methode voor het aanroepen van de basis klasse-constructor met de connection string. Houd er rekening mee dat de registratie van Shards moet worden uitgevoerd in een ander app-domein of proces om ervoor te zorgen dat de initialisatie functie-instellingen voor EF niet conflicteren. 

## <a name="limitations"></a>Beperkingen

De benaderingen die in dit document worden beschreven, omvatten een aantal beperkingen: 

* EF-toepassingen die gebruikmaken van **LocalDb** , moeten eerst worden gemigreerd naar een gewone SQL Server-Data Base voordat ze gebruikmaken van de client bibliotheek voor Elastic data base. Het schalen van een toepassing via sharding met Elastic Scale is niet mogelijk met **LocalDb**. Houd er rekening mee dat ontwikkel aars nog steeds **LocalDb**kunnen gebruiken. 
* Eventuele wijzigingen in de toepassing die wijzigingen in het database schema impliceren, moeten door EF-migraties door gaan op alle Shards. De voorbeeld code voor dit document laat zien hoe u dit doet. Overweeg het gebruik van Update-Data Base met een Connections Tring-para meter om alle Shards te herhalen; of pak het T-SQL-script uit voor de in behandeling zijnde migratie met behulp van Update-Data Base met de optie-script en pas het T-SQL-script toe op uw Shards.  
* Als er een aanvraag wordt ingediend, wordt ervan uitgegaan dat alle database verwerking is opgenomen in één Shard die wordt geïdentificeerd door de sharding-sleutel die door de aanvraag wordt verstrekt. Deze veronderstelling houdt echter niet altijd in op True. Bijvoorbeeld, wanneer het niet mogelijk is om een sharding-sleutel beschikbaar te maken. Om dit op te lossen, biedt de client bibliotheek de **MultiShardQuery** -klasse die een verbindings abstractie implementeert voor het uitvoeren van query's op verschillende Shards. Het leren gebruiken van de **MultiShardQuery** in combi natie met EF valt buiten het bereik van dit document

## <a name="conclusion"></a>Conclusie

Met de stappen die in dit document worden beschreven, kunnen EF-toepassingen gebruikmaken van de functionaliteit van de Elastic data base-client bibliotheek voor gegevensafhankelijke route ring door constructors van de **DbContext** -subklassen in de toepassing EF te herstructureren. Hiermee worden de wijzigingen beperkt die nodig zijn voor de locaties waar **DbContext** -klassen al bestaan. Daarnaast kunnen EF-toepassingen blijven profiteren van automatische schema-implementatie door de stappen te combi neren die de vereiste EF-migraties aanroepen met de registratie van nieuwe Shards en toewijzingen in de Shard-toewijzing. 

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-use-entity-framework-applications-visual-studio/sample.png

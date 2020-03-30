---
title: Een database uitschalen
description: De shardMapManager, de elastische databaseclientbibliotheek gebruiken
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 8175563d8c1c2ec59b4195b2ede06f6e1dbf8556
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256261"
---
# <a name="scale-out-databases-with-the-shard-map-manager"></a>Databases uitschalen met de shardmapmanager

Als u eenvoudig databases in SQL Azure wilt uitschalen, gebruikt u een shardmapbeheer. De shard map manager is een speciale database die wereldwijde mapping informatie over alle scherven (databases) in een shard set onderhoudt. Met de metagegevens kan een toepassing verbinding maken met de juiste database op basis van de waarde van de **shardingsleutel.** Bovendien bevat elke scherf in de set kaarten die de lokale shardgegevens bijhouden (ook wel **shardlets genoemd).**

![Shard-toewijzingsbeheer](./media/sql-database-elastic-scale-shard-map-management/glossary.png)

Begrijpen hoe deze kaarten zijn opgebouwd is essentieel voor het shard mapmanagement. Dit gebeurt met de klasse ShardMapManager[(Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager), gevonden in de [Elastic Database clientbibliotheek](sql-database-elastic-database-client-library.md) om shardkaarten te beheren.  

## <a name="shard-maps-and-shard-mappings"></a>Shardkaarten en shardtoewijzingen

Voor elke scherf moet u het type shardkaart selecteren dat u wilt maken. De keuze is afhankelijk van de databasearchitectuur:

1. Eén tenant per database  
2. Meerdere tenants per database (twee typen):
   1. Lijsttoewijzing
   2. Bereiktoewijzing

Maak voor een model met één tenant een **shardkaart voor lijsttoewijzing.** Het model met één tenant wijst één database per tenant toe. Dit is een effectief model voor SaaS-ontwikkelaars omdat het het beheer vereenvoudigt.

![Lijsttoewijzing][1]

Het multi-tenantmodel wijst meerdere tenants toe aan een afzonderlijke database (en u groepen tenants over meerdere databases distribueren). Gebruik dit model wanneer u verwacht dat elke tenant kleine gegevensbehoeften heeft. Wijs in dit model een reeks tenants toe aan een database met **bereiktoewijzing.**

![Bereiktoewijzing][2]

U ook een databasemodel met meerdere tenants implementeren met behulp van een *lijsttoewijzing* om meerdere tenants toe te wijzen aan een afzonderlijke database. DB1 wordt bijvoorbeeld gebruikt om informatie over tenant-ID 1 en 5 op te slaan en DB2 slaat gegevens op voor tenant 7 en tenant 10.

![Meerdere tenants op single DB][3]

### <a name="supported-types-for-sharding-keys"></a>Ondersteunde typen voor shardingstoetsen

Elastic Scale ondersteunt de volgende typen als shardingtoetsen:

| .NET | Java |
| --- | --- |
| geheel getal |geheel getal |
| long |long |
| Guid |uuid |
| byte[]  |byte[] |
| datum/tijd | tijdstempel |
| Tijdspanne | duur|
| datumtijdverschuiving |offsetdatumtijd |

### <a name="list-and-range-shard-maps"></a>Lijst- en bereikshardkaarten

Shardkaarten kunnen worden gemaakt met **behulp van lijsten met afzonderlijke shardingskernwaarden,** of ze kunnen worden geconstrueerd met behulp van bereiken van **shardingskernwaarden**.

### <a name="list-shard-maps"></a>Lijst shardkaarten

**Scherven** bevatten **shardlets** en het in kaart brengen van shardlets tot scherven wordt onderhouden door een shardkaart. Een **lijstshardkaart** is een koppeling tussen de afzonderlijke sleutelwaarden die de shardlets identificeren en de databases die als shards dienen.  **Lijsttoewijzingen** zijn expliciet en verschillende kernwaarden kunnen aan dezelfde database worden toegewezen. Belangrijke waarde 1 wordt bijvoorbeeld toegewezen aan database A en belangrijke waarden 3 en 6 beide kaarten naar Database B.

| Sleutel | Shardlocatie |
| --- | --- |
| 1 |Database_A |
| 3 |Database_B |
| 4 |Database_C |
| 6 |Database_B |
| ... |... |

### <a name="range-shard-maps"></a>De shardkaarten van het bereik

In een **range shardmap**wordt het sleutelbereik beschreven door een paar **[Lage waarde, Hoge waarde)** waarbij de *lage waarde* de minimumsleutel in het bereik is en de hoge *waarde* de eerste waarde is die hoger is dan het bereik.

**[0, 100)** bevat bijvoorbeeld alle gehele getallen groter dan of gelijk 0 en minder dan 100. Houd er rekening mee dat meerdere bereiken naar dezelfde database kunnen wijzen en dat onsamenhangende bereiken worden ondersteund (bijvoorbeeld [100.200) en [400.600) die beide naar Database C in het volgende voorbeeld wijzen.)

| Sleutel | Shardlocatie |
| --- | --- |
| [1,50) |Database_A |
| [50,100) |Database_B |
| [100,200) |Database_C |
| [400,600) |Database_C |
| ... |... |

Elk van de bovenstaande tabellen is een conceptueel voorbeeld van een **ShardMap-object.** Elke rij is een vereenvoudigd voorbeeld van een afzonderlijk **PointMapping** -object (voor de lijstshardkaart) of het object **RangeMapping** (voor de bereikshardkaart).

## <a name="shard-map-manager"></a>Shard-toewijzingsbeheer

In de clientbibliotheek is de shardmapmanager een verzameling shardkaarten. De gegevens die worden beheerd door een **shardmapmanager-instantie** worden op drie plaatsen bewaard:

1. **Global Shard Map (GSM)**: U geeft een database op om te dienen als opslagplaats voor al zijn shardkaarten en mappings. Speciale tabellen en opgeslagen procedures worden automatisch gemaakt om de informatie te beheren. Dit is meestal een kleine database en licht toegankelijk, en het mag niet worden gebruikt voor andere behoeften van de toepassing. De tabellen bevinden zich in een speciaal schema met de naam **__ShardManagement**.
2. **Local Shard Map (LSM)**: Elke database die u opgeeft als een scherf is, wordt gewijzigd om verschillende kleine tabellen en speciale opgeslagen procedures te bevatten die shardkaartinformatie bevatten die specifiek is voor die shard. Deze informatie is overbodig met de informatie in de GSM, en het stelt de toepassing in staat om cached shard kaart informatie te valideren zonder enige belasting op de GSM; de toepassing gebruikt de LSM om te bepalen of een toewijzing in de cache nog geldig is. De tabellen die overeenkomen met de LSM op elke shard staan ook in het schema **__ShardManagement**.
3. **Toepassingscache:** Elke toepassingsinstantie die toegang heeft tot een **ShardMapManager-object,** behoudt een lokale cache in het geheugen van de toewijzingen. Het slaat routeringsinformatie op die onlangs is opgehaald.

## <a name="constructing-a-shardmapmanager"></a>Een ShardMapManager maken

Een **shardMapManager-object** wordt gemaakt met behulp van een fabriekspatroon ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory) De methode **ShardMapManagerFactory.GetSqlShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET)](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)neemt referenties (inclusief de servernaam en databasenaam die de GSM vasthoudt) in de vorm van een **ConnectionString** en retourneert een instantie van een **ShardMapManager**.  

**Let op:** De **ShardMapManager** mag slechts één keer per app-domein worden geinstantieerd, binnen de initialisatiecode voor een toepassing. Het maken van extra exemplaren van ShardMapManager in hetzelfde app-domein resulteert in meer geheugen en CPU-gebruik van de toepassing. Een **ShardMapManager** kan een willekeurig aantal shardkaarten bevatten. Hoewel een enkele shardkaart voor veel toepassingen voldoende kan zijn, zijn er momenten waarop verschillende sets databases worden gebruikt voor verschillende schema's of voor unieke doeleinden; in die gevallen kunnen meerdere shardkaarten de voorkeur krijgen.

In deze code probeert een toepassing een bestaande **ShardMapManager** te openen met de TryGetSqlShardMapManager ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.trygetsqlshardmapmanager), [.NET-methode.](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager) Als objecten die een GSM (Global **ShardMapManager)** vertegenwoordigen, nog niet in de database voorkomen, maakt de clientbibliotheek deze met behulp van de methode CreateSqlShardMapManager[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.createsqlshardmapmanager), [.NET).](/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.createsqlshardmapmanager)

```Java
// Try to get a reference to the Shard Map Manager in the shardMapManager database.
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager = null;
boolean shardMapManagerExists = ShardMapManagerFactory.tryGetSqlShardMapManager(shardMapManagerConnectionString,ShardMapManagerLoadPolicy.Lazy, refShardMapManager);
shardMapManager = refShardMapManager.argValue;

if (shardMapManagerExists) {
    ConsoleUtils.writeInfo("Shard Map %s already exists", shardMapManager);
}
else {
    // The Shard Map Manager does not exist, so create it
    shardMapManager = ShardMapManagerFactory.createSqlShardMapManager(shardMapManagerConnectionString);
    ConsoleUtils.writeInfo("Created Shard Map %s", shardMapManager);
}
```

```csharp
// Try to get a reference to the Shard Map Manager via the Shard Map Manager database.  
// If it doesn't already exist, then create it.
ShardMapManager shardMapManager;
bool shardMapManagerExists = ShardMapManagerFactory.TryGetSqlShardMapManager(
                                        connectionString,
                                        ShardMapManagerLoadPolicy.Lazy,
                                        out shardMapManager);

if (shardMapManagerExists)
{
    Console.WriteLine("Shard Map Manager already exists");
}
else
{
    // Create the Shard Map Manager.
    ShardMapManagerFactory.CreateSqlShardMapManager(connectionString);
    Console.WriteLine("Created SqlShardMapManager");

    shardMapManager = ShardMapManagerFactory.GetSqlShardMapManager(
            connectionString,
            ShardMapManagerLoadPolicy.Lazy);

// The connectionString contains server name, database name, and admin credentials for privileges on both the GSM and the shards themselves.
}
```

Voor de .NET-versie u PowerShell gebruiken om een nieuwe Shard Map Manager te maken. Een voorbeeld is [hier](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db)beschikbaar.

## <a name="get-a-rangeshardmap-or-listshardmap"></a>Een RangeShardMap of ListShardMap

Na het maken van een shard map manager, u de RangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) of ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) met behulp van de TryGetRangeShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetrangeshardmap), [.NET),](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap)de TryGetListShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.trygetlistshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap)), of de GetShardMap ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getshardmap), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap)) methode.

```Java
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
static <T> RangeShardMap<T> createOrGetRangeShardMap(ShardMapManager shardMapManager,
            String shardMapName,
            ShardKeyType keyType) {
    // Try to get a reference to the Shard Map.
    ReferenceObjectHelper<RangeShardMap<T>> refRangeShardMap = new ReferenceObjectHelper<>(null);
    boolean isGetSuccess = shardMapManager.tryGetRangeShardMap(shardMapName, keyType, refRangeShardMap);
    RangeShardMap<T> shardMap = refRangeShardMap.argValue;

    if (isGetSuccess && shardMap != null) {
        ConsoleUtils.writeInfo("Shard Map %1$s already exists", shardMap.getName());
    }
    else {
        // The Shard Map does not exist, so create it
        try {
            shardMap = shardMapManager.createRangeShardMap(shardMapName, keyType);
        }
        catch (Exception e) {
            e.printStackTrace();
        }
        ConsoleUtils.writeInfo("Created Shard Map %1$s", shardMap.getName());
    }

    return shardMap;
}
```

```csharp
// Creates a new Range Shard Map with the specified name, or gets the Range Shard Map if it already exists.
public static RangeShardMap<T> CreateOrGetRangeShardMap<T>(ShardMapManager shardMapManager, string shardMapName)
{
    // Try to get a reference to the Shard Map.
    RangeShardMap<T> shardMap;
    bool shardMapExists = shardMapManager.TryGetRangeShardMap(shardMapName, out shardMap);

    if (shardMapExists)
    {
        ConsoleUtils.WriteInfo("Shard Map {0} already exists", shardMap.Name);
    }
    else
    {
        // The Shard Map does not exist, so create it
        shardMap = shardMapManager.CreateRangeShardMap<T>(shardMapName);
        ConsoleUtils.WriteInfo("Created Shard Map {0}", shardMap.Name);
    }

    return shardMap;
}
```

### <a name="shard-map-administration-credentials"></a>Shard map beheer referenties

Toepassingen die shardkaarten beheren en manipuleren, verschillen van toepassingen die de shardkaarten gebruiken om verbindingen te routeren.

Om shardkaarten te beheren (shards toevoegen of wijzigen, shardkaarten, shardtoewijzingen, enz.) moet u de **ShardMapManager** instantiëren met **referenties die lees-/schrijfrechten hebben op zowel de GSM-database als op elke database die als een scherf dient.** De referenties moeten het mogelijk maken voor schrijfbewerkingen tegen de tabellen in zowel de GSM en LSM als shard kaart informatie wordt ingevoerd of gewijzigd, evenals voor het maken van LSM tabellen op nieuwe scherven.  

Zie [Referenties die worden gebruikt om toegang te krijgen tot de clientbibliotheek van de Elastic Database.](sql-database-elastic-scale-manage-credentials.md)

### <a name="only-metadata-affected"></a>Alleen metagegevens getroffen

Methoden die worden gebruikt voor het vullen of wijzigen van de **ShardMapManager-gegevens,** wijzigen de gebruikersgegevens die in de shards zelf zijn opgeslagen, niet. Methoden zoals **CreateShard**, **DeleteShard**, **UpdateMapping,** enz. Ze verwijderen, voegen of wijzigen geen gebruikersgegevens in de shards. In plaats daarvan zijn deze methoden ontworpen om te worden gebruikt in combinatie met afzonderlijke bewerkingen die u uitvoert om werkelijke databases te maken of te verwijderen, of die rijen van de ene scherf naar de andere verplaatsen om een verharde omgeving opnieuw in evenwicht te brengen.  (De **split-merge** tool opgenomen met elastische database tools maakt gebruik van deze API's samen met het orkestreren van de werkelijke gegevensbeweging tussen scherven.) Zie [Schalen met het gereedschap Gesplitstsamenvoegen van elastic database](sql-database-elastic-scale-overview-split-and-merge.md).

## <a name="data-dependent-routing"></a>Gegevensafhankelijke routering

De shardmapmanager wordt gebruikt in toepassingen waarvoor databaseverbindingen nodig zijn om de app-specifieke gegevensbewerkingen uit te voeren. Deze verbindingen moeten worden gekoppeld aan de juiste database. Dit wordt **gegevensafhankelijke routering genoemd.** Voor deze toepassingen u een object voor shardmapbeheer uit de fabriek instantiëren met referenties die alleen-lezen toegang hebben in de GSM-database. Individuele aanvragen voor latere verbindingen leveren referenties die nodig zijn om verbinding te maken met de juiste sharddatabase.

Houd er rekening mee dat deze toepassingen (met **shardmapbeheer** geopend met alleen-lezen referenties) geen wijzigingen kunnen aanbrengen in de kaarten of toewijzingen. Maak voor die behoeften beheerspecifieke toepassingen of PowerShell-scripts die eerder besproken referenties met een hoger bevoorrecht niveau leveren. Zie [Referenties die worden gebruikt om toegang te krijgen tot de clientbibliotheek van de Elastic Database.](sql-database-elastic-scale-manage-credentials.md)

Zie [Routering voor gegevensafhankelijke routering](sql-database-elastic-scale-data-dependent-routing.md)voor meer informatie.

## <a name="modifying-a-shard-map"></a>Een shardkaart wijzigen

Een shardkaart kan op verschillende manieren worden gewijzigd. Alle volgende methoden wijzigen de metagegevens die de shards en hun toewijzingen beschrijven, maar ze wijzigen gegevens niet fysiek binnen de scherven, noch maken of verwijderen ze de werkelijke databases.  Sommige bewerkingen op de onderstaande shardkaart moeten mogelijk worden gecoördineerd met administratieve acties die gegevens fysiek verplaatsen of databases toevoegen en verwijderen die als shards dienen.

Deze methoden werken samen als de bouwstenen die beschikbaar zijn voor het wijzigen van de algehele verdeling van gegevens in uw geshard databaseomgeving.  

* Shards toevoegen of verwijderen: gebruik **CreateShard** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.createshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard)) en **DeleteShard** ([Java](https://docs.microsoft.com/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap.deleteshard), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.deleteshard)) van de klasse shardmap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.shardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap)
  
    De server en database die de doelshard weergeven, moeten al bestaan om deze bewerkingen uit te voeren. Deze methoden hebben geen invloed op de databases zelf, alleen op metadata in de shardmap.
* Punten of bereiken maken of verwijderen die zijn toegewezen aan de shards: gebruik **CreateRangeMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.createrangemapping), [.NET](https://docs.microsoft.com/previous-versions/azure/dn841993(v=azure.100))), **DeleteMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.deletemapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) van de klasse RangeShardMapping ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)en **CreatePointMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap.createpointmapping), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)) van de klasse ListShardMap ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.listshardmap), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.listshardmap-1)
  
    Veel verschillende punten of bereiken kunnen worden toegewezen aan dezelfde shard. Deze methoden hebben alleen invloed op metagegevens - ze hebben geen invloed op gegevens die al aanwezig kunnen zijn in scherven. Als gegevens uit de database moeten worden verwijderd om consistent te zijn met **deletemapping-bewerkingen,** voert u deze bewerkingen afzonderlijk uit, maar in combinatie met het gebruik van deze methoden.  
* Bestaande bereiken splitsen in twee of aangrenzende bereiken samenvoegen tot één: **SplitMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.splitmapping), [.NET)](https://msdn.microsoft.com/library/azure/dn824205.aspx)en **MergeMappings** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.mergemappings), [.NET )](https://msdn.microsoft.com/library/azure/dn824201.aspx)gebruiken.  
  
    Houd er rekening mee dat gesplitste en samenvoegbewerkingen **de shard waaraan de hoofdwaarden zijn toegewezen, niet wijzigen.** Een splitsing breekt een bestaand bereik in twee delen, maar laat beide als toegewezen aan dezelfde shard. Een samenvoeging werkt op twee aangrenzende bereiken die al zijn toegewezen aan dezelfde shard, samensmelten ze in een enkel bereik.  De verplaatsing van punten of bereiken zelf tussen scherven moet worden gecoördineerd door **updatemapping** te gebruiken in combinatie met de werkelijke gegevensverplaatsing.  U de **split/merge-service** die deel uitmaakt van elastische databasetools gebruiken om wijzigingen in de shardkaart te coördineren met gegevensverplaatsing, wanneer beweging nodig is.
* Afzonderlijke punten of bereiken opnieuw toewijzen (of verplaatsen) naar verschillende shards: **UpdateMapping** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.updatemapping), [.NET )](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)gebruiken.  
  
    Aangezien gegevens mogelijk van de ene shard naar de andere moeten worden verplaatst om consistent te zijn met **UpdateMapping-bewerkingen,** moet u die beweging afzonderlijk uitvoeren, maar in combinatie met het gebruik van deze methoden.

* Om kaarten online en offline te nemen: gebruik **MarkMappingOffline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingoffline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) en **MarkMappingOnline** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.map.rangeshardmap.markmappingonline), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.rangeshardmap-1)) om de online status van een toewijzing te beheren.
  
    Bepaalde bewerkingen op shardtoewijzingen zijn alleen toegestaan wanneer een toewijzing zich in een 'offline' status bevindt, waaronder **UpdateMapping** en **DeleteMapping.** Wanneer een toewijzing offline is, geeft een gegevensafhankelijke aanvraag op basis van een sleutel in die toewijzing een fout. Bovendien worden wanneer een bereik voor het eerst offline wordt gehaald, alle verbindingen met de betreffende shard automatisch worden gedood om inconsistente of onvolledige resultaten te voorkomen voor query's die zijn gericht tegen bereiken die worden gewijzigd.

Toewijzingen zijn onveranderlijke objecten in .Net.  Alle bovenstaande methoden die mappings wijzigen, maken ook alle verwijzingen naar deze methoden in uw code ongeldig. Om het eenvoudiger te maken om reeksen bewerkingen uit te voeren die de status van een toewijzing wijzigen, retourneren alle methoden die een toewijzing wijzigen een nieuwe toewijzingsverwijzing, zodat bewerkingen kunnen worden geketend. Als u bijvoorbeeld een bestaande toewijzing in shardmapsm met de sleutel 25 wilt verwijderen, u het volgende uitvoeren:

```
    sm.DeleteMapping(sm.MarkMappingOffline(sm.GetMappingForKey(25)));
```

## <a name="adding-a-shard"></a>Een scherf toevoegen

Toepassingen moeten vaak nieuwe shards toevoegen om gegevens te verwerken die worden verwacht van nieuwe sleutels of sleutelbereiken, voor een shardkaart die al bestaat. Een toepassing die is geshard door Tenant ID moet bijvoorbeeld een nieuwe shard inrichten voor een nieuwe tenant, of gegevens die maandelijks worden geshard, hebben mogelijk een nieuwe shard nodig die vóór het begin van elke nieuwe maand is ingericht.

Als het nieuwe bereik van de belangrijkste waarden nog geen deel uitmaakt van een bestaande toewijzing en er geen gegevensverplaatsing nodig is, is het eenvoudig om de nieuwe shard toe te voegen en de nieuwe sleutel of het nieuwe bereik aan die shard te koppelen. Zie [Een nieuwe shard toevoegen](sql-database-elastic-scale-add-a-shard.md)voor meer informatie over het toevoegen van nieuwe scherven.

Voor scenario's die gegevensverplaatsing vereisen, is het hulpprogramma voor gesplitste samenvoegen echter nodig om de gegevensbeweging tussen shards te orkestreren in combinatie met de benodigde shardkaartupdates. Zie [Overzicht van gesplitste samenvoeging voor](sql-database-elastic-scale-overview-split-and-merge.md) meer informatie over het gebruik van het gereedschap splitsen

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-scale-shard-map-management/listmapping.png
[2]: ./media/sql-database-elastic-scale-shard-map-management/rangemapping.png
[3]: ./media/sql-database-elastic-scale-shard-map-management/multipleonsingledb.png

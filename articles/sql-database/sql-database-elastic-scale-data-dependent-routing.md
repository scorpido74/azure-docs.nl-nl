---
title: Gegevensafhankelijke routering
description: De klasse ShardMapManager gebruiken in .NET-apps voor gegevensafhankelijke routering, een functie van gesharddatabases in Azure SQL Database
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
ms.openlocfilehash: fbdf8e316368be02ebd0c4bfd320917c20d80777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77069452"
---
# <a name="use-data-dependent-routing-to-route-a-query-to-appropriate-database"></a>Gegevensafhankelijke routering gebruiken om een query door te sturen naar de juiste database

**Gegevensafhankelijke routering** is de mogelijkheid om de gegevens in een query te gebruiken om de aanvraag door te sturen naar een geschikte database. Gegevensafhankelijke routering is een fundamenteel patroon bij het werken met gesharddatabases. De context van de aanvraag kan ook worden gebruikt om de aanvraag te routeren, vooral als de shardingssleutel geen deel uitmaakt van de query. Elke specifieke query of transactie in een toepassing met behulp van gegevensafhankelijke routering is beperkt tot toegang tot één database per aanvraag. Voor de azure SQL Database Elastic-hulpprogramma's wordt deze routering uitgevoerd met de klasse **ShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager), [.NET).](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager)

De toepassing hoeft geen verschillende verbindingstekenreeksen of DB-locaties bij te houden die zijn gekoppeld aan verschillende segmenten van gegevens in de geshard omgeving. In plaats daarvan opent de [Shard Map Manager](sql-database-elastic-scale-shard-map-management.md) verbindingen met de juiste databases wanneer dat nodig is, op basis van de gegevens in de shardkaart en de waarde van de shardingssleutel die het doel is van de aanvraag van de toepassing. De sleutel is meestal de *customer_id,* *tenant_id,* *date_key*of een andere specifieke id die een fundamentele parameter van de databaseaanvraag is.

Zie [SQL Server schalen met gegevensafhankelijke routering](https://technet.microsoft.com/library/cc966448.aspx)voor meer informatie.

## <a name="download-the-client-library"></a>De clientbibliotheek downloaden

Downloaden:

* De Java-versie van de bibliotheek, zie [Maven Central Repository](https://search.maven.org/#search%7Cga%7C1%7Celastic-db-tools).
* De .NET-versie van de bibliotheek, zie [NuGet](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/).

## <a name="using-a-shardmapmanager-in-a-data-dependent-routing-application"></a>Een ShardMapManager gebruiken in een gegevensafhankelijke routeringstoepassing

Toepassingen moeten de **ShardMapManager** instantiëren tijdens de initialisatie, met behulp van de fabrieksoproep **GetSQLShardMapManager** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanagerfactory.getsqlshardmapmanager), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager)). In dit voorbeeld worden zowel een **ShardMapManager** als een specifieke **ShardMap** die deze bevat geïnitialiseerd. In dit voorbeeld worden de methoden GetSqlShardMapManager en GetRangeShardMap[(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapmanager.shardmapmanager.getrangeshardmap), [.NET)](https://docs.microsoft.com/previous-versions/azure/dn824173(v=azure.100))weergegeven.

```Java
ShardMapManager smm = ShardMapManagerFactory.getSqlShardMapManager(connectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> rangeShardMap = smm.getRangeShardMap(Configuration.getRangeShardMapName(), ShardKeyType.Int32);
```

```csharp
ShardMapManager smm = ShardMapManagerFactory.GetSqlShardMapManager(smmConnectionString, ShardMapManagerLoadPolicy.Lazy);
RangeShardMap<int> customerShardMap = smm.GetRangeShardMap<int>("customerMap"); 
```

### <a name="use-lowest-privilege-credentials-possible-for-getting-the-shard-map"></a>Gebruik de laagste privilegereferenties mogelijk voor het verkrijgen van de shardkaart

Als een toepassing de shardkaart zelf niet manipuleert, moeten de referenties die in de fabrieksmethode worden gebruikt, alleen-lezen machtigingen hebben voor de **database met globale shardkaart.** Deze referenties verschillen meestal van referenties die worden gebruikt om verbindingen met de shardmapbeheerder te openen. Zie ook [Referenties die worden gebruikt om toegang te krijgen tot de clientbibliotheek van de Elastic Database.](sql-database-elastic-scale-manage-credentials.md)

## <a name="call-the-openconnectionforkey-method"></a>De methode OpenConnectionForKey aanroepen

De **methode ShardMap.OpenConnectionForKey** [(Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkey), [.NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey)retourneert een verbinding die klaar is voor het uitgeven van opdrachten aan de juiste database op basis van de waarde van de **sleutelparameter.** Shard informatie wordt opgeslagen in de toepassing door de **ShardMapManager**, dus deze verzoeken meestal niet betrekking hebben op een database lookup tegen de **Global Shard Map** database.

```Java
// Syntax:
public Connection openConnectionForKey(Object key, String connectionString, ConnectionOptions options)
```

```csharp
// Syntax:
public SqlConnection OpenConnectionForKey<TKey>(TKey key, string connectionString, ConnectionOptions options)
```

* De **sleutelparameter** wordt gebruikt als opzoeksleutel in de shardkaart om de juiste database voor de aanvraag te bepalen.
* De **verbindingString** wordt gebruikt om alleen de gebruikersreferenties voor de gewenste verbinding door te geven. Er is geen databasenaam of servernaam opgenomen in deze *verbindingString* omdat de methode de database en server bepaalt met behulp van de **ShardMap**.
* De **verbindingsopties** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.connectionoptions), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.connectionoptions)) moeten worden ingesteld op **ConnectionOptions.Validate** als een omgeving waar shard kaarten kunnen veranderen en rijen kunnen worden verplaatst naar andere databases als gevolg van gesplitste of samenvoegbewerkingen. Deze validatie omvat een korte query naar de lokale shardkaart op de doeldatabase (niet naar de globale shardkaart) voordat de verbinding naar de toepassing wordt geleverd.

Als de validatie tegen de lokale shardmap mislukt (als u aangeeft dat de cache onjuist is), vraagt de Shard Map Manager de globale shardkaart op om de nieuwe juiste waarde voor de opzoeking te verkrijgen, de cache bij te werken en de juiste databaseverbinding te verkrijgen en terug te geven .

Gebruik **ConnectionOptions.None** alleen wanneer wijzigingen in het in kaartbrengen van shardworden niet verwacht terwijl een toepassing online is. In dat geval kunnen de in de cache opgeslagen waarden altijd correct worden geacht en kan de extra retourvalidatieoproep naar de doeldatabase veilig worden overgeslagen. Dat vermindert het databaseverkeer. De **verbindingsopties** kunnen ook worden ingesteld via een waarde in een configuratiebestand om aan te geven of shardingswijzigingen gedurende een bepaalde periode worden verwacht of niet.  

In dit voorbeeld wordt de waarde gebruikt van een integer key **CustomerID**, met behulp van een **ShardMap-object** met de naam **customerShardMap**.  

```Java
int customerId = 12345;
int productId = 4321;
// Looks up the key in the shard map and opens a connection to the shard
try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
    // Create a simple command that will insert or update the customer information
    PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

    ps.setInt(1, productId);
    ps.setInt(2, customerId);
    ps.executeUpdate();
} catch (SQLException e) {
    e.printStackTrace();
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

// Connect to the shard for that customer ID. No need to call a SqlConnection
// constructor followed by the Open method.
using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
{
    // Execute a simple command.
    SqlCommand cmd = conn.CreateCommand();
    cmd.CommandText = @"UPDATE Sales.Customer
                        SET PersonID = @newPersonID WHERE CustomerID = @customerID";

    cmd.Parameters.AddWithValue("@customerID", customerId);cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
    cmd.ExecuteNonQuery();
}  
```

Met de methode **OpenConnectionForKey** wordt een nieuwe reeds geopende verbinding naar de juiste database geretourneerd. Verbindingen die op deze manier worden gebruikt, maken nog steeds optimaal gebruik van het bundelen van verbindingen.

De **OpenConnectionForKeyAsync-methode** ([Java](/java/api/com.microsoft.azure.elasticdb.shard.mapper.listshardmapper.openconnectionforkeyasync), [.NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkeyasync)) is ook beschikbaar als uw toepassing asynchrone programmering maakt.

## <a name="integrating-with-transient-fault-handling"></a>Integratie met tijdelijke foutafhandeling

Een aanbevolen manier bij het ontwikkelen van toepassingen voor gegevenstoegang in de cloud is ervoor te zorgen dat tijdelijke fouten door de app worden opgevangen en dat de bewerkingen meerdere keren opnieuw worden geprobeerd voordat er een fout wordt gemaakt. Tijdelijke foutafhandeling voor cloudtoepassingen wordt besproken bij Transient Fault Handling[(Java](/java/api/com.microsoft.azure.elasticdb.core.commons.transientfaulthandling), [.NET](https://docs.microsoft.com/previous-versions/msp-n-p/dn440719(v=pandp.60))).

Tijdelijke foutafhandeling kan op natuurlijke wijze naast het gegevensafhankelijke routeringspatroon bestaan. De belangrijkste vereiste is om het volledige verzoek voor gegevenstoegang opnieuw te proberen, inclusief het **gebruiksblok** dat de gegevensafhankelijke routeringsverbinding heeft verkregen. Het voorgaande voorbeeld kan als volgt worden herschreven.

### <a name="example---data-dependent-routing-with-transient-fault-handling"></a>Voorbeeld - gegevensafhankelijke routering met tijdelijke foutafhandeling

```Java
int customerId = 12345;
int productId = 4321;
try {
    SqlDatabaseUtils.getSqlRetryPolicy().executeAction(() -> {
        // Looks up the key in the shard map and opens a connection to the shard
        try (Connection conn = shardMap.openConnectionForKey(customerId, Configuration.getCredentialsConnectionString())) {
            // Create a simple command that will insert or update the customer information
            PreparedStatement ps = conn.prepareStatement("UPDATE Sales.Customer SET PersonID = ? WHERE CustomerID = ?");

            ps.setInt(1, productId);
            ps.setInt(2, customerId);
            ps.executeUpdate();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    });
} catch (Exception e) {
    throw new StoreException(e.getMessage(), e);
}
```

```csharp
int customerId = 12345;
int newPersonId = 4321;

Configuration.SqlRetryPolicy.ExecuteAction(() -> {

    // Connect to the shard for a customer ID.
    using (SqlConnection conn = customerShardMap.OpenConnectionForKey(customerId, Configuration.GetCredentialsConnectionString(), ConnectionOptions.Validate))
    {
        // Execute a simple command
        SqlCommand cmd = conn.CreateCommand();

        cmd.CommandText = @"UPDATE Sales.Customer
                            SET PersonID = @newPersonID
                            WHERE CustomerID = @customerID";

        cmd.Parameters.AddWithValue("@customerID", customerId);
        cmd.Parameters.AddWithValue("@newPersonID", newPersonId);
        cmd.ExecuteNonQuery();

        Console.WriteLine("Update completed");
    }
});
```

Pakketten die nodig zijn om tijdelijke foutafhandeling te implementeren, worden automatisch gedownload wanneer u de elastische databasesampletoepassing bouwt.

## <a name="transactional-consistency"></a>Transactionele consistentie

Transactionele eigenschappen zijn gegarandeerd voor alle bewerkingen lokaal tot een scherf. Transacties die via gegevensafhankelijke routering worden ingediend, worden bijvoorbeeld uitgevoerd binnen het bereik van de doelsscherf voor de verbinding. Op dit moment zijn er geen mogelijkheden beschikbaar voor het inschakelen van meerdere verbindingen in een transactie, en daarom zijn er geen transactionele garanties voor bewerkingen die worden uitgevoerd op verschillende shards.

## <a name="next-steps"></a>Volgende stappen

Zie De klasse RecoveryManager gebruiken om problemen met [de shardkaart op te lossen](sql-database-elastic-database-recovery-manager.md) als u een scherf wilt losmaken of een shard wilt bevestigen.

[!INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

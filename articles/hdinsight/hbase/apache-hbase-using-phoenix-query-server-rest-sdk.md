---
title: Phoenix Query Server REST SDK - Azure HDInsight
description: Installeer en gebruik de REST SDK voor de Phoenix Query Server in Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 84c2bad1004029fe61dcfc19321957a170284587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75612254"
---
# <a name="apache-phoenix-query-server-rest-sdk"></a>Apache Phoenix Query Server REST SDK

[Apache Phoenix](https://phoenix.apache.org/) is een open source, massaal parallelle relationele database laag op de top van [Apache HBase](apache-hbase-overview.md). Met Phoenix u SQL-achtige query's gebruiken met HBase via SSH-tools zoals [SQLLine.](apache-hbase-query-with-phoenix.md) Phoenix biedt ook een HTTP-server genaamd Phoenix Query Server (PQS), een thin client die twee transportmechanismen voor clientcommunicatie ondersteunt: JSON en Protocol Buffers. Protocol buffers is het standaardmechanisme en biedt efficiëntere communicatie dan JSON.

In dit artikel wordt beschreven hoe u de PQS REST SDK gebruiken om tabellen te maken, rijen afzonderlijk en in bulk te verhogen en gegevens te selecteren met SQL-instructies. De voorbeelden gebruiken het [Microsoft .NET-stuurprogramma voor Apache Phoenix Query Server](https://www.nuget.org/packages/Microsoft.Phoenix.Client). Deze SDK is gebouwd op [Apache Calcite's Avatica API's,](https://calcite.apache.org/avatica/) die uitsluitend Protocol Buffers gebruiken voor het serialisatieformaat.

Zie [Apache Calcite Avatica Protocol Buffers Reference](https://calcite.apache.org/avatica/docs/protobuf_reference.html)voor meer informatie.

## <a name="install-the-sdk"></a>De SDK installeren

Microsoft .NET driver for Apache Phoenix Query Server is provided as a NuGet package, which can be installed from the Visual Studio **NuGet Package Manager Console** with the following command: Microsoft .NET driver for Apache Phoenix Query Server is provided as a NuGet package, which can be installed from the Visual Studio NuGet Package Manager Console with the following command:

    Install-Package Microsoft.Phoenix.Client

## <a name="instantiate-new-phoenixclient-object"></a>Instantiate nieuwe PhoenixClient object

Als u de bibliotheek wilt gebruiken, `PhoenixClient` wilt u `ClusterCredentials` een `Uri` nieuw object instantiëren en het cluster met de apache hadoop-gebruikersnaam en -wachtwoord van het cluster wilt invoeren.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net/"), "USERNAME", "PASSWORD");
client = new PhoenixClient(credentials);
```

Vervang CLUSTERNAME door uw HDInsight HBase-clusternaam en gebruikersnaam en wachtwoord door de Hadoop-referenties die zijn opgegeven bij het maken van het cluster. De standaard Hadoop-gebruikersnaam is **beheerder**.

## <a name="generate-unique-connection-identifier"></a>Unieke verbindings-id genereren

Als u een of meer aanvragen naar PQS wilt verzenden, moet u een unieke verbindings-id opnemen om de aanvraag(en) aan de verbinding te koppelen.

```csharp
string connId = Guid.NewGuid().ToString();
```

Elk voorbeeld maakt eerst `OpenConnectionRequestAsync` een aanroep naar de methode en geeft de unieke verbindings-id door. Definieer `ConnectionProperties` en `RequestOptions`doorgeef deze objecten en de `ConnectionSyncRequestAsync` gegenereerde verbindings-id vervolgens aan de methode. Het object van `ConnectionSyncRequest` PQS zorgt ervoor dat zowel de client als de server een consistente weergave hebben van de database-eigenschappen.

## <a name="connectionsyncrequest-and-its-connectionproperties"></a>ConnectionSyncRequest en de bijbehorende eigenschappen

Om `ConnectionSyncRequestAsync`te bellen, `ConnectionProperties` pass in een object.

```csharp
ConnectionProperties connProperties = new ConnectionProperties
{
    HasAutoCommit = true,
    AutoCommit = true,
    HasReadOnly = true,
    ReadOnly = false,
    TransactionIsolation = 0,
    Catalog = "",
    Schema = "",
    IsDirty = true
};
await client.ConnectionSyncRequestAsync(connId, connProperties, options);
```

Hier zijn enkele eigenschappen van belang:

| Eigenschap | Beschrijving |
| -- | -- |
| AutoCommit AutoCommit | Een booleaan die `autoCommit` aangeeft of is ingeschakeld voor Phoenix-transacties. |
| ReadOnly | Een booleaan die aangeeft of de verbinding alleen-lezen is. |
| TransactieIsolatie | Een geheel getal dat het niveau van transactieisolatie aangeeft volgens de JDBC-specificatie - zie de volgende tabel.|
| Catalogus | De naam van de catalogus die u wilt gebruiken bij het ophalen van verbindingseigenschappen. |
| Schema | De naam van het schema dat u wilt gebruiken bij het ophalen van verbindingseigenschappen. |
| Isdirty | Een booleaan die aangeeft of de eigenschappen zijn gewijzigd. |

Hier zijn `TransactionIsolation` de waarden:

| Isolatiewaarde | Beschrijving |
| -- | -- |
| 0 | Transacties worden niet ondersteund. |
| 1 | Vuile leest, niet-herhaalbare leest, en fantoom leest kan optreden. |
| 2 | Vuile reads worden voorkomen, maar niet-herhaalbare reads en fantoomleest kunnen optreden. |
| 4 | Vuile reads en niet-herhaalbare reads worden voorkomen, maar fantoomreads kunnen optreden. |
| 8 | Vuile leest, niet-herhaalbare leest, en fantoom leest zijn allemaal voorkomen. |

## <a name="create-a-new-table"></a>Een nieuwe tabel maken

HBase slaat, net als elke andere RDBMS, gegevens op in tabellen. Phoenix gebruikt standaard SQL-query's om nieuwe tabellen te maken en tegelijkertijd de primaire sleutel- en kolomtypen te definiëren.

In dit voorbeeld en alle latere voorbeelden `PhoenixClient` gebruikt u het geinstantieerde object zoals gedefinieerd in [Instantiate een nieuw Object PhoenixClient](#instantiate-new-phoenixclient-object).

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// You can set certain request options, such as timeout in milliseconds:
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
CreateStatementResponse createStatementResponse = null;
OpenConnectionResponse openConnResponse = null;

try
{
    // Opening connection
    var info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Create the statement
    createStatementResponse = client.CreateStatementRequestAsync(connId, options).Result;
    
    // Create the table if it does not exist
    string sql = "CREATE TABLE IF NOT EXISTS Customers (Id varchar(20) PRIMARY KEY, FirstName varchar(50), " +
        "LastName varchar(100), StateProvince char(2), Email varchar(255), Phone varchar(15))";
    await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    Console.WriteLine($"Table \"Customers\" created.");
}
catch (Exception e)
{
    Console.WriteLine(e);
    throw;
}
finally
{
    if (createStatementResponse != null)
    {
        client.CloseStatementRequestAsync(connId, createStatementResponse.StatementId, options).Wait();
        createStatementResponse = null;
    }

    if (openConnResponse != null)
    {
        client.CloseConnectionRequestAsync(connId, options).Wait();
        openConnResponse = null;
    }
}
```

In het voorgaande voorbeeld `Customers` wordt `IF NOT EXISTS` een nieuwe tabel gemaakt met de naam met de optie. De `CreateStatementRequestAsync` oproep maakt een nieuwe instructie in de Avitica (PQS) server. Het `finally` blok sluit `CreateStatementResponse` de `OpenConnectionResponse` geretourneerde en de objecten.

## <a name="insert-data-individually"></a>Gegevens afzonderlijk invoegen

In dit voorbeeld wordt een afzonderlijke `List<string>` gegevensinsert weergegeven, waarin wordt verwezen naar een verzameling afkortingen van amerikaanse staats- en territoriumartikelen:

```csharp
var states = new List<string> { "AL", "AK", "AS", "AZ", "AR", "CA", "CO", "CT", "DE", "DC", "FM", "FL", "GA", "GU", "HI", "ID", "IL", "IN", "IA", "KS", "KY", "LA", "ME", "MH", "MD", "MA", "MI", "MN", "MS", "MO", "MT", "NE", "NV", "NH", "NJ", "NM", "NY", "NC", "ND", "MP", "OH", "OK", "OR", "PW", "PA", "PR", "RI", "SC", "SD", "TN", "TX", "UT", "VT", "VI", "VA", "WA", "WV", "WI", "WY" };
```

De kolomwaarde `StateProvince` van de tabel wordt gebruikt in een latere selectiebewerking.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = await client.PrepareRequestAsync(connId, sql, 100, options);
    statementHandle = prepareResponse.Statement;
    
    var r = new Random();

    // Insert 300 rows
    for (int i = 0; i < 300; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0,1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        Console.WriteLine("Inserting customer " + i);

        await client.ExecuteRequestAsync(statementHandle, list, long.MaxValue, true, options);
    }

    await client.CommitRequestAsync(connId, options);

    Console.WriteLine("Upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

De structuur voor het uitvoeren van een invoeginstructie is vergelijkbaar met het maken van een nieuwe tabel. Aan het einde `try` van het blok wordt de transactie expliciet vastgelegd. In dit voorbeeld wordt een invoegtransactie 300 keer herhaald. In het volgende voorbeeld wordt een efficiënter batchinvoegproces weergegeven.

## <a name="batch-insert-data"></a>Batchinvoeggegevens

De volgende code is bijna identiek aan de code voor het individueel invoegen van gegevens. In dit `UpdateBatch` voorbeeld wordt het `ExecuteBatchRequestAsync`object gebruikt in `ExecuteRequestAsync` een aanroep om, in plaats van herhaaldelijk te bellen met een voorbereide instructie.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();
options.TimeoutMillis = 300000;

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
CreateStatementResponse createStatementResponse = null;
try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);

    // Creating statement
    createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "UPSERT INTO Customers VALUES (?,?,?,?,?,?)";
    PrepareResponse prepareResponse = client.PrepareRequestAsync(connId, sql, long.MaxValue, options).Result;
    var statementHandle = prepareResponse.Statement;
    var updates = new pbc.RepeatedField<UpdateBatch>();

    var r = new Random();

    // Insert 300 rows
    for (int i = 300; i < 600; i++)
    {
        var list = new pbc.RepeatedField<TypedValue>();
        var id = new TypedValue
        {
            StringValue = "id" + i,
            Type = Rep.String
        };
        var firstName = new TypedValue
        {
            StringValue = "first" + i,
            Type = Rep.String
        };
        var lastName = new TypedValue
        {
            StringValue = "last" + i,
            Type = Rep.String
        };
        var state = new TypedValue
        {
            StringValue = states.ElementAt(r.Next(0, 49)),
            Type = Rep.String
        };
        var email = new TypedValue
        {
            StringValue = $"email{1}@junkemail.com",
            Type = Rep.String
        };
        var phone = new TypedValue
        {
            StringValue = $"555-229-341{i.ToString().Substring(0, 1)}",
            Type = Rep.String
        };
        list.Add(id);
        list.Add(firstName);
        list.Add(lastName);
        list.Add(state);
        list.Add(email);
        list.Add(phone);

        var batch = new UpdateBatch
        {
            ParameterValues = list
        };
        updates.Add(batch);

        Console.WriteLine($"Added customer {i} to batch");
    }

    var executeBatchResponse = await client.ExecuteBatchRequestAsync(connId, statementHandle.Id, updates, options);

    Console.WriteLine("Batch upserted customer data");

}
catch (Exception ex)
{

}
finally
{
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

In één testomgeving duurde het individueel invoegen van 300 nieuwe records bijna 2 minuten. Het invoegen van 300 records als batch vereiste daarentegen slechts 6 seconden.

## <a name="select-data"></a>Gegevens selecteren

In dit voorbeeld ziet u hoe u één verbinding opnieuw gebruikt om meerdere query's uit te voeren:

1. Selecteer alle records en haal de resterende records op nadat het standaardmaximum van 100 is geretourneerd.
2. Gebruik een totale keuzeinstructie voor het aantal rijen om het resultaat van de enkele scalaire op te halen.
3. Voer een selecte instructie uit die het totale aantal klanten per staat of gebied retourneert.

```csharp
string connId = Guid.NewGuid().ToString();
RequestOptions options = RequestOptions.GetGatewayDefaultOptions();

// In gateway mode, PQS requests will be https://<cluster dns name>.azurehdinsight.net/hbasephoenix<N>/
// Requests sent to hbasephoenix0/ will be forwarded to PQS on workernode0
options.AlternativeEndpoint = "hbasephoenix0/";
OpenConnectionResponse openConnResponse = null;
StatementHandle statementHandle = null;

try
{
    // Opening connection
    pbc::MapField<string, string> info = new pbc::MapField<string, string>();
    openConnResponse = await client.OpenConnectionRequestAsync(connId, info, options);
    // Syncing connection
    ConnectionProperties connProperties = new ConnectionProperties
    {
        HasAutoCommit = true,
        AutoCommit = true,
        HasReadOnly = true,
        ReadOnly = false,
        TransactionIsolation = 0,
        Catalog = "",
        Schema = "",
        IsDirty = true
    };
    await client.ConnectionSyncRequestAsync(connId, connProperties, options);
    var createStatementResponse = await client.CreateStatementRequestAsync(connId, options);

    string sql = "SELECT * FROM Customers";
    ExecuteResponse executeResponse = await client.PrepareAndExecuteRequestAsync(connId, sql, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> rows = executeResponse.Results[0].FirstFrame.Rows;
    // Loop through all of the returned rows and display the first two columns
    for (int i = 0; i < rows.Count; i++)
    {
        Row row = rows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + " " + row.Value[1].ScalarValue.StringValue);
    }
    
    // 100 is hard-coded on the server side as the default firstframe size
    // FetchRequestAsync is called to get any remaining rows
    Console.WriteLine("");
    Console.WriteLine($"Number of rows: {rows.Count}");

    // Fetch remaining rows, offset is not used, simply set to 0
    // When FetchResponse.Frame.Done is true, all rows were fetched
    FetchResponse fetchResponse = await client.FetchRequestAsync(connId, createStatementResponse.StatementId, 0, int.MaxValue, options);
    Console.WriteLine($"Frame row count: {fetchResponse.Frame.Rows.Count}");
    Console.WriteLine($"Fetch response is done: {fetchResponse.Frame.Done}");
    Console.WriteLine("");

    // Running query 2
    string sql2 = "select count(*) from Customers";
    ExecuteResponse countResponse = await client.PrepareAndExecuteRequestAsync(connId, sql2, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);
    long count = countResponse.Results[0].FirstFrame.Rows[0].Value[0].ScalarValue.NumberValue;

    Console.WriteLine($"Total customer records: {count}");
    Console.WriteLine("");

    // Running query 3
    string sql3 = "select StateProvince, count(*) as Number from Customers group by StateProvince order by Number desc";
    ExecuteResponse groupByResponse = await client.PrepareAndExecuteRequestAsync(connId, sql3, createStatementResponse.StatementId, long.MaxValue, int.MaxValue, options);

    pbc::RepeatedField<Row> stateRows = groupByResponse.Results[0].FirstFrame.Rows;
    for (int i = 0; i < stateRows.Count; i++)
    {
        Row row = stateRows[i];
        Console.WriteLine(row.Value[0].ScalarValue.StringValue + ": " + row.Value[1].ScalarValue.NumberValue);
    }
}
catch (Exception ex)
{

}
finally
{
    if (statementHandle != null)
    {
        await client.CloseStatementRequestAsync(connId, statementHandle.Id, options);
        statementHandle = null;
    }
    if (openConnResponse != null)
    {
        await client.CloseConnectionRequestAsync(connId, options);
        openConnResponse = null;
    }
}
```

De output `select` van de verklaringen moet het volgende resultaat zijn:

```
id0 first0
id1 first1
id10 first10
id100 first100
id101 first101
id102 first102
. . .
id185 first185
id186 first186
id187 first187
id188 first188

Number of rows: 100
Frame row count: 500
Fetch response is done: True

Total customer records: 600

NJ: 21
CA: 19
GU: 17
NC: 16
IN: 16
MA: 16
AZ: 16
ME: 16
IL: 15
OR: 15
. . . 
MO: 10
HI: 10
GA: 10
DC: 9
NM: 9
MD: 9
MP: 9
SC: 7
AR: 7
MH: 6
FM: 5
```

## <a name="next-steps"></a>Volgende stappen

* [Apache Phoenix in HDInsight](../hdinsight-phoenix-in-hdinsight.md)
* [De Apache HBase REST SDK gebruiken](apache-hbase-rest-sdk.md)

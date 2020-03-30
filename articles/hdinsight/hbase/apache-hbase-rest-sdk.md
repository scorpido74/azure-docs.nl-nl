---
title: De HBase .NET SDK gebruiken - Azure HDInsight
description: Gebruik de HBase .NET SDK om tabellen te maken en te verwijderen en om gegevens te lezen en te schrijven.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/02/2019
ms.openlocfilehash: eba7d7ad009b2ef0442a916983489489eb5cceb8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74806657"
---
# <a name="use-the-net-sdk-for-apache-hbase"></a>Gebruik de .NET SDK voor Apache HBase

[Apache HBase](apache-hbase-overview.md) biedt twee primaire keuzes om met uw gegevens te werken: [Apache Hive-query's en roept naar de RESTful API van HBase.](apache-hbase-tutorial-get-started-linux.md) U rechtstreeks met de `curl` REST API werken met de opdracht of een soortgelijk hulpprogramma.

Voor C#- en .NET-toepassingen biedt de [Microsoft HBase REST-clientbibliotheek voor .NET](https://www.nuget.org/packages/Microsoft.HBase.Client/) een clientbibliotheek bovenop de HBase REST API.

## <a name="install-the-sdk"></a>De SDK installeren

De HBase .NET SDK wordt geleverd als een NuGet-pakket, dat kan worden geïnstalleerd vanuit de Visual Studio **NuGet Package Manager Console** met de volgende opdracht:

    Install-Package Microsoft.HBase.Client

## <a name="instantiate-a-new-hbaseclient-object"></a>Een nieuw HBaseClient-object instantiëren

Als u de SDK wilt gebruiken, wilt u een nieuw `HBaseClient` object instantiëren, waarbij `ClusterCredentials` u de binnenkomende `Uri` cluster en de hadoop-gebruikersnaam en -wachtwoord wilt invoeren.

```csharp
var credentials = new ClusterCredentials(new Uri("https://CLUSTERNAME.azurehdinsight.net"), "USERNAME", "PASSWORD");
client = new HBaseClient(credentials);
```

Vervang CLUSTERNAME door uw HDInsight HBase-clusternaam en gebruikersnaam en wachtwoord door de Apache Hadoop-referenties die zijn opgegeven bij het maken van het cluster. De standaard Hadoop-gebruikersnaam is **beheerder**.

## <a name="create-a-new-table"></a>Een nieuwe tabel maken

HBase slaat gegevens op in tabellen. Een tabel bestaat uit een *Rowkey,* de primaire sleutel en een of meer groepen kolommen genaamd *kolomfamilies*. De gegevens in elke tabel worden horizontaal verdeeld door een Rowkey-bereik in *regio's*. Elke regio heeft een start- en eindtoets. Een tabel kan een of meer regio's hebben. Naarmate de gegevens in de tabel groeien, splitst HBase grote regio's op in kleinere regio's. Regio's worden opgeslagen in *regioservers,* waar één regioserver meerdere regio's kan opslaan.

De gegevens worden fysiek opgeslagen in *HFiles*. Eén HFile bevat gegevens voor één tabel, één regio en één kolomfamilie. Rijen in HFile worden opgeslagen gesorteerd op Rowkey. Elke HFile heeft een *B + Tree* index voor het snel ophalen van de rijen.

Als u een nieuwe `TableSchema` tabel wilt maken, geeft u een en kolommen op. De volgende code controleert of de tabel 'RestSDKTable' al bestaat - zo niet, de tabel wordt gemaakt.

```csharp
if (!client.ListTablesAsync().Result.name.Contains("RestSDKTable"))
{
    // Create the table
    var newTableSchema = new TableSchema {name = "RestSDKTable" };
    newTableSchema.columns.Add(new ColumnSchema() {name = "t1"});
    newTableSchema.columns.Add(new ColumnSchema() {name = "t2"});

    await client.CreateTableAsync(newTableSchema);
}
```

Deze nieuwe tabel heeft tweekolomsfamilies, t1 en t2. Aangezien kolomfamilies afzonderlijk in verschillende HFiles worden opgeslagen, is het zinvol om een aparte kolomfamilie te hebben voor veelgevraagde gegevens. In het volgende voorbeeld [Gegevens invoegen](#insert-data) worden kolommen toegevoegd aan de kolomfamilie T1.

## <a name="delete-a-table"></a>Een tabel verwijderen

Een tabel verwijderen:

```csharp
await client.DeleteTableAsync("RestSDKTable");
```

## <a name="insert-data"></a>Gegevens invoegen

Als u gegevens wilt invoegen, geeft u een unieke rijsleutel op als de rij-id. Alle gegevens worden `byte[]` opgeslagen in een array. De volgende code definieert `director`en `release_date` voegt de `title`, en kolommen toe aan de t1-kolomfamilie, omdat deze kolommen het vaakst worden geopend. De `description` `tagline` kolommen en kolommen worden toegevoegd aan de kolomfamilie T2. U uw gegevens naar behoefte verdelen in kolomfamilies.

```csharp
var key = "fifth_element";
var row = new CellSet.Row { key = Encoding.UTF8.GetBytes(key) };
var value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:title"),
    data = Encoding.UTF8.GetBytes("The Fifth Element")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:director"),
    data = Encoding.UTF8.GetBytes("Luc Besson")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t1:release_date"),
    data = Encoding.UTF8.GetBytes("1997")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:description"),
    data = Encoding.UTF8.GetBytes("In the colorful future, a cab driver unwittingly becomes the central figure in the search for a legendary cosmic weapon to keep Evil and Mr Zorg at bay.")
};
row.values.Add(value);
value = new Cell
{
    column = Encoding.UTF8.GetBytes("t2:tagline"),
    data = Encoding.UTF8.GetBytes("The Fifth is life")
};
row.values.Add(value);

var set = new CellSet();
set.rows.Add(row);

await client.StoreCellsAsync("RestSDKTable", set);
```

HBase implementeert [Cloud BigTable,](https://cloud.google.com/bigtable/)zodat de gegevensindeling er als de volgende afbeelding uitziet:

![Apache HBase-voorbeeldgegevensuitvoer](./media/apache-hbase-rest-sdk/hdinsight-table-roles.png)

## <a name="select-data"></a>Gegevens selecteren

Als u gegevens uit een HBase-tabel wilt lezen, geeft u de tabelnaam en de rijsleutel door aan de `GetCellsAsync` methode om de `CellSet`.

```csharp
var key = "fifth_element";

var cells = await client.GetCellsAsync("RestSDKTable", key);
// Get the first value from the row.
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values[0].data));
// Get the value for t1:title
Console.WriteLine(Encoding.UTF8.GetString(cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:title").data));
// With the previous insert, it should yield: "The Fifth Element"
```

In dit geval retourneert de code alleen de eerste overeenkomende rij, omdat er slechts één rij voor een unieke sleutel moet zijn. De geretourneerde waarde `string` wordt `byte[]` gewijzigd in opmaak van de array. U de waarde ook converteren naar andere typen, zoals een geheel getal voor de releasedatum van de film:

```csharp
var releaseDateField = cells.rows[0].values
    .Find(c => Encoding.UTF8.GetString(c.column) == "t1:release_date");
int releaseDate = 0;

if (releaseDateField != null)
{
    releaseDate = Convert.ToInt32(Encoding.UTF8.GetString(releaseDateField.data));
}
Console.WriteLine(releaseDate);
// Should return 1997
```

## <a name="scan-over-rows"></a>Scannen over rijen

HBase `scan` gebruikt om een of meer rijen op te halen. In dit voorbeeld worden meerdere rijen in batches van 10 aanvragen en gegevens opgevraagd waarvan de hoofdwaarden tussen 25 en 35 liggen. Nadat u alle rijen hebt opgehaald, verwijdert u de scanner om bronnen op te schonen.

```csharp
var tableName = "mytablename";

// Assume the table has integer keys and we want data between keys 25 and 35
var scanSettings = new Scanner()
{
    batch = 10,
    startRow = BitConverter.GetBytes(25),
    endRow = BitConverter.GetBytes(35)
};
RequestOptions scanOptions = RequestOptions.GetDefaultOptions();
scanOptions.AlternativeEndpoint = "hbaserest0/";
ScannerInformation scannerInfo = null;
try
{
    scannerInfo = await client.CreateScannerAsync(tableName, scanSettings, scanOptions);
    CellSet next = null;
    while ((next = client.ScannerGetNextAsync(scannerInfo, scanOptions).Result) != null)
    {
        foreach (var row in next.rows)
        {
            // ... read the rows
        }
    }
}
finally
{
    if (scannerInfo != null)
    {
        await client.DeleteScannerAsync(tableName, scannerInfo, scanOptions);
    }
}
```

## <a name="next-steps"></a>Volgende stappen

* [Aan de slag met een voorbeeld van Apache HBase in HDInsight](apache-hbase-tutorial-get-started-linux.md)
* Bouw een end-to-end applicatie met [Analyze real-time Twitter sentiment met Apache HBase](../hdinsight-hbase-analyze-twitter-sentiment.md)

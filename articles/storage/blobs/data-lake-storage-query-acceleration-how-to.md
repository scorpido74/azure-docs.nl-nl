---
title: Gegevens filteren met behulp van Azure Data Lake Storage-query versnelling (preview) | Microsoft Docs
description: Gebruik query Acceleration (preview) om een subset van gegevens op te halen uit uw opslag account.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: cc9235f07c0829abfb8be42e83d05d8428bc1806
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84465861"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Gegevens filteren met behulp van Azure Data Lake Storage-query versnelling (preview-versie)

In dit artikel leest u hoe u query Acceleration (preview) gebruikt om een subset van gegevens uit uw opslag account op te halen. 

Query Acceleration (preview) is een nieuwe mogelijkheid voor Azure Data Lake Storage waarmee toepassingen en analyse raamwerken de gegevens verwerking aanzienlijk kunnen optimaliseren door alleen de gegevens op te halen die nodig zijn voor het uitvoeren van een bepaalde bewerking. Zie [Azure data Lake Storage-query versnelling (preview-versie)](data-lake-storage-query-acceleration.md)voor meer informatie.

> [!NOTE]
> De functie voor het versnellen van query's bevindt zich in de open bare preview en is beschikbaar in de regio's Canada-centraal en Frankrijk-centraal. Zie het artikel [bekende problemen](data-lake-storage-known-issues.md) voor een overzicht van de beperkingen. Zie [dit formulier](https://aka.ms/adls/qa-preview-signup)als u zich wilt inschrijven voor de preview-versie.  

## <a name="prerequisites"></a>Vereisten

### <a name="net"></a>[.NET](#tab/dotnet)

- U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Een **v2-** opslag account voor algemeen gebruik. Zie [een opslag account maken](../common/storage-quickstart-create-account.md).

- [.NET-SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als u nog geen abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

- Een **v2-** opslag account voor algemeen gebruik. Zie [een opslag account maken](../common/storage-quickstart-create-account.md).

- JDK-versie 8 of hoger van [Java Development Kit](/java/azure/jdk/?view=azure-java-stable) .

- [Apache Maven](https://maven.apache.org/download.cgi). 

  > [!NOTE] 
  > In dit artikel wordt ervan uitgegaan dat u een Java-project hebt gemaakt met behulp van Apache Maven. Zie [instellen](storage-quickstart-blobs-java.md#setting-up)voor een voor beeld van het maken van een project met Apache Maven.
  
---

## <a name="install-packages"></a>Pakketten installeren 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Down load de pakketten voor query versnelling. U kunt een gecomprimeerd zip-bestand dat deze pakketten bevat, ophalen met behulp van deze koppeling: [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net) . 

2. Pak de inhoud van dit bestand uit in de projectmap.

3. Open het project bestand (*. csproj*) in een tekst editor en voeg deze pakket verwijzingen in het- \<Project\> element toe.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. Herstel de preview-SDK-pakketten. Met deze voorbeeld opdracht worden de preview-SDK-pakketten hersteld met behulp van de `dotnet restore` opdracht. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Herstel alle andere afhankelijkheden vanuit de open bare NuGet-opslag plaats.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Maak een map in de hoofdmap van het project. De hoofdmap is de map die het **pom.xml** -bestand bevat.

   > [!NOTE]
   > In de voor beelden in dit artikel wordt ervan uitgegaan dat de naam van de map **lib**is.

2. Down load de pakketten voor query versnelling. U kunt een gecomprimeerd zip-bestand dat deze pakketten bevat, ophalen met behulp van deze koppeling: [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java) . 

3. Pak de bestanden in dit zip-bestand uit naar de map die u hebt gemaakt. In ons voor beeld heet deze map **lib**. 

4. Open het bestand *pom.xml* in uw teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep met afhankelijkheden. 

   ```xml
   <!-- Request static dependencies from Maven -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-core</artifactId>
       <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-core-http-netty</artifactId>
      <version>1.3.0</version>
   </dependency>
   <dependency>
      <groupId>org.apache.avro</groupId>
      <artifactId>avro</artifactId>
      <version>1.9.2</version>
   </dependency>
   <dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-csv</artifactId>
    <version>1.8</version>
   </dependency>
   <!-- Local dependencies -->
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-blob</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-blob-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-common</artifactId>
       <version>12.5.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-common-12.5.0-beta.1.jar</systemPath>
   </dependency>
   <dependency>
       <groupId>com.azure</groupId>
       <artifactId>azure-storage-quickquery</artifactId>
       <version>12.0.0-beta.1</version>
       <scope>system</scope>
       <systemPath>${project.basedir}/lib/azure-storage-quickquery-12.0.0-beta.1.jar</systemPath>
   </dependency>
   ```

---

## <a name="add-statements"></a>Instructies toevoegen


### <a name="net"></a>[.NET](#tab/dotnet)

Voeg deze `using` instructies toe aan de bovenkant van het code bestand.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Met query versnelling worden gegevens uit CSV-en JSON-indeling opgehaald. Zorg er daarom voor dat u met instructies toevoegt voor de CSV-of JSON-bibliotheken die u wilt gebruiken. De voor beelden die in dit artikel worden weer gegeven, parseren een CSV-bestand met behulp van de [CsvHelper](https://www.nuget.org/packages/CsvHelper/) -bibliotheek die beschikbaar is op NuGet. Daarom voegen we deze `using` instructies toe aan het begin van het code bestand.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Voor het compileren van voor beelden die in dit artikel worden weer gegeven, moet u ook deze `using` instructies toevoegen.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Voeg deze `import` instructies toe aan de bovenkant van het code bestand.

```java
import com.azure.storage.blob.*;
import com.azure.storage.blob.models.*;
import com.azure.storage.common.*;
import com.azure.storage.quickquery.*;
import com.azure.storage.quickquery.models.*;
import java.io.*;
import org.apache.commons.csv.*;
```

---

## <a name="retrieve-data-by-using-a-filter"></a>Gegevens ophalen met behulp van een filter

U kunt SQL gebruiken om de rij filter predikaten en kolom projectie op te geven in een aanvraag voor query versnelling. Met de volgende code wordt een query uitgevoerd op een CSV-bestand in de opslag en worden alle rijen met gegevens geretourneerd, waarbij de derde kolom overeenkomt met de waarde `Hemingway, Ernest` . 

- In de SQL-query wordt het sleutel woord `BlobStorage` gebruikt voor het aanduiden van het bestand dat wordt opgevraagd.

- Kolom verwijzingen worden opgegeven `_N` waar de eerste kolom zich bevindt `_1` . Als het bron bestand een veldnamenrij bevat, kunt u naar kolommen verwijzen met de naam die is opgegeven in de rij met koppen. 

### <a name="net"></a>[.NET](#tab/dotnet)

De asynchrone methode `BlobQuickQueryClient.QueryAsync` verzendt de query naar de API voor query versnelling, waarna de resultaten worden teruggestuurd naar de toepassing als een [Stream](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8) -object.

```cs
static async Task QueryHemingway(BlockBlobClient blob)
{
    string query = @"SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    await DumpQueryCsv(blob, query, false);
}

private static async Task DumpQueryCsv(BlockBlobClient blob, string query, bool headers)
{
    try
    {
        using (var reader = new StreamReader((await blob.GetQuickQueryClient().QueryAsync(query,
                new CsvTextConfiguration() { HasHeaders = headers }, 
                new CsvTextConfiguration() { HasHeaders = false }, 
                new ErrorHandler(),
                new BlobRequestConditions(), 
                new ProgressHandler(),
                CancellationToken.None)).Value.Content))
        {
            using (var parser = new CsvReader(reader, new CsvConfiguration(CultureInfo.CurrentCulture) 
            { HasHeaderRecord = false }))
            {
                while (await parser.ReadAsync())
                {
                    parser.Context.Record.All(cell =>
                    {
                        Console.Out.Write(cell + "  ");
                        return true;
                    });
                    Console.Out.WriteLine();
                }
            }
        }
    }
    catch (Exception ex)
    {
        Console.Error.WriteLine("Exception: " + ex.ToString());
    }
}

class ErrorHandler : IBlobQueryErrorReceiver
{
    public void ReportError(BlobQueryError err)
    {
        Console.Error.WriteLine($"Error: {err.Name}:{ err.Description }");
    }
}

class ProgressHandler : IProgress<long>
{
    public void Report(long value)
    {
        Console.Error.WriteLine("Bytes scanned: " + value.ToString());
    }
}

```

### <a name="java"></a>[Java](#tab/java)

De `BlobQuickQueryClient.openInputStream()` -methode verzendt de query naar de API voor query versnelling, waarna de resultaten worden teruggestuurd naar de toepassing als een `InputStream` object dat kan worden gelezen zoals elk ander InputStream-object.

```java
static void QueryHemingway(BlobClient blobClient) {
    String expression = "SELECT * FROM BlobStorage WHERE _3 = 'Hemingway, Ernest'";
    DumpQueryCsv(blobClient, expression, false);
}

static void DumpQueryCsv(BlobClient blobClient, String query, Boolean headers) {
    try {
    
        BlobQuickQueryDelimitedSerialization input = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(headers)
            .setFieldQuote('\0')
            .setEscapeChar('\\');

        BlobQuickQueryDelimitedSerialization output = new BlobQuickQueryDelimitedSerialization()
            .setRecordSeparator('\n')
            .setColumnSeparator(',')
            .setHeadersPresent(false)
            .setFieldQuote('\0')
            .setEscapeChar('\n');
                
        BlobRequestConditions requestConditions = null;
        /* ErrorReceiver determines what to do on errors. */
        ErrorReceiver<BlobQuickQueryError> errorReceiver = System.out::println;

        /* ProgressReceiver details how to log progress*/
        com.azure.storage.common.ProgressReceiver progressReceiver = System.out::println;
    
        /* Create a query acceleration client to the blob. */
        BlobQuickQueryClient qqClient = new BlobQuickQueryClientBuilder(blobClient)
            .buildClient();
        /* Open the query input stream. */
        InputStream stream = qqClient.openInputStream(query, input, output, requestConditions, errorReceiver, progressReceiver);
            
        try (BufferedReader reader = new BufferedReader(new InputStreamReader(stream))) {
            /* Read from stream like you normally would. */
            for (CSVRecord record : CSVParser.parse(reader, CSVFormat.EXCEL)) {
                System.out.println(record.toString());
            }
        }
    } catch (Exception e) {
        System.err.println("Exception: " + e.toString());
    }
}
```

---

## <a name="retrieve-specific-columns"></a>Specifieke kolommen ophalen

U kunt de resultaten in een subset van kolommen bereiken. Op die manier haalt u alleen de kolommen op die nodig zijn om een bepaalde berekening uit te voeren. Dit verbetert de prestaties van de toepassing en vermindert de kosten omdat er minder gegevens via het netwerk worden overgedragen. 

Met deze code wordt alleen de `PublicationYear` kolom voor alle boeken in de gegevensset opgehaald. De gegevens uit de rij met koppen in het bron bestand worden ook gebruikt om te verwijzen naar kolommen in de query.


### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryPublishDates(BlockBlobClient blob)
{
    string query = @"SELECT PublicationYear FROM BlobStorage";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryPublishDates(BlobClient blobClient)
{
    String expression = "SELECT PublicationYear FROM BlobStorage";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

De volgende code combineert rijen en kolom projecties in dezelfde query. 

### <a name="net"></a>[.NET](#tab/dotnet)

```cs
static async Task QueryMysteryBooks(BlockBlobClient blob)
{
    string query = @"SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    await DumpQueryCsv(blob, query, true);
}
```

### <a name="java"></a>[Java](#tab/java)

```java
static void QueryMysteryBooks(BlobClient blobClient)
{
    String expression = "SELECT BibNum, Title, Author, ISBN, Publisher FROM BlobStorage WHERE Subjects LIKE '%Mystery%'";
    DumpQueryCsv(blobClient, expression, true);
}
```

---

## <a name="next-steps"></a>Volgende stappen

- [Inschrijvings formulier voor query versnelling](https://aka.ms/adls/qa-preview-signup)    
- [Azure Data Lake Storage-query versnelling (preview-versie)](data-lake-storage-query-acceleration.md)
- [Naslag informatie over SQL-taal voor query versnelling (preview-versie)](query-acceleration-sql-reference.md)

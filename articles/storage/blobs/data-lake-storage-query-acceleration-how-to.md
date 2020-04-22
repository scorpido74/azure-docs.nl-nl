---
title: Gegevens filteren met azure data lake storage-queryversnelling (voorbeeld) | Microsoft Documenten
description: Gebruik queryversnelling (voorbeeld) om een subset van gegevens op te halen uit uw opslagaccount.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: jamsbak
ms.openlocfilehash: ae3dfc7681ef0d8ce3fcf679bddbd0ff195f4e3b
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81771844"
---
# <a name="filter-data-by-using-azure-data-lake-storage-query-acceleration-preview"></a>Gegevens filteren met azure data lake storage-queryversnelling (voorbeeld)

In dit artikel ziet u hoe u queryversnelling (voorbeeld) gebruiken om een subset van gegevens uit uw opslagaccount op te halen. 

Queryversnelling (preview) is een nieuwe mogelijkheid voor Azure Data Lake Storage waarmee toepassingen en analyseframeworks de gegevensverwerking drastisch kunnen optimaliseren door alleen de gegevens op te halen die ze nodig hebben om een bepaalde bewerking uit te voeren. Zie [Azure Data Lake Storage Query Acceleration (preview) voor](data-lake-storage-query-acceleration.md)meer informatie.

> [!NOTE]
> De functie queryversnelling bevindt zich in een openbare preview en is beschikbaar in de regio's Canada Centraal en France Central. Zie het artikel [Bekende problemen](data-lake-storage-known-issues.md) om beperkingen te bekijken. Zie [dit formulier](https://aka.ms/adls/qa-preview-signup)om u in te schrijven voor het voorbeeld.  

## <a name="prerequisites"></a>Vereisten

### <a name="net"></a>[.NET](#tab/dotnet)

- U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als je nog geen abonnement hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

- Een **v2-opslagaccount voor algemene doeleinden.** zie [Een opslagaccount maken](../common/storage-quickstart-create-account.md).

- [.NET SDK](https://dotnet.microsoft.com/download). 

### <a name="java"></a>[Java](#tab/java)

- U hebt een Azure-abonnement nodig voor toegang tot Azure Storage. Als je nog geen abonnement hebt, maak je een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat je begint.

- Een **v2-opslagaccount voor algemene doeleinden.** zie [Een opslagaccount maken](../common/storage-quickstart-create-account.md).

- [Java Development Kit (JDK)](/java/azure/jdk/?view=azure-java-stable) versie 8 of hoger.

- [Apache Maven.](https://maven.apache.org/download.cgi) 

  > [!NOTE] 
  > In dit artikel wordt ervan uitgegaan dat u een Java-project hebt gemaakt met Apache Maven. Zie Instellen voor een voorbeeld van het maken van een project door Apache Maven te [gebruiken.](storage-quickstart-blobs-java.md#setting-up)
  
---

## <a name="install-packages"></a>Pakketten installeren 

### <a name="net"></a>[.NET](#tab/dotnet)

1. Download de queryversnellingspakketten. U een gecomprimeerd .zip-bestand met deze pakketten [https://aka.ms/adls/qqsdk/.net](https://aka.ms/adls/qqsdk/.net)verkrijgen via deze koppeling:. 

2. Haal de inhoud van dit bestand naar uw projectmap.

3. Open uw projectbestand (*.csproj*) in een teksteditor en \<\> voeg deze pakketverwijzingen toe in het element Project.

   ```xml
   <ItemGroup>
       <PackageReference Include="Azure.Storage.Blobs" Version="12.5.0-preview.1" />
       <PackageReference Include="Azure.Storage.Common" Version="12.4.0-preview.1" />
       <PackageReference Include="Azure.Storage.QuickQuery" Version="12.0.0-preview.1" />
   </ItemGroup>
   ```

4. De preview SDK-pakketten herstellen. Met deze voorbeeldopdracht wordt de preview-SDK-pakketten hersteld met behulp van de `dotnet restore` opdracht. 

   ```console
   dotnet restore --source C:\Users\contoso\myProject
   ```

5. Herstel alle andere afhankelijkheden uit de openbare NuGet-repository.

   ```console
   dotnet restore
   ```

### <a name="java"></a>[Java](#tab/java)

1. Maak directory in de hoofdmap van uw project. De hoofdmap is de map die het **pom.xml-bestand** bevat.

   > [!NOTE]
   > De voorbeelden in dit artikel gaan ervan uit dat de naam van de directory **lib**is.

2. Download de queryversnellingspakketten. U een gecomprimeerd .zip-bestand met deze pakketten [https://aka.ms/adls/qqsdk/java](https://aka.ms/adls/qqsdk/java)verkrijgen via deze koppeling:. 

3. Haal de bestanden in dit zip-bestand uit naar de map die u hebt gemaakt. In ons voorbeeld wordt die map **lib**genoemd. 

4. Open het *pom.xml-bestand* in uw teksteditor. Voeg de volgende afhankelijkheidselementen toe aan de groep afhankelijkheden. 

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

Voeg `using` deze instructies toe aan de bovenkant van uw codebestand.

```csharp
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using Azure.Storage.QuickQuery;
using Azure.Storage.QuickQuery.Models;
```

Queryversnelling haalt CSV- en Json-opgemaakte gegevens op. Zorg er daarom voor dat u instructies toevoegt voor alle CSV- of Json-parsingbibliotheken die u wilt gebruiken. De voorbeelden die in dit artikel worden weergegeven, ontleden een CSV-bestand met behulp van de [CsvHelper-bibliotheek](https://www.nuget.org/packages/CsvHelper/) die beschikbaar is op NuGet. Daarom voegen we deze `using` instructies toe aan de bovenkant van het codebestand.

```csharp
using CsvHelper;
using CsvHelper.Configuration;
```

Om voorbeelden in dit artikel samen te stellen, `using` moet u deze instructies ook toevoegen.

```csharp
using System.Threading.Tasks;
using System.IO;
using System.Globalization;
using System.Threading;
using System.Linq;
```

### <a name="java"></a>[Java](#tab/java)

Voeg `import` deze instructies toe aan de bovenkant van uw codebestand.

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

U SQL gebruiken om de rijfilterpredicaten en kolomprojecties op te geven in een queryversnellingsaanvraag. Met de volgende code wordt een CSV-bestand in opslag opgevraagd `Hemingway, Ernest`en worden alle rijen gegevens geretourneerd waarin de derde kolom overeenkomt met de waarde . 

- In de SQL-query `BlobStorage` wordt het trefwoord gebruikt om het bestand aan te duiden dat wordt opgevraagd.

- Kolomverwijzingen worden opgegeven `_N` als waar `_1`de eerste kolom is . Als het bronbestand een koptekstrij bevat, u naar kolommen verwijzen met de naam die is opgegeven in de koptekstrij. 

### <a name="net"></a>[.NET](#tab/dotnet)

De async-methode `BlobQuickQueryClient.QueryAsync` stuurt de query naar de QUERYversnellings-API en streamt de resultaten vervolgens terug naar de toepassing als een [streamobject.](https://docs.microsoft.com/dotnet/api/system.io.stream?view=netframework-4.8)

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
        Console.Error.WriteLine(String.Format("Error: {1}:{2}", err.Name, err.Description));
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

De `BlobQuickQueryClient.openInputStream()` methode stuurt de query naar de API voor queryversnelling en `InputStream` streamt de resultaten vervolgens terug naar de toepassing als een object dat kan worden gelezen zoals elk ander InputStream-object.

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

U uw resultaten naar een subset van kolommen scopen. Op die manier haalt u alleen de kolommen op die nodig zijn om een bepaalde berekening uit te voeren. Dit verbetert de prestaties van toepassingen en verlaagt de kosten omdat er minder gegevens via het netwerk worden overgedragen. 

Met deze code `PublicationYear` wordt alleen de kolom opgehaald voor alle boeken in de gegevensset. Het gebruikt ook de informatie uit de koptekstrij in het bronbestand om kolommen in de query te verwijzen.


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

De volgende code combineert rijfiltering en kolomprojecties in dezelfde query. 

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

- [Inschrijvingsformulier voor queryversnelling](https://aka.ms/adls/queryaccelerationpreview)    
- [Azure Data Lake Storage-queryversnelling (voorbeeld)](data-lake-storage-query-acceleration.md)
- [SQL-taalverwijzing voor queryversnelling (voorbeeld)](query-acceleration-sql-reference.md)
- QueryversnellingRESTAPI-verwijzing

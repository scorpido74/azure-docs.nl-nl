---
title: Toegangsreferenties beveiligen met gekoppelde services in Apache Spark voor Azure Synapse Analytics
description: Dit artikel biedt concepten over hoe u Apache Spark voor Synapse Analytics veilig kunt integreren met andere services met behulp van gekoppelde services en een tokenbibliotheek
services: synapse-analytics
author: mlee3gsd
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 08/26/2020
ms.author: martinle
ms.reviewer: euang
ms.openlocfilehash: 90e7297236994650e0820e883c94a98b29c49fb7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91249413"
---
# <a name="securing-your-credentials-through-linked-services-with-the-tokenlibrary"></a>Uw referenties beveiligen via gekoppelde services met de tokenbibliotheek
Toegang tot gegevens uit externe bronnen verkrijgen is een veelvoorkomend patroon. Tenzij de externe gegevensbron anonieme toegang toestaat, is de kans groot dat u uw verbinding moet beveiligen met een referentie, geheim of verbindingsreeks.  

Azure Synapse Analytics biedt gekoppelde services om het integratieproces te vereenvoudigen door de verbindingsgegevens op te slaan in een gekoppelde service of Azure Key Vault. Zodra u een gekoppelde service hebt gemaakt, kan Apache Spark naar de gekoppelde service verwijzen om de verbindingsgegevens in uw code toe te passen. 

Zie [gekoppelde services](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) voor meer informatie.
> [!NOTE]
> Voor het openen van bestanden uit de Azure Data Lake Storage in uw werkruimte wordt AAD-passthrough gebruikt voor verificatie, waardoor u de tokenbibliotheek dus niet hoeft te gebruiken. 


## <a name="prerequisite"></a>Vereiste
* Gekoppelde service - U moet een gekoppelde service naar de externe gegevensbron maken en naar de gekoppelde service verwijzen vanuit de tokenbibliotheek. Meer informatie over [gekoppelde services](../../data-factory/concepts-linked-services.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).


## <a name="connect-to-adls-gen2-outside-of-synapse-workspace"></a>Verbinding maken met ADLS Gen2 buiten de Synapse-werkruimte

Synapse biedt een geïntegreerde ervaring met gekoppelde services voor Azure Data Lake Storage Gen2.

```scala
// Scala code
val sc = spark.sparkContext
sc.hadoopConfiguration.set("spark.storage.synapse.linkedServiceName", "<LINKED SERVICE NAME>")
sc.hadoopConfiguration.set("fs.azure.account.auth.type", "SAS")
sc.hadoopConfiguration.set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

val df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```

```python
# Python code
sc._jsc.hadoopConfiguration().set("spark.storage.synapse.linkedServiceName", "<lINKED SERVICE NAME>")
sc._jsc.hadoopConfiguration().set("fs.azure.account.auth.type", "SAS")
sc._jsc.hadoopConfiguration().set("fs.azure.sas.token.provider.type", "com.microsoft.azure.synapse.tokenlibrary.LinkedServiceBasedSASProvider")

df = spark.read.csv("abfss://<CONTAINER>@<ACCOUNT>.dfs.core.windows.net/<DIRECTORY PATH>")

df.show()
```
## <a name="use-the-token-library"></a>De tokenbibliotheek gebruiken

Om verbinding te maken met andere gekoppelde services, kunt u de tokenbibliotheek rechtstreeks aanroepen.

### <a name="getconnectionstring"></a>GetConnectionString
 Om de verbindingsreeks op te halen, gebruikt u de functie <b>getConnectionString</b> en geeft u de <b>naam van de gekoppelde service</b> door.

```scala
// Scala
// retrieve connectionstring from TokenLibrary

import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val connectionString: String = TokenLibrary.getConnectionString("<LINKED SERVICE NAME>")
println(connectionString)
```

```python
# Python
# retrieve connectionstring from TokenLibrary

from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
connection_string = token_library.getConnectionString("<LINKED SERVICE NAME>")
print(connection_string)
```
```csharp
// C#
// retrieve connectionstring from TokenLibrary

using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Utils;

string connectionString = TokenLibrary.GetConnectionString(<LINKED SERVICE NAME>);
Console.WriteLine(connectionString);
```

### <a name="getconnectionstringasmap"></a>GetConnectionStringAsMap
Om specifieke waarden uit een <i>key=value</i>-paar in de verbindingsreeks te parseren, zoals 

<i>DefaultEndpointsProtocol=https;AccountName=\<AccountName>;AccountKey=\<AccountKey></i>

gebruikt u de functie <b>getConnectionStringAsMap</b> en geeft u de sleutel door om de waarde te retourneren.
```scala
// Linked services can be used for storing and retreiving credentials (e.g, account key)
// Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
import com.microsoft.azure.synapse.tokenlibrary.TokenLibrary

val accountKey: String = TokenLibrary.getConnectionStringAsMap("<LINKED SERVICE NAME">).get("<KEY NAME>")
println(accountKey)
```

```python
# Linked services can be used for storing and retreiving credentials (e.g, account key)
# Example connection string (for storage): "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
from pyspark.sql import SparkSession

sc = SparkSession.builder.getOrCreate()
token_library = sc._jvm.com.microsoft.azure.synapse.tokenlibrary.TokenLibrary
accountKey = token_library.getConnectionStringAsMap("<LINKED SERVICE NAME>").get("<KEY NAME>")
print(accountKey)
```

## <a name="next-steps"></a>Volgende stappen

- [Schrijven naar SQL-pool](./synapse-spark-sql-pool-import-export.md)


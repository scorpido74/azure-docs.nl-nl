---
title: Hyper Space-indexen voor Apache Spark
description: Prestatieoptimalisatie voor Apache Spark met behulp van Hyperspace-indexen
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 08/12/2020
ms.author: euang
ms.reviewer: euang
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: f25aae64e117452cd689b68c5478e7431d1a21bf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91249362"
---
# <a name="hyperspace-an-indexing-subsystem-for-apache-spark"></a>Hyper Space: een subsysteem voor indexering voor Apache Spark

Hyper Space introduceert de mogelijkheid om Apache Spark-gebruikers indexen te maken op hun gegevens sets, zoals CSV, JSON en Parquet, en ze gebruiken voor mogelijke query's en werklast versnelling.

In dit artikel benadrukken we de basis beginselen van Hyper Space, benadrukt u de eenvoud en laten we zien hoe deze kunnen worden gebruikt door alleen informatie over iedereen.

Disclaimer: Hyper Space helpt u bij het versnellen van uw workloads of query's in twee omstandigheden:

* Query's bevatten filters op predikaten met hoge selectiviteit. U kunt bijvoorbeeld 100 overeenkomende rijen selecteren uit een miljoen kandidaten rijen.
* Query's bevatten een koppeling waarvoor zware wille keurige Volg ordes zijn vereist. U kunt bijvoorbeeld een gegevensset van 100 GB toevoegen met een gegevensset van 10 GB.

U kunt uw werk belastingen zorgvuldig controleren en bepalen of het indexeren per geval wordt voor komen.

Dit document is ook beschikbaar in de notitieblok vorm, voor [python](https://github.com/microsoft/hyperspace/blob/master/notebooks/python/Hitchhikers%20Guide%20to%20Hyperspace.ipynb), [C#](https://github.com/microsoft/hyperspace/blob/master/notebooks/csharp/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)en [scala](https://github.com/microsoft/hyperspace/blob/master/notebooks/scala/Hitchhikers%20Guide%20to%20Hyperspace.ipynb)

## <a name="setup"></a>Instellen

Start een nieuwe Spark-sessie om te beginnen met. Aangezien dit document een zelf studie is waarmee u kunt zien wat Hyper Space kan bieden, maakt u een configuratie wijziging waarmee we kunnen markeren wat Hyper Space op kleine gegevens sets doet. 

Spark maakt standaard gebruik van broadcast-koppeling om samenvoeg query's te optimaliseren wanneer de gegevens grootte voor één zijde van de koppeling klein is (dit is het geval voor de voorbeeld gegevens die we in deze zelf studie gebruiken). Daarom worden broadcast-samen voegingen uitgeschakeld, zodat er later wordt gebruikgemaakt van de functie voor sorteren samen voegen. Dit is hoofd zakelijk om te laten zien hoe Hyper Space-indexen op schaal worden gebruikt voor het versnellen van samenvoeg query's.

De uitvoer van de volgende cel bevat een verwijzing naar de met succes gemaakte Spark-sessie en drukt '-1 ' af als de waarde voor de gewijzigde koppelings configuratie, wat aangeeft dat broadcast-koppeling is uitgeschakeld.

:::zone pivot = "programming-language-scala"

```scala
// Start your Spark session
spark

// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

// Verify that BroadcastHashJoin is set correctly
println(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))

```

::: zone-end

:::zone pivot = "programming-language-python"

```python
# Start your Spark session.
spark

# Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.conf.set("spark.sql.autoBroadcastJoinThreshold", -1)

# Verify that BroadcastHashJoin is set correctly 
print(spark.conf.get("spark.sql.autoBroadcastJoinThreshold"))
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
// Disable BroadcastHashJoin, so Spark will use standard SortMergeJoin. Currently, Hyperspace indexes utilize SortMergeJoin to speed up query.
spark.Conf().Set("spark.sql.autoBroadcastJoinThreshold", -1);

// Verify that BroadcastHashJoin is set correctly.
Console.WriteLine(spark.Conf().Get("spark.sql.autoBroadcastJoinThreshold"));
```

::: zone-end

Resulteert in:

```console
res3: org.apache.spark.sql.SparkSession = org.apache.spark.sql.SparkSession@297e957d
-1
```

## <a name="data-preparation"></a>Gegevensvoorbereiding

Om uw omgeving voor te bereiden, maakt u voorbeeld gegevens records en slaat u deze op als Parquet-gegevens bestanden. Parquet wordt gebruikt voor een afbeelding, maar u kunt ook andere indelingen gebruiken, zoals CSV. In de volgende cellen ziet u hoe u verschillende Hyper Space-indexen kunt maken in deze voor beeld-gegevensset en hoe u Spark gebruikt bij het uitvoeren van query's.

De voorbeeld records komen overeen met twee gegevens sets: afdeling en werk nemer. U moet de paden ' empLocation ' en ' deptLocation ' zodanig configureren dat ze op het opslag account naar de gewenste locatie verwijzen om gegenereerde gegevens bestanden op te slaan.

In de uitvoer van de volgende cel wordt de inhoud van de gegevens sets weer gegeven als lijsten met triplets gevolgd door verwijzingen naar dataFrames die zijn gemaakt om de inhoud van elke gegevensset op de gewenste locatie op te slaan.

:::zone pivot = "programming-language-scala"

```scala
import org.apache.spark.sql.DataFrame

// Sample department records
val departments = Seq(
      (10, "Accounting", "New York"),
      (20, "Research", "Dallas"),
      (30, "Sales", "Chicago"),
      (40, "Operations", "Boston"))

// Sample employee records
val employees = Seq(
      (7369, "SMITH", 20),
      (7499, "ALLEN", 30),
      (7521, "WARD", 30),
      (7566, "JONES", 20),
      (7698, "BLAKE", 30),
      (7782, "CLARK", 10),
      (7788, "SCOTT", 20),
      (7839, "KING", 10),
      (7844, "TURNER", 30),
      (7876, "ADAMS", 20),
      (7900, "JAMES", 30),
      (7934, "MILLER", 10),
      (7902, "FORD", 20),
      (7654, "MARTIN", 30))

// Save sample data in the Parquet format
import spark.implicits._
val empData: DataFrame = employees.toDF("empId", "empName", "deptId")
val deptData: DataFrame = departments.toDF("deptId", "deptName", "location")

val empLocation: String = "/<yourpath>/employees.parquet"       //TODO ** customize this location path **
val deptLocation: String = "/<yourpath>/departments.parquet"     //TODO ** customize this location path **
empData.write.mode("overwrite").parquet(empLocation)
deptData.write.mode("overwrite").parquet(deptLocation)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql.types import StructField, StructType, StringType, IntegerType

# Sample department records
departments = [(10, "Accounting", "New York"), (20, "Research", "Dallas"), (30, "Sales", "Chicago"), (40, "Operations", "Boston")]

# Sample employee records
employees = [(7369, "SMITH", 20), (7499, "ALLEN", 30), (7521, "WARD", 30), (7566, "JONES", 20), (7698, "BLAKE", 30)]

# Create a schema for the dataframe
dept_schema = StructType([StructField('deptId', IntegerType(), True), StructField('deptName', StringType(), True), StructField('location', StringType(), True)])
emp_schema = StructType([StructField('empId', IntegerType(), True), StructField('empName', StringType(), True), StructField('deptId', IntegerType(), True)])

departments_df = spark.createDataFrame(departments, dept_schema)
employees_df = spark.createDataFrame(employees, emp_schema)

#TODO ** customize this location path **
emp_Location = "/<yourpath>/employees.parquet"
dept_Location = "/<yourpath>/departments.parquet"

employees_df.write.mode("overwrite").parquet(emp_Location)
departments_df.write.mode("overwrite").parquet(dept_Location)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Sql.Types;

// Sample department records
var departments = new List<GenericRow>()
{
    new GenericRow(new object[] {10, "Accounting", "New York"}),
    new GenericRow(new object[] {20, "Research", "Dallas"}),
    new GenericRow(new object[] {30, "Sales", "Chicago"}),
    new GenericRow(new object[] {40, "Operations", "Boston"})
};

// Sample employee records
var employees = new List<GenericRow>() {
      new GenericRow(new object[] {7369, "SMITH", 20}),
      new GenericRow(new object[] {7499, "ALLEN", 30}),
      new GenericRow(new object[] {7521, "WARD", 30}),
      new GenericRow(new object[] {7566, "JONES", 20}),
      new GenericRow(new object[] {7698, "BLAKE", 30}),
      new GenericRow(new object[] {7782, "CLARK", 10}),
      new GenericRow(new object[] {7788, "SCOTT", 20}),
      new GenericRow(new object[] {7839, "KING", 10}),
      new GenericRow(new object[] {7844, "TURNER", 30}),
      new GenericRow(new object[] {7876, "ADAMS", 20}),
      new GenericRow(new object[] {7900, "JAMES", 30}),
      new GenericRow(new object[] {7934, "MILLER", 10}),
      new GenericRow(new object[] {7902, "FORD", 20}),
      new GenericRow(new object[] {7654, "MARTIN", 30})
};

// Save sample data in the Parquet format
var departmentSchema = new StructType(new List<StructField>()
{
    new StructField("deptId", new IntegerType()),
    new StructField("deptName", new StringType()),
    new StructField("location", new StringType())
});
var employeeSchema = new StructType(new List<StructField>()
{
    new StructField("empId", new IntegerType()),
    new StructField("empName", new StringType()),
    new StructField("deptId", new IntegerType())
});

DataFrame empData = spark.CreateDataFrame(employees, employeeSchema); 
DataFrame deptData = spark.CreateDataFrame(departments, departmentSchema); 

string empLocation = "/<yourpath>/employees.parquet";       //TODO ** customize this location path **
string deptLocation = "/<yourpath>/departments.parquet";     //TODO ** customize this location path **
empData.Write().Mode("overwrite").Parquet(empLocation);
deptData.Write().Mode("overwrite").Parquet(deptLocation);

```

::: zone-end

Resulteert in:

```console
import org.apache.spark.sql.DataFrame  
departments: Seq[(Int, String, String)] = List((10,Accounting,New York), (20,Research,Dallas), (30,Sales,Chicago), (40,Operations,Boston))  
employees: Seq[(Int, String, Int)] = List((7369,SMITH,20), (7499,ALLEN,30), (7521,WARD,30), (7566,JONES,20), (7698,BLAKE,30), (7782,CLARK,10), (7788,SCOTT,20), (7839,KING,10), (7844,TURNER,30), (7876,ADAMS,20), (7900,JAMES,30), (7934,MILLER,10), (7902,FORD,20), (7654,MARTIN,30))  
import spark.implicits._  
empData: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
empLocation: String = /your-path/employees.parquet  
deptLocation: String = /your-path/departments.parquet  
```

U kunt de inhoud van de Parquet-bestanden die we hebben gemaakt, controleren om er zeker van te zijn dat ze de verwachte records in de juiste indeling bevatten. Later gebruiken we deze gegevens bestanden om Hyper Space-indexen te maken en voorbeeld query's uit te voeren.

Het uitvoeren van de volgende cel produceert en uitvoer waarmee de rijen in de dataFrames van werk nemers en afdelingen in een tabel vorm worden weer gegeven. Er moeten 14 werk nemers en 4 afdelingen zijn die overeenkomen met een van de triplets die u hebt gemaakt in de vorige cel.

:::zone pivot = "programming-language-scala"

```scala
// empLocation and deptLocation are the user defined locations above to save parquet files
val empDF: DataFrame = spark.read.parquet(empLocation)
val deptDF: DataFrame = spark.read.parquet(deptLocation)

// Verify the data is available and correct
empDF.show()
deptDF.show()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# emp_Location and dept_Location are the user-defined locations above to save parquet files
emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

# Verify the data is available and correct
emp_DF.show()
dept_DF.show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// empLocation and deptLocation are the user-defined locations above to save parquet files
DataFrame empDF = spark.Read().Parquet(empLocation);
DataFrame deptDF = spark.Read().Parquet(deptLocation);

// Verify the data is available and correct
empDF.Show();
deptDF.Show();

```

::: zone-end

Resulteert in:

```console
empDF: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDF: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

```console
|EmpId|EmpName|DeptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
| 7900|  JAMES|    30|
| 7934| MILLER|    10|
| 7839|   KING|    10|
| 7566|  JONES|    20|
| 7698|  BLAKE|    30|
| 7782|  CLARK|    10|
| 7788|  SCOTT|    20|
| 7902|   FORD|    20|
| 7654| MARTIN|    30|  
```

&nbsp; &nbsp;

```console
|DeptId|  DeptName|Location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="indexes"></a>Indexen

Met Hyper Space kunt u indexen maken voor records die worden gescand vanuit persistente gegevens bestanden. Nadat ze zijn gemaakt, wordt een item dat overeenkomt met de index toegevoegd aan de meta gegevens van de Hyper Space. Deze meta gegevens worden later gebruikt door de optimalisatie van Apache Spark (met onze uitbrei dingen) tijdens de verwerking van query's om de juiste indexen te vinden en te gebruiken.

Nadat indexen zijn gemaakt, kunt u verschillende acties uitvoeren:

* **Vernieuw of de onderliggende gegevens worden gewijzigd.** U kunt een bestaande index vernieuwen om de wijzigingen vast te leggen.
* **Verwijder dit als de index niet nodig is.** U kunt een tijdelijke verwijdering uitvoeren, dat wil zeggen dat de index niet fysiek wordt verwijderd, maar is gemarkeerd als ' verwijderd ' zodat deze niet meer wordt gebruikt in uw workloads.
* **Vacuüm als een index niet meer nodig is.** U kunt een index vacuüm, waardoor de inhoud van de index en de gekoppelde meta gegevens volledig worden verwijderd uit de meta gegevens van Hyper Spaces.

Vernieuwen als de onderliggende gegevens worden gewijzigd, kunt u een bestaande index vernieuwen om die te registreren.
Verwijderen als de index niet nodig is, kunt u een tijdelijke verwijdering uitvoeren. index is niet fysiek verwijderd maar is gemarkeerd als ' verwijderd ' zodat deze niet meer wordt gebruikt in uw workloads.

In de volgende secties ziet u hoe de bewerkingen voor index beheer kunnen worden uitgevoerd in Hyper Space.

Eerst moet u de vereiste bibliotheken importeren en een exemplaar van Hyper Space maken. Later gebruikt u dit exemplaar om verschillende Hyper Space-Api's aan te roepen om indexen te maken voor uw voorbeeld gegevens en om die indexen te wijzigen.

In de uitvoer van de volgende cel wordt een verwijzing naar het gemaakte exemplaar van Hyper Space weer gegeven.

:::zone pivot = "programming-language-scala"

```scala
// Create an instance of Hyperspace
import com.microsoft.hyperspace._

val hyperspace: Hyperspace = Hyperspace()
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create an instance of Hyperspace
hyperspace = Hyperspace(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create an instance of Hyperspace
using Microsoft.Spark.Extensions.Hyperspace;

Hyperspace hyperspace = new Hyperspace(spark);

```

::: zone-end

Resulteert in:

```console
import com.microsoft.hyperspace._  
hyperspace: com.microsoft.hyperspace.Hyperspace = com.microsoft.hyperspace.Hyperspace@1432f740
```

## <a name="create-indexes"></a>Indexen maken

Als u een Hyper Space-index wilt maken, moet u twee soorten gegevens opgeven:

* Een Spark-data frame die verwijst naar de gegevens die moeten worden geïndexeerd.
* Een index configuratie object, IndexConfig, waarmee de index naam en de geïndexeerde en opgenomen kolommen van de index worden opgegeven.

U begint met het maken van drie Hyper Space-indexen op basis van de voorbeeld gegevens: twee indexen op de afdelings-gegevensset met de naam ' deptIndex1 ' en ' deptIndex2 ' en één index voor de gegevensset van de werk nemer met de naam ' empIndex '. Voor elke index hebt u een bijbehorende IndexConfig nodig om de naam samen met kolommen lijsten voor de geïndexeerde en opgenomen kolommen vast te leggen. Als u de volgende cel uitvoert, worden deze IndexConfigs gemaakt en worden deze in de uitvoer weer gegeven.

> [!Note]
> Een index kolom is een kolom die wordt weer gegeven in de filters of de voor waarden voor samen voegen. Een opgenomen kolom is een kolom die wordt weer gegeven in uw SELECT/project.

Bijvoorbeeld in de volgende query:

```sql
SELECT X
FROM T
WHERE Y = 2
```

X kan een index kolom zijn en Y kan een opgenomen kolom zijn.

:::zone pivot = "programming-language-scala"

```scala
// Create index configurations
import com.microsoft.hyperspace.index.IndexConfig

val empIndexConfig: IndexConfig = IndexConfig("empIndex", Seq("deptId"), Seq("empName"))
val deptIndexConfig1: IndexConfig = IndexConfig("deptIndex1", Seq("deptId"), Seq("deptName"))
val deptIndexConfig2: IndexConfig = IndexConfig("deptIndex2", Seq("location"), Seq("deptName"))
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create index configurations

emp_IndexConfig = IndexConfig("empIndex1", ["deptId"], ["empName"])
dept_IndexConfig1 = IndexConfig("deptIndex1", ["deptId"], ["deptName"])
dept_IndexConfig2 = IndexConfig("deptIndex2", ["location"], ["deptName"])

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

using Microsoft.Spark.Extensions.Hyperspace.Index;

var empIndexConfig = new IndexConfig("empIndex", new string[] {"deptId"}, new string[] {"empName"});
var deptIndexConfig1 = new IndexConfig("deptIndex1", new string[] {"deptId"}, new string[] {"deptName"});
var deptIndexConfig2 = new IndexConfig("deptIndex2", new string[] {"location"}, new string[] {"deptName"});

```

::: zone-end

Resulteert in:

```console
import com.microsoft.hyperspace.index.IndexConfig  
empIndexConfig: com.microsoft.hyperspace.index.IndexConfig = [indexName: empIndex; indexedColumns: deptid; includedColumns: empname]  
deptIndexConfig1: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex1; indexedColumns: deptid; includedColumns: deptname]  
deptIndexConfig2: com.microsoft.hyperspace.index.IndexConfig = [indexName: deptIndex2; indexedColumns: location; includedColumns: deptname]  
```
Nu maakt u drie indexen met behulp van uw index configuraties. Voor dit doel roept u de opdracht ' createIndex ' aan op onze Hyper Space-instantie. Voor deze opdracht is een index configuratie vereist en de data frame die rijen bevatten die moeten worden geïndexeerd. Als u de volgende cel uitvoert, worden er drie indexen gemaakt.

:::zone pivot = "programming-language-scala"

```scala
// Create indexes from configurations
import com.microsoft.hyperspace.index.Index

hyperspace.createIndex(empDF, empIndexConfig)
hyperspace.createIndex(deptDF, deptIndexConfig1)
hyperspace.createIndex(deptDF, deptIndexConfig2)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Create indexes from configurations

hyperspace.createIndex(emp_DF, emp_IndexConfig)
hyperspace.createIndex(dept_DF, dept_IndexConfig1)
hyperspace.createIndex(dept_DF, dept_IndexConfig2)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Create indexes from configurations
hyperspace.CreateIndex(empDF, empIndexConfig);
hyperspace.CreateIndex(deptDF, deptIndexConfig1);
hyperspace.CreateIndex(deptDF, deptIndexConfig2);

```

::: zone-end

Resulteert in:

```console
import com.microsoft.hyperspace.index.Index
```

## <a name="list-indexes"></a>Lijst indexen

De volgende code laat zien hoe u alle beschik bare indexen in een Hyper Space-exemplaar kunt weer geven. Er wordt gebruikgemaakt van de API indices die informatie over bestaande indexen retourneert als Spark-data frame zodat u extra bewerkingen kunt uitvoeren. 

U kunt bijvoorbeeld geldige bewerkingen op deze data frame aanroepen om de inhoud ervan te controleren of deze verder te analyseren (bijvoorbeeld om specifieke indexen te filteren of ze te groeperen op basis van een gewenste eigenschap).

In de volgende cel wordt de actie ' show ' gebruikt om de rijen volledig af te drukken en de details van de indexen in een tabel vorm weer te geven. Voor elke index kunt u alle informatie-Hyper-in de meta gegevens opslaan. U ziet dan onmiddellijk het volgende:

* config. indexnaam, config. indexedColumns, config. includedColumns en status. status zijn de velden waarnaar een gebruiker normaal gesp roken verwijst.
* dfSignature wordt automatisch gegenereerd door Hyper Space en is uniek voor elke index. Hyper Space gebruikt deze hand tekening intern voor het onderhouden van de index en om deze te gebruiken op het moment van de query.


In de volgende uitvoer moeten alle drie de indexen ' actief ' hebben als status en hun naam, geïndexeerde kolommen en opgenomen kolommen moeten overeenkomen met wat we hebben gedefinieerd in de bovenstaande index configuraties.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.Indexes().Show();

```

::: zone-end

Resulteert in:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="delete-indexes"></a>Indexen verwijderen

U kunt een bestaande index verwijderen door de API ' deleteIndex ' te gebruiken en de index naam op te geven. Index verwijderen voert een tijdelijke verwijdering uit: het hoofd zakelijk de index status in de Hyper Space-meta gegevens van ' ACTIVE ' wordt bijgewerkt naar ' verwijderd '. Hiermee wordt de verwijderde index uitgesloten van toekomstige query optimalisatie en Hyper Space wordt die index niet meer voor een query gekozen. 

Index bestanden voor een verwijderde index blijven echter wel beschikbaar (omdat het een tijdelijke verwijdering is), zodat de index kan worden hersteld als de gebruiker om vraagt.

De volgende cel verwijdert index met de naam ' deptIndex2 ' en bevat vervolgens de Hyper Space-meta gegevens. De uitvoer moet vergelijkbaar zijn met de bovenstaande cel voor ' lijst indexen ', met uitzonde ring van ' deptIndex2 ', waarvan de status nu moet worden gewijzigd in ' verwijderd '.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
hyperspace.DeleteIndex("deptIndex2");

hyperspace.Indexes().Show();

```

::: zone-end

Resulteert in:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="restore-indexes"></a>Indexen herstellen

U kunt de API ' restoreIndex ' gebruiken om een verwijderde index te herstellen. Hiermee wordt de meest recente versie van de index teruggezet naar de actieve status en wordt deze opnieuw bruikbaar voor query's. In de volgende cel ziet u een voor beeld van het gebruik van ' restoreIndex '. U verwijdert "deptIndex1" en herstelt het. De uitvoer laat zien dat "deptIndex1" eerst is verwijderd na het aanroepen van "deleteIndex" en de opdracht ' ACTIVE ' na het aanroepen van "restoreIndex" werd weer gegeven.

:::zone pivot = "programming-language-scala"

```scala
hyperspace.deleteIndex("deptIndex1")

hyperspace.indexes.show

hyperspace.restoreIndex("deptIndex1")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.deleteIndex("deptIndex1")
hyperspace.indexes().show()
hyperspace.restoreIndex("deptIndex1")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.DeleteIndex("deptIndex1");
hyperspace.Indexes().Show();
hyperspace.RestoreIndex("deptIndex1");
hyperspace.Indexes().Show();

```

::: zone-end

Resulteert in:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.indexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

&nbsp; &nbsp;

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|      deptIndex2|           [location]|            [deptName]|`location` STRING...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|     DELETED|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="vacuum-indexes"></a>Vacuüm indexen

U kunt een harde verwijdering uitvoeren, dat wil zeggen, bestanden volledig verwijderen en de meta gegevens vermelding voor een verwijderde index met behulp van de **vacuumIndex** -opdracht. Deze actie kan niet ongedaan worden gemaakt. Hiermee verwijdert u alle index bestanden, wat de oorzaak is van een harde verwijdering.

De volgende cel vacuüm de index "deptIndex2" en geeft Hyper Space-meta gegevens na vacuüm weer. U zou meta gegevens vermeldingen voor twee indexen "deptIndex1" en "empIndex" moeten zien, zowel met de status "actief" en geen vermelding voor "deptIndex2".

:::zone pivot = "programming-language-scala"

```scala
hyperspace.vacuumIndex("deptIndex2")

hyperspace.indexes.show
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

hyperspace.vacuumIndex("deptIndex2")
hyperspace.indexes().show()

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

hyperspace.VacuumIndex("deptIndex2");
hyperspace.Indexes().Show();

```

::: zone-end

Resulteert in:

```console
|Config.IndexName|Config.IndexedColumns|Config.IncludedColumns|        SchemaString|   SignatureProvider|         DfSignature|      SerializedPlan|NumBuckets|             DirPath|Status.Value|Stats.IndexSize|
|----------------|---------------------|----------------------|--------------------|--------------------|--------------------|--------------------|----------|--------------------|------------|---------------|
|      deptIndex1|             [deptId]|            [deptName]|`deptId` INT,`dep...|com.microsoft.cha...|0effc1610ae2e7c49...|Relation[deptId#3...|       200|abfss://datasets@...|      ACTIVE|              0|
|        empIndex|             [deptId]|             [empName]|`deptId` INT,`emp...|com.microsoft.cha...|30768c6c9b2533004...|Relation[empId#32...|       200|abfss://datasets@...|      ACTIVE|              0|
```

## <a name="enable-or-disable-hyperspace"></a>Hyper Space in-of uitschakelen

Hyper Space biedt Api's voor het in-of uitschakelen van index gebruik met Spark.

* Door gebruik te maken van de **enableHyperspace** -opdracht, worden de Hyper Space-optimalisatie regels zichtbaar voor de Spark-Optimizer en kunnen ze gebruikmaken van bestaande Hyper Space-indexen om gebruikers query's te optimaliseren.
* Met behulp van de **disableHyperspace** -opdracht worden Hyper Space-regels niet meer toegepast tijdens de optimalisatie van query's. Het uitschakelen van Hyper Space heeft geen invloed op gemaakte indexen, omdat deze intact blijven.

In de volgende cel ziet u hoe u deze opdrachten kunt gebruiken om Hyper Space in of uit te scha kelen. In de uitvoer ziet u een verwijzing naar de bestaande Spark-sessie waarvan de configuratie is bijgewerkt.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

// Disable Hyperspace
spark.disableHyperspace
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

# Disable Hyperspace
Hyperspace.disable(spark)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

// Disable Hyperspace
spark.DisableHyperspace();

```

::: zone-end

Resulteert in:

```console
res48: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb  
res51: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.SparkSession@39fe1ddb
```

## <a name="index-usage"></a>Index gebruik

Als u met Spark Hyper Space-indexen wilt maken tijdens het verwerken van query's, moet u ervoor zorgen dat Hyper Space is ingeschakeld.

In de volgende cel kunt u Hyper-DataFrames inschakelen en twee gegevens maken die uw voorbeeld gegevensrecords bevatten, die u gebruikt voor het uitvoeren van voorbeeld query's. Voor elke data frame worden enkele voorbeeld rijen afgedrukt.

:::zone pivot = "programming-language-scala"

```scala
// Enable Hyperspace
spark.enableHyperspace

val empDFrame: DataFrame = spark.read.parquet(empLocation)
val deptDFrame: DataFrame = spark.read.parquet(deptLocation)

empDFrame.show(5)
deptDFrame.show(5)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Enable Hyperspace
Hyperspace.enable(spark)

emp_DF = spark.read.parquet(emp_Location)
dept_DF = spark.read.parquet(dept_Location)

emp_DF.show(5)
dept_DF.show(5)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Enable Hyperspace
spark.EnableHyperspace();

DataFrame empDFrame = spark.Read().Parquet(empLocation);
DataFrame deptDFrame = spark.Read().Parquet(deptLocation);

empDFrame.Show(5);
deptDFrame.Show(5);

```

::: zone-end

Resulteert in:

```console
res53: org.apache.spark.sql.Spark™Session = org.apache.spark.sql.Spark™Session@39fe1ddb  
empDFrame: org.apache.spark.sql.DataFrame = [empId: int, empName: string ... 1 more field]  
deptDFrame: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
```

&nbsp; &nbsp;

```console
|empId|empName|deptId|
|-----|-------|------|
| 7499|  ALLEN|    30|
| 7521|   WARD|    30|
| 7369|  SMITH|    20|
| 7844| TURNER|    30|
| 7876|  ADAMS|    20|
```

&nbsp;&nbsp;Hiermee worden alleen de bovenste vijf rijen &nbsp; weer gegeven&nbsp;

```console
|deptId|  deptName|location|
|------|----------|--------|
|    10|Accounting|New York|
|    40|Operations|  Boston|
|    20|  Research|  Dallas|
|    30|     Sales| Chicago|
```

## <a name="index-types"></a>Indextypen

Hyper Space bevat momenteel regels voor het exploiteren van indexen voor twee groepen query's:

* Selectie query's met selectieve predikaten voor het opzoeken van lookups of bereiken.
* Voeg query's toe met een gelijkheid samenvoegings predicaat (equijoins).

## <a name="indexes-for-accelerating-filters"></a>Indexen voor versnellende filters

In het eerste voor beeld wordt er een zoek opdracht op afdelings records weer gegeven, zoals in de volgende cel. In SQL ziet deze query eruit als in het volgende voor beeld:

```sql
SELECT deptName
FROM departments
WHERE deptId = 20
```

De uitvoer van het uitvoeren van de volgende cel wordt weer gegeven:

* Query resultaat: dit is één afdelings naam.
* Query plan dat Spark gebruikt om de query uit te voeren.

In het query plan toont de operator **FileScan** aan de onderkant van het plan de gegevens bron waaruit de records zijn gelezen. De locatie van dit bestand geeft het pad naar de meest recente versie van de index ' deptIndex1 ' aan. In deze informatie ziet u dat volgens de query en het gebruik van de Hyper Space-optimalisatie regels, Spark heeft besloten de juiste index tijdens runtime te misbruiken.

:::zone pivot = "programming-language-scala"

```scala
// Filter with equality predicate

val eqFilter: DataFrame = deptDFrame.filter("deptId = 20").select("deptName")
eqFilter.show()

eqFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with equality predicate

eqFilter = dept_DF.filter("""deptId = 20""").select("""deptName""")
eqFilter.show()

eqFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame eqFilter = deptDFrame.Filter("deptId = 20").Select("deptName");
eqFilter.Show();

eqFilter.Explain(true);

```

::: zone-end

Resulteert in:

```console
eqFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|DeptName|
|--------|
|Research|
```

&nbsp; &nbsp;

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 = 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 = 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), EqualTo(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

Het tweede voor beeld is een bereik selectie query op afdelings records. In SQL ziet deze query eruit als in het volgende voor beeld:

```sql
SELECT deptName
FROM departments
WHERE deptId > 20
```

Net als in het eerste voor beeld ziet u in de uitvoer van de volgende cel de query resultaten (namen van twee afdelingen) en het query plan. De locatie van het gegevens bestand in de **FileScan** -operator geeft aan dat ' deptIndex1 ' is gebruikt om de query uit te voeren.

:::zone pivot = "programming-language-scala"

```scala
// Filter with range selection predicate

val rangeFilter: DataFrame = deptDFrame.filter("deptId > 20").select("deptName")
rangeFilter.show()

rangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Filter with range selection predicate

rangeFilter = dept_DF.filter("""deptId > 20""").select("deptName")
rangeFilter.show()

rangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Filter with range selection predicate
DataFrame rangeFilter = deptDFrame.Filter("deptId > 20").Select("deptName");
rangeFilter.Show();

rangeFilter.Explain(true);

```

::: zone-end

Resulteert in:

```console
rangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|  DeptName|
|----------|
|Operations|
|     Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#534]
+- Filter (deptId#533 > 20)
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [deptName#534]
+- Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(1) Project [deptName#534]
+- *(1) Filter (isnotnull(deptId#533) && (deptId#533 > 20))
   +- *(1) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```
Het derde voor beeld is een query waarmee afdelings-en werknemerrecords worden toegevoegd aan de afdelings-ID. De equivalente SQL-instructie wordt als volgt weer gegeven:

```sql
SELECT employees.deptId, empName, departments.deptId, deptName
FROM   employees, departments
WHERE  employees.deptId = departments.deptId
```
In de uitvoer van de volgende cel worden de query resultaten weer gegeven. Dit zijn de namen van 14 werk nemers en de naam van de afdeling waarop elke werk nemer werkt. Het query plan is ook opgenomen in de uitvoer. U ziet hoe de bestands locaties voor twee **FileScan** -Opera tors tonen dat Spark ' empIndex ' en ' deptIndex1 ' indexen gebruikt voor het uitvoeren van de query.

:::zone pivot = "programming-language-scala"

```scala
// Join

val eqJoin: DataFrame =
      empDFrame.
      join(deptDFrame, empDFrame("deptId") === deptDFrame("deptId")).
      select(empDFrame("empName"), deptDFrame("deptName"))

eqJoin.show()

eqJoin.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

# Join

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

eqJoin.show()

eqJoin.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

// Join
DataFrame eqJoin =
      empDFrame
      .Join(deptDFrame, empDFrame.Col("deptId") == deptDFrame.Col("deptId"))
      .Select(empDFrame.Col("empName"), deptDFrame.Col("deptName"));

eqJoin.Show();

eqJoin.Explain(true);

```

::: zone-end

Resulteert in:

```console
eqJoin: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Relation[empId#527,empName#528,deptId#529] parquet
   +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534] parquet

== Physical Plan ==
*(3) Project [empName#528, deptName#534]
+- *(3) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>, SelectedBucketsCount: 200 out of 200
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>, SelectedBucketsCount: 200 out of 200
```

## <a name="support-for-sql-semantics"></a>Ondersteuning voor SQL-semantiek

Het index gebruik is transparant om te bepalen of u de data frame-API of Spark SQL gebruikt. In het volgende voor beeld ziet u hetzelfde voor beeld, zoals voorheen, in SQL-formulier, waarin het gebruik van indexen, indien van toepassing, wordt weer gegeven.

:::zone pivot = "programming-language-scala"

```scala
empDFrame.createOrReplaceTempView("EMP")
deptDFrame.createOrReplaceTempView("DEPT")

val joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

from pyspark.sql import SparkSession

emp_DF.createOrReplaceTempView("EMP")
dept_DF.createOrReplaceTempView("DEPT")

joinQuery = spark.sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId")

joinQuery.show()
joinQuery.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

empDFrame.CreateOrReplaceTempView("EMP");
deptDFrame.CreateOrReplaceTempView("DEPT");

var joinQuery = spark.Sql("SELECT EMP.empName, DEPT.deptName FROM EMP, DEPT WHERE EMP.deptId = DEPT.deptId");

joinQuery.Show();
joinQuery.Explain(true);

```

::: zone-end

Resulteert in:

```console

joinQuery: org.apache.spark.sql.DataFrame = [empName: string, deptName: string]
```

```console
|empName|  deptName|
|-------|----------|
|  SMITH|  Research|
|  JONES|  Research|
|   FORD|  Research|
|  ADAMS|  Research|
|  SCOTT|  Research|
|   KING|Accounting|
|  CLARK|Accounting|
| MILLER|Accounting|
|  JAMES|     Sales|
|  BLAKE|     Sales|
| MARTIN|     Sales|
|  ALLEN|     Sales|
|   WARD|     Sales|
| TURNER|     Sales|
```

```console
== Parsed Logical Plan ==
'Project ['EMP.empName, 'DEPT.deptName]
+- 'Filter ('EMP.deptId = 'DEPT.deptId)
   +- 'Join Inner
      :- 'UnresolvedRelation `EMP`
      +- 'UnresolvedRelation `DEPT`

== Analyzed Logical Plan ==
empName: string, deptName: string
Project [empName#528, deptName#534]
+- Filter (deptId#529 = deptId#533)
   +- Join Inner
      :- SubqueryAlias `emp`
      :  +- Relation[empId#527,empName#528,deptId#529] parquet
      +- SubqueryAlias `dept`
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Optimized Logical Plan ==
Project [empName#528, deptName#534]
+- Join Inner, (deptId#529 = deptId#533)
   :- Project [empName#528, deptId#529]
   :  +- Filter isnotnull(deptId#529)
   :     +- Relation[empId#527,empName#528,deptId#529] parquet
   +- Project [deptId#533, deptName#534]
      +- Filter isnotnull(deptId#533)
         +- Relation[deptId#533,deptName#534,location#535] parquet

== Physical Plan ==
*(5) Project [empName#528, deptName#534]
+- *(5) SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,empName:string>
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="explain-api"></a>Uitleg over API

Zijn er fantastische indexen, maar hoe weet u dan of ze worden gebruikt? Met Hyper Space kunnen gebruikers hun oorspronkelijke abonnement vergelijken met het bijgewerkte schema voor index afhankelijk voordat ze hun query uitvoeren. U hebt de mogelijkheid om te kiezen uit HTML, lees bare tekst of console modus om de uitvoer van de opdracht weer te geven.

In de volgende cel ziet u een voor beeld met HTML. De gemarkeerde sectie vertegenwoordigt het verschil tussen de oorspronkelijke en bijgewerkte abonnementen, samen met de indexen die worden gebruikt.

:::zone pivot = "programming-language-scala"

```scala
spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin) { displayHTML }
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

eqJoin = emp_DF.join(dept_DF, emp_DF.deptId == dept_DF.deptId).select(emp_DF.empName, dept_DF.deptName)

spark.conf.set("spark.hyperspace.explain.displayMode", "html")
hyperspace.explain(eqJoin, True, displayHTML)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

spark.Conf().Set("spark.hyperspace.explain.displayMode", "html");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.beginTag", "<b style=\"background:LightGreen\">");
spark.Conf().Set("spark.hyperspace.explain.displayMode.highlight.endTag", "</b>");

hyperspace.Explain(eqJoin, false, input => DisplayHTML(input));

```

::: zone-end

Resulteert in:

### <a name="plan-with-indexes"></a>Plannen met indexen

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(1) Project [empName#528, deptId#529]
   :  +- *(1) Filter isnotnull(deptId#529)
   :     +- *(1) FileScan parquet [deptId#529,empName#528] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(2) Project [deptId#533, deptName#534]
      +- *(2) Filter isnotnull(deptId#533)
         +- *(2) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="plan-without-indexes"></a>Plannen zonder indexen

```console
Project [empName#528, deptName#534]
+- SortMergeJoin [deptId#529], [deptId#533], Inner
   :- *(2) Sort [deptId#529 ASC NULLS FIRST], false, 0
   :  +- Exchange hashpartitioning(deptId#529, 200)
   :     +- *(1) Project [empName#528, deptId#529]
   :        +- *(1) Filter isnotnull(deptId#529)
   :           +- *(1) FileScan parquet [empName#528,deptId#529] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/employees.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
   +- *(4) Sort [deptId#533 ASC NULLS FIRST], false, 0
      +- Exchange hashpartitioning(deptId#533, 200)
         +- *(3) Project [deptId#533, deptName#534]
            +- *(3) Filter isnotnull(deptId#533)
               +- *(3) FileScan parquet [deptId#533,deptName#534] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/your-path/departments.parquet], PartitionFilters: [], PushedFilters: [IsNotNull(deptId)], ReadSchema: struct
```

### <a name="indexes-used"></a>Gebruikte indexen

```console
deptIndex1:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/deptIndex1/v__=0
empIndex:abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/<container>/indexes/public/empIndex/v__=0
```

## <a name="refresh-indexes"></a>Indexen vernieuwen

Als de oorspronkelijke gegevens waarop een index is gemaakt, worden gewijzigd, wordt de meest recente status van de gegevens niet meer door de index vastgelegd. U kunt een verlopen index vernieuwen met behulp van de **refreshIndex** -opdracht. Met deze opdracht wordt de index volledig opnieuw opgebouwd en bijgewerkt op basis van de meest recente gegevens records. We laten u zien hoe u uw index incrementeel kunt vernieuwen in andere notitie blokken.

In de volgende twee cellen ziet u een voor beeld van dit scenario:

* De eerste cel voegt twee meer afdelingen toe aan de gegevens van de oorspronkelijke kosten plaatsen. Er wordt een lijst met afdelingen gelezen en afgedrukt om te controleren of nieuwe afdelingen correct worden toegevoegd. In de uitvoer ziet u zes afdelingen in totaal: vier oude en twee nieuwe. Het aanroepen van **refreshIndex** -updates ' deptIndex1 ' zodat de index nieuwe afdelingen vastlegt.
* In de tweede cel wordt het bereik selectie query-voor beeld uitgevoerd. De resultaten moeten nu vier afdelingen bevatten: twee zijn de diensten die worden weer gegeven voordat de voor gaande query werd uitgevoerd en twee de nieuwe afdelingen die we hebben toegevoegd.

### <a name="specific-index-refresh"></a>Specifieke index vernieuwen

:::zone pivot = "programming-language-scala"

```scala
val extraDepartments = Seq(
      (50, "Inovation", "Seattle"),
      (60, "Human Resources", "San Francisco"))

val extraDeptData: DataFrame = extraDepartments.toDF("deptId", "deptName", "location")
extraDeptData.write.mode("Append").parquet(deptLocation)

val deptDFrameUpdated: DataFrame = spark.read.parquet(deptLocation)

deptDFrameUpdated.show(10)

hyperspace.refreshIndex("deptIndex1")
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

extra_Departments = [(50, "Inovation", "Seattle"), (60, "Human Resources", "San Francisco")]

extra_departments_df = spark.createDataFrame(extra_Departments, dept_schema)
extra_departments_df.write.mode("Append").parquet(dept_Location)


dept_DFrame_Updated = spark.read.parquet(dept_Location)

dept_DFrame_Updated.show(10)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

var extraDepartments = new List<GenericRow>()
{
    new GenericRow(new object[] {50, "Inovation", "Seattle"}),
    new GenericRow(new object[] {60, "Human Resources", "San Francisco"})
};
    
DataFrame extraDeptData = spark.CreateDataFrame(extraDepartments, departmentSchema);
extraDeptData.Write().Mode("Append").Parquet(deptLocation);

DataFrame deptDFrameUpdated = spark.Read().Parquet(deptLocation);

deptDFrameUpdated.Show(10);

hyperspace.RefreshIndex("deptIndex1");

```

::: zone-end

Resulteert in:

```console
extraDepartments: Seq[(Int, String, String)] = List((50,Inovation,Seattle), (60,Human Resources,San Francisco))  
extraDeptData: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]  
deptDFrameUpdated: org.apache.spark.sql.DataFrame = [deptId: int, deptName: string ... 1 more field]
```

&nbsp; &nbsp;

```console  
|deptId|       deptName|     location|
|------|---------------|-------------|
|    60|Human Resources|San Francisco|
|    10|     Accounting|     New York|
|    50|      Inovation|      Seattle|
|    40|     Operations|       Boston|
|    20|       Research|       Dallas|
|    30|          Sales|      Chicago|
```

### <a name="range-selection"></a>Bereik selectie

:::zone pivot = "programming-language-scala"

```scala
val newRangeFilter: DataFrame = deptDFrameUpdated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(true)
```

::: zone-end

:::zone pivot = "programming-language-python"

```python

newRangeFilter = dept_DFrame_Updated.filter("deptId > 20").select("deptName")
newRangeFilter.show()

newRangeFilter.explain(True)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

DataFrame newRangeFilter = deptDFrameUpdated.Filter("deptId > 20").Select("deptName");
newRangeFilter.Show();

newRangeFilter.Explain(true);

```

::: zone-end

Resulteert in:

```console
newRangeFilter: org.apache.spark.sql.DataFrame = [deptName: string]
```

```console
|       DeptName|
|---------------|
|Human Resources|
|      Inovation|
|     Operations|
|          Sales|
```

```console
== Parsed Logical Plan ==
'Project [unresolvedalias('deptName, None)]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Analyzed Logical Plan ==
deptName: string
Project [deptName#675]
+- Filter (deptId#674 > 20)
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Optimized Logical Plan ==
Project [deptName#675]
+- Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- Relation[deptId#674,deptName#675,location#676] parquet

== Physical Plan ==
*(1) Project [deptName#675]
+- *(1) Filter (isnotnull(deptId#674) && (deptId#674 > 20))
   +- *(1) FileScan parquet [deptId#674,deptName#675] Batched: true, Format: Parquet, Location: InMemoryFileIndex[abfss://datasets@hyperspacebenchmark.dfs.core.windows.net/hyperspaceon..., PartitionFilters: [], PushedFilters: [IsNotNull(deptId), GreaterThan(deptId,20)], ReadSchema: struct<deptId:int,deptName:string>
```

## <a name="next-steps"></a>Volgende stappen

* [Project-Hyper Space](https://microsoft.github.io/hyperspace/)
* [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)

---
title: Informatie over Apache Spark-codeconcepten voor Azure Data Lake Analytics U-SQL-ontwikkelaars.
description: In dit artikel worden Apache Spark-concepten beschreven om U-SQL-ontwikkelaars te helpen spark-codeconcepten te begrijpen.
author: guyhay
ms.author: guyhay
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: conceptual
ms.custom: Understand-apache-spark-code-concepts
ms.date: 10/15/2019
ms.openlocfilehash: bdb38e36a9f1344a3adde15d349a2ec176c0fe95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74424001"
---
# <a name="understand-apache-spark-code-for-u-sql-developers"></a>Apache Spark-code voor U-SQL-ontwikkelaars begrijpen

Deze sectie biedt richtlijnen op hoog niveau voor het transformeren van U-SQL-scripts naar Apache Spark.

- Het begint met een [vergelijking van de verwerkingsparadigma's van de twee talen](#understand-the-u-sql-and-spark-language-and-processing-paradigms)
- Geeft tips over hoe u:
   - [Scripts transformeren,](#transform-u-sql-scripts) inclusief [de rijsetexpressies](#transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions) van U-SQL
   - [.NET-code](#transform-net-code)
   - [Gegevenstypen](#transform-typed-values)
   - [Catalogusobjecten](#transform-u-sql-catalog-objects).

## <a name="understand-the-u-sql-and-spark-language-and-processing-paradigms"></a>Begrijp de U-SQL- en Spark-taal- en verwerkingsparadigma's

Voordat u de U-SQL-scripts van Azure Data Lake Analytics naar Spark gaat migreren, is het handig om de algemene taal- en verwerkingsfilosofieën van de twee systemen te begrijpen.

U-SQL is een SQL-achtige declaratieve querytaal die gebruik maakt van een data-flow paradigma en waarmee u eenvoudig gebruikerscode insluiten en uitschalen die is geschreven in .NET (bijvoorbeeld C#), Python en R. De gebruikersextensies kunnen eenvoudige expressies of door de gebruiker gedefinieerde functies implementeren, maar kunnen de gebruiker ook de mogelijkheid bieden om zogenaamde door de gebruiker gedefinieerde operatoren te implementeren die aangepaste operatoren implementeren om transformaties, extracties en schrijfuitvoer op rijsetniveau uit te voeren.

Spark is een scale-out framework dat verschillende taalbindingen aanbiedt in Scala, Java, Python, .NET etc. waar u voornamelijk uw code in een van deze talen schrijft, gegevensabstracties maakt die resilient distributed datasets (RDD), dataframes en datasets en datasets en gebruik vervolgens een LINQ-achtige domeinspecifieke taal (DSL) om ze te transformeren. Het biedt sparksql ook als een declaratieve subtaal op het dataframe en dataset abstracties. De DSL biedt twee categorieën van bewerkingen, transformaties en acties. Het toepassen van transformaties op de gegevensabstracties zal de transformatie niet uitvoeren, maar in plaats daarvan het uitvoeringsplan opbouwen dat ter evaluatie wordt ingediend met een actie (bijvoorbeeld het schrijven van het resultaat in een tijdelijke tabel of bestand, of het afdrukken van de resultaat).

Dus bij het vertalen van een U-SQL-script naar een Spark-programma, moet u beslissen welke taal u wilt gebruiken om op zijn minst de abstractie van het gegevensframe te genereren (wat momenteel de meest gebruikte gegevensabstractie is) en of u de declaratieve dataflow transformaties met behulp van de DSL of SparkSQL. In sommige complexere gevallen moet u uw U-SQL-script mogelijk splitsen in een reeks Spark-en andere stappen die zijn geïmplementeerd met Azure Batch- of Azure-functies.

Bovendien biedt Azure Data Lake Analytics U-SQL in een serverloze taakserviceomgeving, terwijl zowel Azure Databricks als Azure HDInsight Spark bieden in de vorm van een clusterservice. Bij het transformeren van uw toepassing moet u rekening houden met de implicaties van het nu maken, verkleinen, schalen en ontmantelen van de clusters.

## <a name="transform-u-sql-scripts"></a>U-SQL-scripts transformeren

U-SQL-scripts volgen het volgende verwerkingspatroon:

1. Gegevens worden gelezen uit ongestructureerde `EXTRACT` bestanden, met behulp van de instructie, een specificatie voor locatie- of bestandsset, en de ingebouwde of door de gebruiker gedefinieerde extractor en het gewenste schema, of uit U-SQL-tabellen (beheerde of externe tabellen). Het wordt weergegeven als een rijset.
2. De rijsets worden getransformeerd in meerdere U-SQL-instructies die U-SQL-expressies toepassen op de rijsets en nieuwe rijsets produceren.
3. Ten slotte worden de resulterende rijsets `OUTPUT` uitgevoerd naar bestanden met behulp van de instructie die de locatie(en) en een ingebouwde of door de gebruiker gedefinieerde outputter of in een U-SQL-tabel opgeeft.

Het script wordt lui geëvalueerd, wat betekent dat elke extractie- en transformatiestap is samengesteld in een expressiestructuur en wereldwijd geëvalueerd (de dataflow).

Spark-programma's zijn vergelijkbaar omdat u Spark-connectors gebruikt om de gegevens te lezen en de dataframes te maken, vervolgens de transformaties op de dataframes toe te passen met behulp van de LINQ-achtige DSL of SparkSQL, en vervolgens het resultaat in bestanden, tijdelijke Spark-tabellen, sommige programmeertaaltypen of de console.

## <a name="transform-net-code"></a>.NET-code transformeren

U-SQL's expressietaal is C# en biedt verschillende manieren om aangepaste .NET-code uit te schalen.

Aangezien Spark momenteel geen native ondersteuning biedt voor het uitvoeren van .NET-code, moet u uw expressies opnieuw schrijven naar een gelijkwaardige Spark-, Scala-, Java- of Python-expressie of een manier vinden om uw .NET-code aan te roepen. Als uw script .NET-bibliotheken gebruikt, hebt u de volgende opties:

- Vertaal uw .NET-code naar Scala of Python.
- Splits uw U-SQL-script op in verschillende stappen, waarbij u Azure Batch-processen gebruikt om de .NET-transformaties toe te passen (als u acceptabele schaal krijgen)
- Gebruik een .NET-taalbinding die beschikbaar is in Open Source genaamd Moebius. Dit project is niet in een ondersteunde staat.

Als u in ieder geval een grote hoeveelheid .NET-logica in uw U-SQL-scripts hebt, neemt u contact met ons op via uw Microsoft-accountvertegenwoordiger voor verdere begeleiding.

De volgende details zijn voor de verschillende gevallen van .NET- en C#-gebruik in U-SQL-scripts.

### <a name="transform-scalar-inline-u-sql-c-expressions"></a>Scalaire inline U-SQL C#-expressies transformeren

De expressietaal van U-SQL is C#. Veel van de scalaire Inline U-SQL-expressies worden native geïmplementeerd voor betere prestaties, terwijl complexere expressies kunnen worden uitgevoerd door in het .NET-framework aan te roepen.

Spark heeft een eigen scalaire expressietaal (als onderdeel van de DSL of in SparkSQL) en maakt het mogelijk om door de gebruiker gedefinieerde functies aan te roepen die in de hostingtaal zijn geschreven.

Als u scalaire expressies in U-SQL hebt, moet u eerst de meest geschikte native begrepen Spark-scalaire expressie vinden om de meeste prestaties te krijgen en vervolgens de andere expressies in kaart brengen in een door de gebruiker gedefinieerde functie van de Spark-hostingtaal van uw keuze.

Houd er rekening mee dat .NET en C# andere type semantiek hebben dan de Spark-hostingtalen en spark's DSL. Zie [hieronder](#transform-typed-values) voor meer informatie over de verschillen in het typesysteem.

### <a name="transform-user-defined-scalar-net-functions-and-user-defined-aggregators"></a>Door de gebruiker gedefinieerde scalaire .NET-functies en door de gebruiker gedefinieerde aggregators transformeren

U-SQL biedt manieren om willekeurige scalaire .NET-functies aan te roepen en door de gebruiker gedefinieerde aggregators aan te roepen die zijn geschreven in .NET.

Spark biedt ook ondersteuning voor door de gebruiker gedefinieerde functies en door de gebruiker gedefinieerde aggregators die zijn geschreven in de meeste hostingtalen die kunnen worden aangeroepen via Spark's DSL en SparkSQL.

### <a name="transform-user-defined-operators-udos"></a>Door de gebruiker gedefinieerde operatoren (UDO's) transformeren

U-SQL biedt verschillende categorieën van door de gebruiker gedefinieerde operatoren (UDO's), zoals extractors, outputters, reducers, processors, appliers en combiners die kunnen worden geschreven in .NET (en - tot op zekere hoogte - in Python en R).

Spark biedt niet hetzelfde extensibiliteitsmodel voor operators, maar heeft voor sommigen gelijkwaardige mogelijkheden.

De Spark die gelijkwaardig is aan extractors en outputters is Spark-connectoren. Voor veel U-SQL-extractors u een gelijkwaardige connector vinden in de Spark-community. Voor anderen moet je een aangepaste connector schrijven. Als de U-SQL-extractor complex is en gebruik maakt van verschillende .NET-bibliotheken, kan het beter zijn om een connector in Scala te bouwen die interop gebruikt om aan te roepen in de .NET-bibliotheek die de werkelijke verwerking van de gegevens doet. In dat geval moet u de runtime .NET Core implementeren in het Spark-cluster en ervoor zorgen dat de waarnaar verwezen .NET-bibliotheken voldoen aan .NET Standard 2.0.

De andere typen U-SQL UDO's moeten worden herschreven met behulp van door de gebruiker gedefinieerde functies en aggregators en de semantisch geschikte Spark DLS- of SparkSQL-expressie. Een processor kan bijvoorbeeld worden toegewezen aan een select van een verscheidenheid aanudf-aanroepen, verpakt als een functie die een gegevensframe als argument gebruikt en een gegevensframe retourneert.

### <a name="transform-u-sqls-optional-libraries"></a>De optionele bibliotheken van U-SQL transformeren

U-SQL biedt een reeks optionele en demobibliotheken die [Python,](data-lake-analytics-u-sql-python-extensions.md) [R,](data-lake-analytics-u-sql-r-extensions.md) [JSON, XML, AVRO-ondersteuning](https://github.com/Azure/usql/tree/master/Examples/DataFormats)en enkele [mogelijkheden voor cognitieve services](data-lake-analytics-u-sql-cognitive.md)bieden.

Spark biedt zijn eigen Python- en R-integratie, respectievelijk pySpark en SparkR, en biedt connectoren om JSON, XML en AVRO te lezen en te schrijven.

Als u een script wilt transformeren waarin wordt verwezen naar de bibliotheken voor cognitieve services, raden we u aan contact met ons op te nemen via uw Microsoft-accountvertegenwoordiger.

## <a name="transform-typed-values"></a>Getypte waarden transformeren

Omdat het typesysteem van U-SQL is gebaseerd op het .NET-typesysteem en Spark een eigen typesysteem heeft, dat wordt beïnvloed door de hosttaalbinding, moet u ervoor zorgen dat de typen waarop u werkt dicht zijn en voor bepaalde typen de typebereiken, precisie en/of schaal enigszins kunnen verschillen. Bovendien behandelen `null` U-SQL en Spark waarden anders.

### <a name="data-types"></a>Gegevenstypen

In de volgende tabel worden de equivalente typen in Spark, Scala en PySpark voor de opgegeven U-SQL-typen vermeld.

| U-SQL | Spark |  Scala | PySpark (PySpark) |
| ------ | ------ | ------ | ------ |
|`byte`       ||||
|`sbyte`      |`ByteType` |`Byte` | `ByteType`|
|`int`        |`IntegerType` |`Int` | `IntegerType`|
|`uint`       ||||
|`long`       |`LongType` |`Long` | `LongType`|
|`ulong`      ||||
|`float`      |`FloatType` |`Float` | `FloatType`|
|`double`     |`DoubleType` |`Double` | `DoubleType`|
|`decimal`    |`DecimalType` |`java.math.BigDecimal` | `DecimalType`|
|`short`      |`ShortType` |`Short` | `ShortType`|
|`ushort`     ||||
|`char`   | |`Char`||
|`string` |`StringType` |`String` |`StringType` |
|`DateTime`   |`DateType`, `TimestampType` |`java.sql.Date`, `java.sql.Timestamp` | `DateType`, `TimestampType`|
|`bool`   |`BooleanType` |`Boolean` | `BooleanType`|
|`Guid`   ||||
|`byte[]` |`BinaryType` |`Array[Byte]` | `BinaryType`|
|`SQL.MAP<K,V>`   |`MapType(keyType, valueType, valueContainsNull)` |`scala.collection.Map` | `MapType(keyType, valueType, valueContainsNull=True)`|
|`SQL.ARRAY<T>`   |`ArrayType(elementType, containsNull)` |`scala.collection.Seq` | `ArrayType(elementType, containsNull=True)`|

Zie voor meer informatie:

- [org.apache.spark.sql.types](https://spark.apache.org/docs/latest/api/scala/index.html#org.apache.spark.sql.types.package)
- [Spark SQL- en DataFrames-typen](https://spark.apache.org/docs/latest/sql-reference.html#data-types)
- [Scala-waardetypen](https://www.scala-lang.org/api/current/scala/AnyVal.html)
- [pyspark.sql.types](https://spark.apache.org/docs/latest/api/python/pyspark.sql.html#module-pyspark.sql.types)

### <a name="treatment-of-null"></a>Behandeling van NULL

In Spark staan typen per standaard NULL-waarden toe, terwijl u in U-SQL expliciet scalaire, niet-object markeert als nullable. Hoewel Spark u toestaat om een kolom als niet tenietbaar te definiëren, zal deze de beperking niet afdwingen en [kan dit leiden tot een verkeerd resultaat.](https://medium.com/@weshoffman/apache-spark-parquet-and-troublesome-nulls-28712b06f836)

In Spark geeft NULL aan dat de waarde onbekend is. Een Spark NULL-waarde verschilt van elke waarde, inclusief zichzelf. Vergelijkingen tussen twee Spark NULL-waarden, of tussen een NULL-waarde en een andere waarde, retourneren onbekend omdat de waarde van elke NULL onbekend is.  

Dit gedrag is anders dan U-SQL, die `null` C# semantiek volgt waar verschilt van elke waarde, maar gelijk is aan zichzelf.  

`SELECT` Een SparkSQL-instructie `WHERE column_name = NULL` die nul rijen gebruikt, zodat `column_name`er NULL-waarden zijn in , `column_name` terwijl deze `null`in U-SQL is ingesteld, de rijen waar deze is ingesteld op . Op dezelfde manier `SELECT` retourneert `WHERE column_name != NULL` een Spark-instructie nulrijen, `column_name`zelfs als er niet-null-waarden zijn in , terwijl deze in U-SQL de rijen retourneert die niet-null hebben. Dus, als u wilt dat de U-SQL null-check semantiek, moet u gebruik maken [van isnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnull) en [isnotnull](https://spark.apache.org/docs/2.3.0/api/sql/index.html#isnotnull) respectievelijk (of hun DSL-equivalent).

## <a name="transform-u-sql-catalog-objects"></a>U-SQL-catalogusobjecten transformeren

Een groot verschil is dat U-SQL Scripts gebruik kunnen maken van de catalogusobjecten, waarvan er vele geen direct Spark-equivalent hebben.

Spark biedt wel ondersteuning voor de Hive Meta-winkelconcepten, voornamelijk databases en tabellen, zodat u U-SQL-databases en -schema's toewijzen aan Hive-databases en U-SQL-tabellen naar Spark-tabellen (zie [Bewegende gegevens die zijn opgeslagen in U-SQL-tabellen),](understand-spark-data-formats.md#move-data-stored-in-u-sql-tables)maar het heeft geen ondersteuning voor weergaven, functies met tabelwaarde (TVF's), opgeslagen procedures, U-SQL-assemblages, externe gegevensbronnen enz.

De U-SQL-codeobjecten zoals weergaven, TVF's, opgeslagen procedures en samenstellingen kunnen worden gemodelleerd via codefuncties en bibliotheken in Spark en worden verwezen met behulp van de functie en procedurele abstractiemechanismen van de hosttaal (bijvoorbeeld door het importeren van Python-modules of verwijzende Scala-functies).

Als de U-SQL-catalogus is gebruikt om gegevens en codeobjecten te delen in projecten en teams, moeten gelijkwaardige mechanismen voor delen worden gebruikt (bijvoorbeeld Maven voor het delen van codeobjecten).

## <a name="transform-u-sql-rowset-expressions-and-sql-based-scalar-expressions"></a>U-SQL-rijsetexpressies en SQL-gebaseerde scalaire expressies transformeren

De kerntaal van U-SQL transformeert rijsets en is gebaseerd op SQL. Het volgende is een niet-limitatieve lijst van de meest voorkomende rowset-expressies die in U-SQL worden aangeboden:

- `SELECT`/`FROM`/`WHERE`/`GROUP BY`+Aggregaten+`HAVING`/`ORDER BY`+`FETCH`
- `INNER`/`OUTER`/`CROSS`/`SEMI``JOIN` expressies
- `CROSS`/`OUTER``APPLY` expressies
- `PIVOT`/`UNPIVOT`Expressies
- `VALUES`rijsetconstructor

- Expressies instellen`UNION`/`OUTER UNION`/`INTERSECT`/`EXCEPT`

Daarnaast biedt U-SQL een verscheidenheid aan SQL-gebaseerde scalaire expressies, zoals

- `OVER`vensterexpressies
- een verscheidenheid aan ingebouwde aggregators`SUM`en `FIRST` ranking functies ( , enz.)
- Enkele van de meest bekende `CASE`SQL `LIKE`scalar `IN` `AND`expressies: , , ( `OR` `NOT`) , , enz.

Spark biedt voor de meeste van deze expressies gelijkwaardige expressies in zowel de DSL- als SparkSQL-vorm. Sommige van de expressies die niet native worden ondersteund in Spark, moeten worden herschreven met behulp van een combinatie van de oorspronkelijke Spark-expressies en semantisch gelijkwaardige patronen. Zo moet `OUTER UNION` bijvoorbeeld worden vertaald in de gelijkwaardige combinatie van projecties en vakbonden.

Vanwege de verschillende verwerking van NULL-waarden zal een U-SQL-join altijd overeenkomen met een rij als beide kolommen die worden vergeleken een NULL-waarde bevatten, terwijl een join in Spark niet overeenkomt met dergelijke kolommen, tenzij expliciete null-controles worden toegevoegd.

## <a name="transform-other-u-sql-concepts"></a>Andere U-SQL-concepten transformeren

U-SQL biedt ook een verscheidenheid aan andere functies en concepten, zoals federatieve query's tegen SQL Server-databases, parameters, scalar- en lambdaexpressievariabelen, systeemvariabelen, `OPTION` hints.

### <a name="federated-queries-against-sql-server-databasesexternal-tables"></a>Federatieve query's tegen SQL Server-databases/externe tabellen

U-SQL biedt gegevensbron- en externe tabellen en directe query's tegen Azure SQL Database. Hoewel Spark niet dezelfde objectabstracties biedt, biedt het [Spark-connector voor Azure SQL Database](../sql-database/sql-database-spark-connector.md) die kan worden gebruikt om SQL-databases op te vragen.

### <a name="u-sql-parameters-and-variables"></a>U-SQL-parameters en -variabelen

Parameters en gebruikersvariabelen hebben gelijkwaardige concepten in Spark en hun hostingtalen.

In Scala u bijvoorbeeld een `var` variabele definiëren met het trefwoord:

```
var x = 2 * 3;
println(x)
```

De systeemvariabelen van U-SQL (variabelen `@@`die beginnen met ) kunnen in twee categorieën worden opgesplitst:

- Settable systeemvariabelen die kunnen worden ingesteld op specifieke waarden om het scriptsgedrag te beïnvloeden
- Informatiesysteemvariabelen die informatie over systeem- en taakniveau onderzoeken

De meeste settable systeemvariabelen hebben geen direct equivalent in Spark. Sommige van de variabelen van het informatieve systeem kunnen worden gemodelleerd door de informatie door te geven als argumenten tijdens taakuitvoering, andere kunnen een gelijkwaardige functie hebben in de hostingtaal van Spark.

### <a name="u-sql-hints"></a>U-SQL-hints

U-SQL biedt verschillende syntactische manieren om hints te geven aan de queryoptimizer en execution engine:  

- Een U-SQL-systeemvariabele instellen
- een `OPTION` clausule die is gekoppeld aan de rijsetexpressie om een gegevens- of planhint te geven
- een joinhint in de syntaxis van `BROADCASTLEFT`de joinexpressie (bijvoorbeeld )

Spark's op kosten gebaseerde queryoptimizer heeft zijn eigen mogelijkheden om hints te geven en de queryprestaties af te stemmen. Raadpleeg de bijbehorende documentatie.

## <a name="next-steps"></a>Volgende stappen

- [Spark-gegevensindelingen voor U-SQL-ontwikkelaars begrijpen](understand-spark-data-formats.md)
- [.NET voor Apache Spark](https://docs.microsoft.com/dotnet/spark/what-is-apache-spark-dotnet)
- [Upgrade uw oplossingen voor big data-analyse van Azure Data Lake Storage Gen1 naar Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md)
- [Gegevens transformeren met Spark-activiteit in Azure Data Factory](../data-factory/transform-data-using-spark.md)
- [Gegevens transformeren met Hadoop Hive-activiteit in Azure Data Factory](../data-factory/transform-data-using-hadoop-hive.md)
- [Wat is Apache Spark in Azure HDInsight?](../hdinsight/spark/apache-spark-overview.md)

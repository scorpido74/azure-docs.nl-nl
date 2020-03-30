---
title: Analyseer &-proces JSON met Apache Hive - Azure HDInsight
description: Meer informatie over het gebruik van JSON-documenten en deze analyseren met Apache Hive in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/29/2019
ms.openlocfilehash: 1c519533625835677ddae0a274c9ce9f10edc6dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73098004"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Json-documenten verwerken en analyseren met Apache Hive in Azure HDInsight

Meer informatie over het verwerken en analyseren van Json-bestanden (JavaScript Object Notation) met Apache Hive in Azure HDInsight. In dit artikel wordt het volgende JSON-document gebruikt:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

Het bestand is `wasb://processjson@hditutorialdata.blob.core.windows.net/`te vinden op . Zie [HDFS-compatibele Azure Blob-opslag gebruiken met Apache Hadoop in HDInsight](../hdinsight-hadoop-use-blob-storage.md)voor meer informatie over het gebruik van Azure Blob-opslag met HDInsight. U het bestand kopiëren naar de standaardcontainer van uw cluster.

In dit artikel gebruikt u de Apache Hive-console. Zie [Apache Ambari Hive View gebruiken met Apache Hadoop in HDInsight](apache-hadoop-use-hive-ambari-view.md)voor instructies over het openen van de Hive-console.

## <a name="flatten-json-documents"></a>JSON-documenten afvlakken

De methoden in de volgende sectie vereisen dat het JSON-document uit één rij bestaat. U moet het JSON-document dus platmaken tot een tekenreeks. Als uw JSON-document al is afgevlakt, u deze stap overslaan en rechtstreeks naar de volgende sectie over het analyseren van JSON-gegevens gaan. Als u het JSON-document wilt afvlakken, voert u het volgende script uit:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

Het raw JSON-bestand `wasb://processjson@hditutorialdata.blob.core.windows.net/`bevindt zich op . De tabel **StudentsRaw** Hive verwijst naar het ruwe JSON-document dat niet is afgevlakt.

De **tabel StudentsOneLine** Hive slaat de gegevens op in het standaardbestandssysteem HDInsight onder het **/json/students/pad.**

Met de instructie **INSERT** wordt de tabel **StudentOneLine** gevuld met de afgevlakte JSON-gegevens.

De **instructie SELECT** retourneert slechts één rij.

Hier is de uitvoer van de **SELECT-instructie:**

![HDInsight vlakt het JSON-document af](./media/using-json-in-hive/hdinsight-flatten-json.png)

## <a name="analyze-json-documents-in-hive"></a>Json-documenten analyseren in Hive

Hive biedt drie verschillende mechanismen om query's uit te voeren op JSON-documenten, of u uw eigen tekst schrijven:

* Gebruik de get_json_object door de gebruiker gedefinieerde functie (UDF).
* Gebruik de json_tuple UDF.
* Gebruik de aangepaste Serializer/Deserializer (SerDe).
* Schrijf uw eigen UDF met behulp van Python of andere talen. Zie [Python UDF met Apache Hive voor](./python-udf-hdinsight.md)meer informatie over het uitvoeren van uw eigen Python-code met Hive.

### <a name="use-the-get_json_object-udf"></a>Gebruik de get_json_object UDF

Hive biedt een ingebouwde UDF genaamd [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) die JSON-query's tijdens runtime kan uitvoeren. Deze methode heeft twee argumenten: de tabelnaam en de naam van de methode, die het afgevlakte JSON-document en het JSON-veld heeft dat moet worden ontleed. Laten we eens kijken naar een voorbeeld om te zien hoe deze UDF werkt.

In de volgende query worden de voor- en achternaam voor elke student geretourneerd:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Hier is de uitvoer wanneer u deze query uitvoert in het consolevenster:

![Apache Hive krijgen json object UDF](./media/using-json-in-hive/hdinsight-get-json-object.png)

Er zijn beperkingen van de get_json_object UDF:

* Omdat elk veld in de query herstel van de query vereist, heeft dit invloed op de prestaties.
* **GET\_JSON_OBJECT()** retourneert de tekenreeksweergave van een array. Als u deze array wilt converteren naar een Hive-array, moet u reguliere expressies gebruiken om de vierkante haakjes "[" en "]" te vervangen, en dan moet u ook split aanroepen om de array te krijgen.

Dit is de reden waarom de Hive wiki aanbeveelt dat je **json_tuple**gebruikt.  

### <a name="use-the-json_tuple-udf"></a>De json_tuple UDF gebruiken

Een andere UDF die door Hive wordt genoemd [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), die beter presteert dan [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Deze methode neemt een set sleutels en een JSON-tekenreeks en retourneert een tuple waarden met één functie. De volgende query retourneert de studenten-id en het cijfer uit het JSON-document:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

De uitvoer van dit script in de Hive-console:

![Resultaten van Apache Hive json-query's](./media/using-json-in-hive/hdinsight-json-tuple.png)

De json_tuple UDF gebruikt de [syntaxis van](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) \_de laterale weergave in Hive, waarmee json tuple een virtuele tabel kan maken door de UDT-functie toe te passen op elke rij van de oorspronkelijke tabel. Complexe JSON's worden te log door het herhaalde gebruik van **LATERAL VIEW**. Bovendien kunnen **JSON_TUPLE** niet overweg met geneste JSONs.

### <a name="use-a-custom-serde"></a>Een aangepaste SerDe gebruiken

SerDe is de beste keuze voor het ontleden van geneste JSON-documenten. Hiermee u het JSON-schema definiëren en vervolgens u het schema gebruiken om de documenten te ontleden. Zie Een [aangepaste JSON SerDe gebruiken met Microsoft Azure HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/)voor instructies.

## <a name="summary"></a>Samenvatting

Tot slot, het type JSON-operator in Hive dat u kiest, is afhankelijk van uw scenario. Als u een eenvoudig JSON-document hebt en u slechts één veld hebt om op te zoeken, u ervoor kiezen om de Hive **UDF-get_json_object**te gebruiken. Als u meer dan één sleutel hebt om op te zoeken, u **json_tuple**gebruiken. Als u een genest document hebt, moet u de **JSON SerDe**gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie voor gerelateerde artikelen:

* [Gebruik Apache Hive en HiveQL met Apache Hadoop in HDInsight om een voorbeeld Apache log4j-bestand te analyseren](../hdinsight-use-hive.md)
* [Gegevens over vluchtvertraging analyseren met behulp van Interactieve Query in HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
* [Analyseer Twitter-gegevens met Apache Hive in HDInsight](../hdinsight-analyze-twitter-data-linux.md)

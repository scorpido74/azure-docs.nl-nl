---
title: Schema analyseren met matrices en geneste structuren
description: Matrices en geneste structuren analyseren met Apache Spark en SQL
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 06/15/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: fdf3dc56575a45ad0c9e716054184ba2691133ba
ms.sourcegitcommit: 2ff0d073607bc746ffc638a84bb026d1705e543e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87831699"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Complexe gegevens typen analyseren in azure Synapse Analytics

Dit artikel is relevant voor Parquet-bestanden en-containers in de [Synapse-koppeling voor Azure Cosmos DB](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md). Hierin wordt uitgelegd hoe gebruikers Spark of SQL kunnen gebruiken om gegevens te lezen of te transformeren met complexe schema's zoals matrices of geneste structuren. Het volgende voor beeld wordt met één document voltooid, maar kan eenvoudig worden geschaald naar miljarden documenten met Spark of SQL. De code die in dit artikel is opgenomen, maakt gebruik van PySpark (python).

## <a name="use-case"></a>Toepassing

Complexe gegevens typen worden steeds vaker gebruikt en vormen een uitdaging voor gegevens technici, zoals het analyseren van geneste schema's en matrices bevat vaak tijdrovende en complexe SQL-query's. Het kan ook lastig zijn om het gegevens type geneste kolommen te hernoemen of te casten. Prestatie problemen doen zich ook voor bij het werken met diep geneste objecten.

Data engineers moeten begrijpen hoe complexe gegevens typen efficiënt kunnen worden verwerkt en eenvoudig toegankelijk zijn voor iedereen.

In het volgende voor beeld wordt Synapse Spark gebruikt voor het lezen en transformeren van objecten naar een platte structuur via gegevens frames. Synapse SQL Server wordt gebruikt om dergelijke objecten direct te doorzoeken en deze resultaten als een normale tabel te retour neren.

## <a name="what-are-arrays-and-nested-structures"></a>Wat zijn matrices en geneste structuren?

Het volgende object is afkomstig uit [app Insight](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). In dit object zijn geneste structuren en matrices die geneste structuren bevatten.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>Schema voorbeeld van matrices en geneste structuren
Wanneer u het schema van het gegevens frame van het object ( **DF**) afdrukt met de opdracht `df.printschema` , zien we de volgende representatie:

* Gele kleur vertegenwoordigt geneste structuur
* Een groene kleur vertegenwoordigt een matrix met twee elementen

[![Schema oorsprong](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

**_rid**, **_ts**en **_etag** zijn aan het systeem toegevoegd als het document is opgenomen in azure Cosmos DB transactionele Store.

Het bovenstaande gegevens frame telt vijf kolommen en één rij alleen. Na de trans formatie bevat het gegevens frame met de curator 13 kolommen en twee rijen in tabel vorm.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Geneste structuren platen en matrices exploderen met Apache Spark

Met Synapse Spark kunt u eenvoudig geneste structuren omzetten in kolommen en matrix elementen in meerdere rijen. De volgende stappen kunnen worden gebruikt voor de implementatie.

[![Stappen voor mousserende trans formaties](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

**Stap 1**: we definiëren een functie voor het afvlakken van het geneste schema. Deze functie kan zonder wijziging worden gebruikt. Maak een cel in een [PySpark-notebook](quickstart-apache-spark-notebook.md) met de volgende functie:

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

**Stap 2**: gebruik de functie voor het afvlakken van het geneste schema van het gegevens frame (**DF**) in een nieuw gegevens frame `df_flat` :

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

De weergave functie moet 10 kolommen en één rij retour neren. De matrix en de geneste elementen ervan zijn nog steeds beschikbaar.

**Stap 3**: de matrix `context_custom_dimensions` in het gegevens frame transformeren `df_flat` naar een nieuwe data frame `df_flat_explode` . In de volgende code definiëren we ook welke kolom moet worden geselecteerd:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

De weergave functie moet 10 kolommen en twee rijen retour neren. De volgende stap is het samen voegen van geneste schema's met de functie die u in stap 1 hebt gedefinieerd.

**Stap 4**: gebruik de functie voor het afvlakken van het geneste schema van het gegevens frame `df_flat_explode` in een nieuw gegevens frame `df_flat_explode_flat` :
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

De weergave functie moet 13 kolommen en twee rijen weer geven.

De functie `printSchema` van het gegevens frame `df_flat_explode_flat` retourneert het volgende resultaat:

[![Definitief schema](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Matrices en geneste structuren rechtstreeks lezen met SQL serverloos

Het uitvoeren van query's en het maken van weer gaven en tabellen via dergelijke objecten is mogelijk met SQL serverloos.

Eerst moeten gebruikers de volgende taxonomie gebruiken, afhankelijk van de manier waarop de gegevens zijn opgeslagen. Alles wat in hoofd letters wordt weer gegeven, is specifiek voor uw use-case:

| Loss              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = AccountName; data base = DATABASENAME; verzameling = verzamelingnaam; Region = REGIONTOQUERY ', geheim = ' YOURSECRET ' |' CosmosDB ' (Synapse-koppeling)|


> [!NOTE]
> SQL Server wordt ondersteund voor de koppeling van de gekoppelde service voor Synapse voor Azure Cosmos en AAD passthrough. De mogelijkheid is momenteel onder gated Preview voor Synapse-koppeling.

Vervang elk veld als volgt:
* ' Uw BULKSGEWIJZE boven ' = de connection string van de gegevens bron waarmee u verbinding maakt
* ' Uw TYPE boven ' = de indeling die u gebruikt om verbinding te maken met de bron

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

Er zijn twee verschillende soorten bewerkingen:

Het eerste bewerkings type wordt aangegeven in de volgende regel code, waarmee de kolom wordt gedefinieerd `contextdataeventTime` die verwijst naar het geneste element: context. data. eventTime 
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Deze regel definieert de kolom met de naam contextdataeventTime die verwijst naar het nest element: context>data>eventTime

Het tweede bewerkings type gebruikt `cross apply` voor het maken van nieuwe rijen voor elk element onder de matrix en vervolgens definieert elk genest object dat lijkt op het eerste punt in de opsomming: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Als de matrix vijf elementen met 4 geneste structuren had, retourneert SQL serverloze 5 rijen en 4 kolommen. SQL Server kan een query in-place uitvoeren, de matrix toewijzen in twee rijen en alle geneste structuren in kolommen weer geven.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het opvragen van de Synapse-koppeling voor Azure Cosmos DB met Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet geneste typen opvragen](./sql/query-parquet-nested-types.md) 

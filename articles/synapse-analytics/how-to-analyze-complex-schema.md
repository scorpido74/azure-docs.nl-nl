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
ms.openlocfilehash: b02c3627cea5e441739c77d1882505c6b82489bc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84908120"
---
# <a name="analyze-complex-data-types-in-synapse"></a>Complexe gegevens typen in Synapse analyseren

Dit artikel is relevant voor Parquet-bestanden en-containers in **Azure Synapse-koppeling voor Azure Cosmos DB**. Hierin wordt uitgelegd hoe gebruikers Spark of SQL kunnen gebruiken om gegevens te lezen of te transformeren met een complex schema, zoals matrices of geneste structuren. Het volgende voor beeld wordt uitgevoerd met één document, maar kan eenvoudig worden geschaald naar miljarden documenten met Spark of SQL. In de onderstaande code wordt PySpark (python) gebruikt.

## <a name="use-case"></a>Use-case

Met moderne gegevens typen zijn complexe gegevens typen vaak gebruikelijk en vertegenwoordigen ze een uitdaging voor gegevens technici. Het analyseren van genest schema en matrices bevat enkele uitdagingen:
* Complex om SQL-query's te schrijven
* Het is lastig om het gegevens type van geneste kolommen te wijzigen
* Problemen met prestaties treffers met diep geneste objecten

Data engineers moeten begrijpen hoe deze gegevens typen efficiënt kunnen worden verwerkt en ze gemakkelijk toegankelijk zijn voor iedereen.

In het onderstaande voor beeld wordt Synapse Spark gebruikt voor het lezen en transformeren van objecten via gegevens frames naar een vlakke structuur. Synapse SQL Server wordt gebruikt om direct objecten op te vragen en deze resultaten te retour neren als een normale tabel.

## <a name="what-are-arrays-and-nested-structures"></a>Wat zijn matrices en geneste structuren?

Het volgende object is afkomstig uit app Insight. In dit object zijn geneste structuren, maar ook matrices die geneste structuren bevatten.

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
Bij het afdrukken van het schema van het gegevens frame van dat object ( **DF**genoemd) met de opdracht **DF. printschema**, zien we de volgende representatie:

* de gele kleur vertegenwoordigt geneste structuur
* de groene kleur vertegenwoordigt een matrix met twee elementen

[![Schema oorsprong](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

_rid, _ts en _etag zijn toegevoegd aan het systeem als het document is opgenomen in Azure Cosmos DB transactionele Store.

Het bovenstaande gegevens frame telt vijf kolommen en één rij alleen. Na de trans formatie bevat het gegevens frame met de curator 13 kolommen en twee rijen in tabel vorm.

## <a name="flatten-nested-structures-and-explode-arrays-with-apache-spark"></a>Geneste structuren platen en matrices exploderen met Apache Spark

Met Synapse Spark kunt u eenvoudig geneste structuren transformeren naar kolommen en matrix elementen in meerdere rijen. De onderstaande stappen kunnen door iedereen worden gebruikt voor hun eigen implementatie.

[![Stappen voor mousserende trans formaties](./media/how-to-complex-schema/spark-transfo-steps.png)](./media/how-to-complex-schema/spark-transfo-steps.png#lightbox)

**Stap 1**: we definiëren een functie voor het afvlakking van genest schema. Deze functie kan zonder wijziging worden gebruikt. Maak een cel in een Pyspark-notebook met die functie:

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

**Stap 2**: gebruik de functie voor het afvlakken van het geneste schema van het gegevens frame **VG** in een nieuw gegevens frame **df_flat**:

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

De weergave functie moet 10 kolommen en één rij retour neren. De matrix en de geneste elementen ervan zijn nog steeds beschikbaar.

**Stap 3**: we transformeren nu de matrix **context_custom_dimensions** in het gegevens frame **df_flat** naar een nieuwe data frame **df_flat_explode**. In de onderstaande code definiëren we ook welke kolom er wordt geselecteerd:

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

De weergave functie moet het volgende resultaat retour neren: 10 kolommen en 2 rijen. De volgende stap is het samen voegen van geneste schema's met de functie die u in stap 1 hebt gedefinieerd.

**Stap 4**: gebruik de functie voor het afvlakken van het geneste schema van het gegevens frame **df_flat_explode** in een nieuw gegevens frame **df_flat_explode_flat**:
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

De weergave functie moet 13 kolommen en 2 rijen weer geven:

De functie printSchema van het gegevens frame df_flat_explode_flat retourneert het volgende resultaat:

[![Definitief schema](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly-with-sql-serverless"></a>Matrices en geneste structuren rechtstreeks lezen met SQL serverloos

Het maken van query's, weer gaven en tabellen over dergelijke objecten is mogelijk met SQL serverloos.

Eerst moeten gebruikers de volgende taxonomie gebruiken, afhankelijk van de manier waarop gegevens zijn opgeslagen. Alle hoofd letters zijn specifiek voor uw use-case:

| Loss              | FORMAT |
| -------------------- | --- |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |' Parquet ' (ADLSg2)|
| N'endpoint = https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/ ; account = AccountName; data base = DATABASENAME; verzameling = verzamelingnaam; Region = REGIONTOQUERY, geheim = ' YOURSECRET ' |' CosmosDB ' (Synapse-koppeling)|



**SQL Server** biedt ondersteuning voor de gekoppelde service voor Azure Synapse-koppeling voor Azure Cosmos DB en Aad passthrough. De mogelijkheid is momenteel onder gated Preview voor Synapse-koppeling.

Vervangen:
* ' Uw BULK waarde hierboven ' door de connection string van de gegevens bron waarmee u verbinding maakt
* ' Uw TYPE hierboven ' op basis van de indeling die u gebruikt om verbinding te maken met de bron

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

Er zijn twee verschillende soorten bewerkingen uitgevoerd:
* In de onderstaande regel code wordt de kolom gedefinieerd met de naam contextdataeventTime die verwijst naar het geneste element: context. data. eventTime
```sql
contextdataeventTime varchar(50) '$.context.data.eventTime'
```

Deze regel definieert de kolom met de naam contextdataeventTime die verwijst naar het nest element: context>data>eventTime

* **Apply** wordt gebruikt voor het maken van nieuwe rijen voor elk element onder de matrix en definieert vervolgens elk genest object dat lijkt op het eerste punt in de opsomming: 
```sql
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
```

Als de matrix vijf elementen met 4 geneste structuren had, retourneert SQL serverloze 5 rijen en 4 kolommen.

SQL Server kan een query in-place uitvoeren, de matrix toewijzen in twee rijen en alle geneste structuren in kolommen weer geven.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over het opvragen van een Azure Synapse-koppeling voor Azure Cosmos DB met Spark](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet geneste typen opvragen](./sql/query-parquet-nested-types.md) 
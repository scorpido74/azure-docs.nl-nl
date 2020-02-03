---
title: Gegevens verkennen in Hive-tabellen met Hive-query's - Team Data Science Process
description: Gebruik de voorbeeld-Hive-scripts die worden gebruikt om gegevens in Hive-tabellen in een HDInsight Hadoop-cluster te verkennen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75dce2b5a83d13fe4a7d166595456e9a8d6324ba
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722166"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Gegevens in Hive-tabellen verkennen met Hive-query's

Dit artikel bevat een voorbeeld van Hive-scripts die worden gebruikt om gegevens in Hive-tabellen in een HDInsight Hadoop-cluster te verkennen.

Deze taak is een stap in het [team data Science process](overview.md).

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u hebt:

* Een Azure storage-account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Een aangepaste Hadoop-cluster met de HDInsight-service wordt ingericht. Zie [Azure HDInsight Hadoop clusters aanpassen voor geavanceerde analyse](customize-hadoop-cluster.md)als u instructies nodig hebt.
* De gegevens is geüpload naar de Hive-tabellen in Azure HDInsight Hadoop-clusters. Als dat niet het geval is, volgt u de instructies in [maken en gegevens laden in Hive-tabellen](move-hive-tables.md) om eerst gegevens te uploaden naar Hive-tabellen.
* Externe toegang tot het cluster ingeschakeld. Zie [toegang tot het hoofd knooppunt van het Hadoop-cluster](customize-hadoop-cluster.md)als u instructies nodig hebt.
* Zie [Hive-Query's verzenden](move-hive-tables.md#submit) als u instructies nodig hebt voor het verzenden van Hive-query's.

## <a name="example-hive-query-scripts-for-data-exploration"></a>Voorbeeld van de Hive-query scripts voor gegevens verkennen
1. Het aantal observaties per partitie ophalen `SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Het aantal observaties per dag ophalen `SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. De niveaus in een categorische kolom ophalen  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Het aantal niveaus in combi natie van twee categorische kolommen ophalen `SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. De verdeling van de numerieke kolommen ophalen  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Records ophalen uit het koppelen van twee tabellen
   
        SELECT
            a.<common_columnname1> as <new_name1>,
            a.<common_columnname2> as <new_name2>,
            a.<a_column_name1> as <new_name3>,
            a.<a_column_name2> as <new_name4>,
            b.<b_column_name1> as <new_name5>,
            b.<b_column_name2> as <new_name6>
        FROM
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <a_column_name1>,
                <a_column_name2>,
            FROM <databasename>.<tablename1>
            ) a
            join
            (
            SELECT <common_columnname1>,
                <common_columnname2>,
                <b_column_name1>,
                <b_column_name2>,
            FROM <databasename>.<tablename2>
            ) b
            ON a.<common_columnname1>=b.<common_columnname1> and a.<common_columnname2>=b.<common_columnname2>

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Aanvullende query-scripts voor scenario's reis voor gegevens over taxi 's
Er worden ook voor beelden gegeven van query's die specifiek zijn voor [NYC-gegevens](https://chriswhong.com/open-data/foil_nyc_taxi/) Scenario's voor taxi's in de [github-opslag plaats](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's al hebben gegevensschema opgegeven en kunnen worden verzonden om uit te voeren.


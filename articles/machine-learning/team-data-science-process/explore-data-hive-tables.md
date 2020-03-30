---
title: Gegevens verkennen in Hive-tabellen met Hive-query's - Team Data Science Process
description: Gebruik voorbeeldhive-scripts die worden gebruikt om gegevens in Hive-tabellen in een HDInsight Hadoop-cluster te verkennen.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76722166"
---
# <a name="explore-data-in-hive-tables-with-hive-queries"></a>Gegevens in Hive-tabellen verkennen met Hive-query's

In dit artikel vindt u voorbeeldhive-scripts die worden gebruikt om gegevens in Hive-tabellen in een HDInsight Hadoop-cluster te verkennen.

Deze taak is een stap in het [Team Data Science Process.](overview.md)

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u:

* Een Azure-opslagaccount maken. Als u instructies nodig hebt, [raadpleegt u Een Azure Storage-account maken](../../storage/common/storage-account-create.md)
* Ingericht een aangepaste Hadoop cluster met de HDInsight service. Zie [Azure HDInsight Hadoop Clusters voor geavanceerde analyse aanpassen](customize-hadoop-cluster.md)als u instructies nodig hebt.
* De gegevens zijn geüpload naar Hive-tabellen in Azure HDInsight Hadoop-clusters. Als dit niet het zo is, volgt u de instructies in [Tabellen maken en laden naar Hive-tabellen](move-hive-tables.md) om eerst gegevens naar Hive-tabellen te uploaden.
* Ingeschakelde externe toegang tot het cluster. Zie Toegang tot [het hoofdknooppunt van hadoopcluster](customize-hadoop-cluster.md)als u instructies nodig hebt.
* Als u instructies nodig hebt voor het indienen van Hive-query's, raadpleegt [u Hive-query's indienen](move-hive-tables.md#submit)

## <a name="example-hive-query-scripts-for-data-exploration"></a>Voorbeeld hivequeryscripts voor gegevensverkenning
1. Het aantal waarnemingen per partitie ophalen`SELECT <partitionfieldname>, count(*) from <databasename>.<tablename> group by <partitionfieldname>;`
2. Haal het aantal waarnemingen per dag op`SELECT to_date(<date_columnname>), count(*) from <databasename>.<tablename> group by to_date(<date_columnname>);`
3. De niveaus in een categorische kolom opbrengen  
    `SELECT  distinct <column_name> from <databasename>.<tablename>`
4. Het aantal niveaus in combinatie met twee categorische kolommen`SELECT <column_a>, <column_b>, count(*) from <databasename>.<tablename> group by <column_a>, <column_b>`
5. De verdeling voor numerieke kolommen opvragen  
    `SELECT <column_name>, count(*) from <databasename>.<tablename> group by <column_name>`
6. Records extraheren uit het samenvoegen van twee tabellen
   
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

## <a name="additional-query-scripts-for-taxi-trip-data-scenarios"></a>Aanvullende queryscripts voor scenario's voor taxiritten
Voorbeelden van query's die specifiek zijn voor [NYC Taxi Trip Data](https://chriswhong.com/open-data/foil_nyc_taxi/) scenario's zijn ook opgenomen in [GitHub repository](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts). Deze query's hebben al een gegevensschema opgegeven en zijn klaar om te worden verzonden om uit te voeren.


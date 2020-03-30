---
title: Azure Data Lake Storage Gen1 analyseren met HDInsight Apache Spark
description: Apache Spark-taken uitvoeren om gegevens te analyseren die zijn opgeslagen in Azure Data Lake Storage Gen1
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/13/2019
ms.openlocfilehash: f7a6ab954aff1bcc2e3dae3fc035db4b136ccbbe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73818167"
---
# <a name="use-hdinsight-spark-cluster-to-analyze-data-in-data-lake-storage-gen1"></a>HdInsight Spark-cluster gebruiken om gegevens te analyseren in Data Lake Storage Gen1

In dit artikel gebruikt u [Jupyter Notebook](https://jupyter.org/) dat beschikbaar is met HDInsight Spark-clusters om een taak uit te voeren die gegevens van een Data Lake Storage-account leest.

## <a name="prerequisites"></a>Vereisten

* Azure Data Lake Storage Gen1-account. Volg de instructies bij [Aan de slag met Azure Data Lake Storage Gen1 met behulp van de Azure-portal.](../../data-lake-store/data-lake-store-get-started-portal.md)

* Azure HDInsight Spark-cluster met Data Lake Storage Gen1 als opslag. Volg de instructies bij [Quickstart: Clusters instellen in HDInsight](../../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

## <a name="prepare-the-data"></a>De gegevens voorbereiden

> [!NOTE]  
> U hoeft deze stap niet uit te voeren als u het HDInsight-cluster hebt gemaakt met Data Lake Storage als standaardopslag. Het proces voor het maken van het cluster voegt enkele voorbeeldgegevens toe aan het Data Lake Storage-account dat u opgeeft tijdens het maken van het cluster. Ga naar de sectie HDInsight Spark-cluster gebruiken met Data Lake Storage.

Als u een HDInsight-cluster hebt gemaakt met Data Lake Storage als extra opslag en Azure Storage Blob als standaardopslag, moet u eerst enkele voorbeeldgegevens kopiëren naar het Data Lake Storage-account. U de voorbeeldgegevens van de Azure Storage Blob gebruiken die is gekoppeld aan het HDInsight-cluster. U hiervoor het [adlcopy-hulpprogramma](https://www.microsoft.com/download/details.aspx?id=50358) gebruiken. Download en installeer het hulpprogramma via de koppeling.

1. Open een opdrachtprompt en navigeer naar de map waar `%HOMEPATH%\Documents\adlcopy`AdlCopy is geïnstalleerd, meestal .

2. Voer de volgende opdracht uit om een specifieke blob van de broncontainer naar Data Lake Storage te kopiëren:

        AdlCopy /source https://<source_account>.blob.core.windows.net/<source_container>/<blob name> /dest swebhdfs://<dest_adls_account>.azuredatalakestore.net/<dest_folder>/ /sourcekey <storage_account_key_for_storage_container>

    Kopieer het **hvac.csv-voorbeeldgegevensbestand** op **/HdiSamples/HdiSamples/SensorSampleData/hvac/** naar het Azure Data Lake Storage-account. Het codefragment moet er als volgt uitzien:

        AdlCopy /Source https://mydatastore.blob.core.windows.net/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv /dest swebhdfs://mydatalakestore.azuredatalakestore.net/hvac/ /sourcekey uJUfvD6cEvhfLoBae2yyQf8t9/BpbWZ4XoYj4kAS5Jf40pZaMNf0q6a8yqTxktwVgRED4vPHeh/50iS9atS5LQ==

   > [!WARNING]  
   > Zorg ervoor dat de bestands- en padnamen de juiste hoofdletters gebruiken.

3. U wordt gevraagd de referenties in te voeren voor het Azure-abonnement waaronder u uw Data Lake Storage-account hebt. Het volgende (of een vergelijkbaar) codefragment wordt weergegeven:

        Initializing Copy.
        Copy Started.
        100% data copied.
        Copy Completed. 1 file copied.

    Het gegevensbestand (**HVAC.csv**) wordt gekopieerd onder een map **/hvac** in het Data Lake Storage-account.

## <a name="use-an-hdinsight-spark-cluster-with-data-lake-storage-gen1"></a>Gebruik een HDInsight Spark-cluster met Data Lake Storage Gen1

1. Klik vanaf [het](https://portal.azure.com/)startbord op de tegel voor uw Apache Spark-cluster (als u deze aan het startbord hebt vastgemaakt). U ook naar uw cluster navigeren onder **Bladeren door alle** > **HDInsight-clusters.**

2. Klik vanuit de blade Spark-cluster op **Snelkoppelingen**. Klik vervolgens vanuit het **Cluster-dashboard** op **Jupyter Notebook**. Voer de beheerdersreferenties voor het cluster in als u daarom wordt gevraagd.

   > [!NOTE]  
   > Mogelijk bereikt u de Jupyter-notebook voor uw cluster ook door de volgende URL in uw browser te openen. Vervang **CLUSTERNAME** door de naam van uw cluster.
   >
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

3. Maak een nieuwe notebook. Klik op **Nieuw** en klik vervolgens op **PySpark**.

    ![Een nieuwe Jupyter-notebook maken](./media/apache-spark-use-with-data-lake-store/hdinsight-create-jupyter-notebook.png "Een nieuwe Jupyter-notebook maken")

4. Omdat u de notebook met behulp van de PySpark-kernel hebt gemaakt, hoeft u niet expliciet contexten te maken. De Spark- en Hive-contexten worden automatisch voor u gemaakt tijdens het uitvoeren van de eerste codecel. Als eerste stap importeert u de typen die voor dit scenario zijn vereist. Plak hiertoe het volgende codefragment in een cel en druk op **SHIFT + ENTER**.

        from pyspark.sql.types import *

    Telkens wanneer u een taak in Jupyter uitvoert, toont de venstertitel van uw webbrowser de status **(Bezet)** samen met de notebooktitel. Ook ziet u een gevulde cirkel naast de **PySpark**-tekst in de rechterbovenhoek. Nadat de taak is voltooid, verandert deze in een lege cirkel.

     ![Status van een Jupyter-notebooktaak](./media/apache-spark-use-with-data-lake-store/hdinsight-jupyter-job-status.png "Status van een Jupyter-notebooktaak")

5. Laad voorbeeldgegevens in een tijdelijke tabel met het **BESTAND HVAC.CSV** dat u hebt gekopieerd naar het Data Lake Storage Gen1-account. U hebt toegang tot de gegevens in het Data Lake Storage-account via het volgende URL-patroon.

   * Als u Data Lake Storage Gen1 als standaardopslag hebt, bevindt HVAC.csv zich op het pad dat vergelijkbaar is met de volgende URL:

           adl://<data_lake_store_name>.azuredatalakestore.net/<cluster_root>/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

       U ook een verkorte indeling gebruiken, zoals:

           adl:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv

   * Als u Data Lake Storage als extra opslag hebt, bevindt HVAC.csv zich op de locatie waar u de opslag hebt gekopieerd, zoals:

           adl://<data_lake_store_name>.azuredatalakestore.net/<path_to_file>

     Plak in een lege cel het volgende codevoorbeeld, vervang **MYDATALAKESTORE** door de naam van uw Data Lake Storage-account en druk op **SHIFT + ENTER**. Dit codevoorbeeld registreert de gegevens in een tijdelijke tabel genaamd **hvac**.

           # Load the data. The path below assumes Data Lake Storage is default storage for the Spark cluster
           hvacText = sc.textFile("adl://MYDATALAKESTORE.azuredatalakestore.net/cluster/mysparkcluster/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

           # Create the schema
           hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])

           # Parse the data in hvacText
           hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))

           # Create a data frame
           hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)

           # Register the data fram as a table to run queries against
           hvacdf.registerTempTable("hvac")

6. Omdat u een PySpark-kernel gebruikt, kunt u nu rechtstreeks een SQL-query uitvoeren op de tijdelijke tabel **hvac**, die u zojuist hebt gemaakt met behulp van de `%%sql`-magic. Zie `%%sql` [Kernels beschikbaar op Jupyter-laptops met Apache Spark HDInsight-clusters](apache-spark-jupyter-notebook-kernels.md#parameters-supported-with-the-sql-magic)voor meer informatie over de magie en andere magie die beschikbaar is met de PySpark-kernel.

        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

7. Nadat de taak is voltooid, wordt standaard de volgende uitvoer in tabelvorm weergegeven.

      ![Tabeluitvoer van het queryresultaat](./media/apache-spark-use-with-data-lake-store/jupyter-tabular-output.png "Tabeluitvoer van het queryresultaat")

     U kunt de resultaten ook in andere visualisaties bekijken. Zo ziet een gebiedsgrafiek voor dezelfde uitvoer er als volgt uit.

     ![Gebiedsgrafiek van het queryresultaat](./media/apache-spark-use-with-data-lake-store/jupyter-area-output1.png "Gebiedsgrafiek van het queryresultaat")

8. Wanneer u klaar bent met het uitvoeren van de toepassing, moet u de notebook afsluiten om de resources vrij te geven. Dit doet u door in het menu **Bestand** in de notebook te klikken op **Sluiten en stoppen**. Hiermee wordt de notebook afgesloten.


## <a name="next-steps"></a>Volgende stappen

* [Een zelfstandige Scala-toepassing maken die moet worden uitgevoerd op het Cluster Apache Spark](apache-spark-create-standalone-application.md)
* [HdInsight-hulpprogramma's gebruiken in Azure Toolkit voor IntelliJ om Apache Spark-toepassingen te maken voor HDInsight Spark Linux-cluster](apache-spark-intellij-tool-plugin.md)
* [Gebruik HDInsight-tools in Azure Toolkit voor Eclipse om Apache Spark-toepassingen te maken voor HDInsight Spark Linux-cluster](apache-spark-eclipse-tool-plugin.md)
* [Azure Data Lake Storage Gen2 gebruiken met Azure HDInsight-clusters](../hdinsight-hadoop-use-data-lake-storage-gen2.md)

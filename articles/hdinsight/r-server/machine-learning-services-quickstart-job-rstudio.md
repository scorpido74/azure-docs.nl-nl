---
title: 'Snelstart: RStudio Server en ML Services voor R - Azure HDInsight'
description: In de quickstart voert u een R-script met behulp van RStudio Server uit op een ML Services-cluster in Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 8a6a204ee5080e3acf99c13ecba1e1c7664d68b4
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "73241884"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-rstudio-server"></a>Snelstart: Een R-script met behulp van RStudio Server uitvoeren op een ML Services-cluster in Azure HDInsight

Met ML-services in Azure HDInsight kunnen R-scripts gedistribueerde berekeningen uitvoeren met behulp van Apache Spark en Apache Hadoop MapReduce. ML-services bepalen hoe aanroepen worden uitgevoerd door de compute-context in te stellen. Het edge-knooppunt van een cluster biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren. Met een edge-knooppunt hebt u de mogelijkheid om de geparallelliseerde gedistribueerde functies van RevoScaleR uit te voeren op de kernen van de server van het edge-knooppunt. U kunt ze ook uitvoeren op de knooppunten van het cluster door compute-contexten van Hadoop Map Reduce of Apache Spark van RevoScaleR te gebruiken.

In deze quickstart leert u hoe u een R-script uitvoert met RStudio Server waarmee het gebruik van Spark voor gedistribueerde R-berekeningen wordt gedemonstreerd. U definieert een compute-context om berekeningen lokaal uit te voeren op een edge-knooppunt en verder te distribueren naar de knooppunten in het HDInsight-cluster.

## <a name="prerequisite"></a>Vereiste

Een ML Services-cluster beheren in HDInsight. Zie [Apache Hadoop-clusters maken met behulp van Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **ML Services** voor **Clustertype**.

## <a name="connect-to-rstudio-server"></a>Verbinding maken met RStudio Server

RStudio Server wordt uitgevoerd op het edge-knooppunt van het cluster. Ga naar de volgende URL, waarbij `CLUSTERNAME` de naam is van het ML Services-cluster dat u hebt gemaakt:

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

De eerste keer dat u zich aanmeldt, moet u tweemaal verifiëren. Bij de eerste verificatieprompt geeft u de aanmeldings-id en het wachtwoord voor de beheerder op voor het cluster. De standaardwaarde is `admin`. Bij de tweede verificatieprompt geeft u de aanmeldings-id en het wachtwoord voor SSH op. De standaardwaarde is `sshuser`. Bij alle volgende aanmeldingen zijn alleen SSH-referenties vereist.

Zodra u verbonden bent, moet het scherm vergelijkbaar zijn met de volgende schermafbeelding:

![Overzichten van RStudio-webconsole](./media/ml-services-quickstart-job-rstudio/connect-to-r-studio1.png)

## <a name="use-a-compute-context"></a>Een compute-context gebruiken

1. Gebruik vanuit RStudio Server de volgende code om voorbeeldgegevens te laden in de standaardopslag voor HDInsight:

    ```RStudio
    # Set the HDFS (WASB) location of example data
     bigDataDirRoot <- "/example/data"
    
     # create a local folder for storing data temporarily
     source <- "/tmp/AirOnTimeCSV2012"
     dir.create(source)
    
     # Download data to the tmp folder
     remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012"
     download.file(file.path(remoteDir, "airOT201201.csv"), file.path(source, "airOT201201.csv"))
     download.file(file.path(remoteDir, "airOT201202.csv"), file.path(source, "airOT201202.csv"))
     download.file(file.path(remoteDir, "airOT201203.csv"), file.path(source, "airOT201203.csv"))
     download.file(file.path(remoteDir, "airOT201204.csv"), file.path(source, "airOT201204.csv"))
     download.file(file.path(remoteDir, "airOT201205.csv"), file.path(source, "airOT201205.csv"))
     download.file(file.path(remoteDir, "airOT201206.csv"), file.path(source, "airOT201206.csv"))
     download.file(file.path(remoteDir, "airOT201207.csv"), file.path(source, "airOT201207.csv"))
     download.file(file.path(remoteDir, "airOT201208.csv"), file.path(source, "airOT201208.csv"))
     download.file(file.path(remoteDir, "airOT201209.csv"), file.path(source, "airOT201209.csv"))
     download.file(file.path(remoteDir, "airOT201210.csv"), file.path(source, "airOT201210.csv"))
     download.file(file.path(remoteDir, "airOT201211.csv"), file.path(source, "airOT201211.csv"))
     download.file(file.path(remoteDir, "airOT201212.csv"), file.path(source, "airOT201212.csv"))
    
     # Set directory in bigDataDirRoot to load the data into
     inputDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")
    
     # Make the directory
     rxHadoopMakeDir(inputDir)
    
     # Copy the data from source to input
     rxHadoopCopyFromLocal(source, bigDataDirRoot)
    ```

    Deze stap neemt circa acht minuten in beslag.

1. Maak enkele gegevens en definieer twee gegevensbronnen. Voer in RStudio de volgende code in:

    ```RStudio
    # Define the HDFS (WASB) file system
     hdfsFS <- RxHdfsFileSystem()
    
     # Create info list for the airline data
     airlineColInfo <- list(
          DAY_OF_WEEK = list(type = "factor"),
          ORIGIN = list(type = "factor"),
          DEST = list(type = "factor"),
          DEP_TIME = list(type = "integer"),
          ARR_DEL15 = list(type = "logical"))
    
     # get all the column names
     varNames <- names(airlineColInfo)
    
     # Define the text data source in hdfs
     airOnTimeData <- RxTextData(inputDir, colInfo = airlineColInfo, varsToKeep = varNames, fileSystem = hdfsFS)
    
     # Define the text data source in local system
     airOnTimeDataLocal <- RxTextData(source, colInfo = airlineColInfo, varsToKeep = varNames)
    
     # formula to use
     formula = "ARR_DEL15 ~ ORIGIN + DAY_OF_WEEK + DEP_TIME + DEST"
    ```

1. Voer een logistic regression uit op de gegevens met behulp van de **lokale** compute-context. Voer in RStudio de volgende code in:

    ```RStudio
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    Het berekenen duurt ongeveer zeven minuten. De uitvoer die u nu ziet, eindigt met regels die er ongeveer uitzien als het volgende fragment:

    ```output
    Data: airOnTimeDataLocal (RxTextData Data Source)
     File name: /tmp/AirOnTimeCSV2012
     Dependent variable(s): ARR_DEL15
     Total independent variables: 634 (Including number dropped: 3)
     Number of valid observations: 6005381
     Number of missing observations: 91381
     -2*LogLikelihood: 5143814.1504 (Residual deviance on 6004750 degrees of freedom)
    
     Coefficients:
                      Estimate Std. Error z value Pr(>|z|)
      (Intercept)   -3.370e+00  1.051e+00  -3.208  0.00134 **
      ORIGIN=JFK     4.549e-01  7.915e-01   0.575  0.56548
      ORIGIN=LAX     5.265e-01  7.915e-01   0.665  0.50590
      ......
      DEST=SHD       5.975e-01  9.371e-01   0.638  0.52377
      DEST=TTN       4.563e-01  9.520e-01   0.479  0.63172
      DEST=LAR      -1.270e+00  7.575e-01  -1.676  0.09364 .
      DEST=BPT         Dropped    Dropped Dropped  Dropped
    
      ---
    
      Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
      Condition number of final variance-covariance matrix: 11904202
      Number of iterations: 7
    ```

1. Voer dezelfde logistic regression uit met behulp van de **Spark**-context. De Spark-context distribueert de verwerking over alle werkknooppunten in het HDInsight-cluster. Voer in RStudio de volgende code in:

    ```RStudio
    # Define the Spark compute context
     mySparkCluster <- RxSpark()
    
     # Set the compute context
     rxSetComputeContext(mySparkCluster)
    
     # Run a logistic regression
     system.time(  
        modelSpark <- rxLogit(formula, data = airOnTimeData)
     )
    
     # Display a summary
     summary(modelSpark)
    ```

    Het berekenen duurt ongeveer vijf minuten.

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de quickstart hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of de Azure CLI](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een R-script uitvoert met RStudio Server waarmee het gebruik van Spark voor gedistribueerde R-berekeningen is gedemonstreerd.  Ga naar het volgende artikel voor meer informatie over de opties die u kunt kiezen om aan te geven of en hoe uitvoering wordt geparallelliseerd tussen kernen van het edge-knooppunt of in het HDInsight-cluster.

> [!div class="nextstepaction"]
>[Opties voor compute-context voor ML Services in HDInsight](./r-server-compute-contexts.md)

> [!NOTE]
> Op deze pagina vindt u een beschrijving van de softwarefuncties van RStudio. Microsoft Azure HDInsight is niet gelieerd aan RStudio, Inc.

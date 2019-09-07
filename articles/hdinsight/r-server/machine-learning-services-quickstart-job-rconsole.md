---
title: 'Quickstart: R-script uitvoeren op ML-Services met behulp van R-console-Azure HDInsight'
description: In de Quick Start voert u een R-script uit op een cluster met MILLILITER Services in azure HDInsight met behulp van de R-console.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: quickstart
ms.date: 06/19/2019
ms.author: hrasheed
ms.custom: mvc
ms.openlocfilehash: 44bc90a4c556108b4b6874a2d1297a6467413824
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736364"
---
# <a name="quickstart-execute-an-r-script-on-an-ml-services-cluster-in-azure-hdinsight-using-r-console"></a>Quickstart: Een R-script uitvoeren op een ML Services-cluster in azure HDInsight met behulp van de R-console

Met ML-Services op Azure HDInsight kunnen R-scripts Apache Spark en Apache Hadoop MapReduce gebruiken om gedistribueerde berekeningen uit te voeren. MILLILITERs services bepalen hoe aanroepen worden uitgevoerd door de compute-context in te stellen. Het Edge-knoop punt van een cluster biedt een handige plaats om verbinding te maken met het cluster en om uw R-scripts uit te voeren. Met een Edge-knoop punt hebt u de mogelijkheid om de geparallel gedistribueerde functies van RevoScaleR uit te voeren op de kernen van de server met het Edge-knoop punt. U kunt ze ook uitvoeren op de knoop punten van het cluster met behulp van de Hadoop-toewijzing van RevoScaleR of Apache Spark reken contexten.

In deze Quick Start leert u hoe u een R-script kunt uitvoeren met R-console waarin wordt getoond dat Spark wordt gebruikt voor gedistribueerde R-berekeningen. U definieert een compute-context om berekeningen lokaal uit te voeren op een Edge-knoop punt en wordt opnieuw gedistribueerd over de knoop punten in het HDInsight-cluster.

## <a name="prerequisites"></a>Vereisten

* Een cluster met MILLILITERs Services op HDInsight. Zie [Apache Hadoop-clusters maken met behulp van de Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer **ml Services** voor het **cluster type**.

* Een SSH-client. Zie voor meer informatie [Verbinding maken met HDInsight (Apache Hadoop) via SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).


## <a name="connect-to-r-console"></a>Verbinding maken met R-console

1. Maak met SSH verbinding met het Edge-knoop punt van een HDInsight-cluster van ML. Bewerk de onderstaande opdracht door de `CLUSTERNAME` naam van uw cluster te vervangen en voer de volgende opdracht in:

    ```cmd
    ssh sshuser@CLUSTERNAME-ed-ssh.azurehdinsight.net
    ```

1. Gebruik tijdens de SSH-sessie de volgende opdracht om de R-console te starten:

    ```
    R
    ```

    U moet uitvoer zien die naast andere informatie de versie van ML Server bevat.


## <a name="use-a-compute-context"></a>Een compute-context gebruiken

1. U kunt de R-code invoeren vanuit de `>`-prompt. Gebruik de volgende code om voorbeeld gegevens te laden in de standaard opslag voor HDInsight:

    ```R
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

    Het kan ongeveer tien minuten duren voordat deze stap is voltooid.

1. Maak enkele gegevens informatie en Definieer twee gegevens bronnen. Voer de volgende code in in de R-console:

    ```R
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

1. Voer een logistiek regressie uit op basis van de gegevens met behulp van de **lokale** Compute-context. Voer de volgende code in in de R-console:

    ```R
    # Set a local compute context
     rxSetComputeContext("local")
    
     # Run a logistic regression
     system.time(
        modelLocal <- rxLogit(formula, data = airOnTimeDataLocal)
     )
    
     # Display a summary
     summary(modelLocal)
    ```

    De berekeningen moeten in ongeveer 7 minuten worden voltooid. U ziet dat er uitvoer wordt weer gegeven die eindigt op regels die vergelijkbaar zijn met het volgende code fragment:

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

1. Voer dezelfde logistiek regressie uit met behulp van de **Spark** -context. De Spark-context distribueert de verwerking over alle werkknooppunten in het HDInsight-cluster. Voer de volgende code in in de R-console:

    ```R
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

    De berekeningen moeten in ongeveer 5 minuten worden voltooid.

1. Gebruik de volgende opdracht om de R-console af te sluiten:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de Snelstartgids hebt voltooid, kunt u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage zodat u een cluster veilig kunt verwijderen wanneer deze niet wordt gebruikt. Voor een HDInsight-cluster worden ook kosten in rekening gebracht, zelfs wanneer het niet wordt gebruikt. Aangezien de kosten voor het cluster vaak zoveel hoger zijn dan de kosten voor opslag, is het financieel gezien logischer clusters te verwijderen wanneer ze niet worden gebruikt.

Als u een cluster wilt verwijderen, raadpleegt u [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](../hdinsight-delete-cluster.md).

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een R-script kunt uitvoeren met R-console die Spark gebruikt voor gedistribueerde R-berekeningen.  Ga naar het volgende artikel voor meer informatie over de opties die beschikbaar zijn om op te geven of en hoe uitvoering wordt geevenwijdigd over kernen van het Edge-knoop punt of HDInsight-cluster.

> [!div class="nextstepaction"]
>[Opties voor Compute-context voor MILLILITERs Services in HDInsight](./r-server-compute-contexts.md)
---
title: 'Zelfstudie: R gebruiken in een Spark-compute-context in Azure HDInsight'
description: Zelfstudie - Ga aan de slag met R en Spark op een Azure HDInsight Machine Learning-servicescluster.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/21/2019
ms.openlocfilehash: 73ca0d089ab758fb13e69d341337139d79194cc5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "71121937"
---
# <a name="tutorial-use-r-in-a-spark-compute-context-in-azure-hdinsight"></a>Zelfstudie: R gebruiken in een Spark-compute-context in Azure HDInsight

Deze zelfstudie biedt een stapsgewijze inleiding tot het gebruik van de R-functies in Apache Spark die worden uitgevoerd op een Azure HDInsight Machine Learning-servicescluster.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * De voorbeeldgegevens downloaden naar lokale opslag
> * De gegevens kopiëren naar standaardopslag
> * Een gegevensset instellen
> * Gegevensbronnen maken
> * Een rekencontext voor Spark maken
> * Een lineair model passen
> * Samengestelde XDF-bestanden gebruiken
> * XDF converteren naar CSV

## <a name="prerequisites"></a>Vereisten

* Een Azure HDInsight Machine Learning-servicescluster. Ga naar [Apache Hadoop-clusters maken met de Azure-portal](../hdinsight-hadoop-create-linux-clusters-portal.md) en selecteer VOOR **Clustertype** **ML-services**.

## <a name="connect-to-rstudio-server"></a>Verbinding maken met RStudio Server

RStudio Server wordt uitgevoerd op het randknooppunt van het cluster. Ga naar de volgende site (waarbij *CLUSTERNAME* in de URL de naam is van het hdInsight Machine Learning-servicescluster dat u hebt gemaakt):

```
https://CLUSTERNAME.azurehdinsight.net/rstudio/
```

De eerste keer dat u zich aanmeldt, verifieert u twee keer. Geef bij de eerste verificatieprompt de gebruikersnaam en het wachtwoord van de clusterbeheerder op (de standaard is *beheerder).* Geef bij de tweede verificatieprompt de SSH-gebruikersnaam en -wachtwoord op (de standaardinstelling is *sshuser).* Latere aanmeldingen vereisen alleen de SSH-referenties.

## <a name="download-the-sample-data-to-local-storage"></a>De voorbeeldgegevens downloaden naar lokale opslag

De *luchtvaartmaatschappij 2012 On-Time Data Set* bestaat uit 12 door komma's gescheiden bestanden die vluchtaankomst- en vertrekgegevens bevatten voor alle commerciële vluchten binnen de VS voor het jaar 2012. Deze dataset is groot, met meer dan 6 miljoen waarnemingen.

1. Initialiseer een paar omgevingsvariabelen. Voer in de RStudio Server-console de volgende code in:

    ```R
    bigDataDirRoot <- "/tutorial/data" # root directory on cluster default storage
    localDir <- "/tmp/AirOnTimeCSV2012" # directory on edge node
    remoteDir <- "https://packages.revolutionanalytics.com/datasets/AirOnTimeCSV2012" # location of data
    ```

1. Selecteer in het rechterdeelvenster het tabblad **Omgeving.** De variabelen worden weergegeven onder **Waarden**.

    ![HDInsight R studio webconsole](./media/ml-services-tutorial-spark-compute/hdinsight-rstudio-image.png)

1. Maak een lokale map en download de voorbeeldgegevens. Voer in RStudio de volgende code in:

    ```R
    # Create local directory
    dir.create(localDir)
    
    # Download data to the tmp folder(local)
    download.file(file.path(remoteDir, "airOT201201.csv"), file.path(localDir, "airOT201201.csv"))
    download.file(file.path(remoteDir, "airOT201202.csv"), file.path(localDir, "airOT201202.csv"))
    download.file(file.path(remoteDir, "airOT201203.csv"), file.path(localDir, "airOT201203.csv"))
    download.file(file.path(remoteDir, "airOT201204.csv"), file.path(localDir, "airOT201204.csv"))
    download.file(file.path(remoteDir, "airOT201205.csv"), file.path(localDir, "airOT201205.csv"))
    download.file(file.path(remoteDir, "airOT201206.csv"), file.path(localDir, "airOT201206.csv"))
    download.file(file.path(remoteDir, "airOT201207.csv"), file.path(localDir, "airOT201207.csv"))
    download.file(file.path(remoteDir, "airOT201208.csv"), file.path(localDir, "airOT201208.csv"))
    download.file(file.path(remoteDir, "airOT201209.csv"), file.path(localDir, "airOT201209.csv"))
    download.file(file.path(remoteDir, "airOT201210.csv"), file.path(localDir, "airOT201210.csv"))
    download.file(file.path(remoteDir, "airOT201211.csv"), file.path(localDir, "airOT201211.csv"))
    download.file(file.path(remoteDir, "airOT201212.csv"), file.path(localDir, "airOT201212.csv"))
    ```

    De download moet in ongeveer 9,5 minuten voltooid zijn.

## <a name="copy-the-data-to-default-storage"></a>De gegevens kopiëren naar standaardopslag

De HDFS-locatie (Hadoop Distributed File `airDataDir` System) wordt met de variabele opgegeven. Voer in RStudio de volgende code in:

```R
# Set directory in bigDataDirRoot to load the data into
airDataDir <- file.path(bigDataDirRoot,"AirOnTimeCSV2012")

# Create directory (default storage)
rxHadoopMakeDir(airDataDir)

# Copy data from local storage to default storage
rxHadoopCopyFromLocal(localDir, bigDataDirRoot)
    
# Optional. Verify files
rxHadoopListFiles(airDataDir)
```

De stap moet in ongeveer 10 seconden voltooid zijn.

## <a name="set-up-a-dataset"></a>Een gegevensset instellen

1. Maak een bestandssysteemobject dat de standaardwaarden gebruikt. Voer in RStudio de volgende code in:

    ```R
    # Define the HDFS (WASB) file system
    hdfsFS <- RxHdfsFileSystem()
    ```

1. Omdat de oorspronkelijke CSV-bestanden nogal logge variabele namen hebben, levert u een *colInfo-lijst* om ze beter beheersbaar te maken. Voer in RStudio de volgende code in:

    ```R
    airlineColInfo <- list(
         MONTH = list(newName = "Month", type = "integer"),
        DAY_OF_WEEK = list(newName = "DayOfWeek", type = "factor",
            levels = as.character(1:7),
            newLevels = c("Mon", "Tues", "Wed", "Thur", "Fri", "Sat",
                          "Sun")),
        UNIQUE_CARRIER = list(newName = "UniqueCarrier", type =
                                "factor"),
        ORIGIN = list(newName = "Origin", type = "factor"),
        DEST = list(newName = "Dest", type = "factor"),
        CRS_DEP_TIME = list(newName = "CRSDepTime", type = "integer"),
        DEP_TIME = list(newName = "DepTime", type = "integer"),
        DEP_DELAY = list(newName = "DepDelay", type = "integer"),
        DEP_DELAY_NEW = list(newName = "DepDelayMinutes", type =
                             "integer"),
        DEP_DEL15 = list(newName = "DepDel15", type = "logical"),
        DEP_DELAY_GROUP = list(newName = "DepDelayGroups", type =
                               "factor",
           levels = as.character(-2:12),
           newLevels = c("< -15", "-15 to -1","0 to 14", "15 to 29",
                         "30 to 44", "45 to 59", "60 to 74",
                         "75 to 89", "90 to 104", "105 to 119",
                         "120 to 134", "135 to 149", "150 to 164",
                         "165 to 179", ">= 180")),
        ARR_DELAY = list(newName = "ArrDelay", type = "integer"),
        ARR_DELAY_NEW = list(newName = "ArrDelayMinutes", type =
                             "integer"),  
        ARR_DEL15 = list(newName = "ArrDel15", type = "logical"),
        AIR_TIME = list(newName = "AirTime", type =  "integer"),
        DISTANCE = list(newName = "Distance", type = "integer"),
        DISTANCE_GROUP = list(newName = "DistanceGroup", type =
                             "factor",
         levels = as.character(1:11),
         newLevels = c("< 250", "250-499", "500-749", "750-999",
             "1000-1249", "1250-1499", "1500-1749", "1750-1999",
             "2000-2249", "2250-2499", ">= 2500")))
    
    varNames <- names(airlineColInfo)
    ```

## <a name="create-data-sources"></a>Gegevensbronnen maken

In een Spark-compute-context u gegevensbronnen maken met behulp van de volgende functies:

|Functie | Beschrijving |
|---------|-------------|
|`RxTextData` | Een door komma's afgebakende tekstgegevensbron. |
|`RxXdfData` | Gegevens in de xdf-bestandsindeling. In RevoScaleR wordt de XDF-bestandsindeling gewijzigd voor Hadoop om gegevens op te slaan in een samengestelde set bestanden in plaats van in één bestand. |
|`RxHiveData` | Hiermee genereert u een object Hive Data Source.|
|`RxParquetData` | Hiermee genereert u een object Parquet Data Source.|
|`RxOrcData` | Hiermee genereert u een orc-gegevensbronobject.|

Maak een [RxTextData-object](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxtextdata) met behulp van de bestanden die u naar HDFS hebt gekopieerd. Voer in RStudio de volgende code in:

```R
airDS <- RxTextData( airDataDir,
                        colInfo = airlineColInfo,
                        varsToKeep = varNames,
                        fileSystem = hdfsFS ) 
```

## <a name="create-a-compute-context-for-spark"></a>Een rekencontext voor Spark maken

Als u gegevens wilt laden en analyses wilt uitvoeren op werknemersknooppunten, stelt u de rekencontext in uw script in op [RxSpark.](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxspark) In deze context distribueren R-functies de werkbelasting automatisch over alle werknemersknooppunten, zonder ingebouwde vereisten voor het beheren van taken of de wachtrij. De Spark-compute-context `RxSpark` wordt `rxSparkConnect()` ingesteld door of om de `rxSparkDisconnect()` Spark-compute-context te maken en wordt gebruikt om terug te keren naar een lokale rekencontext. Voer in RStudio de volgende code in:

```R
# Define the Spark compute context
mySparkCluster <- RxSpark()

# Set the compute context
rxSetComputeContext(mySparkCluster)
```

## <a name="fit-a-linear-model"></a>Een lineair model passen

1. Gebruik de [rxLinMod-functie](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxlinmod) om een `airDS` lineair model te passen met behulp van uw gegevensbron. Voer in RStudio de volgende code in:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDS,
              cube = TRUE)
    )
    ```
    
    Deze stap moet in 2 tot 3 minuten voltooid zijn.

1. Bekijk de resultaten. Voer in RStudio de volgende code in:

    ```R
    summary(delayArr)
    ```

    U ziet de volgende resultaten:

    ```output
    Call:
    rxLinMod(formula = ArrDelay ~ DayOfWeek, data = airDS, cube = TRUE)
    
    Cube Linear Regression Results for: ArrDelay ~ DayOfWeek
    Data: airDataXdf (RxXdfData Data Source)
    File name: /tutorial/data/AirOnTimeCSV2012
    Dependent variable(s): ArrDelay
    Total independent variables: 7 
    Number of valid observations: 6005381
    Number of missing observations: 91381 
     
    Coefficients:
                   Estimate Std. Error t value Pr(>|t|)     | Counts
    DayOfWeek=Mon   3.54210    0.03736   94.80 2.22e-16 *** | 901592
    DayOfWeek=Tues  1.80696    0.03835   47.12 2.22e-16 *** | 855805
    DayOfWeek=Wed   2.19424    0.03807   57.64 2.22e-16 *** | 868505
    DayOfWeek=Thur  4.65502    0.03757  123.90 2.22e-16 *** | 891674
    DayOfWeek=Fri   5.64402    0.03747  150.62 2.22e-16 *** | 896495
    DayOfWeek=Sat   0.91008    0.04144   21.96 2.22e-16 *** | 732944
    DayOfWeek=Sun   2.82780    0.03829   73.84 2.22e-16 *** | 858366
    ---
    Signif. codes:  0 ‘***’ 0.001 ‘**’ 0.01 ‘*’ 0.05 ‘.’ 0.1 ‘ ’ 1
    
    Residual standard error: 35.48 on 6005374 degrees of freedom
    Multiple R-squared: 0.001827 (as if intercept included)
    Adjusted R-squared: 0.001826 
    F-statistic:  1832 on 6 and 6005374 DF,  p-value: < 2.2e-16 
    Condition number: 1 
    ```

    De resultaten geven aan dat u alle gegevens hebt verwerkt, 6 miljoen waarnemingen, met behulp van alle CSV-bestanden in de opgegeven map. Omdat u `cube = TRUE`opgegeven , heb je een geschatte coëfficiënt voor elke dag van de week (en niet de onderschepping).

## <a name="use-composite-xdf-files"></a>Samengestelde XDF-bestanden gebruiken

Zoals u hebt gezien, u CSV-bestanden rechtstreeks analyseren met R op Hadoop. Maar u de analyse sneller uitvoeren als u de gegevens in een efficiënter formaat opslaat. De R XDF-bestandsindeling is efficiënt, maar is enigszins gewijzigd voor HDFS, zodat afzonderlijke bestanden binnen één HDFS-blok blijven. (De grootte van het HDFS-blok varieert van installatie tot installatie, maar is meestal 64 MB of 128 MB.) 

Wanneer u [rxImport](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rximport) op Hadoop gebruikt om een set samengestelde `RxTextData` XDF-bestanden te maken, geeft u een gegevensbron op, zoals `AirDS` de inData en een `RxXdfData` gegevensbron met bestandsSysteem ingesteld op een HDFS-bestandssysteem als het argument outFile. Vervolgens u `RxXdfData` het object gebruiken als het argument gegevens in volgende R-analyses.

1. Een `RxXdfData` object definiëren. Voer in RStudio de volgende code in:

    ```R
    airDataXdfDir <- file.path(bigDataDirRoot,"AirOnTimeXDF2012")
    
    airDataXdf <- RxXdfData( airDataXdfDir,
                            fileSystem = hdfsFS )
    ```

1. Stel een blokgrootte van 250000 rijen in en geef op dat we alle gegevens lezen. Voer in RStudio de volgende code in:

    ```R
    blockSize <- 250000
    numRowsToRead = -1
    ```

1. Importeer de `rxImport`gegevens met behulp van . Voer in RStudio de volgende code in:

    ```R
    rxImport(inData = airDS,
             outFile = airDataXdf,
             rowsPerRead = blockSize,
             overwrite = TRUE,
             numRows = numRowsToRead )
    ```
    
    Deze stap moet in een paar minuten voltooid zijn.

1. Herschat hetzelfde lineaire model opnieuw met behulp van de nieuwe, snellere gegevensbron. Voer in RStudio de volgende code in:

    ```R
    system.time(
         delayArr <- rxLinMod(ArrDelay ~ DayOfWeek, data = airDataXdf,
              cube = TRUE)
    )
    ```
    
    De stap moet in minder dan een minuut voltooid zijn.

1. Bekijk de resultaten. De resultaten moeten hetzelfde zijn als uit de CSV-bestanden. Voer in RStudio de volgende code in:

    ```R
    summary(delayArr)
    ```

## <a name="convert-xdf-to-csv"></a>XDF converteren naar CSV

### <a name="in-a-spark-context"></a>In een Spark-context

Als u uw CSV-bestanden hebt geconverteerd naar XDF-bestandsindeling voor een grotere efficiëntie tijdens het uitvoeren van de analyses, maar uw gegevens nu wilt converteren naar CSV, u dit doen met [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep).

Als u een map met CSV-bestanden wilt maken, maakt u eerst een `RxTextData` object met een mapnaam als bestandsargument. Dit object vertegenwoordigt de map waarin de CSV-bestanden kunnen worden gemaakt. Deze map wordt gemaakt `rxDataStep`wanneer u de . Wijs dit `RxTextData` object vervolgens `outFile` aan `rxDataStep`in het argument van de . Elke CSV die is gemaakt, wordt benoemd op basis van de naam van de map en gevolgd door een getal.

Stel dat u een map met CSV-bestanden in `airDataXdf` HDFS wilt uitschrijven vanuit uw samengestelde XDF nadat u de logistieke regressie en voorspelling hebt uitgevoerd, zodat de nieuwe CSV-bestanden de voorspelde waarden en restwaarden bevatten. Voer in RStudio de volgende code in:

```R
airDataCsvDir <- file.path(bigDataDirRoot,"AirDataCSV2012")
airDataCsvDS <- RxTextData(airDataCsvDir,fileSystem=hdfsFS)
rxDataStep(inData=airDataXdf, outFile=airDataCsvDS)
```

Deze stap moet in ongeveer 2,5 minuut voltooid zijn.

Het `rxDataStep` schreef één CSV-bestand uit voor elk XDFD-bestand in het samengestelde XDF-bestand. Dit is het standaardgedrag voor het schrijven van CSV-bestanden van samengestelde `RxSpark`XDF-bestanden naar HDFS wanneer de rekencontext is ingesteld op .

### <a name="in-a-local-context"></a>In een lokale context

Als u klaar bent met het uitvoeren van uw analyses, `local` u uw `RxTextData` rekencontext terugschakelen naar twee argumenten binnen die u `createFileSet` `rowsPerOutFile`iets meer controle geven wanneer u CSV-bestanden naar HDFS schrijft: en. Wanneer u `createFileSet` `TRUE`ingesteld op , wordt een map met CSV-bestanden naar de door u opgegeven map geschreven. Wanneer u `createFileSet` `FALSE`bent ingesteld op één CSV-bestand wordt geschreven. U het tweede `rowsPerOutFile`argument instellen op een geheel getal om aan `createFileSet` `TRUE`te geven hoeveel rijen u naar elk CSV-bestand moet schrijven wanneer dat is.

Voer in RStudio de volgende code in:

```R
rxSetComputeContext("local")
airDataCsvRowsDir <- file.path(bigDataDirRoot,"AirDataCSVRows2012")
airDataCsvRowsDS <- RxTextData(airDataCsvRowsDir, fileSystem=hdfsFS, createFileSet=TRUE, rowsPerOutFile=1000000)
rxDataStep(inData=airDataXdf, outFile=airDataCsvRowsDS)
```

Deze stap moet in ongeveer 10 minuten voltooid zijn.

Wanneer u `RxSpark` een rekencontext gebruikt, `createFileSet` wordt deze standaard `TRUE` ingesteld en `rowsPerOutFile` heeft dit geen effect. Als u daarom één CSV wilt maken of het aantal `rxDataStep` rijen `local` per bestand wilt aanpassen, voert u het uit in een rekencontext (de gegevens kunnen nog steeds in HDFS zijn).

## <a name="final-steps"></a>Laatste stappen

1. Ruim de gegevens op. Voer in RStudio de volgende code in:

    ```R
    rxHadoopRemoveDir(airDataDir)
    rxHadoopRemoveDir(airDataXdfDir)
    rxHadoopRemoveDir(airDataCsvDir)
    rxHadoopRemoveDir(airDataCsvRowsDir)
    rxHadoopRemoveDir(bigDataDirRoot)
    ```

1. Stop de externe Spark-toepassing. Voer in RStudio de volgende code in:

    ```R
    rxStopEngine(mySparkCluster)
    ```

1. Stop met de R-sessie. Voer in RStudio de volgende code in:

    ```R
    quit()
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Nadat u de zelfstudie hebt voltooid, u het cluster verwijderen. Met HDInsight worden uw gegevens opgeslagen in Azure Storage, zodat u een cluster veilig verwijderen wanneer het niet in gebruik is. U betaalt ook voor een HDInsight-cluster, zelfs als het niet in gebruik is. Omdat de kosten voor het cluster vele malen meer zijn dan de kosten voor opslag, is het economisch zinvol om clusters te verwijderen wanneer ze niet in gebruik zijn.

Zie [Een HDInsight-cluster verwijderen met behulp van uw browser, PowerShell of Azure CLI](../hdinsight-delete-cluster.md)als u een cluster wilt verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u R-functies in Apache Spark gebruiken die worden uitgevoerd op een HDInsight Machine Learning-servicescluster. Raadpleeg voor meer informatie de volgende artikelen:

* [Rekencontextopties voor een Azure HDInsight Machine Learning-servicescluster](r-server-compute-contexts.md)
* [R-functies voor Spark op Hadoop](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/revoscaler-hadoop-functions)

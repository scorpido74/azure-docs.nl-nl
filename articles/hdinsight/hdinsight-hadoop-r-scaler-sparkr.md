---
title: ScaleR en SparkR gebruiken met Azure HDInsight
description: ScaleR en SparkR gebruiken voor gegevensmanipulatie en modelontwikkeling met ML Services op Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/26/2019
ms.openlocfilehash: 5989692aeb59c7394299b4cb2474b244818895b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75500072"
---
# <a name="combine-scaler-and-sparkr-in-hdinsight"></a>Combineer ScaleR en SparkR in HDInsight

Dit document laat zien hoe u vertragingen bij vluchtaankomst voorspellen met behulp van een **ScaleR** logistiek regressiemodel. In het voorbeeld worden vluchtvertragings- en weergegevens gebruikt, samengevoegd met **SparkR.**

Hoewel beide pakketten worden uitgevoerd op de Spark-uitvoeringsengine van Apache Hadoop, zijn ze geblokkeerd voor het delen van in-memory gegevens, omdat ze elk hun eigen respectievelijke Spark-sessies vereisen. Totdat dit probleem is verholpen in een aankomende versie van ML Server, is de tijdelijke oplossing om niet-overlappende Spark-sessies te onderhouden en gegevens uit te wisselen via tussenliggende bestanden. De instructies hier tonen aan dat deze eisen eenvoudig te bereiken zijn.

Dit voorbeeld werd in eerste instantie gedeeld in een toespraak op Strata 2016 door Mario Inchiosa en Roni Burd. U vindt deze lezing bij [Het bouwen van een schaalbaar Data Science Platform met R](https://channel9.msdn.com/blogs/Cloud-and-Enterprise-Premium/Building-A-Scalable-Data-Science-Platform-with-R-and-Hadoop).

De code is oorspronkelijk geschreven voor ML Server die wordt uitgevoerd op Spark in een HDInsight-cluster op Azure. Maar het concept van het mengen van het gebruik van SparkR en ScaleR in één script is ook geldig in de context van on-premises omgevingen.

De stappen in dit document gaan ervan uit dat u een tussenliggend kennisniveau van R hebt en de [ScaleR-bibliotheek](https://msdn.microsoft.com/microsoft-r/scaler-user-guide-introduction) van ML Server bent. Je maakt kennis met [SparkR](https://spark.apache.org/docs/2.1.0/sparkr.html) terwijl je door dit scenario loopt.

## <a name="the-airline-and-weather-datasets"></a>De luchtvaart- en weerdatasets

De vluchtgegevens zijn beschikbaar in de archieven van de [Amerikaanse overheid.](https://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236) Het is ook beschikbaar als rits van [AirOnTimeCSV.zip.](https://packages.revolutionanalytics.com/datasets/AirOnTime87to12/AirOnTimeCSV.zip)

De weersgegevens kunnen worden gedownload als zip-bestanden in ruwe vorm, per maand, van de [National Oceanic and Atmospheric Administration repository](https://www.ncdc.noaa.gov/orders/qclcd/). Download bijvoorbeeld de gegevens voor mei 2007 - december 2012. Gebruik de gegevensbestanden `YYYYMMMstation.txt` en bestanden per uur binnen elk van de ritsen.

## <a name="setting-up-the-spark-environment"></a>De Spark-omgeving instellen

Gebruik de volgende code om de Spark-omgeving in te stellen:

```
workDir        <- '~'  
myNameNode     <- 'default' 
myPort         <- 0
inputDataDir   <- 'wasb://hdfs@myAzureAccount.blob.core.windows.net'
hdfsFS         <- RxHdfsFileSystem(hostName=myNameNode, port=myPort)

# create a persistent Spark session to reduce startup times 
#   (remember to stop it later!)
 
sparkCC        <- RxSpark(consoleOutput=TRUE, nameNode=myNameNode, port=myPort, persistentRun=TRUE)

# create working directories 

rxHadoopMakeDir('/user')
rxHadoopMakeDir('user/RevoShare')
rxHadoopMakeDir('user/RevoShare/remoteuser')

(dataDir <- '/share')
rxHadoopMakeDir(dataDir)
rxHadoopListFiles(dataDir) 

setwd(workDir)
getwd()

# version of rxRoc that runs in a local CC 
rxRoc <- function(...){
  rxSetComputeContext(RxLocalSeq())
  roc <- RevoScaleR::rxRoc(...)
  rxSetComputeContext(sparkCC)
  return(roc)
}

logmsg <- function(msg) { cat(format(Sys.time(), "%Y-%m-%d %H:%M:%S"),':',msg,'\n') } 
t0 <- proc.time() 

#..start 

logmsg('Start') 
(trackers <- system("mapred job -list-active-trackers", intern = TRUE))
logmsg(paste('Number of task nodes=',length(trackers)))
```

Voeg vervolgens `Spark_Home` toe aan het zoekpad voor R-pakketten. Als u deze toevoegt aan het zoekpad, u SparkR gebruiken en een SparkR-sessie initialiseren:

```
#..setup for use of SparkR  

logmsg('Initialize SparkR') 

.libPaths(c(file.path(Sys.getenv("SPARK_HOME"), "R", "lib"), .libPaths()))
library(SparkR)

sparkEnvir <- list(spark.executor.instances = '10',
                   spark.yarn.executor.memoryOverhead = '8000')

sc <- sparkR.init(
  sparkEnvir = sparkEnvir,
  sparkPackages = "com.databricks:spark-csv_2.10:1.3.0"
)

sqlContext <- sparkRSQL.init(sc)
```

## <a name="preparing-the-weather-data"></a>Het voorbereiden van de weergegevens

Als u de weersgegevens wilt voorbereiden, u deze subseten naar de kolommen die nodig zijn voor het modelleren: 

- "Zichtbaarheid"
- "DryBulbCelsius"
- "DauwpuntCelsius"
- "Relatievevochtigheid"
- "WindSpeed"
- "Altimeter"

Voeg vervolgens een luchthavencode toe die is gekoppeld aan het weerstation en zet de metingen om van lokale tijd naar UTC.

Begin met het maken van een bestand om de informatie van het weerstation (WBAN) in kaart te brengen op een luchthavencode. De volgende code leest elk van de bestanden van de ruwe weergegevens per uur, subsets van de kolommen die we nodig hebben, voegt het weerstation-mapping-bestand samen, past de datumtijden van metingen aan utc aan en schrijft vervolgens een nieuwe versie van het bestand uit:

```
# Look up AirportID and Timezone for WBAN (weather station ID) and adjust time

adjustTime <- function(dataList)
{
  dataset0 <- as.data.frame(dataList)
  
  dataset1 <- base::merge(dataset0, wbanToAirIDAndTZDF1, by = "WBAN")

  if(nrow(dataset1) == 0) {
    dataset1 <- data.frame(
      Visibility = numeric(0),
      DryBulbCelsius = numeric(0),
      DewPointCelsius = numeric(0),
      RelativeHumidity = numeric(0),
      WindSpeed = numeric(0),
      Altimeter = numeric(0),
      AdjustedYear = numeric(0),
      AdjustedMonth = numeric(0),
      AdjustedDay = integer(0),
      AdjustedHour = integer(0),
      AirportID = integer(0)
    )
    
    return(dataset1)
  }
  
  Year <- as.integer(substr(dataset1$Date, 1, 4))
  Month <- as.integer(substr(dataset1$Date, 5, 6))
  Day <- as.integer(substr(dataset1$Date, 7, 8))
  
  Time <- dataset1$Time
  Hour <- ceiling(Time/100)
  
  Timezone <- as.integer(dataset1$TimeZone)
  
  adjustdate = as.POSIXlt(sprintf("%4d-%02d-%02d %02d:00:00", Year, Month, Day, Hour), tz = "UTC") + Timezone * 3600

  AdjustedYear = as.POSIXlt(adjustdate)$year + 1900
  AdjustedMonth = as.POSIXlt(adjustdate)$mon + 1
  AdjustedDay   = as.POSIXlt(adjustdate)$mday
  AdjustedHour  = as.POSIXlt(adjustdate)$hour
  
  AirportID = dataset1$AirportID
  Weather = dataset1[,c("Visibility", "DryBulbCelsius", "DewPointCelsius", "RelativeHumidity", "WindSpeed", "Altimeter")]
  
  data.set = data.frame(cbind(AdjustedYear, AdjustedMonth, AdjustedDay, AdjustedHour, AirportID, Weather))
  
  return(data.set)
}

wbanToAirIDAndTZDF <- read.csv("wban-to-airport-id-tz.csv")

colInfo <- list(
  WBAN = list(type="integer"),
  Date = list(type="character"),
  Time = list(type="integer"),
  Visibility = list(type="numeric"),
  DryBulbCelsius = list(type="numeric"),
  DewPointCelsius = list(type="numeric"),
  RelativeHumidity = list(type="numeric"),
  WindSpeed = list(type="numeric"),
  Altimeter = list(type="numeric")
)

weatherDF <- RxTextData(file.path(inputDataDir, "WeatherRaw"), colInfo = colInfo)

weatherDF1 <- RxTextData(file.path(inputDataDir, "Weather"), colInfo = colInfo,
                filesystem=hdfsFS)

rxSetComputeContext("localpar")
rxDataStep(weatherDF, outFile = weatherDF1, rowsPerRead = 50000, overwrite = T,
           transformFunc = adjustTime,
           transformObjects = list(wbanToAirIDAndTZDF1 = wbanToAirIDAndTZDF))
```

## <a name="importing-the-airline-and-weather-data-to-spark-dataframes"></a>De luchtvaartmaatschappij en weergegevens importeren in Spark DataFrames

Nu gebruiken we de functie SparkR [read.df()](https://spark.apache.org/docs/latest/api/R/read.df.html) om de weer- en luchtvaartgegevens te importeren in Spark DataFrames. Deze functie wordt, net als veel andere Spark-methoden, lui uitgevoerd, wat betekent dat ze in de wachtrij staan voor uitvoering, maar niet worden uitgevoerd totdat dit nodig is.

```
airPath     <- file.path(inputDataDir, "AirOnTime08to12CSV")
weatherPath <- file.path(inputDataDir, "Weather") # pre-processed weather data
rxHadoopListFiles(airPath) 
rxHadoopListFiles(weatherPath) 

# create a SparkR DataFrame for the airline data

logmsg('create a SparkR DataFrame for the airline data') 
# use inferSchema = "false" for more robust parsing
airDF <- read.df(sqlContext, airPath, source = "com.databricks.spark.csv", 
                 header = "true", inferSchema = "false")

# Create a SparkR DataFrame for the weather data

logmsg('create a SparkR DataFrame for the weather data') 
weatherDF <- read.df(sqlContext, weatherPath, source = "com.databricks.spark.csv", 
                     header = "true", inferSchema = "true")
```

## <a name="data-cleansing-and-transformation"></a>Gegevensreiniging en -transformatie

Vervolgens doen we wat opruimen op de gegevens van de luchtvaartmaatschappij die we hebben geïmporteerd om kolommen te hernoemen. We houden alleen de benodigde variabelen en ronden geplande vertrektijden tot het dichtstbijzijnde uur om het samenvoegen met de nieuwste weersgegevens bij vertrek mogelijk te maken:

```
logmsg('clean the airline data') 
airDF <- rename(airDF,
                ArrDel15 = airDF$ARR_DEL15,
                Year = airDF$YEAR,
                Month = airDF$MONTH,
                DayofMonth = airDF$DAY_OF_MONTH,
                DayOfWeek = airDF$DAY_OF_WEEK,
                Carrier = airDF$UNIQUE_CARRIER,
                OriginAirportID = airDF$ORIGIN_AIRPORT_ID,
                DestAirportID = airDF$DEST_AIRPORT_ID,
                CRSDepTime = airDF$CRS_DEP_TIME,
                CRSArrTime =  airDF$CRS_ARR_TIME
)

# Select desired columns from the flight data. 
varsToKeep <- c("ArrDel15", "Year", "Month", "DayofMonth", "DayOfWeek", "Carrier", "OriginAirportID", "DestAirportID", "CRSDepTime", "CRSArrTime")
airDF <- select(airDF, varsToKeep)

# Apply schema
coltypes(airDF) <- c("character", "integer", "integer", "integer", "integer", "character", "integer", "integer", "integer", "integer")

# Round down scheduled departure time to full hour.
airDF$CRSDepTime <- floor(airDF$CRSDepTime / 100)
```

Nu voeren we vergelijkbare bewerkingen uit op de weergegevens:

```
# Average weather readings by hour
logmsg('clean the weather data') 
weatherDF <- agg(groupBy(weatherDF, "AdjustedYear", "AdjustedMonth", "AdjustedDay", "AdjustedHour", "AirportID"), Visibility="avg",
                  DryBulbCelsius="avg", DewPointCelsius="avg", RelativeHumidity="avg", WindSpeed="avg", Altimeter="avg"
                  )

weatherDF <- rename(weatherDF,
                    Visibility = weatherDF$'avg(Visibility)',
                    DryBulbCelsius = weatherDF$'avg(DryBulbCelsius)',
                    DewPointCelsius = weatherDF$'avg(DewPointCelsius)',
                    RelativeHumidity = weatherDF$'avg(RelativeHumidity)',
                    WindSpeed = weatherDF$'avg(WindSpeed)',
                    Altimeter = weatherDF$'avg(Altimeter)'
)
```

## <a name="joining-the-weather-and-airline-data"></a>Deelnemen aan de gegevens van het weer en luchtvaartmaatschappijen

We gebruiken nu de SparkR [join()](https://spark.apache.org/docs/latest/api/R/join.html) functie om een links buiten-join van de luchtvaartmaatschappij en weergegevens uit te voeren door airportID en datetime te vertrekken. De buitenste join stelt ons in staat om alle gegevens van de luchtvaartmaatschappij te bewaren, zelfs als er geen overeenkomende weergegevens zijn. Na de join verwijderen we enkele overbodige kolommen en hernoemen we de naam van de bewaarde kolommen om het binnenkomende DataFrame-voorvoegsel te verwijderen dat door de join is geïntroduceerd.

```
logmsg('Join airline data with weather at Origin Airport')
joinedDF <- SparkR::join(
  airDF,
  weatherDF,
  airDF$OriginAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF1 <- select(joinedDF, varsToKeep)

joinedDF2 <- rename(joinedDF1,
                    VisibilityOrigin = joinedDF1$Visibility,
                    DryBulbCelsiusOrigin = joinedDF1$DryBulbCelsius,
                    DewPointCelsiusOrigin = joinedDF1$DewPointCelsius,
                    RelativeHumidityOrigin = joinedDF1$RelativeHumidity,
                    WindSpeedOrigin = joinedDF1$WindSpeed,
                    AltimeterOrigin = joinedDF1$Altimeter
)
```

Op een vergelijkbare manier voegen we ons bij de weer- en luchtvaartgegevens op basis van aankomst AirportID en datetime:

```
logmsg('Join airline data with weather at Destination Airport')
joinedDF3 <- SparkR::join(
  joinedDF2,
  weatherDF,
  airDF$DestAirportID == weatherDF$AirportID &
    airDF$Year == weatherDF$AdjustedYear &
    airDF$Month == weatherDF$AdjustedMonth &
    airDF$DayofMonth == weatherDF$AdjustedDay &
    airDF$CRSDepTime == weatherDF$AdjustedHour,
  joinType = "left_outer"
)

# Remove redundant columns
vars <- names(joinedDF3)
varsToDrop <- c('AdjustedYear', 'AdjustedMonth', 'AdjustedDay', 'AdjustedHour', 'AirportID')
varsToKeep <- vars[!(vars %in% varsToDrop)]
joinedDF4 <- select(joinedDF3, varsToKeep)

joinedDF5 <- rename(joinedDF4,
                    VisibilityDest = joinedDF4$Visibility,
                    DryBulbCelsiusDest = joinedDF4$DryBulbCelsius,
                    DewPointCelsiusDest = joinedDF4$DewPointCelsius,
                    RelativeHumidityDest = joinedDF4$RelativeHumidity,
                    WindSpeedDest = joinedDF4$WindSpeed,
                    AltimeterDest = joinedDF4$Altimeter
                    )
```

## <a name="save-results-to-csv-for-exchange-with-scaler"></a>Resultaten opslaan in CSV voor uitwisseling met ScaleR

Dat maakt de joins compleet die we met SparkR moeten doen. We slaan de gegevens van het uiteindelijke Spark DataFrame "joinedDF5" op in een CSV voor invoer naar ScaleR en sluiten vervolgens de SparkR-sessie af. We vertellen SparkR expliciet om de resulterende CSV op te slaan in 80 afzonderlijke partities om voldoende parallellisme in ScaleR-verwerking mogelijk te maken:

```
logmsg('output the joined data from Spark to CSV') 
joinedDF5 <- repartition(joinedDF5, 80) # write.df below will produce this many CSVs

# write result to directory of CSVs
write.df(joinedDF5, file.path(dataDir, "joined5Csv"), "com.databricks.spark.csv", "overwrite", header = "true")

# We can shut down the SparkR Spark context now
sparkR.stop()

# remove non-data files
rxHadoopRemove(file.path(dataDir, "joined5Csv/_SUCCESS"))
```

## <a name="import-to-xdf-for-use-by-scaler"></a>Importeren in XDF voor gebruik door ScaleR

We kunnen het CSV-bestand van verbonden luchtvaartmaatschappij en weergegevens gebruiken voor modellering via een ScaleR-bron voor tekstgegevens. Maar we importeren het eerst in XDF, omdat het efficiënter is bij het uitvoeren van meerdere bewerkingen op de gegevensset:

```
logmsg('Import the CSV to compressed, binary XDF format') 

# set the Spark compute context for ML Services 
rxSetComputeContext(sparkCC)
rxGetComputeContext()

colInfo <- list(
  ArrDel15 = list(type="numeric"),
  Year = list(type="factor"),
  Month = list(type="factor"),
  DayofMonth = list(type="factor"),
  DayOfWeek = list(type="factor"),
  Carrier = list(type="factor"),
  OriginAirportID = list(type="factor"),
  DestAirportID = list(type="factor"),
  RelativeHumidityOrigin = list(type="numeric"),
  AltimeterOrigin = list(type="numeric"),
  DryBulbCelsiusOrigin = list(type="numeric"),
  WindSpeedOrigin = list(type="numeric"),
  VisibilityOrigin = list(type="numeric"),
  DewPointCelsiusOrigin = list(type="numeric"),
  RelativeHumidityDest = list(type="numeric"),
  AltimeterDest = list(type="numeric"),
  DryBulbCelsiusDest = list(type="numeric"),
  WindSpeedDest = list(type="numeric"),
  VisibilityDest = list(type="numeric"),
  DewPointCelsiusDest = list(type="numeric")
)

joinedDF5Txt <- RxTextData(file.path(dataDir, "joined5Csv"),
                           colInfo = colInfo, fileSystem = hdfsFS)
rxGetInfo(joinedDF5Txt) 

destData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

rxImport(inData = joinedDF5Txt, destData, overwrite = TRUE)

rxGetInfo(destData, getVarInfo = T)

# File name: /user/RevoShare/dev/delayDataLarge/joined5XDF 
# Number of composite data files: 80 
# Number of observations: 148619655 
# Number of variables: 22 
# Number of blocks: 320 
# Compression type: zlib 
# Variable information: 
#   Var 1: ArrDel15, Type: numeric, Low/High: (0.0000, 1.0000)
# Var 2: Year
# 26 factor levels: 1987 1988 1989 1990 1991 ... 2008 2009 2010 2011 2012
# Var 3: Month
# 12 factor levels: 10 11 12 1 2 ... 5 6 7 8 9
# Var 4: DayofMonth
# 31 factor levels: 1 3 4 5 7 ... 29 30 2 18 31
# Var 5: DayOfWeek
# 7 factor levels: 4 6 7 1 3 2 5
# Var 6: Carrier
# 30 factor levels: PI UA US AA DL ... HA F9 YV 9E VX
# Var 7: OriginAirportID
# 374 factor levels: 15249 12264 11042 15412 13930 ... 13341 10559 14314 11711 10558
# Var 8: DestAirportID
# 378 factor levels: 13303 14492 10721 11057 13198 ... 14802 11711 11931 12899 10559
# Var 9: CRSDepTime, Type: integer, Low/High: (0, 24)
# Var 10: CRSArrTime, Type: integer, Low/High: (0, 2400)
# Var 11: RelativeHumidityOrigin, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 12: AltimeterOrigin, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 13: DryBulbCelsiusOrigin, Type: numeric, Low/High: (-46.1000, 47.8000)
# Var 14: WindSpeedOrigin, Type: numeric, Low/High: (0.0000, 81.0000)
# Var 15: VisibilityOrigin, Type: numeric, Low/High: (0.0000, 90.0000)
# Var 16: DewPointCelsiusOrigin, Type: numeric, Low/High: (-41.7000, 29.0000)
# Var 17: RelativeHumidityDest, Type: numeric, Low/High: (0.0000, 100.0000)
# Var 18: AltimeterDest, Type: numeric, Low/High: (28.1700, 31.1600)
# Var 19: DryBulbCelsiusDest, Type: numeric, Low/High: (-46.1000, 53.9000)
# Var 20: WindSpeedDest, Type: numeric, Low/High: (0.0000, 136.0000)
# Var 21: VisibilityDest, Type: numeric, Low/High: (0.0000, 88.0000)
# Var 22: DewPointCelsiusDest, Type: numeric, Low/High: (-43.0000, 29.0000)

finalData <- RxXdfData(file.path(dataDir, "joined5XDF"), fileSystem = hdfsFS)

```

## <a name="splitting-data-for-training-and-test"></a>Gegevens splitsen voor training en test

We gebruiken rxDataStep om de 2012-gegevens uit te splitsen voor het testen en de rest te bewaren voor training:

```
# split out the training data

logmsg('split out training data as all data except year 2012')
trainDS <- RxXdfData( file.path(dataDir, "finalDataTrain" ),fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = trainDS,
            rowSelection = ( Year != 2012 ), overwrite = T )

# split out the testing data

logmsg('split out the test data for year 2012') 
testDS <- RxXdfData( file.path(dataDir, "finalDataTest" ), fileSystem = hdfsFS)

rxDataStep( inData = finalData, outFile = testDS,
            rowSelection = ( Year == 2012 ), overwrite = T )

rxGetInfo(trainDS)
rxGetInfo(testDS)
```

## <a name="train-and-test-a-logistic-regression-model"></a>Een logistiek regressiemodel trainen en testen

Nu zijn we klaar om een model te bouwen. Om de invloed van weersgegevens op vertraging in de aankomsttijd te zien, maken we gebruik van scaler's logistieke regressieroutine. We gebruiken het om te modelleren of een aankomstvertraging van meer dan 15 minuten wordt beïnvloed door het weer op de vertrek- en aankomstluchthavens:

```
logmsg('train a logistic regression model for Arrival Delay > 15 minutes') 
formula <- as.formula(ArrDel15 ~ Year + Month + DayofMonth + DayOfWeek + Carrier +
                     OriginAirportID + DestAirportID + CRSDepTime + CRSArrTime + 
                     RelativeHumidityOrigin + AltimeterOrigin + DryBulbCelsiusOrigin +
                     WindSpeedOrigin + VisibilityOrigin + DewPointCelsiusOrigin + 
                     RelativeHumidityDest + AltimeterDest + DryBulbCelsiusDest +
                     WindSpeedDest + VisibilityDest + DewPointCelsiusDest
                   )

# Use the scalable rxLogit() function but set max iterations to 3 for the purposes of 
# this exercise 

logitModel <- rxLogit(formula, data = trainDS, maxIterations = 3)

base::summary(logitModel)
```

Laten we nu eens kijken hoe het doet op de testgegevens door het maken van een aantal voorspellingen en kijken naar ROC en AUC.

```
# Predict over test data (Logistic Regression).

logmsg('predict over the test data') 
logitPredict <- RxXdfData(file.path(dataDir, "logitPredict"), fileSystem = hdfsFS)

# Use the scalable rxPredict() function

rxPredict(logitModel, data = testDS, outData = logitPredict,
          extraVarsToWrite = c("ArrDel15"), 
          type = 'response', overwrite = TRUE)

# Calculate ROC and Area Under the Curve (AUC).

logmsg('calculate the roc and auc') 
logitRoc <- rxRoc("ArrDel15", "ArrDel15_Pred", logitPredict)
logitAuc <- rxAuc(logitRoc)
head(logitAuc)
logitAuc

plot(logitRoc)
```

## <a name="scoring-elsewhere"></a>Elders scoren

We kunnen het model ook gebruiken voor het scoren van gegevens op een ander platform. Door het op te slaan in een RDS-bestand en vervolgens die RDS over te zetten en te importeren in een doelscoringsomgeving zoals MIcrosoft SQL Server R Services. Het is belangrijk om ervoor te zorgen dat de factorniveaus van de te scoren gegevens overeenkomen met die waarop het model is gebouwd. Die overeenkomst kan worden bereikt door de kolominformatie die aan de `rxCreateColInfo()` modelleringsgegevens via de functie van ScaleR wordt gekoppeld te extraheren en op te slaan en die kolominformatie vervolgens toe te passen op de invoergegevensbron voor voorspelling. In het volgende slaan we een paar rijen van de testgegevensset op en extraheren en gebruiken we de kolominformatie uit dit voorbeeld in het voorspellingsscript:

```
# save the model and a sample of the test dataset 

logmsg('save serialized version of the model and a sample of the test data')
rxSetComputeContext('localpar') 
saveRDS(logitModel, file = "logitModel.rds")
testDF <- head(testDS, 1000)  
saveRDS(testDF    , file = "testDF.rds"    )
list.files()

rxHadoopListFiles(file.path(inputDataDir,''))
rxHadoopListFiles(dataDir)

# stop the spark engine 
rxStopEngine(sparkCC) 

logmsg('Done.')
elapsed <- (proc.time() - t0)[3]
logmsg(paste('Elapsed time=',sprintf('%6.2f',elapsed),'(sec)\n\n'))
```

## <a name="summary"></a>Samenvatting

In dit artikel hebben we laten zien hoe het mogelijk is om het gebruik van SparkR voor gegevensmanipulatie te combineren met ScaleR voor modelontwikkeling in Hadoop Spark. Dit scenario vereist dat u afzonderlijke Spark-sessies onderhoudt, slechts één sessie tegelijk uitvoert en gegevens uitwisselt via CSV-bestanden. Hoewel eenvoudig, moet dit proces nog eenvoudiger zijn in een aankomende ML Services-release, wanneer SparkR en ScaleR een Spark-sessie kunnen delen en zo Spark DataFrames kunnen delen.

## <a name="next-steps-and-more-information"></a>Volgende stappen en meer informatie

- Zie de [handleiding Aan de slag voor](https://msdn.microsoft.com/microsoft-r/scaler-spark-getting-started)meer informatie over het gebruik van ML Server op Apache Spark.

- Zie Overzicht van ML Services [op HDInsight](r-server/r-server-overview.md)voor informatie over ML Services op HDInsight.

Zie voor meer informatie over het gebruik van SparkR:

- [Apache SparkR-document](https://spark.apache.org/docs/2.1.0/sparkr.html).

- [SparkR-overzicht](https://docs.databricks.com/spark/latest/sparkr/overview.html) van Databricks.

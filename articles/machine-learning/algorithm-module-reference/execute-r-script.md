---
title: 'R-script uitvoeren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module R-script uitvoeren in Azure Machine Learning om R-code uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: peterlu
ms.date: 06/01/2019
ms.openlocfilehash: f9aae1302f0d83c27d5d8f01745ddecbaeea9467
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497878"
---
# <a name="execute-r-script"></a>R-Script uitvoeren

In dit artikel wordt beschreven hoe u de **script module Execute** r gebruikt voor het uitvoeren van r-code in uw Azure machine learning Designer (preview)-pijp lijn.

Met R kunt u taken uitvoeren die momenteel niet worden ondersteund door bestaande modules, zoals: 
- Aangepaste gegevens transformaties maken
- Uw eigen metrische gegevens gebruiken om voor spellingen te evalueren
- Modellen bouwen met behulp van algoritmen die niet zijn geïmplementeerd als zelfstandige modules in de ontwerp functie

## <a name="r-version-support"></a>R-versie ondersteuning

Azure Machine Learning Designer gebruikt de KRANs (uitgebreid R-archief netwerk) distributie van R. De versie die momenteel wordt gebruikt, is KRANs 3.5.1.

## <a name="supported-r-packages"></a>Ondersteunde R-pakketten

De R-omgeving wordt vooraf geïnstalleerd met meer dan 100 pakketten. Zie de sectie [vooraf geïnstalleerde R-pakketten](#pre-installed-r-packages)voor een volledige lijst.

U kunt ook de volgende code toevoegen aan elke wille keurige **R-script** module en de geïnstalleerde pakketten weer geven.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R-pakketten installeren
Als u extra R-pakketten wilt installeren, gebruikt u de methode `install.packages()`. Zorg ervoor dat u de locatie van de KRANs opgeeft. Pakketten worden geïnstalleerd voor elke **Execute r-script** module en worden niet gedeeld met andere **uitvoering r-script** modules.

In dit voor beeld ziet u hoe u Zoo installeert:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  
  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")
  library(zoo)
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```
 > [!NOTE]
  > Controleer of het pakket al bestaat voordat u dit installeert om te voor komen dat de installatie wordt herhaald. Als `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` in de bovenstaande voorbeeld code. Herhalings installatie kan een time-out voor de webservice tot gevolg hebben.     

## <a name="how-to-configure-execute-r-script"></a>Het uitvoeren van een R-script configureren

De module voor het **uitvoeren van R-scripts** bevat voorbeeld code die u als uitgangs punt kunt gebruiken. Als u de module voor het uitvoeren van een **R-script** wilt configureren, geeft u een set ingangen en code op die moet worden uitgevoerd.

![R-module](media/module/execute-r-script.png)

Gegevens sets die zijn opgeslagen in de ontwerp functie worden automatisch geconverteerd naar een R-gegevens frame wanneer deze met deze module wordt geladen.

1.  Voeg de module voor het **uitvoeren van R-scripts** toe aan de pijp lijn.

  

1. Verbind alle invoer gegevens die nodig zijn voor het script. Invoer zijn optioneel en kunnen gegevens en aanvullende R-code bevatten.

    * **Dataset1**: referentie de eerste invoer als `dataframe1`. De invoer gegevensset moet zijn opgemaakt als een CSV-, TSV-, ARFF-of verbinding maken met een Azure Machine Learning-gegevensset.

    * **Dataset2**: referentie de tweede invoer als `dataframe2`. Deze gegevensset moet ook worden opgemaakt als een CSV-, TSV-, ARFF-bestand of als Azure Machine Learning-gegevensset.

    * **Script bundel**: de derde invoer accepteert zip-bestanden. Het zip-bestand kan meerdere bestanden en meerdere bestands typen bevatten.

1. Typ of plak een geldig R-script in het tekstvak **R-script** .

    Om u te helpen aan de slag te gaan, is het tekstvak **R-script** vooraf ingevuld met voorbeeld code, die u kunt bewerken of vervangen.
    
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.

  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

 * Het script moet een functie bevatten met de naam `azureml_main`, het toegangs punt voor deze module.

 * De functie toegangs punt kan Maxi maal twee invoer argumenten bevatten: `Param<dataframe1>` en `Param<dataframe2>`
 
   > [!NOTE]
    > Er wordt verwezen naar de gegevens die worden door gegeven aan de **Execute R-script** module als `dataframe1` en `dataframe2`. Dit wijkt af van Azure machine learning Designer (de naslag informatie voor de ontwerper als `dataset1`, `dataset2`). Controleer of de ingevoerde gegevens correct zijn referneced in het script.  
 
    > [!NOTE]
    >  Bestaande R-code heeft mogelijk kleine wijzigingen nodig om te worden uitgevoerd in een designer-pijp lijn. Invoer gegevens die u in CSV-indeling opgeeft, moeten bijvoorbeeld expliciet worden geconverteerd naar een gegevensset voordat u deze in uw code kunt gebruiken. Gegevens-en kolom typen die in de R-taal worden gebruikt, verschillen ook op een aantal manieren van de gegevens-en kolom typen die in de ontwerp functie worden gebruikt.

1.  **Wille keurige Seed**: Typ een waarde die in de R-omgeving moet worden gebruikt als de wille keurige Seed-waarde. Deze para meter is gelijk aan het aanroepen van `set.seed(value)` in R-code.  

1. Voer de pijplijn uit.  

## <a name="results"></a>Resultaten

De **script** modules voor het uitvoeren van r kunnen meerdere uitvoer retour neren, maar moeten als R-gegevens frames worden weer gegeven. Gegevens frames worden automatisch geconverteerd naar gegevens sets in de ontwerp functie voor compatibiliteit met andere modules.

Standaard berichten en fouten van R worden teruggestuurd naar het logboek van de module.

## <a name="sample-scripts"></a>Voorbeeldscripts

Er zijn veel manieren waarop u uw pijp lijn kunt uitbreiden door gebruik te maken van een aangepast R-script.  Deze sectie bevat voorbeeld code voor algemene taken.


### <a name="add-r-script-as-an-input"></a>R-script als invoer toevoegen

De **script module Execute r** ondersteunt wille keurige R-script bestanden als invoer. Om dit te doen, moeten ze worden geüpload naar uw werk ruimte als onderdeel van het ZIP-bestand.

1. Als u een ZIP-bestand met R-code naar uw werk ruimte wilt uploaden, klikt u op **Nieuw**, klikt u op **gegevensset**en selecteert u vervolgens een **lokaal bestand** en de optie **zip-bestand** .  

1. Controleer of het gezipte bestand beschikbaar is in de lijst met **opgeslagen gegevens sets** .

1.  Verbind de gegevensset met de invoer poort van de **script bundel** .

1. Alle bestanden die zijn opgenomen in het ZIP-bestand, zijn beschikbaar tijdens de uitvoerings tijd van de pipeline. 

    Als het script bundel bestand een mapstructuur bevat, blijft de structuur behouden. U moet uw code echter aanpassen aan laten voorafgaan door van de Directory **./script-bundel** tot het pad.

### <a name="process-data"></a>Gegevens verwerken

In het volgende voor beeld ziet u hoe u invoer gegevens kunt schalen en normaliseren:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.
# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  # If a zip file is connected to the third input port, it is
  # unzipped under "./Script Bundle". This directory is added
  # to sys.path.
  series <- dataframe1$width
  # find the maximum and minimum values of width column in dataframe1
  max_v <- max(series)
  min_v <- min(series)
  # calculate the scale and bias
  scale <- max_v - min_v
  bias <- min_v / dis
  # apply min-max normalizing
  dataframe1$width <- dataframe1$width / scale - bias
  dataframe2$width <- dataframe2$width / scale - bias
  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
 ```

### <a name="read-a-zip-file-as-input"></a>Een ZIP-bestand als invoer lezen

In dit voor beeld ziet u hoe u een gegevensset in een ZIP-bestand kunt gebruiken als invoer voor de module voor het uitvoeren van een **R-script** .

1. Maak het gegevens bestand in CSV-indeling en noem het ' mydatafile. csv '.
1. Maak een ZIP-bestand en voeg het CSV-bestand toe aan het archief.
1. Upload het zip-bestand naar uw Azure Machine Learning-werk ruimte. 
1. Verbind de resulterende gegevensset met de **ScriptBundle** -invoer van de module voor het uitvoeren van een **R-script** .
1. Gebruik de volgende code om de CSV-gegevens te lezen uit het zip-bestand.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Rijen repliceren

In dit voor beeld ziet u hoe u positieve records in een gegevensset repliceert om het voor beeld te verdelen:

```R
azureml_main <- function(dataframe1, dataframe2){
  data.set <- dataframe1[dataframe1[,1]==-1,]  
  # positions of the positive samples
  pos <- dataframe1[dataframe1[,1]==1,]
  # replicate the positive samples to balance the sample  
  for (i in 1:20) data.set <- rbind(data.set,pos)  
  row.names(data.set) <- NULL
  # Return datasets as a Named List
  return(list(dataset1=data.set, dataset2=dataframe2))
}
```

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-objecten door geven tussen R-script modules

U kunt R-objecten door geven tussen exemplaren van de module **r-script uitvoeren** met het mechanisme voor interne serialisatie. In dit voor beeld wordt ervan uitgegaan dat u het R-object met de naam `A` wilt verplaatsen tussen twee **R-script modules uitvoeren** .

1. Voeg de eerste **R-script** module voor uitvoeren toe aan uw pijp lijn en typ de volgende code in het tekstvak **R-script** om een geserialiseerd object te maken `A` als een kolom in de uitvoer gegevens tabel van de module:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    De expliciete conversie naar een geheel getal wordt uitgevoerd, omdat de serialisatiefunctie-functie gegevens uitvoer in de R-`Raw` indeling, die niet wordt ondersteund door de ontwerp functie.

1. Voeg een tweede exemplaar van de module voor het **uitvoeren van R-scripts** toe en verbind deze met de uitvoer poort van de voor gaande module.

1. Typ de volgende code in het tekstvak **R-script** als u object `A` wilt extra heren uit de invoer gegevens tabel. 

    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      A <- unserialize(as.raw(dataframe1$serialized))  
      # Return datasets as a Named List
      return(list(dataset1=dataframe1, dataset2=dataframe2))
    }
    ```

## <a name="pre-installed-r-packages"></a>Vooraf geïnstalleerde R-pakketten

De huidige lijst met vooraf geïnstalleerde R-pakketten die beschikbaar zijn voor gebruik:

|              |            | 
|--------------|------------| 
| Pakket      | Versie    | 
| askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| Baseer         | 3.5.1      | 
| base64enc    | 0,1-3      | 
| BH           | 1.69.0-1   | 
| Binder        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| modus         | 1,3-22     | 
| broom        | 0.5.2      | 
| aanroeper        | 3.2.0      | 
| verplaatsen        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| CellRange   | 1.1.0      | 
| Klasse        | 7.3-15     | 
| cli          | 1.1.0      | 
| Cliper        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| code-    | 0,2-16     | 
| colorspace   | 1.4-1      | 
| Programma's     | 3.5.1      | 
| potloden       | 1.3.4      | 
| Ezelsoor         | 3,3        | 
| data. table   | 1.12.2     | 
| Sets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| vatting       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evalueren     | 0,14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| ForeignName      | 0,8-71     | 
| be           | 1.3.1      | 
| gdata        | 2.18.0     | 
| algemene typen     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2,0-18     | 
| Daardoor         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| PNG     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| YRaster         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| recent        | 2.1.0      | 
| hoger        | 0,8        | 
| hms          | 0.4.2      | 
| HTML-bestand    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1,6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| Labels     | 0,3        | 
| raster      | 0,20-38    | 
| Lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| KANSDICHTHEIDS         | 7.3-51.4   | 
| Overzicht       | 1.2-17     | 
| Technieken      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| -         | 0,7        | 
| ModelMetrics | 1.2.2      | 
| Modeler       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| Openssl      | 1,4        | 
| parallel     | 3.5.1      | 
| tweede       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| proces-x     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| gang     | 1.2.2      | 
| PCL           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1,5-7      | 
| quantmod     | 0,4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| Lees bewerking        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recepten      | 0.1.5      | 
| opnieuw zoeken      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1,12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1,13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| schalen       | 1.0.0      | 
| kiezer      | 0,4-1      | 
| ruimtelijke      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| statistieken        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| teken reeks      | 1.3.1      | 
| Survival     | 2.44-1.1   | 
| laden          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| opruimen        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043,102   | 
| tinytex      | 0,13       | 
| hulpprogramma's        | 3.5.1      | 
| tseries      | 0,10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0,3-2      | 
| met de        | omschreven      | 
| xfun         | 0,8        | 
| xml2         | 1.2.0      | 
| XTS          | 0,11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| dieren          | 1.8-6      | 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

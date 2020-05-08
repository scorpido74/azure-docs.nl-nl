---
title: 'R-script uitvoeren: module verwijzing'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de module R-script uitvoeren in Azure Machine Learning om R-code uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 04/27/2020
ms.openlocfilehash: 7b72d83740e0e2b02ef9d2ea3cd1cbf04a4c99cc
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/08/2020
ms.locfileid: "82983564"
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
> [!NOTE]
> Als uw pijp lijn meerdere R-script modules voor uitvoeren bevat en dezelfde pakketten nodig heeft die niet voor komen in de lijst met vooraf geïnstalleerde, moet u de pakketten in elke module installeren. 

## <a name="installing-r-packages"></a>R-pakketten installeren
Als u extra R-pakketten wilt installeren `install.packages()` , gebruikt u de-methode. Pakketten worden geïnstalleerd voor elke **Execute r-script** module en worden niet gedeeld met andere **uitvoering r-script** modules.

> [!NOTE]
> Geef de KRANs opslagplaats op bij het installeren van pakketten zoals`install.packages("zoo",repos = "http://cran.us.r-project.org")`

In dit voor beeld ziet u hoe u Zoo installeert:
```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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
  > Controleer of het pakket al bestaat voordat u dit installeert om te voor komen dat de installatie wordt herhaald. Zoals `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` in de bovenstaande voorbeeld code. Herhalings installatie kan een time-out voor de webservice tot gevolg hebben.     

## <a name="upload-files"></a>Bestanden uploaden
Het **Execute R-script** ondersteunt het uploaden van bestanden met behulp van Azure machine learning R SDK.

In het volgende voor beeld ziet u hoe u een afbeeldings bestand uploadt in het **script Execute R**:
```R

# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
#   Param<dataframe1>: a R DataFrame
#   Param<dataframe2>: a R DataFrame
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")

  # Generate a jpeg graph
  img_file_name <- "rect.jpg"
  jpeg(file=img_file_name)
  example(rect)
  dev.off()

  upload_files_to_run(names = list(file.path("graphic", img_file_name)), paths=list(img_file_name))


  # Return datasets as a Named List
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

Nadat de pijplijn uitvoering is voltooid, kunt u een voor beeld van de afbeelding in het rechterdeel venster van de module bekijken

> [!div class="mx-imgBorder"]
> ![Geüpload-afbeelding](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>Het uitvoeren van een R-script configureren

De module voor het **uitvoeren van R-scripts** bevat voorbeeld code die u als uitgangs punt kunt gebruiken. Als u de module voor het uitvoeren van een **R-script** wilt configureren, geeft u een set ingangen en code op die moet worden uitgevoerd.

![R-module](media/module/execute-r-script.png)

Gegevens sets die zijn opgeslagen in de ontwerp functie worden automatisch geconverteerd naar een R-gegevens frame wanneer deze met deze module wordt geladen.

1.  Voeg de module voor het **uitvoeren van R-scripts** toe aan de pijp lijn.

  

1. Verbind alle invoer gegevens die nodig zijn voor het script. Invoer zijn optioneel en kunnen gegevens en aanvullende R-code bevatten.

    * **Dataset1**: referentie de eerste invoer als `dataframe1`. De invoer gegevensset moet zijn opgemaakt als een CSV-, TSV-, ARFF-of verbinding maken met een Azure Machine Learning-gegevensset.

    * **Dataset2**: verwijst naar de tweede invoer `dataframe2`als. Deze gegevensset moet ook worden opgemaakt als een CSV-, TSV-, ARFF-bestand of als Azure Machine Learning-gegevensset.

    * **Script bundel**: de derde invoer accepteert zip-bestanden. Het zip-bestand kan meerdere bestanden en meerdere bestands typen bevatten.

1. Typ of plak een geldig R-script in het tekstvak **R-script** .

    > [!NOTE]
    > Zorg ervoor dat u uw script schrijft en er zeker van zijn dat er geen syntaxis fout is, zoals het gebruik van niet-gedeclareerde variabelen of niet-geïmporteerde modules of functies. U kunt ook extra aandacht best Eden aan de lijst met vooraf geïnstalleerde pakketten aan het einde van dit document. Als u pakketten wilt gebruiken die niet worden vermeld, installeert u ze in uw script zoals`install.packages("zoo",repos = "http://cran.us.r-project.org")`
    
    > [!NOTE]
    > Functions is afhankelijk van de X11-bibliotheek, zoals View, worden niet ondersteund omdat de X11-bibliotheek niet vooraf is geïnstalleerd.
    
    Om u te helpen aan de slag te gaan, is het tekstvak **R-script** vooraf ingevuld met voorbeeld code, die u kunt bewerken of vervangen.
    
    ```R
    # R version: 3.5.1
    # The script MUST contain a function named azureml_main
    # which is the entry point for this module.

    # Please note that functions dependant on X11 library
    # such as "View" are not supported because X11 library
    # is not pre-installed.
    
    # The entry point function MUST have two input arguments.
    # If the input port is not connected, the corresponding
    # dataframe argument will be null.
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

 * Het script moet een functie bevatten met `azureml_main`de naam, het toegangs punt voor deze module.

 * De functie voor het toegangs punt moet twee invoer argumenten `Param<dataframe1>` hebben `Param<dataframe2>`: en, zelfs wanneer deze twee argumenten niet worden gebruikt in de functie.

   > [!NOTE]
    > Er wordt verwezen naar de gegevens die worden door gegeven aan de **Execute R-script** module `dataframe1` `dataframe2`en die verschilt van Azure machine learning Designer ( `dataset1`de `dataset2`ontwerp functie als). Controleer of de ingevoerde gegevens correct zijn referneced in het script.  
 
    > [!NOTE]
    >  Bestaande R-code heeft mogelijk kleine wijzigingen nodig om te worden uitgevoerd in een designer-pijp lijn. Invoer gegevens die u in CSV-indeling opgeeft, moeten bijvoorbeeld expliciet worden geconverteerd naar een gegevensset voordat u deze in uw code kunt gebruiken. Gegevens-en kolom typen die in de R-taal worden gebruikt, verschillen ook op een aantal manieren van de gegevens-en kolom typen die in de ontwerp functie worden gebruikt.

1.  **Wille keurige Seed**: Typ een waarde die in de R-omgeving moet worden gebruikt als de wille keurige Seed-waarde. Deze para meter is gelijk aan `set.seed(value)` het aanroepen van de R-code.  

1. Verzend de pijp lijn.  

## <a name="results"></a>Resultaten

De **script** modules voor het uitvoeren van r kunnen meerdere uitvoer retour neren, maar moeten als R-gegevens frames worden weer gegeven. Gegevens frames worden automatisch geconverteerd naar gegevens sets in de ontwerp functie voor compatibiliteit met andere modules.

Standaard berichten en fouten van R worden teruggestuurd naar het logboek van de module.

Als u resultaten wilt afdrukken in het R-script, kunt u de afgedrukte resultaten vinden in het tabblad **70_driver_log** onder **outputs + logs** in het rechterdeel venster van de module.

## <a name="sample-scripts"></a>Voorbeeldscripts

Er zijn veel manieren waarop u uw pijp lijn kunt uitbreiden door gebruik te maken van een aangepast R-script.  Deze sectie bevat voorbeeld code voor algemene taken.


### <a name="add-r-script-as-an-input"></a>R-script als invoer toevoegen

De **script module Execute r** ondersteunt wille keurige R-script bestanden als invoer. Om dit te doen, moeten ze worden geüpload naar uw werk ruimte als onderdeel van het ZIP-bestand.

1. Als u een ZIP-bestand met R-code naar uw werk ruimte wilt uploaden, gaat u naar de pagina **gegevens sets** , klikt u op **gegevensset maken**en selecteert u vervolgens een **lokaal bestand** en de optie type **Bestands** gegevensset.  

1. Controleer of het gezipte bestand beschikbaar is in de lijst **mijn gegevens sets** onder **gegevens sets** in de structuur van de module links.

1.  Verbind de gegevensset met de invoer poort van de **script bundel** .

1. Alle bestanden die zijn opgenomen in het ZIP-bestand, zijn beschikbaar tijdens de uitvoerings tijd van de pipeline. 

    Als het script bundel bestand een mapstructuur bevat, blijft de structuur behouden. U moet uw code echter aanpassen aan laten voorafgaan door van de Directory **./script-bundel** tot het pad.

### <a name="process-data"></a>Gegevens verwerken

In het volgende voor beeld ziet u hoe u invoer gegevens kunt schalen en normaliseren:

```R
# R version: 3.5.1
# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# Please note that functions dependant on X11 library
# such as "View" are not supported because X11 library
# is not pre-installed.

# The entry point function MUST have two input arguments.
# If the input port is not connected, the corresponding
# dataframe argument will be null.
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

U kunt R-objecten door geven tussen exemplaren van de module **r-script uitvoeren** met het mechanisme voor interne serialisatie. In dit voor beeld wordt ervan uitgegaan dat u het R- `A` object wilt verplaatsen met een naam tussen twee **R-script modules uitvoeren** .

1. Voeg de eerste **R-script** module voor uitvoeren toe aan uw pijp lijn en typ de volgende code in het tekstvak **R-script** om een geserialiseerd object `A` te maken als een kolom in de uitvoer gegevens tabel van de module:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    De expliciete conversie naar een geheel getal wordt uitgevoerd, omdat de serialisatie-functie gegevens uitvoer `Raw` in de R-indeling, wat niet wordt ondersteund door de ontwerp functie.

1. Voeg een tweede exemplaar van de module voor het **uitvoeren van R-scripts** toe en verbind deze met de uitvoer poort van de voor gaande module.

1. Typ de volgende code in het tekstvak **R-script** om het object `A` op te halen uit de invoer gegevens tabel. 

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
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1,3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| klasse        | 7.3-15     | 
| cli          | 1.1.0      | 
| Cliper        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0,2-16     | 
| colorspace   | 1.4-1      | 
| compiler     | 3.5.1      | 
| crayon       | 1.3.4      | 
| curl         | 3,3        | 
| data.table   | 1.12.2     | 
| gegevenssets     | 3.5.1      | 
| DBI          | 1.0.0      | 
| dbplyr       | 1.4.1      | 
| digest       | 0.6.19     | 
| dplyr        | 0.7.6      | 
| e1071        | 1.7-2      | 
| evalueren     | 0.14       | 
| fansi        | 0.4.0      | 
| forcats      | 0.3.0      | 
| foreach      | 1.4.4      | 
| foreign      | 0,8-71     | 
| be           | 1.3.1      | 
| gdata        | 2.18.0     | 
| algemene typen     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2,0-18     | 
| glue         | 1.3.1      | 
| gower        | 0.2.1      | 
| gplots       | 3.0.1.1    | 
| afbeeldingen     | 3.5.1      | 
| grDevices    | 3.5.1      | 
| grid         | 3.5.1      | 
| gtable       | 0.3.0      | 
| gtools       | 3.8.1      | 
| haven        | 2.1.0      | 
| highr        | 0.8        | 
| hms          | 0.4.2      | 
| htmltools    | 0.3.6      | 
| httr         | 1.4.0      | 
| ipred        | 0,9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0,20-38    | 
| lava         | 1.6.5      | 
| lazyeval     | 0.2.2      | 
| lubridate    | 1.7.4      | 
| magrittr     | 1.5        | 
| markdown     | 1          | 
| MASS         | 7.3-51.4   | 
| Matrix       | 1.2-17     | 
| methods      | 3.5.1      | 
| mgcv         | 1.8-28     | 
| mime         | 0.7        | 
| ModelMetrics | 1.2.2      | 
| modelr       | 0.1.4      | 
| munsell      | 0.5.0      | 
| nlme         | 3.1-140    | 
| nnet         | 7.3-12     | 
| numDeriv     | 2016.8-1.1 | 
| openssl      | 1.4        | 
| parallel     | 3.5.1      | 
| pillar       | 1.4.1      | 
| pkgconfig    | 2.0.2      | 
| plogr        | 0.2.0      | 
| plyr         | 1.8.4      | 
| prettyunits  | 1.0.2      | 
| processx     | 3.3.1      | 
| prodlim      | 2018.04.18 | 
| progress     | 1.2.2      | 
| PCL           | 1.3.0      | 
| purrr        | 0.3.2      | 
| quadprog     | 1.5-7      | 
| quantmod     | 0.4-15     | 
| R6           | 2.4.0      | 
| randomForest | 4.6-14     | 
| RColorBrewer | 1.1-2      | 
| Rcpp         | 1.0.1      | 
| RcppRoll     | 0.3.0      | 
| readr        | 1.3.1      | 
| readxl       | 1.3.1      | 
| recipes      | 0.1.5      | 
| rematch      | 1.0.1      | 
| reprex       | 0.3.0      | 
| reshape2     | 1.4.3      | 
| reticulate   | 1.12       | 
| rlang        | 0.4.0      | 
| rmarkdown    | 1.13       | 
| ROCR         | 1.0-7      | 
| rpart        | 4.1-15     | 
| rstudioapi   | 0.1        | 
| rvest        | 0.3.4      | 
| scales       | 1.0.0      | 
| selectr      | 0.4-1      | 
| spatial      | 7.3-11     | 
| splines      | 3.5.1      | 
| SQUAREM      | 2017.10-1  | 
| stats        | 3.5.1      | 
| stats4       | 3.5.1      | 
| stringi      | 1.4.3      | 
| stringr      | 1.3.1      | 
| survival     | 2.44-1.1   | 
| sys          | 3.2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| hulpprogramma's        | 3.5.1      | 
| tseries      | 0,10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0,11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set met modules die beschikbaar zijn](module-reference.md) voor Azure machine learning. 

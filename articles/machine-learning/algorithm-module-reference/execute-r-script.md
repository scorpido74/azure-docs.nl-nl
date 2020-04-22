---
title: 'R-script uitvoeren: modulenaslag'
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Scriptmodule Uitvoeren in Azure Machine Learning om R-code uit te voeren.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/10/2020
ms.openlocfilehash: eb778c8d24639320b60927438de76a29de724ac2
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684718"
---
# <a name="execute-r-script"></a>R-Script uitvoeren

In dit artikel wordt beschreven hoe u de **Script-module Uitvoeren** gebruikt om R-code uit te voeren in uw Azure Machine Learning-ontwerper (preview)-pijplijn.

Met R u taken uitvoeren die momenteel niet worden ondersteund door bestaande modules, zoals: 
- Aangepaste gegevenstransformaties maken
- Gebruik je eigen statistieken om voorspellingen te evalueren
- Modellen bouwen met algoritmen die niet zijn geïmplementeerd als zelfstandige modules in de ontwerper

## <a name="r-version-support"></a>Ondersteuning voor R-versies

Azure Machine Learning-ontwerper maakt gebruik van de CRAN-distributie (Comprehensive R Archive Network) van R. De momenteel gebruikte versie is CRAN 3.5.1.

## <a name="supported-r-packages"></a>Ondersteunde R-pakketten

De R-omgeving is vooraf geïnstalleerd met meer dan 100 pakketten. Zie voor een volledige lijst de sectie [Vooraf geïnstalleerde R-pakketten](#pre-installed-r-packages).

U ook de volgende code toevoegen aan elke **Script-module uitvoeren** en de geïnstalleerde pakketten bekijken.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  dataframe1 <- data.frame(installed.packages())
  return(list(dataset1=dataframe1, dataset2=dataframe2))
}
```

## <a name="installing-r-packages"></a>R-pakketten installeren
Als u extra R-pakketten wilt installeren, gebruikt u de `install.packages()` methode. Pakketten worden geïnstalleerd voor elke **Script-module uitvoeren** en worden niet gedeeld via andere **Execute R Script-modules.**

> [!NOTE]
> Geef de CRAN-repository op bij het installeren van pakketten zoals`install.packages("zoo",repos = "http://cran.us.r-project.org")`

In dit voorbeeld ziet u hoe u Zoo installeert:
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
  > Controleer of het pakket al bestaat voordat u het installeert om herhaling te voorkomen. Zoals `  if(!require(zoo)) install.packages("zoo",repos = "http://cran.us.r-project.org")` in bovenstaande voorbeeldcode. Herhaalde installatie kan leiden tot een time-out van webserviceaanvragen.     

## <a name="upload-files"></a>Bestanden uploaden
Het **Uitvoeren van R Script** ondersteunt het uploaden van bestanden met Azure Machine Learning R SDK.

In het volgende voorbeeld ziet u hoe u een afbeeldingsbestand uploadt in het **Script R uitvoeren:**
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

Nadat de pijplijnrun is voltooid, u een voorbeeld van de afbeelding bekijken in het rechterdeelvenster van de module

> [!div class="mx-imgBorder"]
> ![Geüploade afbeelding](media/module/upload-image-in-r-script.png)

## <a name="how-to-configure-execute-r-script"></a>R-script uitvoeren configureren

De module **R-script uitvoeren** bevat voorbeeldcode die u als uitgangspunt gebruiken. Als u de **Script-module Uitvoeren wilt** configureren, moet u een reeks ingangen en code opgeven die moeten worden uitgevoerd.

![R-module](media/module/execute-r-script.png)

Gegevenssets die in de ontwerper zijn opgeslagen, worden automatisch geconverteerd naar een R-gegevensframe wanneer ze met deze module worden geladen.

1.  Voeg de **Script-module R uitvoeren** toe aan uw pijplijn.

  

1. Sluit alle ingangen die nodig zijn door het script. Ingangen zijn optioneel en kunnen gegevens en extra R-code bevatten.

    * **Gegevensset1**: Verwijs `dataframe1`naar de eerste invoer als . De invoergegevensset moet worden opgemaakt als csv, TSV, ARFF of u een Azure Machine Learning-gegevensset verbinden.

    * **Gegevensset2**: Verwijs `dataframe2`naar de tweede invoer als . Deze gegevensset moet ook worden opgemaakt als een CSV-, TSV-, ARFF-bestand of als azure machine learning-gegevensset.

    * **Scriptbundel:** de derde invoer accepteert ZIP-bestanden. Het zip-bestand kan meerdere bestanden en meerdere bestandstypen bevatten.

1. Typ of plak in het tekstvak **R-script** een geldig R-script.

    > [!NOTE]
    > Wees zeer voorzichtig bij het schrijven van uw script en zorgt ervoor dat er geen syntaxisfout optreedt, zoals het gebruik van een niet-gedeclareerde variabele of een niet-geïmporteerde module of functie. Besteed ook extra aandacht aan de vooraf geïnstalleerde pakketlijst aan het einde van dit document. Als u pakketten wilt gebruiken die niet worden vermeld, installeert u deze in uw script, zoals`install.packages("zoo",repos = "http://cran.us.r-project.org")`
    
    > [!NOTE]
    > Functies die afhankelijk zijn van x11-bibliotheek, zoals 'Weergave', worden niet ondersteund omdat de X11-bibliotheek niet vooraf is geïnstalleerd.
    
    Om u op weg te helpen, wordt het tekstvak **R Script** vooraf ingevuld met voorbeeldcode, die u bewerken of vervangen.
    
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

 * Het script moet een `azureml_main`functie met de naam bevatten , het ingangspunt voor deze module.

 * De functie item moet twee `Param<dataframe1>` invoerargumenten hebben: en `Param<dataframe2>`zelfs wanneer deze twee argumenten niet in de functie worden gebruikt.

   > [!NOTE]
    > De gegevens die worden doorgegeven aan `dataframe1` de `dataframe2` **Script-module Uitvoeren** wordt aangeduid als en `dataset1` `dataset2`, wat verschilt van Azure Machine Learning-ontwerper (de ontwerpverwijzing als , ). Controleer of de invoergegevens correct in uw script worden verwezen.  
 
    > [!NOTE]
    >  Bestaande R-code heeft mogelijk kleine wijzigingen nodig om in een ontwerppijplijn uit te voeren. Invoergegevens die u in CSV-indeling opgeeft, moeten bijvoorbeeld expliciet worden geconverteerd naar een gegevensset voordat u deze in uw code gebruiken. Gegevens- en kolomtypen die in de R-taal worden gebruikt, verschillen ook in sommige opzichten van de gegevens en kolomtypen die in de ontwerper worden gebruikt.

1.  **Willekeurig zaad:** Typ een waarde die u in de R-omgeving wilt gebruiken als de willekeurige zaadwaarde. Deze parameter is `set.seed(value)` gelijk aan het aanroepen van R-code.  

1. Verzend de pijplijn.  

## <a name="results"></a>Resultaten

De **Script-modules uitvoeren** kunnen meerdere uitvoerretouren retourneren, maar moeten worden geleverd als R-gegevensframes. Gegevensframes worden automatisch geconverteerd naar gegevenssets in de ontwerper voor compatibiliteit met andere modules.

Standaardberichten en fouten van R worden teruggestuurd naar het logboek van de module.

Als u resultaten in het R-script wilt afdrukken, u de afgedrukte resultaten vinden in de **70_driver_log** onder het tabblad **Uitvoer+logboeken** in het rechterdeelvenster van de module.

## <a name="sample-scripts"></a>Voorbeeldscripts

Er zijn vele manieren waarop u uw pijplijn uitbreiden met behulp van aangepast R-script.  In deze sectie vindt u voorbeeldcode voor veelvoorkomende taken.


### <a name="add-r-script-as-an-input"></a>R-script toevoegen als invoer

De **Module R-script uitvoeren** ondersteunt willekeurige R-scriptbestanden als ingangen. Hiervoor moeten ze worden geüpload naar uw werkruimte als onderdeel van het ZIP-bestand.

1. Als u een ZIP-bestand met R-code wilt uploaden naar uw werkruimte, gaat u naar de **assetpagina Gegevenssets,** klikt u op **Gegevensset maken**en selecteert u **Vervolgens Uit lokaal bestand** en de optie Bestandsgegevenssettype. **File**  

1. Controleer of het zip-bestand beschikbaar is in de lijst **Mijn gegevenssets** onder de categorie **Gegevenssets** in de linkermodulestructuur.

1.  Verbind de gegevensset met de invoerpoort van de **Scriptbundel.**

1. Alle bestanden die in het ZIP-bestand staan, zijn beschikbaar tijdens de looptijd van de pijplijn. 

    Als het scriptbundelbestand een mapstructuur bevat, blijft de structuur behouden. U moet echter uw code wijzigen om de map **./Script-bundel** voor te bereiden op het pad.

### <a name="process-data"></a>Gegevens verwerken

In het volgende voorbeeld ziet u hoe u invoergegevens schaalt en normaliseert:

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

### <a name="read-a-zip-file-as-input"></a>Een ZIP-bestand lezen als invoer

In dit voorbeeld ziet u hoe u een gegevensset in een ZIP-bestand gebruikt als invoer voor de **Script-module Uitvoeren.**

1. Maak het gegevensbestand in CSV-indeling en noem het "mydatafile.csv".
1. Maak een ZIP-bestand en voeg het CSV-bestand toe aan het archief.
1. Upload het zip-bestand naar uw Azure Machine Learning-werkruimte. 
1. Verbind de resulterende gegevensset met de **ScriptBundle-invoer** van de **Scriptmodule Uitvoeren.**
1. Met behulp van de volgende code om de CSV-gegevens uit het zip-bestand te lezen.

```R
azureml_main <- function(dataframe1, dataframe2){
  print("R script run.")
  mydataset<-read.csv("./Script Bundle/mydatafile.csv",encoding="UTF-8");  
  # Return datasets as a Named List
  return(list(dataset1=mydataset, dataset2=dataframe2))
}
```

### <a name="replicate-rows"></a>Rijen repliceren

In dit voorbeeld ziet u hoe u positieve records in een gegevensset repliceren om het voorbeeld in evenwicht te brengen:

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

### <a name="pass-r-objects-between-execute-r-script-modules"></a>R-objecten doorgeven tussen Script-modules uitvoeren

U R-objecten doorgeven tussen exemplaren van de **Script-module Uitvoeren** met behulp van het interne serialisatiemechanisme. In dit voorbeeld wordt ervan uitgegaan dat `A` u het R-object wilt verplaatsen dat is vernoemd tussen twee **Script-modules uitvoeren.**

1. Voeg de eerste **Script-module Uitvoeren** toe aan uw pijplijn en typ de volgende `A` code in het tekstvak R **Script** om een geserialiseerd object als kolom te maken in de uitvoergegevenstabel van de module:  
  
    ```R
    azureml_main <- function(dataframe1, dataframe2){
      print("R script run.")
      # some codes generated A
      
      serialized <- as.integer(serialize(A,NULL))  
      data.set <- data.frame(serialized,stringsAsFactors=FALSE)

      return(list(dataset1=data.set, dataset2=dataframe2))
    }
    ```

    De expliciete conversie naar het gehele getalwordt uitgevoerd omdat `Raw` de serialisatiefunctie gegevens in de R-indeling uitvoert, die niet worden ondersteund door de ontwerper.

1. Voeg een tweede instantie van de **Script-module Uitvoeren toe** en sluit deze aan op de uitvoerpoort van de vorige module.

1. Typ de volgende code in het tekstvak **R Script** om object `A` uit de invoergegevenstabel te extraheren. 

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
| Askpass      | 1.1        | 
| assertthat   | 0.2.1      | 
| backports    | 1.1.4      | 
| base         | 3.5.1      | 
| base64enc    | 0.1-3      | 
| BH           | 1.69.0-1   | 
| bindr        | 0.1.1      | 
| bindrcpp     | 0.2.2      | 
| bitops       | 1.0-6      | 
| boot         | 1.3-22     | 
| broom        | 0.5.2      | 
| callr        | 3.2.0      | 
| caret        | 6.0-84     | 
| caTools      | 1.17.1.2   | 
| cellranger   | 1.1.0      | 
| klasse        | 7.3-15     | 
| cli          | 1.1.0      | 
| clipr        | 0.6.0      | 
| cluster      | 2.0.7-1    | 
| codetools    | 0.2-16     | 
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
| foreign      | 0.8-71     | 
| Fs           | 1.3.1      | 
| gdata        | 2.18.0     | 
| Generics     | 0.0.2      | 
| ggplot2      | 3.2.0      | 
| glmnet       | 2.0-18     | 
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
| ipred        | 0.9-9      | 
| iterators    | 1.0.10     | 
| jsonlite     | 1.6        | 
| KernSmooth   | 2.23-15    | 
| knitr        | 1,23       | 
| labeling     | 0.3        | 
| lattice      | 0.20-38    | 
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
| Ps           | 1.3.0      | 
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
| sys          | 3,2        | 
| tcltk        | 3.5.1      | 
| tibble       | 2.1.3      | 
| tidyr        | 0.8.3      | 
| tidyselect   | 0.2.5      | 
| tidyverse    | 1.2.1      | 
| timeDate     | 3043.102   | 
| tinytex      | 0.13       | 
| hulpprogramma's        | 3.5.1      | 
| tseries      | 0.10-47    | 
| TTR          | 0.23-4     | 
| utf8         | 1.1.4      | 
| utils        | 3.5.1      | 
| vctrs        | 0.1.0      | 
| viridisLite  | 0.3.0      | 
| whisker      | 0.3-2      | 
| withr        | 2.1.2      | 
| xfun xfun xfun         | 0.8        | 
| xml2         | 1.2.0      | 
| xts          | 0.11-2     | 
| yaml         | 2.2.0      | 
| zeallot      | 0.1.0      | 
| zoo          | 1.8-6      | 

## <a name="next-steps"></a>Volgende stappen

Bekijk de [set modules die beschikbaar zijn](module-reference.md) voor Azure Machine Learning. 

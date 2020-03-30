---
title: U-SQL-scripts uitbreiden met R in Azure Data Lake Analytics
description: Meer informatie over het uitvoeren van R-code in U-SQL-scripts met Azure Data Lake Analytics. Inline-code insluiten of verwijzen uit bestanden.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: c1c74e5e-3e4a-41ab-9e3f-e9085da1d315
ms.topic: conceptual
ms.date: 06/20/2017
ms.openlocfilehash: c5dd3f493e85afc925b639c142a293eed1e8cbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672684"
---
# <a name="extend-u-sql-scripts-with-r-code-in-azure-data-lake-analytics"></a>U-SQL-scripts uitbreiden met R-code in Azure Data Lake Analytics

In het volgende voorbeeld worden de basisstappen voor het implementeren van R-code geïllustreerd:
* Gebruik `REFERENCE ASSEMBLY` de instructie om R-extensies in te schakelen voor het U-SQL Script.
* Gebruik `REDUCE` de bewerking om de invoergegevens op een sleutel te partitioneren.
* De R-extensies voor U-SQL bevatten een`Extension.R.Reducer`ingebouwde reducer ( ) die R-code uitvoert op elke hoekpunt toegewezen aan de reducer. 
* Het gebruik van speciale `inputFromUSQL` `outputToUSQL` benoemde gegevensframes die worden aangeroepen en respectievelijk om gegevens door te geven tussen U-SQL en R. Namen van invoer- en uitvoergegevensframe-id's zijn vastgesteld (dat wil zeggen dat gebruikers deze vooraf gedefinieerde namen van invoer- en uitvoergegevensniet kunnen wijzigen).

## <a name="embedding-r-code-in-the-u-sql-script"></a>R-code insluiten in het U-SQL-script

U de R-code van uw U-SQL-script `Extension.R.Reducer`inline met behulp van de opdrachtparameter van de. U het R-script bijvoorbeeld als een tekenreeksvariabele declareren en als parameter doorgeven aan de Reducer.


    REFERENCE ASSEMBLY [ExtR];
    
    DECLARE @myRScript = @"
    inputFromUSQL$Species = as.factor(inputFromUSQL$Species)
    lm.fit=lm(unclass(Species)~.-Par, data=inputFromUSQL)
    #do not return readonly columns and make sure that the column names are the same in usql and r scripts,
    outputToUSQL=data.frame(summary(lm.fit)$coefficients)
    colnames(outputToUSQL) <- c(""Estimate"", ""StdError"", ""tValue"", ""Pr"")
    outputToUSQL
    ";
    
    @RScriptOutput = REDUCE … USING new Extension.R.Reducer(command:@myRScript, rReturnType:"dataframe");

## <a name="keep-the-r-code-in-a-separate-file-and-reference-it--the-u-sql-script"></a>Bewaar de R-code in een apart bestand en verwijs ernaar naar het U-SQL-script

In het volgende voorbeeld wordt een complexer gebruik geïllustreerd. In dit geval wordt de R-code geïmplementeerd als een RESOURCE die het U-SQL-script is.

Sla deze R-code op als een apart bestand.

    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence")) 

Gebruik een U-SQL-script om dat R-script te implementeren met de deploy resource-instructie.

    REFERENCE ASSEMBLY [ExtR];

    DEPLOY RESOURCE @"/usqlext/samples/R/RinUSQL_PredictUsingLinearModelasDF.R";
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT 
            SepalLength double,
            SepalWidth double,
            PetalLength double,
            PetalWidth double,
            Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT 
            Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput = REDUCE @ExtendedData ON Par
        PRODUCE Par, fit double, lwr double, upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile:"RinUSQL_PredictUsingLinearModelasDF.R", rReturnType:"dataframe", stringsAsFactors:false);
        OUTPUT @RScriptOutput TO @OutputFilePredictions USING Outputters.Tsv();

## <a name="how-r-integrates-with-u-sql"></a>Hoe R integreert met U-SQL

### <a name="datatypes"></a>Datatypes
* Tekenreeks- en numerieke kolommen van U-SQL worden geconverteerd tussen R DataFrame `bool`en `integer` `byte`U-SQL [ondersteunde typen: `double`, `string`, , , ].
* Het `Factor` gegevenstype wordt niet ondersteund in U-SQL.
* `byte[]`moeten worden geserialiseerd als een `string`basis64-gecodeerd .
* U-SQL-tekenreeksen kunnen worden geconverteerd naar factoren in R-code, zodra U-SQL R-invoergegevensframe maakt of door de parameter reducer in te `stringsAsFactors: true`stellen.

### <a name="schemas"></a>Schema 's
* U-SQL-gegevenssets mogen geen dubbele kolomnamen hebben.
* Kolomnamen van U-SQL-gegevenssets moeten tekenreeksen zijn.
* Kolomnamen moeten hetzelfde zijn in U-SQL- en R-scripts.
* De kolom Voorlezen kan geen deel uitmaken van het uitvoergegevensframe. Omdat alleen-lezen kolommen automatisch worden teruggespoten in de U-SQL-tabel als het een onderdeel is van het uitvoerschema van UDO.

### <a name="functional-limitations"></a>Functionele beperkingen
* De R Engine kan niet twee keer worden geinstantieerd in hetzelfde proces. 
* Momenteel ondersteunt U-SQL geen Combiner UDO's voor voorspelling met behulp van partitiemodellen die worden gegenereerd met Reducer UDO's. Gebruikers kunnen de gepartitioneerde modellen als resource declareren en `ExtR_PredictUsingLMRawStringReducer.usql`gebruiken in hun R Script (zie voorbeeldcode)

### <a name="r-versions"></a>R-versies
Alleen R 3.2.2 wordt ondersteund.

### <a name="standard-r-modules"></a>Standaard R-modules

    base
    boot
    Class
    Cluster
    codetools
    compiler
    datasets
    doParallel
    doRSR
    foreach
    foreign
    Graphics
    grDevices
    grid
    iterators
    KernSmooth
    lattice
    MASS
    Matrix
    Methods
    mgcv
    nlme
    Nnet
    Parallel
    pkgXMLBuilder
    RevoIOQ
    revoIpe
    RevoMods
    RevoPemaR
    RevoRpeConnector
    RevoRsrConnector
    RevoScaleR
    RevoTreeView
    RevoUtils
    RevoUtilsMath
    Rpart
    RUnit
    spatial
    splines
    Stats
    stats4
    survival
    Tcltk
    Tools
    translations
    utils
    XML

### <a name="input-and-output-size-limitations"></a>Beperkingen voor invoer- en uitvoergrootte
Elke hoekpunt heeft een beperkte hoeveelheid geheugen toegewezen aan het. Omdat de invoer- en uitvoergegevensframes in het geheugen in de R-code moeten bestaan, mag de totale grootte voor de invoer en uitvoer niet groter zijn dan 500 MB.

### <a name="sample-code"></a>Voorbeeldcode
Meer voorbeeldcode is beschikbaar in uw Data Lake Store-account nadat u de U-SQL Advanced Analytics-extensies hebt geïnstalleerd. Het pad voor meer `<your_account_address>/usqlext/samples/R`voorbeeldcode is: . 

## <a name="deploying-custom-r-modules-with-u-sql"></a>Aangepaste R-modules implementeren met U-SQL

Maak eerst een aangepaste R-module en rits deze vast en upload vervolgens het aangepaste modulebestand met ritsr naar uw ADL-winkel. In het voorbeeld uploaden we magittr_1.5.zip naar de root van het standaard ADLS-account voor het ADLA-account dat we gebruiken. Zodra u de module uploadt naar de ADL-winkel, verklaart u deze als `install.packages` DEPLOY RESOURCE gebruiken om deze beschikbaar te maken in uw U-SQL-script en te bellen om deze te installeren.

    REFERENCE ASSEMBLY [ExtR];
    DEPLOY RESOURCE @"/magrittr_1.5.zip";

    DECLARE @IrisData string =  @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFileModelSummary string = @"/R/Output/CustomPackages.txt";

    // R script to run
    DECLARE @myRScript = @"
    # install the magrittr package,
    install.packages('magrittr_1.5.zip', repos = NULL),
    # load the magrittr package,
    require(magrittr),
    # demonstrate use of the magrittr package,
    2 %>% sqrt
    ";

    @InputData =
    EXTRACT SepalLength double,
    SepalWidth double,
    PetalLength double,
    PetalWidth double,
    Species string
    FROM @IrisData
    USING Extractors.Csv();

    @ExtendedData =
    SELECT 0 AS Par,
    *
    FROM @InputData;

    @RScriptOutput = REDUCE @ExtendedData ON Par
    PRODUCE Par, RowId int, ROutput string
    READONLY Par
    USING new Extension.R.Reducer(command:@myRScript, rReturnType:"charactermatrix");

    OUTPUT @RScriptOutput TO @OutputFileModelSummary USING Outputters.Tsv();

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-vensterfuncties gebruiken voor Azure Data Lake Analytics-taken](data-lake-analytics-use-window-functions.md)

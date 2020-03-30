---
title: Aan de slag met U-SQL-taal in Azure Data Lake Analytics
description: Leer de basisprincipes van de U-SQL-taal in Azure Data Lake Analytics. Schrijf uw eerste query met variabelen om extra gegevens uit bestanden te verzamelen, de rijset te transformeren en gegevens te aggregeren.
services: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.service: data-lake-analytics
ms.topic: conceptual
ms.date: 06/23/2017
ms.openlocfilehash: 8130679dcc519cecd25abf43902c003ad8047df3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672821"
---
# <a name="get-started-with-u-sql-in-azure-data-lake-analytics"></a>Aan de slag met U-SQL in Azure Data Lake Analytics
U-SQL is een taal die declaratieve SQL combineert met de noodzaak c# om u gegevens op elke schaal te laten verwerken. Via de schaalbare, gedistribueerde querymogelijkheden van U-SQL u gegevens efficiënt analyseren in relationele opslag, zoals Azure SQL Database. Met U-SQL u ongestructureerde gegevens verwerken door schema toe te passen op lezen en aangepaste logica en UDF's in te voegen. Daarnaast bevat U-SQL uitbreidbaarheid die u fijnekorrelige controle geeft over hoe u op schaal uitvoeren. 

## <a name="learning-resources"></a>Trainingsmateriaal

* De [U-SQL Tutorial](https://aka.ms/usqltutorial) biedt een begeleide walkthrough van het grootste deel van de U-SQL-taal. Dit document wordt aanbevolen om te lezen voor alle ontwikkelaars die U-SQL willen leren.
* Zie de U-SQL Language Reference voor gedetailleerde informatie over de **syntaxis van u-SQL-talen**. [U-SQL Language Reference](https://docs.microsoft.com/u-sql/)
* Om de **U-SQL-ontwerpfilosofie**te begrijpen, raadpleegt u de Visual [Studio-blogpost Introducing U-SQL – A Language that makes Big Data Processing Easy](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

## <a name="prerequisites"></a>Vereisten

Voordat u de U-SQL-voorbeelden in dit document doorloopt, leest en voltooit [u zelfstudie: Ontwikkel U-SQL-scripts met behulp van Data Lake Tools voor Visual Studio.](data-lake-analytics-data-lake-tools-get-started.md) In die zelfstudie worden de mechanismen van het gebruik van U-SQL met Azure Data Lake Tools voor Visual Studio uitgelegd.

## <a name="your-first-u-sql-script"></a>Uw eerste U-SQL-script

Het volgende U-SQL-script is eenvoudig en laat ons vele aspecten van de U-SQL-taal verkennen.

```
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
    USING Extractors.Tsv();

OUTPUT @searchlog   
    TO "/output/SearchLog-first-u-sql.csv"
    USING Outputters.Csv();
```

Dit script heeft geen transformatiestappen. Het leest uit het `SearchLog.tsv`bronbestand genaamd , schematiseert het, en schrijft de rowset terug in een bestand genaamd SearchLog-first-u-sql.csv.

Let op het vraagteken naast het `Duration` gegevenstype in het veld. Het betekent `Duration` dat het veld null kan zijn.

### <a name="key-concepts"></a>Belangrijkste concepten
* **Rowset-variabelen:** elke queryexpressie die een rijset produceert, kan aan een variabele worden toegewezen. U-SQL volgt het T-SQL`@searchlog`variabele naamgevingspatroon ( bijvoorbeeld) in het script.
* Het **trefwoord EXTRACT** leest gegevens uit een bestand en definieert het schema op gelezen. `Extractors.Tsv`is een ingebouwde U-SQL-extractor voor bestanden met een tabscheiding. U op maat gemaakte afzuigers ontwikkelen.
* De **UITVOER** schrijft gegevens van een roeiset naar een bestand. `Outputters.Csv()`is een ingebouwde U-SQL-outputter om een door komma's gescheiden waardebestand te maken. U aangepaste outputters ontwikkelen.

### <a name="file-paths"></a>Bestandspaden

De extract- en uitvoerinstructies gebruiken bestandspaden. Bestandspaden kunnen absoluut of relatief zijn:

Dit volgende absolute bestandspad verwijst naar een `mystore`bestand in een Data Lake Store met de naam:

    adl://mystore.azuredatalakestore.net/Samples/Data/SearchLog.tsv

Dit volgende bestandspad `"/"`begint met . Het verwijst naar een bestand in het standaard Data Lake Store-account:

    /output/SearchLog-first-u-sql.csv

## <a name="use-scalar-variables"></a>Scalaire variabelen gebruiken

U scalaire variabelen ook gebruiken om uw scriptonderhoud gemakkelijker te maken. Het vorige U-SQL-script kan ook worden geschreven als:

    DECLARE @in  string = "/Samples/Data/SearchLog.tsv";
    DECLARE @out string = "/output/SearchLog-scalar-variables.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM @in
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO @out
        USING Outputters.Csv();

## <a name="transform-rowsets"></a>Rijsets transformeren

Select **gebruiken** om rijsets te transformeren:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-rowsets.csv"
        USING Outputters.Csv();

De WHERE-component maakt gebruik van een [C# Booleaanse expressie](/dotnet/csharp/language-reference/operators/index). U de C#-expressietaal gebruiken om uw eigen expressies en functies uit te voeren. U zelfs complexere filtering uitvoeren door ze te combineren met logische voegtermen (AND's) en disjunctions (ORs).

In het volgende script wordt de methode DateTime.Parse() en een combinatie gebruikt.

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT Start, Region, Duration
        FROM @searchlog
    WHERE Region == "en-gb";

    @rs1 =
        SELECT Start, Region, Duration
        FROM @rs1
        WHERE Start >= DateTime.Parse("2012/02/16") AND Start <= DateTime.Parse("2012/02/17");

    OUTPUT @rs1   
        TO "/output/SearchLog-transform-datetime.csv"
        USING Outputters.Csv();

 >[!NOTE]
 >De tweede query werkt op het resultaat van de eerste rijset, waarmee een samenstelling van de twee filters wordt gemaakt. U ook een variabele naam opnieuw gebruiken en de namen worden lexically gescoped.

## <a name="aggregate-rowsets"></a>Geaggregeerde rijsets
U-SQL geeft u de bekende ORDER BY, GROUP BY en aggregaties.

In de volgende query wordt de totale duur per regio gevonden en worden de vijf hoogste duur in volgorde weergegeven.

U-SQL-rijsets behouden hun volgorde niet voor de volgende query. Om een uitvoer te bestellen, moet u dus ORDER BY toevoegen aan de uitvoerinstructie:

    DECLARE @outpref string = "/output/Searchlog-aggregation";
    DECLARE @out1    string = @outpref+"_agg.csv";
    DECLARE @out2    string = @outpref+"_top5agg.csv";

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @rs1 =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
    GROUP BY Region;

    @res =
        SELECT *
        FROM @rs1
        ORDER BY TotalDuration DESC
        FETCH 5 ROWS;

    OUTPUT @rs1
        TO @out1
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

    OUTPUT @res
        TO @out2
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

De U-SQL ORDER BY-clausule vereist het gebruik van de FETCH-component in een SELECT-expressie.

De U-SQL HAVING-clausule kan worden gebruikt om de uitvoer te beperken tot groepen die voldoen aan de HAVING-voorwaarde:

    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    @res =
        SELECT
            Region,
            SUM(Duration) AS TotalDuration
        FROM @searchlog
        GROUP BY Region
        HAVING SUM(Duration) > 200;

    OUTPUT @res
        TO "/output/Searchlog-having.csv"
        ORDER BY TotalDuration DESC
        USING Outputters.Csv();

Zie de U-SQL-referentiedocumentatie voor [aggregaat-, analytische en referentiefuncties](/u-sql/built-in-functions) voor geavanceerde aggregatiescenario's

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake-tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)

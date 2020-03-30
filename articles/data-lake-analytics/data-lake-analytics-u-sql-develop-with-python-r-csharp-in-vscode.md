---
title: U-SQL-taken uitvoeren in Python, R en C# - Azure Data Lake Analytics
description: Meer informatie over het gebruik van code achter met Python, R en C# om een taak in Azure Data Lake in te dienen.
services: data-lake-analytics
ms.service: data-lake-analytics
author: jejiang
ms.author: jejiang
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 11/22/2017
ms.openlocfilehash: cb3ddf0c4147fa982e8ab0f9d440292d12803d35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309711"
---
# <a name="develop-u-sql-with-python-r-and-c-for-azure-data-lake-analytics-in-visual-studio-code"></a>U-SQL ontwikkelen met Python, R en C# voor Azure Data Lake Analytics in Visual Studio Code
Meer informatie over het gebruik van Visual Studio Code (VSCode) om Python-, R- en C-code achter te schrijven met U-SQL en taken in te dienen bij azure data lake-service. Zie [De Azure Data Lake Tools voor Visual Studio Code gebruiken voor](data-lake-analytics-data-lake-tools-for-vscode.md)meer informatie over Azure Data Lake Tools voor VSCode.

Voordat u code-behind aangepaste code schrijft, moet u een map of werkruimte openen in VSCode.


## <a name="prerequisites-for-python-and-r"></a>Voorwaarden voor Python en R
Registreer Python- en R-extensies voor uw ADL-account. 
1. Open uw account in portal.
   - Selecteer **Overzicht**. 
   - Klik **op Voorbeeldscript**.
2. Klik op **Meer**.
3. Selecteer **U-SQL-extensies installeren**. 
4. Bevestigingsbericht wordt weergegeven nadat de U-SQL-extensies zijn geïnstalleerd. 

   ![De omgeving voor python en R instellen](./media/data-lake-analytics-data-lake-tools-for-vscode/setup-the-enrionment-for-python-and-r.png)

   > [!Note]
   > Installeer de VSCode Python- en R-extensie voor de beste ervaringen op python- en R-taalservice. 

## <a name="develop-python-file"></a>Python-bestand ontwikkelen
1. Klik op het **nieuwe bestand** in uw werkruimte.
2. Schrijf uw code in U-SQL. Het volgende is een codevoorbeeld.
    ```U-SQL
    REFERENCE ASSEMBLY [ExtPython];
    @t  = 
        SELECT * FROM 
        (VALUES
            ("D1","T1","A1","@foo Hello World @bar"),
            ("D2","T2","A2","@baz Hello World @beer")
        ) AS 
            D( date, time, author, tweet );

    @m  =
        REDUCE @t ON date
        PRODUCE date string, mentions string
        USING new Extension.Python.Reducer("pythonSample.usql.py", pyVersion : "3.5.1");

    OUTPUT @m
        TO "/tweetmentions.csv"
        USING Outputters.Csv();
    ```
    
3. Klik met de rechtermuisknop op een scriptbestand en selecteer **Vervolgens ADL: Python-code genereren achter bestand**. 
4. Het **xxx.usql.py** bestand wordt gegenereerd in uw werkmap. Schrijf uw code in Python-bestand. Het volgende is een codevoorbeeld.

    ```Python
    def get_mentions(tweet):
        return ';'.join( ( w[1:] for w in tweet.split() if w[0]=='@' ) )

    def usqlml_main(df):
        del df['time']
        del df['author']
        df['mentions'] = df.tweet.apply(get_mentions)
        del df['tweet']
        return df
    ```
5. Klik met de rechtermuisknop in **uSQL-bestand,** u op **Script compileren** of **Taak verzenden** naar taak.

## <a name="develop-r-file"></a>R-bestand ontwikkelen
1. Klik op het **nieuwe bestand** in uw werkruimte.
2. Schrijf uw code in U-SQL-bestand. Het volgende is een codevoorbeeld.
    ```U-SQL
    DEPLOY RESOURCE @"/usqlext/samples/R/my_model_LM_Iris.rda";
    DECLARE @IrisData string = @"/usqlext/samples/R/iris.csv";
    DECLARE @OutputFilePredictions string = @"/my/R/Output/LMPredictionsIris.txt";
    DECLARE @PartitionCount int = 10;

    @InputData =
        EXTRACT SepalLength double,
                SepalWidth double,
                PetalLength double,
                PetalWidth double,
                Species string
        FROM @IrisData
        USING Extractors.Csv();

    @ExtendedData =
        SELECT Extension.R.RandomNumberGenerator.GetRandomNumber(@PartitionCount) AS Par,
            SepalLength,
            SepalWidth,
            PetalLength,
            PetalWidth
        FROM @InputData;

    // Predict Species

    @RScriptOutput =
        REDUCE @ExtendedData
        ON Par
        PRODUCE Par,
                fit double,
                lwr double,
                upr double
        READONLY Par
        USING new Extension.R.Reducer(scriptFile : "RClusterRun.usql.R", rReturnType : "dataframe", stringsAsFactors : false);
    OUTPUT @RScriptOutput
    TO @OutputFilePredictions
    USING Outputters.Tsv();
    ```
3. Klik met de rechtermuisknop in **usql-bestand** en selecteer **Vervolgens ADL: R-code genereren achter bestand**. 
4. Het **xxx.usql.r-bestand** wordt gegenereerd in uw werkmap. Schrijf uw code in R-bestand. Het volgende is een codevoorbeeld.

    ```R
    load("my_model_LM_Iris.rda")
    outputToUSQL=data.frame(predict(lm.fit, inputFromUSQL, interval="confidence"))
    ```
5. Klik met de rechtermuisknop in **uSQL-bestand,** u op **Script compileren** of **Taak verzenden** naar taak.

## <a name="develop-c-file"></a>C#-bestand ontwikkelen
Een code-behind-bestand is een C#-bestand dat is gekoppeld aan één U-SQL-script. U een script definiëren dat is gewijd aan UDO, UDA, UDT en UDF in het code-behind-bestand. De UDO, UDA, UDT en UDF kunnen direct in het script worden gebruikt zonder de assemblage eerst te registreren. Het code-behind-bestand wordt in dezelfde map geplaatst als het peering U-SQL-scriptbestand. Als het script xxx.usql wordt genoemd, wordt de code-behind genoemd als xxx.usql.cs. Als u het code-behind-bestand handmatig verwijdert, wordt de code-behind-functie uitgeschakeld voor het bijbehorende U-SQL-script. Zie Aangepaste code schrijven en gebruiken in [U-SQL: Door de gebruiker gedefinieerde functies]( https://blogs.msdn.microsoft.com/visualstudio/2015/10/28/writing-and-using-custom-code-in-u-sql-user-defined-functions/)voor meer informatie over het schrijven van klantcode voor U-SQL-script.

1. Klik op het **nieuwe bestand** in uw werkruimte.
2. Schrijf uw code in U-SQL-bestand. Het volgende is een codevoorbeeld.
    ```U-SQL
    @a = 
        EXTRACT 
            Iid int,
        Starts DateTime,
        Region string,
        Query string,
        DwellTime int,
        Results string,
        ClickedUrls string 
        FROM @"/Samples/Data/SearchLog.tsv" 
        USING Extractors.Tsv();

    @d =
        SELECT DISTINCT Region 
        FROM @a;

    @d1 = 
        PROCESS @d
        PRODUCE 
            Region string,
        Mkt string
        USING new USQLApplication_codebehind.MyProcessor();

    OUTPUT @d1 
        TO @"/output/SearchLogtest.txt" 
        USING Outputters.Tsv();
    ```
3. Klik met de rechtermuisknop in **usql-bestand** en selecteer **Vervolgens ADL: CS-code genereren achter bestand**. 
4. Het **xxx.usql.cs** bestand wordt gegenereerd in uw werkmap. Schrijf uw code in CS-bestand. Het volgende is een codevoorbeeld.

    ```CS
    namespace USQLApplication_codebehind
    {
        [SqlUserDefinedProcessor]

        public class MyProcessor : IProcessor
        {
            public override IRow Process(IRow input, IUpdatableRow output)
            {
                output.Set(0, input.Get<string>(0));
                output.Set(1, input.Get<string>(0));
                return output.AsReadOnly();
            } 
        }
    }
    ```
5. Klik met de rechtermuisknop in **uSQL-bestand,** u op **Script compileren** of **Taak verzenden** naar taak.

## <a name="next-steps"></a>Volgende stappen
* [De Azure Data Lake-tools gebruiken voor Visual Studio-code](data-lake-analytics-data-lake-tools-for-vscode.md)
* [U-SQL local run en local debug met Visual Studio Code](data-lake-tools-for-vscode-local-run-and-debug.md)
* [Aan de slag met Data Lake Analytics met PowerShell](data-lake-analytics-get-started-powershell.md)
* [Aan de slag met Data Lake Analytics met de Azure-portal](data-lake-analytics-get-started-portal.md)
* [Data Lake-hulpprogramma's gebruiken voor Visual Studio voor het ontwikkelen van U-SQL-toepassingen](data-lake-analytics-data-lake-tools-get-started.md)
* [U-SQL-catalogus (Data Lake Analytics) gebruiken](data-lake-analytics-use-u-sql-catalog.md)

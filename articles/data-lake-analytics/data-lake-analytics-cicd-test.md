---
title: Uw Azure Data Lake Analytics-code testen
description: Meer informatie over het toevoegen van testcases voor U-SQL en uitgebreide C#-code voor Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 08/30/2019
ms.openlocfilehash: d568a267952a22d2e7a6b7acb6d54cf41f803367
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70913965"
---
# <a name="test-your-azure-data-lake-analytics-code"></a>Uw Azure Data Lake Analytics-code testen

Azure Data Lake biedt de [U-SQL-taal.](data-lake-analytics-u-sql-get-started.md) U-SQL combineert declaratieve SQL met de vereiste C# om gegevens op elke schaal te verwerken. In dit document leert u hoe u testcases maakt voor U-SQL en uitgebreide C#-gebruikersgedefinieerde operator (UDO)-code.

## <a name="test-u-sql-scripts"></a>U-SQL-scripts testen

Het U-SQL-script is gecompileerd en geoptimaliseerd voor uitvoerbare code die kan worden uitgevoerd in Azure of op uw lokale computer. Het compilatie- en optimalisatieproces behandelt het gehele U-SQL-script als geheel. Je niet voor elke verklaring een traditionele eenheidstest doen. Door echter de U-SQL-test SDK en de lokale SDK te gebruiken, u tests op scriptniveau uitvoeren.

### <a name="create-test-cases-for-u-sql-script"></a>Testcases maken voor U-SQL-script

Met Azure Data Lake Tools voor Visual Studio u U-SQL-scripttestcases maken.

1. Klik met de rechtermuisknop op een U-SQL-script in Solution Explorer en selecteer **Vervolgens Eenheidstest maken**.

1. Maak een nieuw testproject of voeg de testcase in een bestaand testproject in.

   ![Data Lake-hulpprogramma's voor Visual Studio - een U-SQL-testprojectconfiguratie maken](./media/data-lake-analytics-cicd-test/data-lake-tools-create-usql-test-project-configure.png)

### <a name="manage-the-test-data-source"></a>De testgegevensbron beheren

Wanneer u U-SQL-scripts test, hebt u testinvoerbestanden nodig. Als u de testgegevens wilt beheren, klikt u in **Solution Explorer**met de rechtermuisknop op het U-SQL-project en selecteert u **Eigenschappen**. U een bron invoeren in **Testgegevensbron**.

![Data Lake-hulpprogramma's voor Visual Studio - projecttestgegevensbron configureren](./media/data-lake-analytics-cicd-test/data-lake-tools-configure-project-test-data-source.png)

Wanneer u `Initialize()` de interface in de U-SQL-test SDK aanroept, wordt een tijdelijke lokale gegevenshoofdmap gemaakt onder de werkmap van het testproject. Alle bestanden en mappen in de map met testgegevensbronnen worden gekopieerd naar de tijdelijke lokale map met gegevens voordat u de U-SQL-scripttestcases uitvoert. U meer testgegevensbronmappen toevoegen door het pad van de testgegevensmap te splitsen met een puntkomma.

### <a name="manage-the-database-environment-for-testing"></a>De databaseomgeving beheren voor testen

Als uw U-SQL-scripts U-SQL-databaseobjecten gebruiken of query's gebruiken, moet u de databaseomgeving initialiseren voordat u U-SQL-testcases uitvoert. Deze aanpak kan nodig zijn bij het aanroepen van opgeslagen procedures. Met `Initialize()` de interface in de U-SQL-test SDK u alle databases implementeren die door het U-SQL-project naar de tijdelijke lokale gegevenshoofdmap verwijzen in de werkmap van het testproject.

Zie [Referentie aan een U-SQL-databaseproject](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project)voor meer informatie over het beheren van u-SQL-databaseprojectreferenties voor een U-SQL-project.

### <a name="verify-test-results"></a>Testresultaten verifiëren

De `Run()` interface retourneert een taakuitvoeringsresultaat. *0* betekent succes, en *1* betekent falen. U ook C# assert-functies gebruiken om de uitvoer te verifiëren.

### <a name="run-test-cases-in-visual-studio"></a>Testcases uitvoeren in Visual Studio

Een U-SQL script testproject is gebouwd op de top van een C # unit test framework. Nadat u het project hebt gemaakt, selecteert u**Windows** > **Test Explorer** **testen** > . U testcases uitvoeren vanuit **Test Explorer.** U ook met de rechtermuisknop op het .cs-bestand in uw eenheidstest klikken en **tests uitvoeren.**

## <a name="test-c-udos"></a>Test C# UDO's

### <a name="create-test-cases-for-c-udos"></a>Testcases maken voor C# UDO's

U een C#-testframework gebruiken om uw C#-door de gebruiker gedefinieerde operatoren (UDO's) te testen. Bij het testen van UDO's moet u overeenkomstige **IRowset-objecten** voorbereiden als ingangen.

Er zijn twee manieren om een **IRowset-object** te maken:

- Gegevens uit een bestand laden om **IRowset**te maken:

    ```csharp
    //Schema: "a:int, b:int"
    USqlColumn<int> col1 = new USqlColumn<int>("a");
    USqlColumn<int> col2 = new USqlColumn<int>("b");
    List<IColumn> columns = new List<IColumn> { col1, col2 };
    USqlSchema schema = new USqlSchema(columns);

    //Generate one row with default values
    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Get data from file
    IRowset rowset = UnitTestHelper.GetRowsetFromFile(@"processor.txt", schema, output.AsReadOnly(), discardAdditionalColumns: true, rowDelimiter: null, columnSeparator: '\t');
    ```

- Gebruik gegevens uit een gegevensverzameling om **IRowset**te maken:

    ```csharp
    //Schema: "a:int, b:int"
    USqlSchema schema = new USqlSchema(
        new USqlColumn<int>("a"),
        new USqlColumn<int>("b")
    );

    IUpdatableRow output = new USqlRow(schema, null).AsUpdatable();

    //Generate Rowset with specified values
    List<object[]> values = new List<object[]>{
        new object[2] { 2, 3 },
        new object[2] { 10, 20 }
    };

    IEnumerable<IRow> rows = UnitTestHelper.CreateRowsFromValues(schema, values);
    IRowset rowset = UnitTestHelper.GetRowsetFromCollection(rows, output.AsReadOnly());
    ```

### <a name="verify-test-results"></a>Testresultaten verifiëren

Nadat u UDO-functies hebt aanroepen, u de resultaten verifiëren via de verificatie van de waarde schema en Rowset met behulp van C# assert-functies. U een **U-SQL C# UDO Unit Test Project** toevoegen aan uw oplossing. Selecteer hiervoor **Bestand > Nieuw > Project** in Visual Studio.

### <a name="run-test-cases-in-visual-studio"></a>Testcases uitvoeren in Visual Studio

Nadat u het project hebt gemaakt, selecteert u**Windows** > **Test Explorer** **testen** > . U testcases uitvoeren vanuit **Test Explorer.** U ook met de rechtermuisknop op het .cs-bestand in uw eenheidstest klikken en **tests uitvoeren.**

## <a name="run-test-cases-in-azure-pipelines"></a>Testcases uitvoeren in Azure Pipelines<a name="run-test-cases-in-azure-devops"></a>

Zowel **U-SQL script testprojecten** als **C# UDO testprojecten** erven C# unit test projecten. De [testtaak van Visual Studio](https://docs.microsoft.com/azure/devops/pipelines/test/getting-started-with-continuous-testing?view=vsts) in Azure Pipelines kan deze testaanvragen uitvoeren.

### <a name="run-u-sql-test-cases-in-azure-pipelines"></a>U-SQL-testaanvragen uitvoeren in Azure-pijplijnen

Zorg ervoor dat u voor een `CPPSDK` U-SQL-test op `CPPSDK` uw `USqlScriptTestRunner(cppSdkFolderFullPath: @"")`buildcomputer laadt en geef het pad door aan .

#### <a name="what-is-cppsdk"></a>Wat is CPPSDK?

CPPSDK is een pakket dat Microsoft Visual C++ 14 en Windows SDK 10.0.10240.0 bevat. Dit pakket bevat de omgeving die nodig is voor de U-SQL-runtime. U dit pakket krijgen onder de installatiemap Azure Data Lake Tools for Visual Studio:

- Voor Visual Studio 2015 is het`C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK`
- Voor Visual Studio 2017 is het`C:\Program Files (x86)\Microsoft Visual Studio\2017\<Visual Studio Edition>\SDK\ScopeCppSDK`
- Voor Visual Studio 2019 is het`C:\Program Files (x86)\Microsoft Visual Studio\2019\<Visual Studio Edition>\SDK\ScopeCppSDK`

#### <a name="prepare-cppsdk-in-the-azure-pipelines-build-agent"></a>CPPSDK voorbereiden in de Azure Pipelines-buildagent

De meest voorkomende manier om de CPPSDK-afhankelijkheid in Azure Pipelines voor te bereiden, is als volgt:

1. Rits de map met de CPPSDK-bibliotheken.

1. Schakel het .zip-bestand in op uw bronbeheersysteem. Het .zip-bestand zorgt ervoor dat u alle bibliotheken onder de CPPSDK-map `.gitignore` incheckt, zodat bestanden niet worden genegeerd vanwege een bestand.

1. Rits het zip-bestand in de buildpijplijn uit.

1. Wijs `USqlScriptTestRunner` deze uitgepakte map op de buildcomputer aan.

### <a name="run-c-udo-test-cases-in-azure-pipelines"></a>C# UDO-testaanvragen uitvoeren in Azure-pijplijnen

Voor een C# UDO-test moet u verwijzen naar de volgende samenstellingen, die nodig zijn voor UDO's.

- Microsoft.Analytics.Interfaces
- Microsoft.Analytics.-typen
- Microsoft.Analytics.UnitTest

Als u ze doorverwijst via [het Nuget-pakket Microsoft.Azure.DataLake.USQL.Interfaces,](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.Interfaces/)moet u een NuGet-hersteltaak toevoegen aan uw buildpijplijn.

## <a name="next-steps"></a>Volgende stappen

- [CI/CD-pijplijn instellen voor Azure Data Lake Analytics](data-lake-analytics-cicd-overview.md)
- [U-SQL-script uitvoeren op uw lokale machine](data-lake-analytics-data-lake-tools-local-run.md)
- [U-SQL-databaseproject gebruiken om U-SQL-database te ontwikkelen](data-lake-analytics-data-lake-tools-develop-usql-database.md)

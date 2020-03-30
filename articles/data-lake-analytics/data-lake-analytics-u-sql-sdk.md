---
title: U-SQL-taken lokaal uitvoeren - Azure Data Lake U-SQL SDK
description: Lees hoe u U-SQL-taken lokaal uitvoeren en testen met behulp van de opdrachtregel en programmeerinterfaces op uw lokale werkstation.
services: data-lake-analytics
ms.service: data-lake-analytics
author: yanacai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.topic: conceptual
ms.date: 03/01/2017
ms.openlocfilehash: 51d9060eaf4b30c696ef2a3b5f798a31e2f2a98a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71309685"
---
# <a name="run-and-test-u-sql-with-azure-data-lake-u-sql-sdk"></a>U-SQL uitvoeren en testen met Azure Data Lake U-SQL SDK

Bij het ontwikkelen van U-SQL-script is het gebruikelijk om U-SQL-script lokaal uit te voeren en te testen voordat u het in de cloud indient. Azure Data Lake biedt een Nuget-pakket genaamd Azure Data Lake U-SQL SDK voor dit scenario, waarmee u u-SQL-run en -test eenvoudig schalen. Het is ook mogelijk om deze U-SQL test te integreren met CI (Continuous Integration) systeem om de compile en test te automatiseren.

Als u informatie wilt over het handmatig lokaal uitvoeren en debuggen van U-SQL-script met GUI-tooling, u daarvoor Azure Data Lake Tools voor Visual Studio gebruiken. U meer leren vanaf [hier](data-lake-analytics-data-lake-tools-local-run.md).

## <a name="install-azure-data-lake-u-sql-sdk"></a>Azure Data Lake U-SQL SDK installeren

U de Azure Data Lake U-SQL SDK [hier](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) op Nuget.org. En voordat u het gebruikt, moet u ervoor zorgen dat u afhankelijkheden als volgt hebt.

### <a name="dependencies"></a>Afhankelijkheden

De Data Lake U-SQL SDK vereist de volgende afhankelijkheden:

- [Microsoft .NET Framework 4.6 of nieuwer](https://www.microsoft.com/download/details.aspx?id=17851).
- Microsoft Visual C++ 14 en Windows SDK 10.0.10240.0 of nieuwer (wat in dit artikel CppSDK wordt genoemd). Er zijn twee manieren om CppSDK te krijgen:

  - [Visual Studio Community Edition](https://developer.microsoft.com/downloads/vs-thankyou)installeren . U hebt een map \Windows Kits\10 onder de map Programmabestanden, bijvoorbeeld C:\Program Files (x86)\Windows Kits\10\. U vindt ook de Windows 10 SDK-versie onder \Windows Kits\10\Lib. Als u deze mappen niet ziet, installeert u Visual Studio opnieuw en selecteert u de Windows 10 SDK tijdens de installatie. Als u dit hebt geïnstalleerd met Visual Studio, vindt de lokale u-SQL-compiler deze automatisch.

    ![Data Lake-hulpprogramma's voor visual studio met lokale windows 10 SDK](./media/data-lake-analytics-data-lake-tools-local-run/data-lake-tools-for-visual-studio-local-run-windows-10-sdk.png)

  - Data [Lake-hulpprogramma's installeren voor Visual Studio](https://aka.ms/adltoolsvs). U vindt de voorverpakte Visual C++ en Windows SDK-bestanden bij C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\ADL Tools\X.X.XXXX.X\CppSDK. In dit geval kan de lokale u-SQL-compiler de afhankelijkheden niet automatisch vinden. U moet het CppSDK-pad hiervoor opgeven. U de bestanden naar een andere locatie kopiëren of gebruiken zoals het is.

## <a name="understand-basic-concepts"></a>Basisconcepten begrijpen

### <a name="data-root"></a>Gegevensroot

De map met gegevensbasis is een 'lokale winkel' voor het lokale rekenaccount. Het is gelijk aan het Azure Data Lake Store-account van een Data Lake Analytics-account. Overschakelen naar een andere map met gegevensroot is net als overschakelen naar een ander winkelaccount. Als u toegang wilt krijgen tot veelgedeelde gegevens met verschillende gegevenshoofdmappen, moet u absolute paden in uw scripts gebruiken. Of maak symbolische koppelingen van het bestandssysteem (bijvoorbeeld **mklink** op NTFS) onder de map met gegevensbasis om naar de gedeelde gegevens te verwijzen.

De map met gegevensbasis wordt gebruikt om:

- Sla lokale metagegevens op, waaronder databases, tabellen, functies met een tabelwaarde (TVF's) en verzamelingen.
- Zoek de invoer- en uitvoerpaden op die worden gedefinieerd als relatieve paden in U-SQL. Het gebruik van relatieve paden maakt het eenvoudiger om uw U-SQL-projecten te implementeren in Azure.

### <a name="file-path-in-u-sql"></a>Bestandspad in U-SQL

U zowel een relatief pad als een lokaal absoluut pad gebruiken in U-SQL-scripts. Het relatieve pad is relatief ten opzichte van het opgegeven pad met gegevensbasismap. We raden u aan "/" als padscheidingsteken te gebruiken om uw scripts compatibel te maken met de serverzijde. Hier zijn enkele voorbeelden van relatieve paden en hun gelijkwaardige absolute paden. In deze voorbeelden is C:\LocalRunDataRoot de map met gegevensbasis.

|Relatief pad|Absoluut pad|
|-------------|-------------|
|/abc/def/input.csv |C:\LocalRunDataRoot\abc\def\input.csv|
|abc/def/input.csv  |C:\LocalRunDataRoot\abc\def\input.csv|
|D:/abc/def/input.csv |D:\abc\def\input.csv|

### <a name="working-directory"></a>Werkmap

Wanneer u het U-SQL-script lokaal uitvoert, wordt een werkmap gemaakt tijdens de compilatie onder de huidige werkmap. Naast de compilatie-uitgangen worden de benodigde runtime-bestanden voor lokale uitvoering schaduw gekopieerd naar deze werkmap. De hoofdmap voor werkende mappen wordt "ScopeWorkDir" genoemd en de bestanden onder de werkmap zijn als volgt:

|Map/bestand|Map/bestand|Map/bestand|Definitie|Beschrijving|
|--------------|--------------|--------------|----------|-----------|
|C6A101DDCB470506| | |Hash-tekenreeks van runtime-versie|Schaduwkopie van runtime-bestanden die nodig zijn voor lokale uitvoering|
| |Script_66AE4909AA0ED06C| |Scriptnaam + hashreeks van scriptpad|Compilatie-uitvoer en logboekregistratie van uitvoeringsstappen|
| | |\_script\_.abr|Compileruitvoer|Algebra-bestand|
| | |\_ScopeCodeGen\_.*|Compileruitvoer|Gegenereerde beheerde code|
| | |\_ScopeCodeGenEngine\_.*|Compileruitvoer|Gegenereerde native code|
| | |naar basisverzamelingen verwezen|Assemblageverwijzing|Gerefereerde assemblagebestanden|
| | |deployed_resources|Resourceimplementatie|Bronimplementatiebestanden|
| | |xxxxxxxx.xxx[1..n]\_\*.*|Uitvoeringslogboek|Logboek van uitvoeringsstappen|


## <a name="use-the-sdk-from-the-command-line"></a>De SDK gebruiken vanaf de opdrachtregel

### <a name="command-line-interface-of-the-helper-application"></a>Command-line interface van de helpertoepassing

Onder SDK-map\build\runtime is LocalRunHelper.exe de hulptoepassing voor de opdrachtregel die interfaces biedt voor de meeste veelgebruikte functies die lokaal worden uitgevoerd. Houd er rekening mee dat zowel de opdracht- als de argumentschakelaars hoofdlettergevoelig zijn. Om het aan te roepen:

    LocalRunHelper.exe <command> <Required-Command-Arguments> [Optional-Command-Arguments]

Voer LocalRunHelper.exe uit zonder argumenten of met de **help-schakelaar** om de help-informatie weer te geven:

    > LocalRunHelper.exe help

        Command 'help' :  Show usage information
        Command 'compile' :  Compile the script
        Required Arguments :
            -Script param
                    Script File Path
        Optional Arguments :
            -Shallow [default value 'False']
                    Shallow compile

In de help-informatie:

-  **De opdracht** geeft de naam van de opdracht.  
-  **Vereist Argument** bevat argumenten die moeten worden opgegeven.  
-  **In het optionele argument** worden argumenten weergegeven die optioneel zijn, met standaardwaarden.  Optionele Booleaanse argumenten hebben geen parameters en hun uiterlijk betekent negatief voor de standaardwaarde.

### <a name="return-value-and-logging"></a>Retourwaarde en logboekregistratie

De helpertoepassing retourneert **0** voor succes en **-1** voor uitval. Standaard stuurt de helper alle berichten naar de huidige console. De meeste opdrachten ondersteunen echter het optionele argument **-MessageOut path_to_log_file** dat de uitvoer naar een logboekbestand omleidt.

### <a name="environment-variable-configuring"></a>Omgevingsvariabele configureren

U-SQL local run heeft een opgegeven gegevensroot nodig als lokaal opslagaccount, evenals een opgegeven CppSDK-pad voor afhankelijkheden. U het argument zowel in opdrachtregel instellen als omgevingsvariabele instellen.

- Stel de **SCOPE_CPP_SDK** omgevingsvariabele in.

    Als u Microsoft Visual C++ en de Windows SDK krijgt door Hulpmiddelen voor Gegevensmeer voor Visual Studio te installeren, controleert u of u de volgende map hebt:

        C:\Program Files (x86)\Microsoft Visual Studio 14.0\Common7\IDE\Extensions\Microsoft\Microsoft Azure Data Lake Tools for Visual Studio 2015\X.X.XXXX.X\CppSDK

    Definieer een nieuwe omgevingsvariabele die **SCOPE_CPP_SDK** om naar deze map te wijzen. Of kopieer de map naar de andere locatie en geef **SCOPE_CPP_SDK** op.

    Naast het instellen van de omgevingsvariabele u het argument **-CppSDK** opgeven wanneer u de opdrachtregel gebruikt. Dit argument overschrijft uw standaard CppSDK-omgevingsvariabele.

- Stel de **LOCALRUN_DATAROOT** omgevingsvariabele in.

    Definieer een nieuwe omgevingsvariabele genaamd **LOCALRUN_DATAROOT** die verwijst naar de gegevensroot.

    Naast het instellen van de omgevingsvariabele u het argument **-DataRoot** opgeven met het gegevenshoofdpad wanneer u een opdrachtregel gebruikt. Met dit argument wordt de standaardvariabele gegevens-root-omgeving overschrijft. U moet dit argument toevoegen aan elke opdrachtregel die u uitvoert, zodat u de standaardvariabele gegevenshoofdomgeving voor alle bewerkingen overschrijven.

### <a name="sdk-command-line-usage-samples"></a>Voorbeelden voor het gebruik van SDK-opdrachtregel

#### <a name="compile-and-run"></a>Compileren en uitvoeren

De **opdracht uitvoeren** wordt gebruikt om het script samen te stellen en vervolgens gecompileerde resultaten uit te voeren. De command-line argumenten zijn een combinatie van die van **compileren** en **uit te voeren**.

    LocalRunHelper run -Script path_to_usql_script.usql [optional_arguments]

De volgende argumenten zijn optionele argumenten voor **het uitvoeren:**


|Argument|Standaardwaarde|Beschrijving|
|--------|-------------|-----------|
|-CodeBehind|False|Het script heeft .cs-code achter|
|-CppSDK| |CppSDK-directory|
|-DataRoot| DataRoot-omgevingsvariabele|DataRoot voor lokale run, standaard naar 'LOCALRUN_DATAROOT' omgevingsvariabele|
|-MessageOut| |Berichten op console naar een bestand dumpen|
|-Parallel|1|Het plan uitvoeren met het opgegeven parallellisme|
|-Referenties| |Lijst van paden naar extra referentieverzamelingen of gegevensbestanden van code achter, gescheiden door ';'|
|-UdoRedirect|False|Udo-assemblageomleidingsconfig genereren|
|-GebruikDatabase|model|Database te gebruiken voor code achter tijdelijke assemblageregistratie|
|-Verbose|False|Gedetailleerde uitvoer van runtime weergeven|
|-WorkDir|Huidige map|Map voor compilergebruik en -uitvoer|
|-RunScopeCEP|0|ScopeCEP-modus om te gebruiken|
|-ScopeCEPTempPath|Temp|Temp pad te gebruiken voor streaming gegevens|
|-OptFlags| |Door komma's gescheiden lijst met optimizervlaggen|


Hier volgt een voorbeeld:

    LocalRunHelper run -Script d:\test\test1.usql -WorkDir d:\test\bin -CodeBehind -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB –Parallel 5 -Verbose

Naast het combineren van **compileren** en **uitvoeren,** u de gecompileerde executables apart compileren en uitvoeren.

#### <a name="compile-a-u-sql-script"></a>Een U-SQL-script compileren

De **opdracht compileren** wordt gebruikt om een U-SQL-script te compileren voor uitvoerbare bestanden.

    LocalRunHelper compile -Script path_to_usql_script.usql [optional_arguments]

De volgende argumenten voor **het compileren**zijn:


|Argument|Beschrijving|
|--------|-----------|
| -CodeBehind [standaardwaarde 'False']|Het script heeft .cs-code achter|
| -CppSDK [standaardwaarde '']|CppSDK-directory|
| -DataRoot [standaardwaarde 'DataRoot-omgevingsvariabele']|DataRoot voor lokale run, standaard naar 'LOCALRUN_DATAROOT' omgevingsvariabele|
| -MessageOut [standaardwaarde '']|Berichten op console naar een bestand dumpen|
| -Verwijzingen [standaardwaarde '']|Lijst van paden naar extra referentieverzamelingen of gegevensbestanden van code achter, gescheiden door ';'|
| -Ondiep [standaardwaarde 'False']|Ondiep compileren|
| -UdoRedirect [standaardwaarde 'False']|Udo-assemblageomleidingsconfig genereren|
| -UseDatabase [standaardwaarde 'master']|Database te gebruiken voor code achter tijdelijke assemblageregistratie|
| -WorkDir [standaardwaarde 'Huidige map']|Map voor compilergebruik en -uitvoer|
| -RunScopeCEP [standaardwaarde '0']|ScopeCEP-modus om te gebruiken|
| -ScopeCEPTempPath [standaardwaarde 'temp']|Temp pad te gebruiken voor streaming gegevens|
| -OptFlags [standaardwaarde '']|Door komma's gescheiden lijst met optimizervlaggen|


Hier zijn enkele gebruiksvoorbeelden.

Stel een U-SQL-script samen:

    LocalRunHelper compile -Script d:\test\test1.usql

Stel een U-SQL-script samen en stel de map met gegevensbasis in. Houd er rekening mee dat hiermee de variabele voor de ingestelde omgeving wordt overschreven.

    LocalRunHelper compile -Script d:\test\test1.usql –DataRoot c:\DataRoot

Stel een U-SQL-script samen en stel een werkmap, referentieverzameling en database in:

    LocalRunHelper compile -Script d:\test\test1.usql -WorkDir d:\test\bin -References "d:\asm\ref1.dll;d:\asm\ref2.dll" -UseDatabase testDB

#### <a name="execute-compiled-results"></a>Gecompileerde resultaten uitvoeren

De opdracht **uitvoeren** wordt gebruikt om gecompileerde resultaten uit te voeren.   

    LocalRunHelper execute -Algebra path_to_compiled_algebra_file [optional_arguments]

De volgende argumenten zijn optionele argumenten voor **uitvoering:**

|Argument|Standaardwaarde|Beschrijving|
|--------|-------------|-----------|
|-DataRoot | '' |Gegevensroot voor het uitvoeren van metagegevens. Het standaard aan de **LOCALRUN_DATAROOT** omgevingsvariabele.|
|-MessageOut | '' |Berichten op de console naar een bestand dumpen.|
|-Parallel | '1' |Indicator om de gegenereerde lokale stappen uit te voeren met het opgegeven parallellismeniveau.|
|-Verbose | 'Vals' |Indicator om gedetailleerde uitgangen van runtime weer te geven.|

Hier is een gebruiksvoorbeeld:

    LocalRunHelper execute -Algebra d:\test\workdir\C6A101DDCB470506\Script_66AE4909AA0ED06C\__script__.abr –DataRoot c:\DataRoot –Parallel 5


## <a name="use-the-sdk-with-programming-interfaces"></a>De SDK gebruiken met programmeerinterfaces

De programmeerinterfaces bevinden zich allemaal in de LocalRunHelper.exe. U ze gebruiken om de functionaliteit van de U-SQL SDK en het C#-testframework te integreren om uw lokale U-SQL-script-test te schalen. In dit artikel zal ik het standaard C#-testproject gebruiken om te laten zien hoe u deze interfaces gebruiken om uw U-SQL-script te testen.

### <a name="step-1-create-c-unit-test-project-and-configuration"></a>Stap 1: C#-testproject en -configuratie maken

- Maak een C#-testproject via File > New > Project > Visual C# > Test > Unit Test Project.
- Voeg LocalRunHelper.exe toe als referentie voor het project. De LocalRunHelper.exe bevindt zich op het pakket \build\runtime\LocalRunHelper.exe in Nuget.

    ![Naslaginformatie over U-SQL SDK-toevoegen van Azure Data Lake](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-add-reference.png)

- U-SQL SDK ondersteunt **alleen** de X64-omgeving, zorg ervoor dat u het doel van het buildplatform instelt als x64. U dat instellen via projectproperty > build > platformtarget.

    ![Azure Data Lake U-SQL SDK Configureren x64-project](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-x64.png)

- Zorg ervoor dat u uw testomgeving instelt als x64. In Visual Studio u deze instellen via Test > Test Settings > Default Processor Architecture > x64.

    ![Azure Data Lake U-SQL SDK Configureren x64-testomgeving](./media/data-lake-analytics-u-sql-sdk/data-lake-analytics-u-sql-sdk-configure-test-x64.png)

- Zorg ervoor dat u alle afhankelijkheidsbestanden kopieert onder NugetPackage\build\runtime\ naar de werkmap van het project, die meestal onder ProjectFolder\bin\x64\Debug staat.

### <a name="step-2-create-u-sql-script-test-case"></a>Stap 2: U-SQL-scripttestcase maken

Hieronder vindt u de voorbeeldcode voor U-SQL-scripttests. Voor het testen moet u scripts, invoerbestanden en verwachte uitvoerbestanden voorbereiden.

    using System;
    using Microsoft.VisualStudio.TestTools.UnitTesting;
    using System.IO;
    using System.Text;
    using System.Security.Cryptography;
    using Microsoft.Analytics.LocalRun;

    namespace UnitTestProject1
    {
        [TestClass]
        public class USQLUnitTest
        {
            [TestMethod]
            public void TestUSQLScript()
            {
                //Specify the local run message output path
                StreamWriter MessageOutput = new StreamWriter("../../../log.txt");

                LocalRunHelper localrun = new LocalRunHelper(MessageOutput);

                //Configure the DateRoot path, Script Path and CPPSDK path
                localrun.DataRoot = "../../../";
                localrun.ScriptPath = "../../../Script/Script.usql";
                localrun.CppSdkDir = "../../../CppSDK";

                //Run U-SQL script
                localrun.DoRun();

                //Script output 
                string Result = Path.Combine(localrun.DataRoot, "Output/result.csv");

                //Expected script output
                string ExpectedResult = "../../../ExpectedOutput/result.csv";

                Test.Helpers.FileAssert.AreEqual(Result, ExpectedResult);

                //Don't forget to close MessageOutput to get logs into file
                MessageOutput.Close();
            }
        }
    }

    namespace Test.Helpers
    {
        public static class FileAssert
        {
            static string GetFileHash(string filename)
            {
                Assert.IsTrue(File.Exists(filename));

                using (var hash = new SHA1Managed())
                {
                    var clearBytes = File.ReadAllBytes(filename);
                    var hashedBytes = hash.ComputeHash(clearBytes);
                    return ConvertBytesToHex(hashedBytes);
                }
            }

            static string ConvertBytesToHex(byte[] bytes)
            {
                var sb = new StringBuilder();

                for (var i = 0; i < bytes.Length; i++)
                {
                    sb.Append(bytes[i].ToString("x"));
                }
                return sb.ToString();
            }

            public static void AreEqual(string filename1, string filename2)
            {
                string hash1 = GetFileHash(filename1);
                string hash2 = GetFileHash(filename2);

                Assert.AreEqual(hash1, hash2);
            }
        }
    }


### <a name="programming-interfaces-in-localrunhelperexe"></a>Programmeerinterfaces in LocalRunHelper.exe

LocalRunHelper.exe biedt de programmeerinterfaces voor U-SQL local compile, run, etc. De interfaces worden als volgt weergegeven.

**Constructor**

openbare LocalRunHelper([System.IO.TextWriter messageOutput = null])

|Parameter|Type|Beschrijving|
|---------|----|-----------|
|berichtUitvoer|System.IO.TextWriter|voor uitvoerberichten, ingesteld op null om Console te gebruiken|

**Eigenschappen**

|Eigenschap|Type|Beschrijving|
|--------|----|-----------|
|AlgebraPath AlgebraPath|tekenreeks|Het pad naar algebra-bestand (algebra-bestand is een van de compilatieresultaten)|
|CodeBehindReferences|tekenreeks|Als het script extra code achter verwijzingen heeft, geeft u de paden op die zijn gescheiden met ';'|
|CppSdkDir CppSdkDir|tekenreeks|CppSDK-map|
|HuidigeDir|tekenreeks|Huidige map|
|DataRoot (DataRoot)|tekenreeks|Hoofdpad voor gegevens|
|DebuggerMailPath DebuggerMailPath|tekenreeks|Het pad om het foutopsporingsslot te debuggeren|
|UdoRedirect genereren|Booleaanse waarde|Als we willen monteren laden omleiding overschrijven config genereren|
|HasCodeBehind HasCodeBehind|Booleaanse waarde|Als het script code achter heeft|
|InvoerDir|tekenreeks|Map voor invoergegevens|
|MessagePath (MessagePath)|tekenreeks|Bestandspad berichtdump|
|UitvoerDir|tekenreeks|Map voor uitvoergegevens|
|Parallellisme|int|Parallelisme om de algebra uit te voeren|
|ParentPid ParentPid|int|PID van de ouder waarop de service controleert om af te sluiten, ingesteld op 0 of negatief om te negeren|
|ResultPath|tekenreeks|Pad voor het dumpen van resultaat|
|RuntimeDir RuntimeDir|tekenreeks|Runtime-map|
|Scriptpad|tekenreeks|Waar vind je het script?|
|Ondiepe|Booleaanse waarde|Ondiepe compileren of niet|
|Tempdir|tekenreeks|Tijdelijke map|
|UseDataBase|tekenreeks|Standaard de database opgeven die moet worden gebruikt voor code achter tijdelijke assemblageregistratie, stramien|
|WorkDir (WorkDir)|tekenreeks|Voorkeurswerkmap|


**Methode**

|Methode|Beschrijving|Terug|Parameter|
|------|-----------|------|---------|
|openbare bool DoCompile()|Het U-SQL-script compileren|Waar op succes| |
|openbare bool DoExec()|Het gecompileerde resultaat uitvoeren|Waar op succes| |
|openbare bool DoRun()|Het U-SQL-script uitvoeren (Compileren + uitvoeren)|Waar op succes| |
|openbare bool IsValidRuntimeDir(tekenreekspad)|Controleren of het opgegeven pad een geldig runtimepad is|Geldt voor geldig|Het pad van de runtime-map|


## <a name="faq-about-common-issue"></a>Veelgestelde vragen over veelvoorkomend probleem

### <a name="error-1"></a>Fout 1:
E_CSC_SYSTEM_INTERNAL: Interne fout! Kan geen bestand of assemblage 'ScopeEngineManaged.dll' of een van de afhankelijkheden laden. De opgegeven module kan niet worden gevonden.

Controleer het volgende:

- Zorg ervoor dat je een x64-omgeving hebt. Het build target platform en de testomgeving moeten x64 zijn, zie **stap 1: C# unit testproject en configuratie** hierboven maken.
- Zorg ervoor dat u alle afhankelijkheidsbestanden onder NugetPackage\build\runtime\ hebt gekopieerd naar de werkmap van het project.


## <a name="next-steps"></a>Volgende stappen

* Zie [Aan de slag met de Azure Data Lake Analytics U-SQL-taal](data-lake-analytics-u-sql-get-started.md) om U-SQL te leren.
* Zie [Diagnostische logboeken voor Azure Data Lake Analytics openen voor](data-lake-analytics-diagnostic-logs.md)diagnostische gegevens.
* Zie [Websitelogboeken analyseren met Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md)voor een complexere query.
* Zie [Taakbrowser en taakweergave gebruiken voor Azure Data Lake Analytics-taken](data-lake-analytics-data-lake-tools-view-jobs.md)als u taakgegevens wilt weergeven.
* Zie [De uitvoeringsweergave Vertex gebruiken in Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)als u de weergave vertex-uitvoering wilt gebruiken.

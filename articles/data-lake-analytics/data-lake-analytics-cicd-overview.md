---
title: Een CI/CD-pijplijn instellen voor Azure Data Lake Analytics
description: Meer informatie over het instellen van continue integratie en continue implementatie voor Azure Data Lake Analytics.
services: data-lake-analytics
author: yanancai
ms.author: yanacai
ms.reviewer: jasonwhowell
ms.assetid: 66dd58b1-0b28-46d1-aaae-43ee2739ae0a
ms.service: data-lake-analytics
ms.topic: conceptual
ms.workload: big-data
ms.date: 09/14/2018
ms.openlocfilehash: b035be727df2dfecb613da79681affd740c69bec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60333866"
---
# <a name="how-to-set-up-a-cicd-pipeline-for-azure-data-lake-analytics"></a>Een CI/CD-pijplijn instellen voor Azure Data Lake Analytics  

In dit artikel leert u hoe u een CI/CD-pijplijn (continuous integration and deployment) instelt voor U-SQL-taken en U-SQL-databases.  

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-cicd-for-u-sql-jobs"></a>CI/CD gebruiken voor U-SQL-taken

Azure Data Lake Tools voor Visual Studio biedt het U-SQL-projecttype waarmee u U-SQL-scripts worden georganiseerd. Het gebruik van het U-SQL-project om uw U-SQL-code te beheren, maakt verdere CI/CD-scenario's eenvoudig.

## <a name="build-a-u-sql-project"></a>Een U-SQL-project bouwen

Een U-SQL-project kan worden gebouwd met de Microsoft Build Engine (MSBuild) door overeenkomstige parameters door te geven. Volg de stappen in dit artikel om een buildproces voor een U-SQL-project in te stellen.

### <a name="project-migration"></a>Projectmigratie

Voordat u een buildtaak instelt voor een U-SQL-project, moet u ervoor zorgen dat u over de nieuwste versie van het U-SQL-project beschikt. Open het U-SQL-projectbestand in uw editor en controleer of u deze importitems hebt:

```   
<!-- check for SDK Build target in current path then in USQLSDKPath-->
<Import Project="UsqlSDKBuild.targets" Condition="Exists('UsqlSDKBuild.targets')" />
<Import Project="$(USQLSDKPath)\UsqlSDKBuild.targets" Condition="!Exists('UsqlSDKBuild.targets') And '$(USQLSDKPath)' != '' And Exists('$(USQLSDKPath)\UsqlSDKBuild.targets')" />
``` 

Als dit niet het gevolg is, hebt u twee opties om het project te migreren:

- Optie 1: Wijzig het oude importitem in het vorige item.
- Optie 2: Open het oude project in de Azure Data Lake-hulpprogramma's voor Visual Studio. Gebruik een nieuwere versie dan 2.3.3000.0. De oude projectsjabloon wordt automatisch geüpgraded naar de nieuwste versie. Nieuwe projecten die zijn gemaakt met versies die nieuwer zijn dan 2.3.3000.0, gebruiken de nieuwe sjabloon.

### <a name="get-nuget"></a>Get NuGet

MSBuild biedt geen ingebouwde ondersteuning voor U-SQL-projecten. Om deze ondersteuning te krijgen, moet u een referentie voor uw oplossing toevoegen aan het [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-pakket dat de vereiste taalservice toevoegt.

Als u de nuget-pakketverwijzing wilt toevoegen, klikt u met de rechtermuisknop op de oplossing in Visual Studio Solution Explorer en kiest u **NuGet-pakketten beheren.** U ook een `packages.config` bestand toevoegen dat in de oplossingsmap wordt aangeroepen en de volgende inhoud erin plaatsen:

```xml 
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180620" targetFramework="net452" />
</packages>
``` 

### <a name="manage-u-sql-database-references"></a>U-SQL-databaseverwijzingen beheren

U-SQL-scripts in een U-SQL-project kunnen query-instructies voor U-SQL-databaseobjecten bevatten. In dat geval moet u verwijzen naar het bijbehorende U-SQL-databaseproject dat de definitie van de objecten bevat voordat u het U-SQL-project bouwt. Een voorbeeld hiervan is wanneer u een U-SQL-tabel opvraagt of een vergadering naverwijst. 

Meer informatie over [U-SQL-databaseproject](data-lake-analytics-data-lake-tools-develop-usql-database.md).

>[!NOTE]
>Drop-instructie kan leiden tot ongeval verwijderen probleem. Als u de instructie DROP wilt inschakelen, moet u de argumenten van MSBuild expliciet opgeven. **AllowDropStatement** schakelt niet-gegevensgerelateerde DROP-bewerking in, zoals de functie Drop Assembly en Drop Table Valued. **AllowDataDropStatement** schakelt gegevensgerelateerde DROP-bewerking in, zoals droptable en dropschema. U moet AllowDropStatement inschakelen voordat u AllowDataDropStatement gebruikt.
>

### <a name="build-a-u-sql-project-with-the-msbuild-command-line"></a>Een U-SQL-project bouwen met de MSBuild-opdrachtregel

Migreer eerst het project en ontvang het NuGet-pakket. Bel vervolgens de standaard MSBuild-opdrachtregel met de volgende aanvullende argumenten om uw U-SQL-project te bouwen: 

``` 
msbuild USQLBuild.usqlproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime;USQLTargetType=SyntaxCheck;DataRoot=datarootfolder;/p:EnableDeployment=true
``` 

De definitie en waarden van argumenten zijn als volgt:

* **USQLSDKPath=\<U-SQL Nuget-pakket>\build\runtime**. Deze parameter verwijst naar het installatiepad van het NuGet-pakket voor de U-SQL-taalservice.
* **USQLTargetType=Samenvoegen of SyntaxCheck:**
    * **Samenvoegen**. De samenvoegmodus compileert bestanden achter de code. Voorbeelden zijn **.cs,** **.py**en **.r-bestanden.** Hiermee wordt de resulterende door de gebruiker gedefinieerde codebibliotheek in het U-SQL-script opgenomen. Voorbeelden zijn een dll binary, Python of R-code.
    * **SyntaxisControle**. SyntaxCheck-modus voegt eerst code-behindbestanden samen in het U-SQL-script. Vervolgens wordt het U-SQL-script gecompileerd om uw code te valideren.
* **DataRoot=\<DataRoot-pad>**. DataRoot is alleen nodig voor de syntaxiscontrolemodus. Wanneer het script wordt opgebouwd met de syntaxcheckmodus, controleert MSBuild de verwijzingen naar databaseobjecten in het script. Stel voor het bouwen een overeenkomende lokale omgeving in die de objecten van de U-SQL-database bevat in de DataRoot-map van de buildmachine. U deze databaseafhankelijkheden ook beheren door [te verwijzen naar een U-SQL-databaseproject.](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project) MSBuild controleert alleen databaseobjectverwijzingen, geen bestanden.
* **EnableDeployment=true** of **false**. EnableDeployment geeft aan of het is toegestaan om u-SQL-databases met verwijzingen te implementeren tijdens het buildproces. Als u verwijst naar een U-SQL-databaseproject en de databaseobjecten in uw U-SQL-script gebruikt, stelt u deze parameter in **op true.**

### <a name="continuous-integration-through-azure-pipelines"></a>Continue integratie via Azure Pipelines

Naast de opdrachtregel u ook de Visual Studio Build of een MSBuild-taak gebruiken om U-SQL-projecten in Azure Pipelines te bouwen. Als u een buildpijplijn wilt instellen, moet u twee taken toevoegen aan de buildpijplijn: een NuGet-hersteltaak en een MSBuild-taak.

![MSBuild-taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 

1.  Voeg een NuGet-hersteltaak toe om het nuget-pakket `Azure.DataLake.USQL.SDK`waarnaar wordt verwezen, zodat MSBuild de U-SQL-taaldoelen kan vinden. Stel **de** > map `$(Build.SourcesDirectory)/packages` Geavanceerde**bestemming** in als u het voorbeeld van de argumenten VAN MSBuild direct in stap 2 wilt gebruiken.

    ![NuGet-hersteltaak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2.  Stel MSBuild-argumenten in Visual Studio buildtools of in een MSBuild-taak in, zoals in het volgende voorbeeld wordt weergegeven. U ook variabelen voor deze argumenten definiëren in de pijplijn Azure Pipelines.

    ![CI/CD MSBuild-variabelen definiëren voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables.png) 

    ```
    /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime /p:USQLTargetType=SyntaxCheck /p:DataRoot=$(Build.SourcesDirectory) /p:EnableDeployment=true
    ```

### <a name="u-sql-project-build-output"></a>U-SQL-projectbuild-uitvoer

Nadat u een build hebt uitgevoerd, worden alle scripts in het `USQLProjectName.usqlpack`U-SQL-project gebouwd en naar een zip-bestand met de naam . De mapstructuur in uw project wordt bewaard in de uitvoer van de met rits rits.

> [!NOTE]
>
> Code-behindbestanden voor elk U-SQL-script worden samengevoegd als een inline-instructie voor de scriptbuild-uitvoer.
>

## <a name="test-u-sql-scripts"></a>U-SQL-scripts testen

Azure Data Lake biedt testprojecten voor U-SQL-scripts en C# UDO/UDAG/UDF:
* Meer informatie over het [toevoegen van testcases voor U-SQL-scripts en uitgebreide C#-code](data-lake-analytics-cicd-test.md#test-u-sql-scripts).
* Meer informatie over het [uitvoeren van testaanvragen in Azure Pipelines](data-lake-analytics-cicd-test.md#run-test-cases-in-azure-devops).

## <a name="deploy-a-u-sql-job"></a>Een U-SQL-taak implementeren

Nadat u code hebt geverifieerd via het build- en testproces, u U-SQL-taken rechtstreeks vanuit Azure Pipelines indienen via een Azure PowerShell-taak. U het script ook implementeren in Azure Data Lake Store- of Azure Blob-opslag en [de geplande taken uitvoeren via Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics).

### <a name="submit-u-sql-jobs-through-azure-pipelines"></a>U-SQL-taken verzenden via Azure-pijplijnen

De build output van het U-SQL project is een zip-bestand genaamd **USQLProjectName.usqlpack**. Het zip-bestand bevat alle U-SQL-scripts in het project. U de [Azure PowerShell-taak](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) in Pipelines gebruiken met het volgende voorbeeld PowerShell-script om U-SQL-taken rechtstreeks vanuit Azure Pipelines in te dienen.

```powershell
<#
    This script can be used to submit U-SQL Jobs with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and submit all scripts one-by-one.

    Note: the code behind file for each U-SQL script will be merged into the built U-SQL script in build output.
          
    Example :
        USQLJobSubmission.ps1 -ADLAAccountName "myadlaaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\" -DegreeOfParallelism 2
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLAAccountName, # ADLA account name to submit U-SQL jobs
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DegreeOfParallelism = 1
)

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

## Get U-SQL scripts in U-SQL project build output(.usqlpack file)
Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    $USQLFiles = Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue

    return $USQLFiles
}

## Submit U-SQL scripts to ADLA account one-by-one
Function SubmitAnalyticsJob()
{
    $usqlFiles = GetUsqlFiles

    Write-Output "$($usqlFiles.Count) jobs to be submitted..."

    # Submit each usql script and wait for completion before moving ahead.
    foreach ($usqlFile in $usqlFiles)
    {
        $scriptName = "[Release].[$([System.IO.Path]::GetFileNameWithoutExtension($usqlFile.fullname))]"

        Write-Output "Submitting job for '{$usqlFile}'"

        $jobToSubmit = Submit-AzDataLakeAnalyticsJob -Account $ADLAAccountName -Name $scriptName -ScriptPath $usqlFile -DegreeOfParallelism $DegreeOfParallelism
        
        LogJobInformation $jobToSubmit
        
        Write-Output "Waiting for job to complete. Job ID:'{$($jobToSubmit.JobId)}', Name: '$($jobToSubmit.Name)' "
        $jobResult = Wait-AzDataLakeAnalyticsJob -Account $ADLAAccountName -JobId $jobToSubmit.JobId  
        LogJobInformation $jobResult
    }
}

Function LogJobInformation($jobInfo)
{
    Write-Output "************************************************************************"
    Write-Output ([string]::Format("Job Id: {0}", $(DefaultIfNull $jobInfo.JobId)))
    Write-Output ([string]::Format("Job Name: {0}", $(DefaultIfNull $jobInfo.Name)))
    Write-Output ([string]::Format("Job State: {0}", $(DefaultIfNull $jobInfo.State)))
    Write-Output ([string]::Format("Job Started at: {0}", $(DefaultIfNull  $jobInfo.StartTime)))
    Write-Output ([string]::Format("Job Ended at: {0}", $(DefaultIfNull  $jobInfo.EndTime)))
    Write-Output ([string]::Format("Job Result: {0}", $(DefaultIfNull $jobInfo.Result)))
    Write-Output "************************************************************************"
}

Function DefaultIfNull($item)
{
    if ($item -ne $null)
    {
        return $item
    }
    return ""
}

Function Main()
{
    Write-Output ([string]::Format("ADLA account: {0}", $ADLAAccountName))
    Write-Output ([string]::Format("Root folde for usqlpack: {0}", $ArtifactsRoot))
    Write-Output ([string]::Format("AU count: {0}", $DegreeOfParallelism))

    Write-Output "Starting USQL script deployment..."
    
    SubmitAnalyticsJob

    Write-Output "Finished deployment..."
}

Main
```

### <a name="deploy-u-sql-jobs-through-azure-data-factory"></a>U-SQL-taken implementeren via Azure Data Factory

U U-SQL-taken rechtstreeks vanuit Azure Pipelines indienen. U de gebouwde scripts ook uploaden naar Azure Data Lake Store- of Azure Blob-opslag en [de geplande taken uitvoeren via Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/transform-data-using-data-lake-analytics)

Gebruik de [Azure PowerShell-taak](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-powershell?view=vsts) in Azure Pipelines met het volgende voorbeeld PowerShell-script om de U-SQL-scripts te uploaden naar een Azure Data Lake Store-account:

```powershell
<#
    This script can be used to upload U-SQL files to ADLS with given U-SQL project build output(.usqlpack file).
    This will unzip the U-SQL project build output, and upload all scripts to ADLS one-by-one.
          
    Example :
        FileUpload.ps1 -ADLSName "myadlsaccount" -ArtifactsRoot "C:\USQLProject\bin\debug\"
#>

param(
    [Parameter(Mandatory=$true)][string]$ADLSName, # ADLS account name to upload U-SQL scripts
    [Parameter(Mandatory=$true)][string]$ArtifactsRoot, # Root folder of U-SQL project build output
    [Parameter(Mandatory=$false)][string]$DestinationFolder = "USQLScriptSource" # Destination folder in ADLS
)

Function UploadResources()
{
    Write-Host "************************************************************************"
    Write-Host "Uploading files to $ADLSName"
    Write-Host "***********************************************************************"

    $usqlScripts = GetUsqlFiles

    $files = @(get-childitem $usqlScripts -recurse)
    foreach($file in $files)
    {
        Write-Host "Uploading file: $($file.Name)"
        Import-AzDataLakeStoreItem -AccountName $ADLSName -Path $file.FullName -Destination "/$(Join-Path $DestinationFolder $file)" -Force
    }
}

function Unzip($USQLPackfile, $UnzipOutput)
{
    $USQLPackfileZip = Rename-Item -Path $USQLPackfile -NewName $([System.IO.Path]::ChangeExtension($USQLPackfile, ".zip")) -Force -PassThru
    Expand-Archive -Path $USQLPackfileZip -DestinationPath $UnzipOutput -Force
    Rename-Item -Path $USQLPackfileZip -NewName $([System.IO.Path]::ChangeExtension($USQLPackfileZip, ".usqlpack")) -Force
}

Function GetUsqlFiles()
{

    $USQLPackfiles = Get-ChildItem -Path $ArtifactsRoot -Include *.usqlpack -File -Recurse -ErrorAction SilentlyContinue

    $UnzipOutput = Join-Path $ArtifactsRoot -ChildPath "UnzipUSQLScripts"

    foreach ($USQLPackfile in $USQLPackfiles)
    {
        Unzip $USQLPackfile $UnzipOutput
    }

    return Get-ChildItem -Path $UnzipOutput -Include *.usql -File -Recurse -ErrorAction SilentlyContinue
}

UploadResources
```

## <a name="cicd-for-a-u-sql-database"></a>CI/CD voor een U-SQL-database

Azure Data Lake Tools voor Visual Studio biedt U-SQL-databaseprojectsjablonen waarmee u U-SQL-databases ontwikkelen, beheren en implementeren. Meer informatie over een [U-SQL-databaseproject](data-lake-analytics-data-lake-tools-develop-usql-database.md).

## <a name="build-u-sql-database-project"></a>U-SQL-databaseproject bouwen

### <a name="get-the-nuget-package"></a>Ontvang het NuGet-pakket

MSBuild biedt geen ingebouwde ondersteuning voor U-SQL-databaseprojecten. Om deze mogelijkheid te krijgen, moet u een referentie voor uw oplossing toevoegen aan het [Microsoft.Azure.DataLake.USQL.SDK](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/) NuGet-pakket dat de vereiste taalservice toevoegt.

Als u de nuget-pakketverwijzing wilt toevoegen, klikt u met de rechtermuisknop op de oplossing in Visual Studio Solution Explorer. Kies **NuGet-pakketten beheren.** Zoek en installeer vervolgens het NuGet-pakket. U ook een bestand met de naam **packages.config** toevoegen aan de oplossingsmap en de volgende inhoud erin plaatsen:

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
  <package id="Microsoft.Azure.DataLake.USQL.SDK" version="1.3.180615" targetFramework="net452" />
</packages>
```

### <a name="build-u-sql-a-database-project-with-the-msbuild-command-line"></a>U-SQL een databaseproject bouwen met de MSBuild-opdrachtregel

Als u uw U-SQL-databaseproject wilt bouwen, belt u de standaard MSBuild-opdrachtregel aan en geeft u de u-SQL SDK NuGet-pakketverwijzing door als een extra argument. Zie het volgende voorbeeld: 

```
msbuild DatabaseProject.usqldbproj /p:USQLSDKPath=packages\Microsoft.Azure.DataLake.USQL.SDK.1.3.180615\build\runtime
```

Het `USQLSDKPath=<U-SQL Nuget package>\build\runtime` argument verwijst naar het installatiepad van het NuGet-pakket voor de U-SQL-taalservice.

### <a name="continuous-integration-with-azure-pipelines"></a>Continue integratie met Azure-pijplijnen

Naast de opdrachtregel u Visual Studio Build of een MSBuild-taak gebruiken om U-SQL-databaseprojecten in Azure Pipelines te bouwen. Als u een buildtaak wilt instellen, moet u twee taken toevoegen aan de buildpijplijn: een NuGet-hersteltaak en een MSBuild-taak.

   ![CI/CD MSBuild-taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-task.png) 


1. Voeg een NuGet-hersteltaak toe om het nuget-pakket waarnaar wordt verwezen, inclusief `Azure.DataLake.USQL.SDK`, te krijgen, zodat MSBuild de U-SQL-taaldoelen kan vinden. Stel **de** > map `$(Build.SourcesDirectory)/packages` Geavanceerde**bestemming** in als u het voorbeeld van de argumenten VAN MSBuild direct in stap 2 wilt gebruiken.

   ![CI/CD NuGet-taak voor een U-SQL-project](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-nuget-task.png)

2. Stel MSBuild-argumenten in Visual Studio buildtools of in een MSBuild-taak in, zoals in het volgende voorbeeld wordt weergegeven. U ook variabelen voor deze argumenten definiëren in de pijplijn Azure Pipelines.

   ![CI/CD MSBuild-variabelen definiëren voor een U-SQL-databaseproject](./media/data-lake-analytics-cicd-overview/data-lake-analytics-set-vsts-msbuild-variables-database-project.png) 

   ```
   /p:USQLSDKPath=$(Build.SourcesDirectory)/packages/Microsoft.Azure.DataLake.USQL.SDK.1.3.180615/build/runtime
   ```
 
### <a name="u-sql-database-project-build-output"></a>U-SQL-database projectbuild-uitvoer

De buildoutput voor een U-SQL-databaseproject is een U-SQL-databaseimplementatiepakket, genoemd met het achtervoegsel. `.usqldbpack` Het `.usqldbpack` pakket is een zip-bestand dat alle DDL-instructies in één U-SQL-script in een DDL-map bevat. Het bevat alle **.dlls** en extra bestanden voor montage in een tijdelijke map.

## <a name="test-table-valued-functions-and-stored-procedures"></a>Functies en opgeslagen procedures met testtabel

Het toevoegen van testcases voor functies met tabelwaarde en opgeslagen procedures wordt momenteel niet ondersteund. Als tijdelijke oplossing u een U-SQL-project maken met U-SQL-scripts die deze functies aanroepen en testcases voor hen schrijven. Volg de volgende stappen voor het instellen van testcases voor functies met tabelwaarde en opgeslagen procedures die zijn gedefinieerd in het U-SQL-databaseproject:

1.  Maak een U-SQL-project voor testdoeleinden en schrijf U-SQL-scripts die de functies met tabelwaarde en opgeslagen procedures aanroepen.
2.  Voeg een databaseverwijzing toe aan het U-SQL-project. Als u de functie met tabelwaarde en de opgeslagen proceduredefinitie wilt krijgen, moet u verwijzen naar het databaseproject dat de DDL-instructie bevat. Meer informatie over [databaseverwijzingen](data-lake-analytics-data-lake-tools-develop-usql-database.md#reference-a-u-sql-database-project).
3.  Voeg testcases toe voor U-SQL-scripts die functies met tabelwaarde en opgeslagen procedures aanroepen. Meer informatie over het [toevoegen van testcases voor U-SQL-scripts](data-lake-analytics-cicd-test.md#test-u-sql-scripts).

## <a name="deploy-u-sql-database-through-azure-pipelines"></a>U-SQL-database implementeren via Azure Pipelines

`PackageDeploymentTool.exe`biedt de programmeer- en opdrachtregelinterfaces waarmee u-SQL-databaseimplementatiepakketten kunnen worden geïmplementeerd, **.usqldbpack.** De SDK is opgenomen in het [U-SQL SDK NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.DataLake.USQL.SDK/), gelegen op **build/runtime/PackageDeploymentTool.exe**. Met `PackageDeploymentTool.exe`behulp van U-SQL-databases u u-SQL-databases implementeren voor zowel Azure Data Lake Analytics als lokale accounts.

> [!NOTE]
>
> PowerShell-opdrachtregelondersteuning en Azure Pipelines geven taakondersteuning vrij voor u-SQL-databaseimplementatie is momenteel in behandeling.
>

Volg de volgende stappen om een databaseimplementatietaak in Azure Pipelines in te stellen:

1. Voeg een PowerShell Script-taak toe aan een build- of releasepijplijn en voer het volgende PowerShell-script uit. Met deze taak u `PackageDeploymentTool.exe` Azure `PackageDeploymentTool.exe`SDK-afhankelijkheden voor en. U de parameters **-AzureSDK** en **-DBDeploymentTool** zo instellen dat de afhankelijkheden en implementatiegereedschappen worden geladen in specifieke mappen. Geef het **AzureSDK-pad** door als `PackageDeploymentTool.exe` de parameter **-AzureSDKPath** in stap 2. 

    ```powershell
    <#
        This script is used for getting dependencies and SDKs for U-SQL database deployment.
        PowerShell command line support for deploying U-SQL database package(.usqldbpack file) will come soon.
        
        Example :
            GetUSQLDBDeploymentSDK.ps1 -AzureSDK "AzureSDKFolderPath" -DBDeploymentTool "DBDeploymentToolFolderPath"
    #>

    param (
        [string]$AzureSDK = "AzureSDK", # Folder to cache Azure SDK dependencies
        [string]$DBDeploymentTool = "DBDeploymentTool", # Folder to cache U-SQL database deployment tool
        [string]$workingfolder = "" # Folder to execute these command lines
    )

    if ([string]::IsNullOrEmpty($workingfolder))
    {
        $scriptpath = $MyInvocation.MyCommand.Path
        $workingfolder = Split-Path $scriptpath
    }
    cd $workingfolder

    echo "workingfolder=$workingfolder, outputfolder=$outputfolder"
    echo "Downloading required packages..."

    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Analytics/3.5.1-preview -outf Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.Management.DataLake.Store/2.4.1-preview -outf Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.IdentityModel.Clients.ActiveDirectory/2.28.3 -outf Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime/2.3.11 -outf Microsoft.Rest.ClientRuntime.2.3.11.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure/3.3.7 -outf Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Rest.ClientRuntime.Azure.Authentication/2.3.3 -outf Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip
    iwr https://www.nuget.org/api/v2/package/Newtonsoft.Json/6.0.8 -outf Newtonsoft.Json.6.0.8.zip
    iwr https://www.nuget.org/api/v2/package/Microsoft.Azure.DataLake.USQL.SDK/ -outf USQLSDK.zip

    echo "Extracting packages..."

    Expand-Archive Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview -Force
    Expand-Archive Microsoft.Azure.Management.DataLake.Store.2.4.1-preview.zip -DestinationPath Microsoft.Azure.Management.DataLake.Store.2.4.1-preview -Force
    Expand-Archive Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3.zip -DestinationPath Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.2.3.11.zip -DestinationPath Microsoft.Rest.ClientRuntime.2.3.11 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.3.3.7.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.3.3.7 -Force
    Expand-Archive Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3.zip -DestinationPath Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3 -Force
    Expand-Archive Newtonsoft.Json.6.0.8.zip -DestinationPath Newtonsoft.Json.6.0.8 -Force
    Expand-Archive USQLSDK.zip -DestinationPath USQLSDK -Force

    echo "Copy required DLLs to output folder..."

    mkdir $AzureSDK -Force
    mkdir $DBDeploymentTool -Force
    copy Microsoft.Azure.Management.DataLake.Analytics.3.5.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.Azure.Management.DataLake.Store.2.4.1-preview\lib\net452\*.dll $AzureSDK
    copy Microsoft.IdentityModel.Clients.ActiveDirectory.2.28.3\lib\net45\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.2.3.11\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.3.3.7\lib\net452\*.dll $AzureSDK
    copy Microsoft.Rest.ClientRuntime.Azure.Authentication.2.3.3\lib\net452\*.dll $AzureSDK
    copy Newtonsoft.Json.6.0.8\lib\net45\*.dll $AzureSDK
    copy USQLSDK\build\runtime\*.* $DBDeploymentTool
    ```

2. Voeg een **opdrachtregeltaak** toe aan een opbouw- of `PackageDeploymentTool.exe`releasepijplijn en vul het script in door aan te roepen . `PackageDeploymentTool.exe`bevindt zich onder de gedefinieerde **$DBDeploymentTool** map. Het voorbeeldscript is als volgt: 

    * Een U-SQL-database lokaal implementeren:

        ```
        PackageDeploymentTool.exe deploylocal -Package <package path> -Database <database name> -DataRoot <data root path>
        ```

    * Gebruik de interactieve verificatiemodus om een U-SQL-database te implementeren naar een Azure Data Lake Analytics-account:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -AzureSDKPath <azure sdk path> -Interactive
        ```

    * Gebruik **secrete-verificatie** om een U-SQL-database te implementeren in een Azure Data Lake Analytics-account:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete>
        ```

    * Gebruik **certFile-verificatie** om een U-SQL-database te implementeren in een Azure Data Lake Analytics-account:

        ```
        PackageDeploymentTool.exe deploycluster -Package <package path> -Database <database name> -Account <account name> -ResourceGroup <resource group name> -SubscriptionId <subscript id> -Tenant <tenant name> -ClientId <client id> -Secrete <secrete> -CertFile <certFile>
        ```

### <a name="packagedeploymenttoolexe-parameter-descriptions"></a>Parameterbeschrijvingen packageDeploymentTool.exe

#### <a name="common-parameters"></a>Algemene parameters

| Parameter | Beschrijving | Standaardwaarde | Vereist |
|---------|-----------|-------------|--------|
|Pakket|Het pad van het U-SQL-databaseimplementatiepakket dat moet worden geïmplementeerd.|null|waar|
|Database|De databasenaam die moet worden geïmplementeerd of gemaakt.|model|false|
|Logfile|Het pad van het bestand voor logboekregistratie. Standaard standaard out (console).|null|false|
|Logniveau|Logboekniveau: Verbose, Normaal, Waarschuwing of Fout.|LogLevel.Normaal|false|

#### <a name="parameter-for-local-deployment"></a>Parameter voor lokale implementatie

|Parameter|Beschrijving|Standaardwaarde|Vereist|
|---------|-----------|-------------|--------|
|DataRoot (DataRoot)|Het pad van de lokale hoofdmap voor gegevens.|null|waar|

#### <a name="parameters-for-azure-data-lake-analytics-deployment"></a>Parameters voor Azure Data Lake Analytics-implementatie

|Parameter|Beschrijving|Standaardwaarde|Vereist|
|---------|-----------|-------------|--------|
|Account|Hiermee geeft u op naar welk Azure Data Lake Analytics-account u wilt implementeren op accountnaam.|null|waar|
|ResourceGroup|De naam van de Azure-brongroep voor het Azure Data Lake Analytics-account.|null|waar|
|SubscriptionId|De Azure-abonnements-id voor het Azure Data Lake Analytics-account.|null|waar|
|Tenant|De tenantnaam is de Azure Active Directory -domeinnaam (Azure AD). Vind het op de pagina abonnementbeheer in de Azure-portal.|null|waar|
|AzureSDKPath|Het pad naar afhankelijke verzamelingen zoeken in de Azure SDK.|null|waar|
|Interactief|Al dan niet om interactieve modus te gebruiken voor authenticatie.|false|false|
|ClientId|De Azure AD-toepassings-id die vereist is voor niet-interactieve verificatie.|null|Vereist voor niet-interactieve verificatie.|
|Afscheiden|Het geheim of wachtwoord voor niet-interactieve verificatie. Het mag alleen worden gebruikt in een vertrouwde en veilige omgeving.|null|Vereist voor niet-interactieve verificatie, of gebruik secreteFile.|
|SecreteFile|Het bestand slaat de afscheiding of het wachtwoord op voor niet-interactieve verificatie. Zorg ervoor dat het alleen leesbaar blijft voor de huidige gebruiker.|null|Vereist voor niet-interactieve verificatie, of anders secrete gebruiken.|
|CertFile CertFile CertFile CertFile|Het bestand slaat X.509-certificering op voor niet-interactieve verificatie. De standaardinstelling is het gebruik van client secrete authenticatie.|null|false|
| Taakvoorvoegsel | Het voorvoegsel voor database-implementatie van een U-SQL DDL-taak. | Deploy_ + DateTime.Nu | false |

## <a name="next-steps"></a>Volgende stappen

- [Uw Azure Data Lake Analytics-code testen.](data-lake-analytics-cicd-test.md)
- [Voer U-SQL-script uit op uw lokale machine.](data-lake-analytics-data-lake-tools-local-run.md)
- [Gebruik U-SQL-databaseproject om U-SQL-database te ontwikkelen.](data-lake-analytics-data-lake-tools-develop-usql-database.md)

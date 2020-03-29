---
title: Het NuGet-pakket Azure Stream Analytics CI/CD NuGet gebruiken
description: In dit artikel wordt beschreven hoe u het Azure Stream Analytics CI/CD NuGet-pakket gebruiken om een continu integratie- en implementatieproces in te stellen.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/15/2019
ms.openlocfilehash: 11e68aaa7c70d4f888c0009bc28d9bb90f431f3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354446"
---
# <a name="use-the-azure-stream-analytics-cicd-nuget-package-for-integration-and-development"></a>Het Azure Stream Analytics CI/CD NuGet-pakket gebruiken voor integratie en ontwikkeling 
In dit artikel wordt beschreven hoe u het Azure Stream Analytics CI/CD NuGet-pakket gebruiken om een continu integratie- en implementatieproces in te stellen.

Gebruik versie 2.3.0000.0 of hoger van [Stream Analytics-tools voor Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio) om ondersteuning te krijgen voor MSBuild.

Er is een NuGet-pakket beschikbaar: [Microsoft.Azure.Stream Analytics.CICD](https://www.nuget.org/packages/Microsoft.Azure.StreamAnalytics.CICD/). Het biedt de MSBuild-, lokale run- en implementatietools die het continue integratie- en implementatieproces van [Stream Analytics Visual Studio-projecten](stream-analytics-vs-tools.md)ondersteunen. 
> [!NOTE]
> Het NuGet-pakket kan alleen worden gebruikt met de 2.3.0000.0 of hoger versie van Stream Analytics Tools voor Visual Studio. Als u projecten hebt gemaakt in eerdere versies van Visual Studio-hulpprogramma's, opent u deze met de versie 2.3.0000.0 of hoger en slaat u ze op. Dan zijn de nieuwe mogelijkheden ingeschakeld. 

Zie [Tools voor Stream Analytics voor Visual Studio voor](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-tools-for-visual-studio)meer informatie.

## <a name="msbuild"></a>MSBuild MSBuild
Net als de standaard Visual Studio MSBuild-ervaring, om een project te bouwen heb je twee opties. U met de rechtermuisknop op het project klikken en vervolgens **Bouwen**kiezen. Je **MSBuild** ook gebruiken in het NuGet-pakket vanaf de opdrachtregel.
```
./build/msbuild /t:build [Your Project Full Path] /p:CompilerTaskAssemblyFile=Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll  /p:ASATargetsFilePath="[NuGet Package Local Path]\build\StreamAnalytics.targets"

```

Wanneer een Stream Analytics Visual Studio-project succesvol wordt opgebouwd, worden de volgende twee Azure Resource Manager-sjabloonbestanden gegenereerd onder de **map Foutopsporing/Detailhandel]/Implementeren:** 

*  Resource Manager-sjabloonbestand

       [ProjectName].JobTemplate.json 

*  Bestand resourcebeheerparameters

       [ProjectName].JobTemplate.parameters.json   

De standaardparameters in het bestand parameters.json zijn afkomstig van de instellingen in uw Visual Studio-project. Als u wilt implementeren naar een andere omgeving, vervangt u de parameters dienovereenkomstig.

> [!NOTE]
> Voor alle referenties worden de standaardwaarden ingesteld op null. U **moet** de waarden instellen voordat u deze implementeert in de cloud.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```
Meer informatie over het [implementeren met een Resource Manager-sjabloonbestand en Azure PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy). Meer informatie over het [gebruik van een object als parameter in een resourcemanagersjabloon](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/objects-as-parameters).

Als u Managed Identity voor Azure Data Lake Store Gen1 wilt gebruiken als uitvoerzink, moet u toegang tot de serviceprincipal bieden met PowerShell voordat u wordt geïmplementeerd in Azure. Meer informatie over het [implementeren van ADLS Gen1 met de sjabloon Managed Identity met Resource Manager](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).


## <a name="command-line-tool"></a>Opdrachtregelgereedschap

### <a name="build-the-project"></a>Het project bouwen
Het NuGet-pakket heeft een command-line tool genaamd **SA.exe**. Het ondersteunt projectbuild en lokale tests op een willekeurige machine, die u gebruiken in uw continue integratie en continu leveringsproces. 

De implementatiebestanden worden standaard onder de huidige map geplaatst. U het uitvoerpad opgeven met behulp van de volgende parameter -OutputPath:

```
./tools/SA.exe build -Project [Your Project Full Path] [-OutputPath <outputPath>] 
```

### <a name="test-the-script-locally"></a>Het script lokaal testen

Als uw project lokale invoerbestanden heeft opgegeven in Visual Studio, u een geautomatiseerde scripttest uitvoeren met behulp van de opdracht *localrun.* Het uitvoerresultaat wordt onder de huidige map geplaatst.
 
```
localrun -Project [ProjectFullPath]
```

### <a name="generate-a-job-definition-file-to-use-with-the-stream-analytics-powershell-api"></a>Een taakdefinitiebestand genereren dat u wilt gebruiken met de Stream Analytics PowerShell API

De *armopdracht* neemt de taaksjabloon- en taaksjabloonparameterbestanden die via build worden gegenereerd, als invoer. Vervolgens worden ze gecombineerd tot een JSON-bestand voor taakdefinitie dat kan worden gebruikt met de Stream Analytics PowerShell API.

```powershell
arm -JobTemplate <templateFilePath> -JobParameterFile <jobParameterFilePath> [-OutputFile <asaArmFilePath>]
```
Voorbeeld:
```powershell
./tools/SA.exe arm -JobTemplate "ProjectA.JobTemplate.json" -JobParameterFile "ProjectA.JobTemplate.parameters.json" -OutputFile "JobDefinition.json" 
```



## <a name="next-steps"></a>Volgende stappen

* [Snelstart: een Azure Stream Analytics-cloudtaak maken in Visual Studio](stream-analytics-quick-create-vs.md)
* [Query's van Stream Analytics lokaal testen met Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Azure Stream Analytics-taken verkennen met Visual Studio](stream-analytics-vs-tools.md)

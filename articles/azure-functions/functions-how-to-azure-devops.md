---
title: Functie-app-code continu bijwerken met Azure DevOps
description: Meer informatie over het instellen van een Azure DevOps-pijplijn die zich richt op Azure-functies.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255756"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Continue levering met Azure DevOps

U uw functie automatisch implementeren in een Azure Functions-app met [Azure Pipelines.](/azure/devops/pipelines/)

U hebt twee opties voor het definiëren van uw pijplijn:

- **YAML-bestand**: een YAML-bestand beschrijft de pijplijn. Het bestand kan een sectie met bouwstappen en een releasesectie hebben. Het YAML-bestand moet zich in dezelfde repo als de app bevindt.
- **Sjabloon:** sjablonen zijn kant-en-klare taken die uw app bouwen of implementeren.

## <a name="yaml-based-pipeline"></a>YAML-gebaseerde pijplijn

Als u een op YAML gebaseerde pijplijn wilt maken, bouwt u eerst uw app en implementeert u de app.

### <a name="build-your-app"></a>Uw app maken

Hoe u uw app bouwt in Azure Pipelines, is afhankelijk van de programmeertaal van uw app. Elke taal heeft specifieke bouwstappen die een implementatieartefact maken. Een implementatieartefact wordt gebruikt om uw functie-app in Azure te implementeren.

# <a name="c"></a>[C\#](#tab/csharp)

U het volgende voorbeeld gebruiken om een YAML-bestand te maken om een .NET-app te maken:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: false
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

U het volgende voorbeeld gebruiken om een YAML-bestand te maken om een JavaScript-app te maken:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="python"></a>[Python](#tab/python)

U een van de volgende voorbeelden gebruiken om een YAML-bestand te maken om een app te bouwen voor een specifieke Python-versie. Python wordt alleen ondersteund voor functie-apps die op Linux worden uitgevoerd.

**Versie 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

**Versie 3.6**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

U het volgende voorbeeld gebruiken om een YAML-bestand te maken om een PowerShell-app te verpakken. PowerShell wordt alleen ondersteund voor Windows Azure-functies.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

---

### <a name="deploy-your-app"></a>Uw app implementeren

Afhankelijk van het hosting-besturingssysteem moet u een van de volgende YAML-voorbeelden in uw YAML-bestand opnemen.

#### <a name="windows-function-app"></a>Windows-functie-app

U het volgende fragment gebruiken om een Windows-functie-app te implementeren:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Linux-functie-app

U het volgende fragment gebruiken om een Linux-functie-app te implementeren:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Pijplijn op basis van sjablonen

Sjablonen in Azure DevOps zijn vooraf gedefinieerde groepen taken die een app bouwen of implementeren.

### <a name="build-your-app"></a>Uw app maken

Hoe u uw app bouwt in Azure Pipelines, is afhankelijk van de programmeertaal van uw app. Elke taal heeft specifieke bouwstappen die een implementatieartefact maken. Een implementatieartefact wordt gebruikt om uw functie-app in Azure bij te werken.

Als u ingebouwde bouwsjablonen wilt gebruiken, selecteert u bij het maken van een nieuwe buildpijplijn de optie **De klassieke editor gebruiken** om een pijplijn te maken met behulp van ontwerpsjablonen.

![De klassieke editor Azure Pipelines selecteren](media/functions-how-to-azure-devops/classic-editor.png)

Nadat u de bron van uw code hebt geconfigureerd, zoekt u naar azure-functies voor het bouwen van sjablonen. Selecteer de sjabloon die overeenkomt met uw app-taal.

![Een sjabloon voor het bouwen van Azure Functions selecteren](media/functions-how-to-azure-devops/build-templates.png)

In sommige gevallen hebben bouwartefacten een specifieke mapstructuur. Mogelijk moet u het **selectievakje Prepend-hoofdmapnaam inschakelen om paden te archiveren.**

![De optie om de naam van de hoofdmap voor te bereiden](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript-apps

Als uw JavaScript-app afhankelijk is van Windows-native modules, moet u de versie van de agentgroep bijwerken naar **Hosted VS2017.**

![De versie van de agentgroep bijwerken](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Uw app implementeren

Wanneer u een nieuwe releasepijplijn maakt, zoekt u naar de sjabloon azure functions.

![Zoeken naar de releasesjabloon Azure Functions](media/functions-how-to-azure-devops/release-template.png)

Implementatie naar een implementatiesleuf wordt niet ondersteund in de releasesjabloon.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Een buildpijplijn maken met de Azure CLI

Als u een buildpijplijn in `az functionapp devops-pipeline create` Azure wilt maken, gebruikt u de [opdracht](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). De buildpijplijn is gemaakt om codewijzigingen te maken en vrij te geven die in uw repo worden aangebracht. De opdracht genereert een nieuw YAML-bestand dat de build- en releasepijplijn definieert en deze vervolgens verbindt aan uw repo. De voorwaarden voor deze opdracht zijn afhankelijk van de locatie van uw code.

- Als uw code zich in GitHub bevindt:

    - U moet **schrijfmachtigingen** voor uw abonnement hebben.

    - U moet de projectbeheerder zijn in Azure DevOps.

    - U moet machtigingen hebben om een GitHub personal access token (PAT) te maken met voldoende machtigingen. Zie [GitHub PAT-machtigingsvereisten voor](https://aka.ms/azure-devops-source-repos) meer informatie.

    - Je moet machtigingen hebben om je te committeren aan de master branch in je GitHub repository, zodat je het automatisch gegenereerde YAML-bestand vastleggen.

- Als uw code zich in Azure Repos bevindt:

    - U moet **schrijfmachtigingen** voor uw abonnement hebben.

    - U moet de projectbeheerder zijn in Azure DevOps.

## <a name="next-steps"></a>Volgende stappen

- Bekijk het [overzicht azure-functies](functions-overview.md).
- Bekijk het [overzicht azure DevOps](/azure/devops/pipelines/).

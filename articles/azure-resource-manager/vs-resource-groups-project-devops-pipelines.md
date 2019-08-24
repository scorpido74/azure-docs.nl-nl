---
title: CI/CD met Azure-pijp lijnen en Resource Manager-sjablonen
description: Hierin wordt beschreven hoe u doorlopende integratie in azure-pijp lijnen instelt met behulp van implementatie projecten van Azure-resource groepen in Visual Studio voor het implementeren van Resource Manager-sjablonen.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: ae896fa0820fbd25ed3f2d29c89fbcd56e7fd6f5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982444"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Resource Manager-sjablonen integreren met Azure-pijp lijnen

Visual Studio biedt het project van de Azure-resource groep voor het maken van sjablonen en het implementeren ervan in uw Azure-abonnement. U kunt dit project integreren met Azure-pijp lijnen voor continue integratie en continue implementatie (CI/CD).

Er zijn twee manieren om sjablonen met Azure-pijp lijnen te implementeren:

* **Voeg een taak toe die een Azure PowerShell script uitvoert**. Deze optie biedt het voor deel dat u binnen de levens cyclus van de ontwikkeling consistentie kunt bieden, omdat u hetzelfde script gebruikt dat is opgenomen in het Visual Studio-project (Deploy-AzureResourceGroup. ps1). Het script faseert artefacten van uw project naar een opslag account dat door Resource Manager kan worden geopend. Artefacten zijn items in uw project, zoals gekoppelde sjablonen, scripts en binaire bestanden voor toepassingen. Vervolgens implementeert het script de sjabloon.

* **Voeg taken toe om taken te kopiëren en te implementeren**. Deze optie biedt een handig alternatief voor het project script. U configureert twee taken in de pijp lijn. Met een taak worden de artefacten en de andere taak in de sjabloon geïmplementeerd.

In dit artikel worden beide benaderingen beschreven.

## <a name="prepare-your-project"></a>Uw project voorbereiden

In dit artikel wordt ervan uitgegaan dat uw Visual Studio-project en Azure DevOps-organisatie klaar zijn voor het maken van de pijp lijn. De volgende stappen laten zien hoe u ervoor kunt zorgen dat u klaar bent:

* U hebt een Azure DevOps-organisatie. Als u er nog geen hebt, [maakt u er gratis een](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Als uw team al een Azure DevOps-organisatie heeft, zorg er dan voor dat u een beheerder bent van het Azure DevOps-project dat u wilt gebruiken.

* U hebt een [service verbinding](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) met uw Azure-abonnement geconfigureerd. De taken in de pijp lijn worden uitgevoerd onder de identiteit van de Service-Principal. Zie [een DevOps-project maken](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project)voor de stappen voor het maken van de verbinding.

* U hebt een Visual Studio-project dat is gemaakt op basis van de starter-sjabloon van de **Azure-resource groep** . Zie voor meer informatie over het maken van dit type project [Azure-resource groepen maken en implementeren via Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Uw Visual Studio-project is [verbonden met een Azure DevOps-project](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Pijplijn maken

1. Als u nog geen pijp lijn hebt toegevoegd, moet u een nieuwe pijp lijn maken. Selecteer in uw Azure DevOps-organisatie **pijp lijnen** en **nieuwe pijp lijn**.

   ![Nieuwe pijp lijn toevoegen](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Geef op waar de code wordt opgeslagen. In de volgende afbeelding ziet u hoe u **Azure opslag plaatsen Git**selecteert.

   ![Code bron selecteren](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Selecteer de opslag plaats met de code voor het project uit de bron.

   ![Opslag plaats selecteren](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Selecteer het type pijp lijn dat u wilt maken. U kunt een **starter pijp lijn**selecteren.

   ![Pijp lijn selecteren](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

U kunt een Azure PowerShell-taak of het kopieer bestand toevoegen en taken implementeren.

## <a name="azure-powershell-task"></a>Azure PowerShell taak

In deze sectie wordt uitgelegd hoe u doorlopende implementatie kunt configureren met één taak die het Power shell-script in uw project uitvoert. Met het volgende YAML-bestand wordt een [Azure PowerShell taak](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)gemaakt:

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Wanneer u de taak instelt op `AzurePowerShell@3`, gebruikt de pijp lijn opdrachten uit de AzureRM-module om de verbinding te verifiëren. Het Power shell-script in het Visual Studio-project maakt standaard gebruik van de AzureRM-module. Als u uw script hebt bijgewerkt voor het gebruik van de [AZ-module](/powershell/azure/new-azureps-module-az), stelt `AzurePowerShell@4`u de taak in op.

```yaml
steps:
- task: AzurePowerShell@4
```

Geef `azureSubscription`voor de naam op van de service verbinding die u hebt gemaakt.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Geef `scriptPath`voor het relatieve pad van het pijplijn bestand naar het script op. U kunt het pad bekijken in uw opslag plaats.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Als u artefacten niet nodig hebt, geeft u gewoon de naam en locatie van een resource groep door om te gebruiken voor de implementatie. Met het Visual Studio-script wordt de resource groep gemaakt als deze nog niet bestaat.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Als u artefacten naar een bestaand opslag account moet faseren, gebruikt u:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nu u begrijpt hoe u de taak maakt, gaan we de stappen door lopen om de pijp lijn te bewerken.

1. Open de pijp lijn en vervang de inhoud door uw YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Selecteer **Opslaan**.

   ![Pijplijn opslaan](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Geef een bericht op dat moet worden doorgevoerd en rechtstreeks door te voeren naar de **hoofd server**.

1. Wanneer u **Opslaan**selecteert, wordt de build-pijp lijn automatisch uitgevoerd. Ga terug naar de samen vatting van uw build-pijp lijn en Bekijk de status.

   ![Resultaten weergeven](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

U kunt de pijp lijn die momenteel wordt uitgevoerd selecteren om details over de taken weer te geven. Wanneer deze is voltooid, ziet u de resultaten voor elke stap.

## <a name="copy-and-deploy-tasks"></a>Taken kopiëren en implementeren

In deze sectie wordt beschreven hoe u doorlopende implementatie kunt configureren met behulp van een twee taken voor het faseren van de artefacten en het implementeren van de sjabloon. 

In de volgende YAML wordt de [Azure File Copy-taak](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)weer gegeven:

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Er zijn verschillende onderdelen van deze taak voor het herzien van uw omgeving. `SourcePath` Hiermee wordt de locatie van de artefacten ten opzichte van het pijplijn bestand aangegeven. In dit voor beeld bestaan de bestanden in een map `AzureResourceGroup1` met de naam.

```yaml
SourcePath: '<path-to-artifacts>'
```

Geef `azureSubscription`voor de naam op van de service verbinding die u hebt gemaakt.

```yaml
azureSubscription: '<your-connection-name>'
```

Geef voor opslag en container naam de namen op van het opslag account en de container die u wilt gebruiken voor het opslaan van de artefacten. Het opslag account moet bestaan.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

In de volgende YAML wordt de [implementatie taak voor de Azure-resource groep](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)weer gegeven:

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Er zijn verschillende onderdelen van deze taak voor het herzien van uw omgeving. Geef `azureSubscription`voor de naam op van de service verbinding die u hebt gemaakt.

```yaml
azureSubscription: '<your-connection-name>'
```

Geef `resourceGroupName` voor `location`en de naam en de locatie van de resource groep op die u wilt implementeren. Met de taak wordt de resource groep gemaakt als deze nog niet bestaat.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

De implementatie taak is gekoppeld aan een sjabloon `WebSite.json` met de naam en een para meters-bestand, genaamd website. para meters. json. Gebruik de namen van uw sjabloon en parameter bestanden.

Nu u begrijpt hoe u de taken maakt, gaan we de stappen door lopen om de pijp lijn te bewerken.

1. Open de pijp lijn en vervang de inhoud door uw YAML:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Selecteer **Opslaan**.

1. Geef een bericht op dat moet worden doorgevoerd en rechtstreeks door te voeren naar de **hoofd server**.

1. Wanneer u **Opslaan**selecteert, wordt de build-pijp lijn automatisch uitgevoerd. Ga terug naar de samen vatting van uw build-pijp lijn en Bekijk de status.

   ![Resultaten weergeven](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

U kunt de pijp lijn die momenteel wordt uitgevoerd selecteren om details over de taken weer te geven. Wanneer deze is voltooid, ziet u de resultaten voor elke stap.

## <a name="next-steps"></a>Volgende stappen

Zie [zelf studie voor stapsgewijze instructies voor het gebruik van Azure-pijp lijnen met Resource Manager-sjablonen: Continue integratie van Azure Resource Manager sjablonen met Azure-pijp](resource-manager-tutorial-use-azure-pipelines.md)lijnen.

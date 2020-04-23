---
title: CI/CD met Azure-pijplijnen en sjablonen
description: Beschrijft hoe u continue integratie in Azure Pipelines instellen met azure resourcegroepimplementatieprojecten in Visual Studio om Resource Manager-sjablonen te implementeren.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084648"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>ARM-sjablonen integreren met Azure-pijplijnen

Visual Studio biedt het Azure Resource Group-project voor het maken van ARM-sjablonen (Azure Resource Manager) en het implementeren ervan in uw Azure-abonnement. U dit project integreren met Azure Pipelines voor continue integratie en continue implementatie (CI/CD).

Er zijn twee manieren om sjablonen te implementeren met Azure Pipelines:

* **Taken toevoegen die een Azure PowerShell-script uitvoeren.** Deze optie heeft het voordeel dat u gedurende de hele ontwikkelingslevenscyclus consistentie biedt, omdat u hetzelfde script gebruikt dat is opgenomen in het Visual Studio-project (Deploy-AzureResourceGroup.ps1). In het script worden artefacten van uw project gefaseerd uitgevoerd in een opslagaccount waarToe Resource Manager toegang heeft. Artefacten zijn items in uw project, zoals gekoppelde sjablonen, scripts en toepassingsbinaries. Vervolgens implementeert het script de sjabloon.

* **Taken toevoegen om taken te kopiëren en te implementeren**. Deze optie biedt een handig alternatief voor het projectscript. U configureert twee taken in de pijplijn. De ene taak faseert de artefacten en de andere taak implementeert de sjabloon.

Dit artikel toont beide benaderingen.

## <a name="prepare-your-project"></a>Bereid uw project voor

In dit artikel wordt ervan uitgegaan dat uw Visual Studio-project en Azure DevOps-organisatie klaar zijn voor het maken van de pijplijn. In de volgende stappen wordt uitgelegd hoe u ervoor zorgen dat u er klaar voor bent:

* U hebt een Azure DevOps-organisatie. Als je er geen hebt, [maak er dan gratis een.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Als uw team al een Azure DevOps-organisatie heeft, controleert u of u een beheerder bent van het Azure DevOps-project dat u wilt gebruiken.

* U hebt een [serviceverbinding](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) met uw Azure-abonnement geconfigureerd. De taken in de pijplijn worden uitgevoerd onder de identiteit van de serviceprincipal. Zie [Een Ontwikkelaarsproject maken](deployment-tutorial-pipeline.md#create-a-devops-project)voor stappen om de verbinding te maken.

* U hebt een Visual Studio-project dat is gemaakt op basis van de startersjabloon **Azure Resource Group.** Zie [Azure-brongroepen maken en implementeren via Visual Studio](create-visual-studio-deployment-project.md)voor informatie over het maken van dat type project.

* Uw Visual Studio-project is [verbonden met een Azure DevOps-project.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Pijplijn maken

1. Als u nog geen pijplijn hebt toegevoegd, moet u een nieuwe pijplijn maken. Selecteer **pijplijnen** en **nieuwe pijplijnen in**uw Azure DevOps.

   ![Nieuwe pijplijn toevoegen](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Geef op waar uw code is opgeslagen. In de volgende afbeelding wordt het selecteren van **Azure Repos Git**weergegeven .

   ![Codebron selecteren](./media/add-template-to-azure-pipelines/select-source.png)

1. Selecteer in die bron de opslagplaats met de code voor uw project.

   ![Repository selecteren](./media/add-template-to-azure-pipelines/select-repo.png)

1. Selecteer het type pijplijn dat u wilt maken. U **starterpijplijn**selecteren.

   ![Pijplijn selecteren](./media/add-template-to-azure-pipelines/select-pipeline.png)

U bent klaar om een Azure PowerShell-taak toe te voegen of de kopieerbestands- en implementatietaken toe te voegen.

## <a name="azure-powershell-task"></a>Azure PowerShell-taak

In deze sectie ziet u hoe u continue implementatie configureert met één taak waarmee het PowerShell-script in uw project wordt uitgevoerd. Met het volgende YAML-bestand wordt een [Azure PowerShell-taak uitgevoerd:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

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

Wanneer u de `AzurePowerShell@3`taak instelt op , gebruikt de pijplijn opdrachten uit de AzureRM-module om de verbinding te verifiëren. Standaard gebruikt het PowerShell-script in het Visual Studio-project de AzureRM-module. Als u uw script hebt bijgewerkt om de [Az-module](/powershell/azure/new-azureps-module-az)te gebruiken, stelt u de taak in op `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Geef `azureSubscription`voor , geef de naam van de serviceverbinding die u hebt gemaakt.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Geef `scriptPath`bijvoorbeeld het relatieve pad op van het pijplijnbestand naar uw script. Je in je repository kijken om het pad te zien.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Als u geen artefacten hoeft te fasen, geeft u alleen de naam en locatie van een resourcegroep door die u wilt gebruiken voor implementatie. Het Visual Studio-script maakt de brongroep als deze nog niet bestaat.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Als u artefacten naar een bestaand opslagaccount moet brengen, gebruikt u het als:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Nu u begrijpt hoe u de taak maken, gaan we door de stappen om de pijplijn te bewerken.

1. Open de pijplijn en vervang de inhoud door uw YAML:

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

   ![Pijplijn opslaan](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Geef een boodschap voor de commit en verbind je rechtstreeks aan **master.**

1. Wanneer u **Opslaan**selecteert, wordt de buildpijplijn automatisch uitgevoerd. Ga terug naar het overzicht voor uw buildpijplijn en bekijk de status.

   ![Resultaten weergeven](./media/add-template-to-azure-pipelines/view-results.png)

U de pijplijn die momenteel wordt uitgevoerd selecteren om details over de taken te bekijken. Wanneer het klaar is, ziet u de resultaten voor elke stap.

## <a name="copy-and-deploy-tasks"></a>Taken kopiëren en implementeren

In deze sectie ziet u hoe u continue implementatie configureert met behulp van twee taken om de artefacten te fasen en de sjabloon te implementeren.

In de volgende YAML wordt de [azure-bestandskopieertaak](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)weergegeven:

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

Er zijn verschillende onderdelen van deze taak te herzien voor uw omgeving. Hiermee `SourcePath` wordt de locatie van de artefacten ten opzichte van het pijplijnbestand aangegeven. In dit voorbeeld bestaan de bestanden `AzureResourceGroup1` in een map met de naam die de naam van het project was.

```yaml
SourcePath: '<path-to-artifacts>'
```

Geef `azureSubscription`voor , geef de naam van de serviceverbinding die u hebt gemaakt.

```yaml
azureSubscription: '<your-connection-name>'
```

Voor opslag en containernaam geeft u de namen op van het opslagaccount en de container die u wilt gebruiken voor het opslaan van de artefacten. Het opslagaccount moet bestaan.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

In de volgende YAML wordt de [azure resource manager-sjabloonimplementatietaak](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)weergegeven:

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Er zijn verschillende onderdelen van deze taak te herzien voor uw omgeving.

- `deploymentScope`: Selecteer het implementatiebereik in `Management Group` `Subscription` de `Resource Group`opties: , en . Gebruik **Resource Group** in deze wandeling door. Zie Implementatiescopes voor meer informatie over de [scopes.](deploy-rest.md#deployment-scope)

- `ConnectedServiceName`: Geef de naam op van de serviceverbinding die u hebt gemaakt.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Geef de doel-abonnement-id op. Deze eigenschap is alleen van toepassing op het implementatiebereik van de resourcegroep en het implementatiebereik van het abonnement.

- `resourceGroupName`en: `location`geef de naam en locatie op van de resourcegroep die u wilt implementeren. De taak maakt de resourcegroep als deze niet bestaat.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

De implementatietaak wordt koppelingen `WebSite.json` naar een sjabloon met de naam en een parametersbestand met de naam WebSite.parameters.json. Gebruik de namen van uw sjabloon en parameterbestanden.

Nu u begrijpt hoe u de taken maken, gaan we door de stappen om de pijplijn te bewerken.

1. Open de pijplijn en vervang de inhoud door uw YAML:

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
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Selecteer **Opslaan**.

1. Geef een boodschap voor de commit en verbind je rechtstreeks aan **master.**

1. Wanneer u **Opslaan**selecteert, wordt de buildpijplijn automatisch uitgevoerd. Ga terug naar het overzicht voor uw buildpijplijn en bekijk de status.

   ![Resultaten weergeven](./media/add-template-to-azure-pipelines/view-results.png)

U de pijplijn die momenteel wordt uitgevoerd selecteren om details over de taken te bekijken. Wanneer het klaar is, ziet u de resultaten voor elke stap.

## <a name="next-steps"></a>Volgende stappen

Zie [Zelfstudie: Continue integratie van Azure Resource Manager-sjablonen met Azure Pipelines](deployment-tutorial-pipeline.md)voor stapsgewijze procedures voor het gebruik van Azure Pipelines.

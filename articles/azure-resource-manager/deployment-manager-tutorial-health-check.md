---
title: Azure Deployment Manager gebruiken met Resource Manager-sjablonen | Microsoft Docs
description: Resource Manager-sjablonen met Azure Deployment Manager gebruiken om Azure-resources te implementeren.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.date: 05/31/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 17e27fcbd0e31c8602869be3d884888fe4fe7db0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70095816"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Zelfstudie: Status controle gebruiken in azure Deployment Manager (open bare preview)

Meer informatie over het integreren van de status controle in [Azure Deployment Manager](./deployment-manager-overview.md). Deze zelf studie is gebaseerd op de zelf studie [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md) . U moet deze zelf studie volt ooien voordat u verdergaat met dit.

In de implementatie sjabloon die wordt gebruikt voor het [gebruik van Azure Deployment Manager met Resource Manager-sjablonen](./deployment-manager-tutorial.md), hebt u een wachtende stap gebruikt. In deze zelf studie vervangt u de stap wachten met een status controle.

> [!IMPORTANT]
> Als uw abonnement is gemarkeerd voor de Canarische om nieuwe functies van Azure te testen, kunt u Azure Deployment Manager alleen gebruiken om te implementeren in de Canarische regio's. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een Health Check-service Simulator maken
> * De implementatie sjabloon herzien
> * De topologie implementeren
> * Implementatie van de implementatie met een slechte status
> * De implementaties implementeren
> * Implementeer de implementatie met de status in orde
> * De implementaties implementeren
> * Resources opschonen

Aanvullende bronnen:

- De [Naslag informatie voor Azure Deployment Manager rest API](https://docs.microsoft.com/rest/api/deploymentmanager/).
- [Een Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart)-voor beeld.

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* [Gebruik Azure Deployment Manager volt ooien met Resource Manager-sjablonen](./deployment-manager-tutorial.md).
* Down load [de sjablonen en de artefacten](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) die door deze zelf studie worden gebruikt.

## <a name="create-a-health-check-service-simulator"></a>Een Health Check-service Simulator maken

In productie gebruikt u meestal een of meer bewakings providers. Micro soft werkt samen met een aantal van de belangrijkste service status bewakings bedrijven om u een eenvoudige Kopieer-en plak oplossing te bieden voor de integratie van status controles met uw implementaties, om de status integratie zo eenvoudig mogelijk te maken. Zie [providers voor status controle](./deployment-manager-health-check.md#health-monitoring-providers)voor een lijst van deze bedrijven. Voor het doel van deze zelf studie maakt u een [Azure-functie](/azure/azure-functions/) voor het simuleren van een status bewakings service. Deze functie neemt een status code en retourneert dezelfde code. Uw Azure Deployment Manager-sjabloon gebruikt de status code om te bepalen hoe u kunt door gaan met de implementatie.

De volgende twee bestanden worden gebruikt voor het implementeren van de Azure-functie. U hoeft deze bestanden niet te downloaden om de zelf studie door te lopen.

* Een resource manager-sjabloon bevindt zich op [https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json](https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json). U implementeert deze sjabloon om een Azure-functie te maken.
* Een zip-bestand van de bron code van de [https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip](https://armtutorials.blob.core.windows.net/admtutorial/ADMHCFunction0417.zip)Azure-functie,. Deze zip wordt aangeroepen door de Resource Manager-sjabloon.

Als u de Azure-functie wilt implementeren, selecteert u **proberen** om de Azure Cloud shell te openen en plakt u het volgende script in het shell-venster.  Als u de code wilt plakken, klikt u met de rechter muisknop op het shell venster en selecteert u vervolgens **Plakken**.

> [!IMPORTANT]
> **projectName** in het Power shell-script wordt gebruikt voor het genereren van namen voor de Azure-Services die in deze zelf studie worden geïmplementeerd. Verschillende Azure-Services hebben verschillende vereisten voor de namen. Om ervoor te zorgen dat de implementatie is geslaagd, kiest u een naam van minder dan 12 tekens met alleen kleine letters en cijfers.
> Sla een kopie van de project naam op. U gebruikt hetzelfde projectName-project met de zelf studie.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"

New-AzResourceGroup -Name $resourceGroupName -Location $location
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/deploy_hc_azure_function.json" -projectName $projectName
```

Controleren en testen van de Azure-functie:

1. Open de [Azure Portal](https://portal.azure.com).
1. Open de resource groep.  De standaard naam is de naam van het project waaraan **RG** is toegevoegd.
1. Selecteer app service in de resource groep.  De standaard naam van de app service is de project naam met **webapp** toegevoegd.
1. Vouw **functies**uit en selecteer vervolgens **HttpTrigger1**.

    ![Azure-functie Azure Deployment Manager Health check](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. **Selecteer&lt;/> functie-URL ophalen**.
1. Selecteer **kopiëren** om de URL naar het klem bord te kopiëren.  De URL is vergelijkbaar met:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Vervang `{healthStatus}` in de URL door de status code. In deze zelf studie gebruikt u een **slechte status** om het scenario met een slechte status te testen. u kunt het scenario in orde of **waarschuwing** gebruiken om het probleem in orde te testen. Maak twee Url's, een met de status slecht en de andere met de status in orde. Voor voor beelden:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    U hebt beide Url's nodig om deze zelf studie te volt ooien.

1. Als u de status controle Simulator wilt testen, opent u de Url's die u in de laatste stap hebt gemaakt.  De resultaten voor de slechte status moeten gelijk zijn aan:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>De implementatie sjabloon herzien

Het doel van deze sectie is om te laten zien hoe u een status controle stap in de implementatie sjabloon opneemt. U hoeft geen eigen CreateADMRollout. JSON-bestand te maken om deze zelf studie te volt ooien. De gereviseerde implementatie sjabloon wordt gedeeld in een opslag account dat wordt gebruikt in de volgende secties.

1. Open **CreateADMRollout. json**. Dit JSON-bestand maakt deel uit van de down load.  Zie [Vereisten](#prerequisites).
1. Voeg twee extra para meters toe:

    ```json
    "healthCheckUrl": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check URL."
        }
    },
    "healthCheckAuthAPIKey": {
        "type": "string",
        "metadata": {
            "description": "Specifies the health check Azure Function function authorization key."
        }
    }
    ```

1. De resource definitie voor een gewachte stap vervangen door een resource definitie voor de status controle:

    ```json
    {
      "type": "Microsoft.DeploymentManager/steps",
      "apiVersion": "2018-09-01-preview",
      "name": "healthCheckStep",
      "location": "[parameters('azureResourceLocation')]",
      "properties": {
        "stepType": "healthCheck",
        "attributes": {
          "waitDuration": "PT0M",
          "maxElasticDuration": "PT0M",
          "healthyStateDuration": "PT1M",
          "type": "REST",
          "properties": {
            "healthChecks": [
              {
                "name": "appHealth",
                "request": {
                  "method": "GET",
                  "uri": "[parameters('healthCheckUrl')]",
                  "authentication": {
                    "type": "ApiKey",
                    "name": "code",
                    "in": "Query",
                    "value": "[parameters('healthCheckAuthAPIKey')]"
                  }
                },
                "response": {
                  "successStatusCodes": [
                    "200"
                  ],
                  "regex": {
                    "matches": [
                      "Status: healthy",
                      "Status: warning"
                    ],
                    "matchQuantifier": "Any"
                  }
                }
              }
            ]
          }
        }
      }
    },
    ```

    Op basis van de definitie wordt de implementatie voortgezet als de status *in orde* of *waarschuwing*is.

1. Werk de **dependsON** van de implementatie definitie zo bij dat deze de zojuist gedefinieerde status controle bevat:

    ```json
    "dependsOn": [
        "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
        "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Werk **stepGroups** bij met de status controle stap. De **healthCheckStep** wordt aangeroepen in **postDeploymentSteps** van **stepGroup2**. **stepGroup3** en **stepGroup4** worden alleen geïmplementeerd als de status in orde is of een *waarschuwing*is.

    ```json
    "stepGroups": [
        {
            "name": "stepGroup1",
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup2",
            "dependsOnStepGroups": ["stepGroup1"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceWUS.name,  variables('serviceTopology').serviceWUS.serviceUnit1.name)]",
            "postDeploymentSteps": [
                {
                    "stepId": "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
                }
            ]
        },
        {
            "name": "stepGroup3",
            "dependsOnStepGroups": ["stepGroup2"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit2.name)]",
            "postDeploymentSteps": []
        },
        {
            "name": "stepGroup4",
            "dependsOnStepGroups": ["stepGroup3"],
            "preDeploymentSteps": [],
            "deploymentTargetId": "[resourceId('Microsoft.DeploymentManager/serviceTopologies/services/serviceUnits', variables('serviceTopology').name, variables('serviceTopology').serviceEUS.name,  variables('serviceTopology').serviceEUS.serviceUnit1.name)]",
            "postDeploymentSteps": []
        }
    ]
    ```

    Als u de sectie **stepGroup3** vergelijkt voordat en nadat deze is gereviseerd, is deze sectie nu afhankelijk van **stepGroup2**.  Dit is nodig wanneer **stepGroup3** en de volgende stap groepen afhankelijk zijn van de resultaten van de status controle.

    In de volgende scherm afbeelding ziet u de gebieden die zijn gewijzigd en hoe de stap status controle wordt gebruikt:

    ![Sjabloon Azure Deployment Manager Health check](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>De topologie implementeren

Om de zelf studie te vereenvoudigen, worden de topologie sjabloon en artefacten gedeeld op de volgende locaties, zodat u niet uw eigen kopie hoeft voor te bereiden. Als u uw eigen wilt gebruiken, volgt u de instructies in [de zelf studie: Gebruik Azure Deployment Manager met Resource Manager-](./deployment-manager-tutorial.md)sjablonen.

* Topologie sjabloon: https:\//armtutorials.blob.core.Windows.net/admtutorial/ADMTemplates/CreateADMServiceTopology.json
* Opslag voor artefacten: https\/:/armtutorials.blob.core.Windows.net/admtutorial/ArtifactStore

Als u de topologie wilt implementeren, selecteert u **proberen** om de Cloud shell te openen en plakt u het Power shell-script.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMServiceTopology.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation
```

Controleer of de servicetopologie en de onderstreepte resources zijn gemaakt met behulp van Azure Portal:

![Geïmplementeerde servicetopologieresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Verborgen typen weergeven** moet worden geselecteerd om de resources weer te geven.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>Implementatie van de implementatie met de status beschadigd

Om de zelf studie te vereenvoudigen, wordt de gereviseerde implementatie sjabloon gedeeld op de volgende locaties, zodat u niet uw eigen kopie hoeft voor te bereiden. Als u uw eigen wilt gebruiken, volgt u de instructies in [de zelf studie: Gebruik Azure Deployment Manager met Resource Manager-](./deployment-manager-tutorial.md)sjablonen.

* Topologie sjabloon: https:\//armtutorials.blob.core.Windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json
* Opslag voor artefacten: https\/:/armtutorials.blob.core.Windows.net/admtutorial/ArtifactStore

Gebruik de status van de slechte URL die u hebt gemaakt in [een Health Check-service Simulator maken](#create-a-health-check-service-simulator). Zie [de door de gebruiker toegewezen beheerde identiteit maken](./deployment-manager-tutorial.md#create-the-user-assigned-managed-identity)voor **managedIdentityID**.

```azurepowershell-interactive
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$managedIdentityID = Read-Host -Prompt "Enter a user-assigned managed identity"
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

$resourceGroupName = "${projectName}rg"
$artifactLocation = "https://armtutorials.blob.core.windows.net/admtutorial/ArtifactStore?st=2019-05-06T03%3A57%3A31Z&se=2020-05-07T03%3A57%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=gOh%2Bkhi693rmdxiZFQ9xbKZMU1kbLJDqXw7EP4TaGlI%3D" | ConvertTo-SecureString -AsPlainText -Force

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateUri "https://armtutorials.blob.core.windows.net/admtutorial/ADMTemplatesHC/CreateADMRollout.json" `
    -namePrefix $projectName `
    -azureResourceLocation $location `
    -artifactSourceSASLocation $artifactLocation `
    -managedIdentityID $managedIdentityID `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment`is een asynchrone aanroep. Het bericht alleen geslaagd betekent dat de implementatie is gestart. Als u de implementatie wilt controleren `Get-AZDeploymentManagerRollout`, gebruikt u.  Zie de volgende procedure.

U kunt de voortgang van de implementatie controleren met behulp van het volgende Power shell-script:

```azurepowershell
$projectName = Read-Host -Prompt "Enter the same project name used earlier in this tutorial"
$resourceGroupName = "${projectName}rg"
$rolloutName = "${projectName}Rollout"

# Get the rollout status
Get-AzDeploymentManagerRollout `
    -ResourceGroupName $resourceGroupName `
    -Name $rolloutName `
    -Verbose
```

In de volgende voorbeeld uitvoer ziet u dat de implementatie is mislukt vanwege een slechte status:

```output
Service: myhc0417ServiceWUSrg
    TargetLocation: WestUS
    TargetSubscriptionId: <Subscription ID>

    ServiceUnit: myhc0417ServiceWUSWeb
        TargetResourceGroup: myhc0417ServiceWUSrg

        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
Get-AzDeploymentManagerRollout :
Service: myhc0417ServiceWUSrg
    ServiceUnit: myhc0417ServiceWUSWeb
        Step: RestHealthCheck/healthCheckStep.PostDeploy
            Status: Failed
            StepGroup: stepGroup2
            Operation Info:
                Start Time: 05/06/2019 17:58:31
                End Time: 05/06/2019 17:58:32
                Total Duration: 00:00:01
                Error:
                    Code: ResourceReportedUnhealthy
                    Message: Health checks failed as the following resources were unhealthy: '05/06/2019 17:58:32 UTC: Health check 'appHealth' failed with the following errors: Response from endpoint 'https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy' does not match the regex pattern(s): 'Status: healthy, Status: warning.'. Response content: "Status: unhealthy"..'.
At line:1 char:1
+ Get-AzDeploymentManagerRollout `
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : NotSpecified: (:) [Get-AzDeploymentManagerRollout], Exception
+ FullyQualifiedErrorId : RolloutFailed,Microsoft.Azure.Commands.DeploymentManager.Commands.GetRollout


ResourceGroupName       : myhc0417rg
BuildVersion            : 1.0.0.0
ArtifactSourceId        : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/artifactSources/myhc0417ArtifactSourceRollout
TargetServiceTopologyId : /subscriptions/<Subscription ID>/resourceGroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/serviceTopologies/myhc0417ServiceTopology
Status                  : Failed
TotalRetryAttempts      : 0
Identity                : Microsoft.Azure.Commands.DeploymentManager.Models.PSIdentity
OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
Services                : {myhc0417ServiceWUS, myhc0417ServiceWUSrg}
Name                    : myhc0417Rollout
Type                    : Microsoft.DeploymentManager/rollouts
Location                : centralus
Id                      : /subscriptions/<Subscription ID>/resourcegroups/myhc0417rg/providers/Mi
                          crosoft.DeploymentManager/rollouts/myhc0417Rollout
Tags                    :
```

Nadat de implementatie is voltooid, ziet u een extra resource groep die is gemaakt voor de VS West.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>Implementatie van de implementatie met de status in orde

Herhaal deze sectie voor het opnieuw implementeren van de implementatie met de URL voor de goede status.  Nadat de implementatie is voltooid, ziet u nog één resource groep die is gemaakt voor VS-Oost.

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Open de [Azure Portal](https://portal.azure.com).
2. Blader naar de zojuist gemaakte webtoepassingen onder de nieuwe resourcegroepen die zijn gemaakt door de implementatie.
3. Open de webtoepassing in een webbrowser. Controleer de locatie en de versie in het index.html-bestand.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Gebruik het veld **Filteren op naam** om u te beperken tot de resourcegroepen die u in deze zelfstudie hebt gemaakt. Er zijn er 3-4:

    * **&lt;namePrefix > rg**: bevat de Deployment Manager-resources.
    * **&lt;namePrefix > ServiceWUSrg**: bevat de resources die zijn gedefinieerd door ServiceWUS.
    * **&lt;namePrefix>ServiceEUSrg**: bevat de resources die zijn gedefinieerd door ServiceEUS.
    * De resourcegroep voor de door de gebruiker gedefinieerde beheerde identiteit.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.
5. Herhaal de laatste twee stappen als u andere resourcegroepen wilt verwijderen die zijn gemaakt in deze zelfstudie.

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u de status controle functie van Azure Deployment Manager gebruikt. Zie [de documentatie bij Azure Resource Manager](/azure/azure-resource-manager/) voor meer informatie.

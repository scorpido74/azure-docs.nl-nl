---
title: Statuscontrole in Azure Deployment Manager gebruiken
description: Gebruik statuscontrole om Azure-resources veilig te implementeren met Azure Deployment Manager.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 3c7b74d31bc3c4e2276cd52c8e6450630dc99bcd
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86058024"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Zelfstudie: Statuscontrole gebruiken in Azure Deployment Manager (openbare preview)

Meer informatie over het integreren van statuscontrole in [Azure Deployment Manager](./deployment-manager-overview.md). Deze zelfstudie is gebaseerd op de zelfstudie [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md). U moet die zelfstudie voltooien voordat u verdergaat met deze.

In de implementatiesjabloon die wordt gebruikt in [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md), hebt u een wachtstap gebruikt. In deze zelfstudie vervangt u de wachtstap door een statuscontrolestap.

> [!IMPORTANT]
> Als uw abonnement is gemarkeerd voor Canary om nieuwe functies van Azure te testen, kunt u Azure Deployment Manager alleen gebruiken om te implementeren in de Canary-regio's. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een servicesimulator voor statuscontrole maken
> * De implementatiesjabloon herzien
> * De topologie implementeren
> * De implementatie implementeren met slechte status
> * De implementatie verifiëren
> * De implementatie implementeren met de status in orde
> * De implementatie verifiëren
> * Resources opschonen

Aanvullende bronnen:

* De [Azure Deployment Manager REST API-naslaginformatie](/rest/api/deploymentmanager/).
* [Een Azure Deployment Manager-voorbeeld](https://github.com/Azure-Samples/adm-quickstart).

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Voltooi [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>De artefacten installeren

Download [de sjablonen en de artefacten](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) en pak deze lokaal uit als u dit nog niet hebt gedaan. En voer vervolgens het PowerShell-script uit dat is te vinden op [De artefacten voorbereiden](./deployment-manager-tutorial.md#prepare-the-artifacts). Het script maakt een resourcegroep, maakt een opslagcontainer, maakt een blob-container, uploadt de gedownloade bestanden en maakt vervolgens een SAS-token.

Maak een kopie van de URL met SAS-token. Deze URL is nodig voor om een veld in te vullen in de twee parameterbestanden, het parameterbestand voor de topologie en parameterbestand voor de implementatie.

Open CreateADMServiceTopology.Parameters.json en werk de waarden van **projectName** en **artifactSourceSASLocation** bij.

Open CreateADMRollout.Parameters.json en werk de waarden van **projectName** en **artifactSourceSASLocation** bij.

## <a name="create-a-health-check-service-simulator"></a>Een servicesimulator voor statuscontrole maken

In productie gebruikt u meestal een of meer bewakingsproviders. Microsoft werkt samen met een aantal van de belangrijkste bedrijven op het gebied van servicestatusbewaking om u een eenvoudige kopieer- en plakoplossing te bieden voor de integratie van statuscontroles met uw implementaties, om statusintegratie zo eenvoudig mogelijk te maken. Zie [Providers van statuscontroles](./deployment-manager-health-check.md#health-monitoring-providers) voor een lijst van deze bedrijven. Voor het doel van deze zelfstudie maakt u een [Azure-functie](../../azure-functions/index.yml) om een statuscontroleservice te simuleren. Deze functie neemt een statuscode en retourneert dezelfde code. Uw Azure Deployment Manager-sjabloon gebruikt de statuscode om te bepalen hoe verder moet worden gegaan met de implementatie.

De volgende twee bestanden worden gebruikt om de Azure-functie te implementeren. U hoeft deze bestanden niet te downloaden om de zelfstudie door te lopen.

* Een Resource Manager-sjabloon die zich bevindt op [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json). U implementeert deze sjabloon om een Azure-functie te maken.
* Een zip-bestand van de broncode van de Azure-functie, [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip). Deze zip wordt aangeroepen door de Resource Manager-sjabloon.

Als u de Azure-functie wilt implementeren, selecteert u **Probeer het** om de Azure Cloud-shell te openen en plakt u het volgende script in het shell-venster.  Als u de code wilt plakken, klikt u met de rechtermuisknop op het shell-venster en selecteert u **Plakken**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

De Azure-functie controleren en testen:

1. Open de [Azure Portal](https://portal.azure.com).
1. Open de resourcegroep.  De standaardnaam is de naam van het project met **rg** eraan toegevoegd.
1. Selecteer de appservice in de resourcegroep.  De standaardnaam van de app-service is de naam van het project met **webapp** eraan toegevoegd.
1. Vouw **Functies** uit en selecteer vervolgens **HttpTrigger1**.

    ![Azure-functie Statuscontrole in Azure Deployment Manager gebruiken](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selecteer **&lt;/> Functie-URL ophalen**.
1. Selecteer **Kopiëren** om de URL naar het klembord te kopiëren.  De URL is vergelijkbaar met:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Vervang `{healthStatus}` in de URL door een statuscode. In deze zelfstudie gebruikt u **beschadigd** om het scenario met een beschadigde status te testen, en gebruikt u **in orde** of **waarschuwing** om het goede scenario te testen. Maak twee URL's, een met de status beschadigd en de andere met de status in orde. Voorbeelden:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    U hebt beide URL's nodig om deze zelfstudie te voltooien.

1. Als u de statuscontrolesimulator wilt testen, opent u de URL's die u in de laatste stap hebt gemaakt.  De resultaten voor de beschadigde status moeten gelijk zijn aan:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>De implementatiesjabloon herzien

Het doel van deze sectie is om u te laten zien hoe u een statuscontrolestap in de implementatiesjabloon opneemt.

1. Open **CreateADMRollout.json** die u hebt gemaakt in [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md). Dit JSON-bestand maakt deel uit van de download.  Zie [Vereisten](#prerequisites).
1. Voeg nog twee parameters toe:

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

1. Vervang de resource definitie voor de wachtstap door een resourcedefinitie voor de statuscontrole:

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

    Op basis van de definitie wordt de implementatie voortgezet als de status *in orde* of *waarschuwing* is.

1. Werk de **dependsON** van de implementatiedefinitie bij om de zojuist gedefinieerde statuscontrolestap op te nemen:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Werk **stepGroups** om de statuscontrolestap te omvatten. De **healthCheckStep** wordt aangeroepen in **postDeploymentSteps** van **stepGroup2**. **stepGroup3** en **stepGroup4** worden alleen geïmplementeerd als de status *in orde* of *waarschuwing* is.

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

    Als u de sectie **stepGroup3** vergelijkt voor en nadat deze is gereviseerd, is deze sectie nu afhankelijk van **stepGroup2**.  Dit is nodig wanneer **stepGroup3** en de volgende stapgroepen afhankelijk zijn van de resultaten van de statuscontrole.

    In de volgende schermopname ziet u de gebieden die zijn gewijzigd en hoe de stapstatuscontrole wordt gebruikt:

    ![Statuscontrolesjabloon in Azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>De topologie implementeren

Gebruik het volgende PowerShell-script om de topologie te implementeren. U hebt dezelfde **CreateADMServiceTopology.json** en **CreateADMServiceTopology.Parameters.json** nodig die u ook hebt gebruikt in [Azure Deployment Manager met Resource Manager-sjablonen gebruiken](./deployment-manager-tutorial.md).

```azurepowershell
# Create the service topology
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
```

Controleer of de servicetopologie en de onderstreepte resources zijn gemaakt met behulp van Azure Portal:

![Geïmplementeerde servicetopologieresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

**Verborgen typen weergeven** moet worden geselecteerd om de resources weer te geven.

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>De implementatie implementeren met de beschadigde status

Gebruik de URL van de beschadigde status die u hebt gemaakt in [Een Health Check service Simulator maken](#create-a-health-check-service-simulator). U hebt de gereviseerde **CreateADMServiceTopology.json** en dezelfde **CreateADMServiceTopology.Parameters.json** nodig die u ook hebt gebruikt in [Azure Deployment Manager met Resource Manager-sjablonen gebruiken](./deployment-manager-tutorial.md).

```azurepowershell-interactive
$healthCheckUrl = Read-Host -Prompt "Enter the health check Azure function URL"
$healthCheckAuthAPIKey = $healthCheckUrl.Substring($healthCheckUrl.IndexOf("?code=")+6, $healthCheckUrl.Length-$healthCheckUrl.IndexOf("?code=")-6)
$healthCheckUrl = $healthCheckUrl.Substring(0, $healthCheckUrl.IndexOf("?"))

# Create the rollout
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
    -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json" `
    -healthCheckUrl $healthCheckUrl `
    -healthCheckAuthAPIKey $healthCheckAuthAPIKey
```

> [!NOTE]
> `New-AzResourceGroupDeployment` is een asynchrone aanroep. Het succesbericht betekent alleen dat de implementatie is gestart. Gebruik `Get-AZDeploymentManagerRollout` om de implementatie te controleren.  Zie de volgende procedure.

Controleer de voortgang van de implementatie met behulp van het volgende PowerShell-script:

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

In de volgende voorbeelduitvoer ziet u dat de implementatie is mislukt vanwege de beschadigde status:

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

Nadat de implementatie is voltooid, ziet u één extra resourcegroep die is gemaakt voor US - west.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>De implementatie implementeren met de status in orde

Herhaal deze sectie om de implementatie opnieuw te implementeren met de URL voor de status in orde.  Nadat de implementatie is voltooid, ziet u dat er nog een resourcegroep is gemaakt voor US - oost.

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Open de [Azure Portal](https://portal.azure.com).
2. Blader naar de zojuist gemaakte webtoepassingen onder de nieuwe resourcegroepen die zijn gemaakt door de implementatie.
3. Open de webtoepassing in een webbrowser. Controleer de locatie en de versie in het index.html-bestand.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Gebruik het veld **Filteren op naam** om u te beperken tot de resourcegroepen die u in deze zelfstudie hebt gemaakt. Er zijn er 3-4:

    * **&lt;projectName>rg**: bevat de Deployment Manager-resources.
    * **&lt;projectName>ServiceWUSrg**: bevat de resources die zijn gedefinieerd door ServiceWUS.
    * **&lt;projectName>ServiceEUSrg**: bevat de resources die zijn gedefinieerd door ServiceEUS.
    * De resourcegroep voor de door de gebruiker gedefinieerde beheerde identiteit.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.
5. Herhaal de laatste twee stappen als u andere resourcegroepen wilt verwijderen die zijn gemaakt in deze zelfstudie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de statuscontrolefunctie van Azure Deployment Manager gebruikt. Zie [de documentatie bij Azure Resource Manager](../index.yml) voor meer informatie.

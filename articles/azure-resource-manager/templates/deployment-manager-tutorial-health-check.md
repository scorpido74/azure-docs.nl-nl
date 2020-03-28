---
title: Statuscontrole azure deployment manager gebruiken
description: Gebruik statuscontrole om Azure-resources veilig te implementeren met Azure Deployment Manager.
author: mumian
ms.date: 10/09/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 765c73a3ab8d5fa8939abe597d0141b24b59ac52
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76152474"
---
# <a name="tutorial-use-health-check-in-azure-deployment-manager-public-preview"></a>Zelfstudie: Statuscontrole gebruiken in Azure Deployment Manager (openbare voorbeeld)

Meer informatie over het integreren van statuscontrole in [Azure Deployment Manager](./deployment-manager-overview.md). Deze zelfstudie is gebaseerd op de zelfstudie [Azure Deployment Manager gebruiken met Resource Manager-sjablonen.](./deployment-manager-tutorial.md) U moet die tutorial voltooien voordat u verder gaat met deze.

In de implementatiesjabloon die wordt gebruikt in [Azure Deployment Manager gebruiken met Resource Manager-sjablonen,](./deployment-manager-tutorial.md)hebt u een wachttijd gebruikt. In deze zelfstudie vervangt u de wachtstap door een statuscontrolestap.

> [!IMPORTANT]
> Als uw abonnement is gemarkeerd voor Canary om nieuwe Azure-functies uit te testen, u Azure Deployment Manager alleen gebruiken om te implementeren in de Canarische regio's. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Een statuscontroleservicesimulator maken
> * De uitrolsjabloon herzien
> * De topologie implementeren
> * De implementatie implementeren met een ongezonde status
> * De implementatie van de implementatie controleren
> * De implementatie met een gezonde status implementeren
> * De implementatie van de implementatie controleren
> * Resources opschonen

Aanvullende bronnen:

* De [verwijzing naar de REST API van Azure Deployment Manager](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Een voorbeeld van Azure Deployment Manager](https://github.com/Azure-Samples/adm-quickstart).

Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Volledig [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md).

## <a name="install-the-artifacts"></a>De artefacten installeren

Download [de sjablonen en de artefacten](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) en rits deze lokaal uit als u dit nog niet hebt gedaan. En voer vervolgens het PowerShell-script uit dat is gevonden bij [Prepare the artifacts.](./deployment-manager-tutorial.md#prepare-the-artifacts) Het script maakt een resourcegroep, maakt een opslagcontainer, maakt een blobcontainer, uploadt de gedownloade bestanden en maakt vervolgens een SAS-token.

Maak een kopie van de URL met SAS-token. Deze URL is nodig voor om een veld in te vullen in de twee parameterbestanden, het parameterbestand voor de topologie en parameterbestand voor de implementatie.

Open CreateADMServiceTopology.Parameters.json en werk de waarden van **projectName** en **artifactSourceSASLocation**bij .

Open CreateADMRollout.Parameters.json en werk de waarden van **projectName** en **artifactSourceSASLocation**bij .

## <a name="create-a-health-check-service-simulator"></a>Een statuscontroleservicesimulator maken

In de productie gebruikt u doorgaans een of meer bewakingsproviders. Om de integratie van de gezondheid zo eenvoudig mogelijk te maken, heeft Microsoft samengewerkt met enkele van de beste servicehealthmonitoringbedrijven om u een eenvoudige kopieer-/plakoplossing te bieden om statuscontroles te integreren met uw implementaties. Zie Aanbieders van [gezondheidsmonitoring](./deployment-manager-health-check.md#health-monitoring-providers)voor een lijst van deze bedrijven. Voor deze zelfstudie maakt u een [Azure-functie](/azure/azure-functions/) om een statusbewakingsservice te simuleren. Deze functie heeft een statuscode en retourneert dezelfde code. Uw Azure Deployment Manager-sjabloon gebruikt de statuscode om te bepalen hoe u verder moet gaan met de implementatie.

De volgende twee bestanden worden gebruikt voor het implementeren van de Azure-functie. U hoeft deze bestanden niet te downloaden om de zelfstudie te doorlopen.

* Een resourcemanagersjabloon [https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json)op . U implementeert deze sjabloon om een Azure-functie te maken.
* Een zip-bestand van de [https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMHCFunction0417.zip)broncode van de Azure-functie. Deze zip die wordt aangeroepen, wordt aangeroepen door de sjabloon Resourcemanager.

Als u de Azure-functie wilt implementeren, selecteert **u Proberen** om de Azure Cloud-shell te openen en plakt u het volgende script in het shell-venster.  Als u de code wilt plakken, klikt u met de rechtermuisknop op het shell-venster en selecteert u **Plakken**.

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-adm/deploy_hc_azure_function.json" -projectName $projectName
```

Ga als volgt te werk om de Azure-functie te verifiëren en te testen:

1. Open de [Azure Portal](https://portal.azure.com).
1. Open de resourcegroep.  De standaardnaam is de projectnaam **met rg** toegevoegd.
1. Selecteer de app-service in de resourcegroep.  De standaardnaam van de app-service is de projectnaam met **webapp** toegevoegd.
1. **Vouw functies**uit en selecteer **Vervolgens HttpTrigger1**.

    ![Azure Deployment Manager-status controleren Azure-functie](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-function.png)

1. Selecteer ** &lt;de URL van de functie /> ophalen**.
1. Selecteer **Kopiëren** om de URL naar het klembord te kopiëren.  De URL is vergelijkbaar met:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/{healthStatus}?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    Vervang `{healthStatus}` in de URL door een statuscode. Gebruik in deze zelfstudie **ongezonde tests** om het ongezonde scenario te testen en gebruik **een gezond** of **waarschuwing** om het gezonde scenario te testen. Maak twee URL's, een met de ongezonde status en de andere met een gezonde status. Voor voorbeelden:

    ```url
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/unhealthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    https://myhc0417webapp.azurewebsites.net/api/healthStatus/healthy?code=hc4Y1wY4AqsskAkVw6WLAN1A4E6aB0h3MbQ3YJRF3XtXgHvooaG0aw==
    ```

    U hebt beide URL's nodig om deze zelfstudie te voltooien.

1. Als u de statusbewakingssimulator wilt testen, opent u de URL's die u in de laatste stap hebt gemaakt.  De resultaten voor de ongezonde status moeten vergelijkbaar zijn met:

    ```
    Status: unhealthy
    ```

## <a name="revise-the-rollout-template"></a>De uitrolsjabloon herzien

Het doel van deze sectie is om u te laten zien hoe u een statuscontrolestap in de implementatiesjabloon opnemen.

1. Open **CreateADMRollout.json** die u hebt gemaakt in [Azure Deployment Manager gebruiken met Resource Manager-sjablonen](./deployment-manager-tutorial.md). Dit JSON-bestand maakt deel uit van de download.  Zie [Voorwaarden](#prerequisites).
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

1. Vervang de definitie van de wachtstap resource door een resourcedefinitie voor de statuscontrolestap:

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

    Op basis van de definitie gaat de implementatie door als de status *van de* status gezond is of *waarschuwing.*

1. Werk de **afhankelijkheid** van de implementatiedefinitie bij om de nieuw gedefinieerde statuscontrolestap op te nemen:

    ```json
    "dependsOn": [
      "[resourceId('Microsoft.DeploymentManager/artifactSources', variables('rolloutArtifactSource').name)]",
      "[resourceId('Microsoft.DeploymentManager/steps/', 'healthCheckStep')]"
    ],
    ```

1. Stapgroepen **bijwerken** om de stap voor statuscontrole op te nemen. De **healthCheckStep** wordt genoemd in **postDeploymentSteps** van **stepGroup2**. **stepGroup3** en **stepGroup4** worden alleen ingezet als de gezonde status *gezond* is of *waarschuwing*.

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

    Als u de sectie **stepGroup3** voor en na de herziening vergelijkt, is deze sectie nu afhankelijk van **stepGroup2**.  Dit is nodig wanneer **stepGroup3** en de volgende stappengroepen afhankelijk zijn van de resultaten van gezondheidsmonitoring.

    De volgende schermafbeelding illustreert de gewijzigde gebieden en hoe de statuscontrolestap wordt gebruikt:

    ![Sjabloon voor statuscontrole azure Deployment Manager](./media/deployment-manager-tutorial-health-check/azure-deployment-manager-hc-rollout-template.png)

## <a name="deploy-the-topology"></a>De topologie implementeren

Voer het volgende PowerShell-script uit om de topologie te implementeren. U hebt dezelfde **CreateADMServiceTopology.json** en **CreateADMServiceTopology.Parameters.json** nodig die u hebt gebruikt in [Azure Deployment Manager gebruiken met Resource Manager-sjablonen.](./deployment-manager-tutorial.md)

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

## <a name="deploy-the-rollout-with-the-unhealthy-status"></a>De implementatie implementeren met de ongezonde status

Gebruik de URL van de ongezonde status die u hebt gemaakt in [De servicesimulator Voor statuscontrole maken](#create-a-health-check-service-simulator). U hebt de herziene **CreateADMServiceTopology.json** en dezelfde **CreateADMServiceTopology.Parameters.Json** nodig die u hebt gebruikt in [Azure Deployment Manager met Resource Manager-sjablonen](./deployment-manager-tutorial.md).

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
> `New-AzResourceGroupDeployment`is een asynchrone oproep. Het succesbericht betekent alleen dat de implementatie is begonnen. Als u de `Get-AZDeploymentManagerRollout`implementatie wilt verifiëren, gebruikt u .  Zie de volgende procedure.

Ga als volgt te werk om de voortgang van de implementatie te controleren met het volgende PowerShell-script:

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

In de volgende voorbeelduitvoer ziet u dat de implementatie is mislukt vanwege de ongezonde status:

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

Nadat de implementatie is voltooid, ziet u een extra resourcegroep die is gemaakt voor West US.

## <a name="deploy-the-rollout-with-the-healthy-status"></a>De implementatie implementeren met de gezonde status

Herhaal deze sectie om de implementatie opnieuw te implementeren met de URL voor de status van de status.  Nadat de implementatie is voltooid, ziet u nog een resourcegroep die is gemaakt voor Oost-VS.

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Open de [Azure Portal](https://portal.azure.com).
2. Blader naar de zojuist gemaakte webtoepassingen onder de nieuwe resourcegroepen die zijn gemaakt door de implementatie.
3. Open de webtoepassing in een webbrowser. Controleer de locatie en de versie in het index.html-bestand.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **resourcegroep** in de linkermenu in de Azure-portal.
2. Gebruik het veld **Filteren op naam** om u te beperken tot de resourcegroepen die u in deze zelfstudie hebt gemaakt. Er zijn er 3-4:

    * projectName>rg : bevat de resources Deployment Manager. ** &lt;**
    * projectName>ServiceWUSrg : bevat de bronnen die door ServiceWUS zijn gedefinieerd. ** &lt;**
    * projectName>ServiceEUSrg : bevat de door ServiceEUS gedefinieerde bronnen. ** &lt;**
    * De resourcegroep voor de door de gebruiker gedefinieerde beheerde identiteit.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Brongroep verwijderen** in het bovenste menu.
5. Herhaal de laatste twee stappen als u andere resourcegroepen wilt verwijderen die zijn gemaakt in deze zelfstudie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u de functie statuscontrole van Azure Deployment Manager gebruiken. Zie [de documentatie bij Azure Resource Manager](/azure/azure-resource-manager/) voor meer informatie.

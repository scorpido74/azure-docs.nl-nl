---
title: Azure Deployment Manager gebruiken om sjablonen te implementeren
description: Meer informatie over het gebruik van Resource Manager-sjablonen met Azure Deployment Manager voor het implementeren van Azure-resources.
author: mumian
ms.date: 11/21/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f8091aedcf1f03ef83dbab3b27e9af6b2912d25a
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789988"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Zelfstudie: Azure Deployment Manager gebruiken met Resource Manager-sjablonen (openbare preview)

Meer informatie over het gebruik van [Azure Deployment Manager](./deployment-manager-overview.md) om uw toepassingen in meerdere regio's te implementeren. Als u liever een snellere benadering hebt, worden in [Azure Deployment Manager Quick](https://github.com/Azure-Samples/adm-quickstart) start de vereiste configuraties in uw abonnement gemaakt en worden de artefacten aangepast om een toepassing in meerdere regio's te implementeren. De Snelstartgids voert dezelfde taken uit als in deze zelf studie.

Als u Deployment Manager wilt gebruiken, moet u twee sjablonen maken:

* **Een topologiesjabloon**: beschrijft de Azure-resources die uw toepassingen vormen en waar ze moeten worden geïmplementeerd.
* **Een implementatiesjabloon**: beschrijft de stappen die moeten worden genomen bij het implementeren van uw toepassingen.

> [!IMPORTANT]
> Als uw abonnement is gemarkeerd voor de Canarische om nieuwe functies van Azure te testen, kunt u Azure Deployment Manager alleen gebruiken om te implementeren in de Canarische regio's. 

Deze zelfstudie bestaat uit de volgende taken:

> [!div class="checklist"]
> * Inzicht in het scenario
> * De zelfstudiebestanden downloaden
> * De artefacten voorbereiden
> * De door de gebruiker gedefinieerde beheerde identiteit maken
> * De servicetopologiesjabloon maken
> * De implementatiesjabloon maken
> * De sjablonen implementeren
> * De implementatie controleren
> * De nieuwere versie implementeren
> * Resources opschonen

Aanvullende bronnen:

* De [Naslag informatie voor Azure Deployment Manager rest API](https://docs.microsoft.com/rest/api/deploymentmanager/).
* [Zelf studie: status controle gebruiken in Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Als u dit artikel wilt voltooien, hebt u het volgende nodig:

* Enige ervaring met het ontwikkelen van [Azure Resource Manager-sjablonen](./resource-group-overview.md).
* Azure PowerShell. Zie [Aan de slag met Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps) voor meer informatie.
* Deployment Manager-cmdlets. Als u deze prerelease-cmdlets wilt installeren, hebt u de nieuwste versie van PowerShellGet nodig. Zie [PowerShellGet installeren](/powershell/scripting/gallery/installing-psget) voor informatie over het ophalen van de nieuwste versie. Nadat u PowerShellGet hebt geïnstalleerd, sluit u het PowerShell-venster. Open een nieuw PowerShell-venster met verhoogde bevoegdheden en gebruik de volgende opdracht:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Inzicht in het scenario

De servicetopologiesjabloon beschrijft de Azure-resources die uw service vormen en waar ze moeten worden geïmplementeerd. De definitie van de servicetopologie bevat de volgende hiërarchie:

* Servicetopologie
  * Services
    * Service-eenheden

Het volgende diagram illustreert de servicetopologie die in deze zelfstudie wordt gebruikt:

![Diagram van het scenario in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

Er zijn twee services toegewezen aan de locaties in VS-west en VS-oost.  Elke service heeft twee service eenheden - een webtoepassingsfront-end en een opslagaccount voor de back-end. De definities van de service-eenheid bevatten koppelingen naar de sjabloon en parameterbestanden voor het maken van de webtoepassingen en de opslagaccounts.

## <a name="download-the-tutorial-files"></a>De zelfstudiebestanden downloaden

1. Download [de sjablonen en de artefacten](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) die in deze zelfstudie worden gebruikt.
2. Pak de bestanden uit op uw computer.

Onder de hoofdmap bevinden zich twee mappen:

* **ADMTemplates**: bevat de Deployment Manager-sjablonen, namelijk:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore**: bevat zowel de sjabloonartefacten als de binaire artefacten. Zie [De artefacten voorbereiden](#prepare-the-artifacts).

Let op: er zijn twee sets met sjablonen.  Eén set bestaat uit de Deployment Manager-sjablonen die worden gebruikt voor het implementeren van de servicetopologie en de implementatie; de andere set wordt aangeroepen vanuit de service-eenheden om webservices en opslagaccounts te maken.

## <a name="prepare-the-artifacts"></a>De artefacten voorbereiden

De map ArtifactStore uit de download bevat twee mappen:

![Diagram van de artefactbron in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* De map **templates**: bevat de sjabloonartefacten. **1.0.0.0** en **1.0.0.1** vertegenwoordigen de twee versies van de binaire artefacten. Binnen elke versie is er een map voor elke service (service VS-oost en service VS-west). Elke service heeft een combinatie van een sjabloon en parameterbestanden voor het maken van een opslagaccount en een andere combinatie voor het maken van een webtoepassing. De webtoepassingsjabloon roept een gecomprimeerd pakket aan, dat de bestanden voor de webtoepassing bevat. Het gecomprimeerde bestand is een binair artefact dat is opgeslagen in de map met binaire bestanden.
* De map **binaries**: bevat de binaire artefacten. **1.0.0.0** en **1.0.0.1** vertegenwoordigen de twee versies van de binaire artefacten. Binnen elke versie is een zip-bestand voor het maken van de webtoepassing op de locatie van VS west en het andere zip-bestand voor het maken van de webtoepassing op de locatie van VS-oost.

De twee versies (1.0.0.0 en 1.0.0.1) zijn voor de [implementatie van de revisie](#deploy-the-revision). Hoewel zowel de sjabloonartefacten als de binaire artefacten twee versies hebben, zijn alleen de binaire artefacten verschillend tussen de twee versies. In de praktijk worden binaire artefacten vaker bijgewerkt in vergelijking met sjabloonartefacten.

1. Open **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json** in een teksteditor. Dit is een basissjabloon voor het maken van een nieuw opslagaccount.
2. Open **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Sjabloon voor webtoepassing maken in zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    De sjabloon roept een implementatiepakket aan dat de bestanden van de webtoepassing bevat. In deze zelf studie bevat het gecomprimeerde pakket alleen een bestand index. html.
3. Open **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![ContainerRoot sjabloonparameters webtoepassing maken in zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    De waarde van deployPackageUri is het pad naar het implementatiepakket. De parameter bevat een **$containerRoot**-variabele. De waarde van $containerRoot wordt verstrekt in de [implementatiesjabloon](#create-the-rollout-template) door de SAS-bronlocatie van het artefact, de artefacthoofdmap en deployPackageUri samen te voegen.
4. Open **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    In de HTML worden de locatie en de versie-informatie weergegeven. Het binaire bestand in de map 1.0.0.1 geeft 'Versie 1.0.0.1' weer. Nadat u de service hebt geïmplementeerd, kunt u naar deze pagina's navigeren.
5. Bekijk andere artefactbestanden. Dit helpt u een beter inzicht te krijgen in het scenario.

Sjabloonartefacten worden gebruikt door de servicetopologiesjabloon, en binaire artefacten worden gebruikt door de implementatiesjabloon. Zowel de topologiesjabloon als de implementatiesjabloon definieert een Azure-resource met een artefactbron, wat een resource is die wordt gebruikt om Resource Manager te wijzen op de sjabloon en binaire artefacten die in de implementatie worden gebruikt. Ter vereenvoudiging van de zelfstudie wordt één opslagaccount gebruikt voor het opslaan van zowel de sjabloonartefacten als de binaire artefacten. Beide artefactbronnen verwijzen naar hetzelfde opslagaccount.

Voer het volgende Power shell-script uit om een resource groep te maken, een opslag container te maken, een BLOB-container te maken, de gedownloade bestanden te uploaden en vervolgens een SAS-token te maken.

> [!IMPORTANT]
> **projectName** in het Power shell-script wordt gebruikt voor het genereren van namen voor de Azure-Services die in deze zelf studie worden geïmplementeerd. Verschillende Azure-Services hebben verschillende vereisten voor de namen. Om ervoor te zorgen dat de implementatie is geslaagd, kiest u een naam van minder dan 12 tekens met alleen kleine letters en cijfers.
> Sla een kopie van de project naam op. U gebruikt hetzelfde projectName-project met de zelf studie.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)  -Protocol HttpsOrHttp

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Maak een kopie van de URL met het SAS-token. Deze URL is nodig voor het invullen van een veld in de twee parameter bestanden, het bestand met de topologie parameters bestand en de implementatie parameters.

Open de container vanuit het Azure Portal en controleer of zowel de **binaire bestanden** als de **sjabloon** mappen en de bestanden worden geüpload.

## <a name="create-the-user-assigned-managed-identity"></a>Door de gebruiker toegewezen beheerde identiteit maken

Later in de zelfstudie voert u een implementatie uit. Een door de gebruiker toegewezen beheerde identiteit is nodig voor het uitvoeren van de implementatieacties (bijvoorbeeld het implementeren van de webtoepassingen en het opslagaccount). Deze identiteit moet toegang hebben tot het Azure-abonnement waarin u de service implementeert en voldoende machtigingen hebben om de implementatie van het artefact te kunnen voltooien.

U moet een door de gebruiker toegewezen beheerde identiteit maken en toegangsbeheer voor uw abonnement configureren.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Maak een [door de gebruiker toegewezen beheerde identiteit](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. Selecteer in de portal **Abonnementen** in het linkermenu en selecteer vervolgens uw abonnement.
4. Selecteer **Toegangsbeheer (IAM)** en selecteer vervolgens **Roltoewijzing toevoegen**.
5. Typ of selecteer de volgende waarden:

    ![Toegangscontrole voor door de gebruiker toegewezen beheerde identiteit in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Rol**: geef voldoende machtigingen om de implementatie van het artefact (de webtoepassingen en de opslagaccounts) te kunnen voltooien. Selecteer **Inzender** in deze zelfstudie. In de praktijk wilt u de machtigingen tot het minimum beperken.
    * **Toegang toegewezen tot**: selecteer **Door de gebruiker toegewezen beheerde identiteit**.
    * Selecteer door de gebruiker toegewezen beheerde identiteit die u eerder in de zelfstudie hebt gemaakt.
6. Selecteer **Opslaan**.

## <a name="create-the-service-topology-template"></a>De servicetopologiesjabloon maken

Open **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>De parameters

De sjabloon bevat de volgende parameters:

* **projectName**: deze naam wordt gebruikt om de namen voor de Deployment Manager resources te maken. Als u bijvoorbeeld ' jdoe ' gebruikt, is de naam van de service topologie **jdoe**ServiceTopology.  De resourcenamen worden gedefinieerd in de sectie met variabelen van deze sjabloon.
* **azureResourcelocation**: ter vereenvoudiging van de zelfstudie delen alle resources deze locatie, tenzij anders aangegeven.
* **artifactSourceSASLocation**: de SAS-URI naar de blobcontainer waar service-eenheidsjabloon- en parameterbestanden worden opgeslagen voor implementatie.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
* **templateArtifactRoot**: het offsetpad van de blobcontainer waarin de sjablonen en parameters worden opgeslagen. De standaardwaarde is **templates/1.0.0.0**. Wijzig deze waarde niet tenzij u de mapstructuur wilt wijzigen zoals wordt uitgelegd in [De artefacten voorbereiden](#prepare-the-artifacts). In deze zelfstudie worden relatieve paden gebruikt.  Het volledige pad wordt samengesteld door het samenvoegen van **artifactSourceSASLocation**, **templateArtifactRoot** en **templateArtifactSourceRelativePath** (of **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID**: de abonnements-id waarnaar de Deployment Manager-resources worden geïmplementeerd en gefactureerd. Gebruik in deze zelfstudie uw abonnements-id.

### <a name="the-variables"></a>De variabelen

In de sectie met variabelen worden de namen van de resources, de Azure-locaties voor de twee services: **Service WUS** en **Service EUS**, en de artefactpaden gedefinieerd:

![Topologiesjabloonvariabelen in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Vergelijk de artefactpaden met de mapstructuur die u hebt geüpload naar het opslagaccount. U ziet dat de artefactpaden relatieve paden zijn. Het volledige pad wordt samengesteld door het samenvoegen van **artifactSourceSASLocation**, **templateArtifactRoot** en **templateArtifactSourceRelativePath** (of **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>De resources

Op het hoogste niveau zijn er twee resources gedefinieerd: *een bronartefact* en *een servicetopologie*.

De definitie van de artefactbron is:

![Artefactbron van topologiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Op de volgende schermafbeelding ziet u alleen bepaalde onderdelen van de definities van de servicetopologie, services van de service-eenheden:

![Servicetopologie van topologiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** wordt gebruikt om de artifactbronresource te koppelen aan de servicetopologieresource.
* **dependsOn**: alle servicetopologieresources zijn afhankelijk van de artefactbron.
* **artefacten** verwijzen naar de sjabloonartefacten.  Hier worden relatieve paden gebruikt. Het volledige pad wordt samengesteld door het samenvoegen van artifactSourceSASLocation (gedefinieerd in de artefactbron), artifactRoot (gedefinieerd in de artefactbron) en templateArtifactSourceRelativePath (of parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Topologieparameterbestand

U maakt een parameterbestand dat wordt gebruikt in combinatie met de topologiesjabloon.

1. Open **\ADMTemplates\CreateADMServiceTopology.Parameters** in Visual Studio Code of in een teksteditor.
2. Vul de parameterwaarden in:

    * **projectName**: Voer een teken reeks in met 4-5 tekens. Deze naam wordt gebruikt om unieke Azure-resource namen te maken.
    * **azureResourceLocation**: als u niet bekend bent met Azure-locaties, gebruikt u in deze zelfstudie **centralus**.
    * **artifactSourceSASLocation**: voer de SAS-URI naar de hoofdmap (de blobcontainer) waar service-eenheidsjabloon- en parameterbestanden worden opgeslagen voor implementatie in.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
    * **templateArtifactRoot**: gebruik in deze zelfstudie **templates/1.0.0.0**, tenzij u de mapstructuur van de artefacten wijzigt.

> [!IMPORTANT]
> De topologiesjabloon en de implementatiesjabloon delen enkele algemene parameters. Deze parameters moeten dezelfde waarden hebben. Deze para meters zijn: **projectName**, **azureResourceLocation**en **artifactSourceSASLocation** (beide artefact bronnen delen hetzelfde opslag account in deze zelf studie).

## <a name="create-the-rollout-template"></a>De implementatiesjabloon maken

Open **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>De parameters

De sjabloon bevat de volgende parameters:

![Implementatiesjabloonparameters in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **projectName**: deze naam wordt gebruikt om de namen voor de Deployment Manager resources te maken. Als u bijvoorbeeld ' jdoe ' gebruikt, is de naam van de implementatie **jdoe**implementatie.  De namen worden gedefinieerd in de sectie met variabelen van de sjabloon.
* **azureResourcelocation**: ter vereenvoudiging van de zelfstudie delen alle Deployment Manager-resources deze locatie, tenzij anders aangegeven.
* **artifactSourceSASLocation**: de SAS-URI naar de hoofdmap (de blobcontainer) waar service-eenheidsjabloon- en parameterbestanden worden opgeslagen voor implementatie.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
* **binaryArtifactRoot**: de standaardwaarde is **binaries/1.0.0.0**. Wijzig deze waarde niet tenzij u de mapstructuur wilt wijzigen zoals wordt uitgelegd in [De artefacten voorbereiden](#prepare-the-artifacts). In deze zelfstudie worden relatieve paden gebruikt.  Het volledige pad wordt samengesteld door het samenvoegen van **artifactSourceSASLocation**, **binaryArtifactRoot** en de **deployPackageUri** die is opgegeven in CreateWebApplicationParameters.json.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
* **managedIdentityID**: de door de gebruiker toegewezen beheerde identiteit die de implementatieacties uitvoert. Zie [Door de gebruiker toegewezen beheerde identiteit maken](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>De variabelen

In de sectie met variabelen worden de namen van de resources gedefinieerd. Zorg ervoor dat de naam van de servicetopologie, de servicenamen en de namen van de service-eenheden overeenkomen met de namen die zijn gedefinieerd in de [topologiesjabloon](#create-the-service-topology-template).

![Implementatiesjabloonvariabelen in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>De resources

Op het hoogste niveau zijn er drie resources gedefinieerd: een bronartefact, een stap en een implementatie.

De definitie van de artefactbron is identiek aan die in de topologiesjabloon is gedefinieerd.  Zie [De servicetopologietopologiesjabloon maken](#create-the-service-topology-template) voor meer informatie.

Op de volgende schermafbeeldingen wordt de definitie van de wachtstap weergegeven:

![Wachtstap implementatiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Voor de duur wordt gebruikgemaakt van de [ISO 8601-norm](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (hoofdletters zijn verplicht) is een voorbeeld van 1 minuut wachten.

Op de volgende schermafbeelding worden alleen bepaalde onderdelen van de definitie van de implementatie weergegeven:

![Implementatie van implementatiesjabloonresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn**: de implementatieresource is afhankelijk van de artefactbronresource en alle gedefinieerde stappen.
* **artifactSourceId** wordt gebruikt om de artefactbronresource te koppelen aan de servicetopologieresource.
* **targetServiceTopologyId** wordt gebruikt om de servicetopologieresource te koppelen aan de implementatieresource.
* **deploymentTargetId**: dit is de service-eenheidresource-ID van de servicetopologieresource.
* **preDeploymentSteps** en **postDeploymentSteps**: bevat de implementatiestappen. In de sjabloon wordt een wachtstap aangeroepen.
* **dependsOnStepGroups**: configureer de afhankelijkheden tussen de stapgroepen.

### <a name="rollout-parameters-file"></a>Implementatieparameterbestand

U maakt een parameterbestand dat wordt gebruikt in combinatie met de implementatiesjabloon.

1. Open **\ADMTemplates\CreateADMRollout.Parameters** in Visual Studio Code of in een teksteditor.
2. Vul de parameterwaarden in:

    * **projectName**: Voer een teken reeks in met 4-5 tekens. Deze naam wordt gebruikt om unieke Azure-resource namen te maken.
    * **azureResourceLocation**: Geef een Azure-locatie op.
    * **artifactSourceSASLocation**: voer de SAS-URI naar de hoofdmap (de blobcontainer) waar service-eenheidsjabloon- en parameterbestanden worden opgeslagen voor implementatie in.  Zie [De artefacten voorbereiden](#prepare-the-artifacts).
    * **binaryArtifactRoot**: gebruik in deze zelfstudie **binaries/1.0.0.0**, tenzij u de mapstructuur van de artefacten wijzigt.
    * **managedIdentityID**: voer de door de gebruiker toegewezen beheerde identiteit in. Zie [Door de gebruiker toegewezen beheerde identiteit maken](#create-the-user-assigned-managed-identity). De syntaxis is:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> De topologiesjabloon en de implementatiesjabloon delen enkele algemene parameters. Deze parameters moeten dezelfde waarden hebben. Deze para meters zijn: **projectName**, **azureResourceLocation**en **artifactSourceSASLocation** (beide artefact bronnen delen hetzelfde opslag account in deze zelf studie).

## <a name="deploy-the-templates"></a>De sjablonen implementeren

Azure PowerShell kan worden gebruikt om de sjablonen te implementeren.

1. Voer het script uit om de servicetopologie te implementeren.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Als u dit script uitvoert vanuit een andere Power shell-sessie dan dat u het script voor [artefacten](#prepare-the-artifacts) hebt voor bereid, moet u eerst de variabelen opnieuw vullen, met inbegrip van **$resourceGroupName** en **$filepath**.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` is een asynchrone aanroep. Het bericht alleen geslaagd betekent dat de implementatie is gestart. Zie stap 2 en stap 4 van deze procedure om de implementatie te controleren.

2. Controleer of de servicetopologie en de onderstreepte resources zijn gemaakt met behulp van Azure Portal:

    ![Geïmplementeerde servicetopologieresources in de zelfstudie over Azure Deployment Manager](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    **Verborgen typen weergeven** moet worden geselecteerd om de resources weer te geven.

3. <a id="deploy-the-rollout-template"></a>De implementatiesjabloon implementeren:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Controleer de voortgang van de implementatie met behulp van het volgende PowerShell-script:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    De Deployment Manager PowerShell-cmdlets moeten worden geïnstalleerd voordat u deze cmdlet kunt uitvoeren. Zie Vereisten. De schakel optie-verbose kan worden gebruikt om de hele uitvoer te bekijken.

    Het volgende voorbeeld toont de actieve status:

    ```
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Wanneer de implementatie met succes is voltooid, ziet u dat er twee extra resourcegroepen zijn gemaakt, één voor elke service.

## <a name="verify-the-deployment"></a>De implementatie controleren

1. Open de [Azure-portal](https://portal.azure.com).
2. Blader naar de zojuist gemaakte webtoepassingen onder de nieuwe resourcegroepen die zijn gemaakt door de implementatie.
3. Open de webtoepassing in een webbrowser. Controleer de locatie en de versie in het index.html-bestand.

## <a name="deploy-the-revision"></a>De revisie implementeren

Wanneer u een nieuwe versie (1.0.0.1) voor de webtoepassing hebt. Kunt u de volgende procedure gebruiken om de webtoepassing opnieuw te implementeren.

1. Open CreateADMRollout.Parameters.json.
2. Werk **binaryArtifactRoot** naar **binaries/1.0.0.1** bij.
3. Voer de implementatie opnieuw uit volgens de instructies in [De sjablonen implementeren](#deploy-the-rollout-template).
4. Controleer de implementatie volgens de instructies in [De implementatie controleren](#verify-the-deployment). Op de webpagina wordt de versie 1.0.0.1 weergegeven.

## <a name="clean-up-resources"></a>Resources opschonen

Schoon de geïmplementeerd Azure-resources, wanneer u deze niet meer nodig hebt, op door de resourcegroep te verwijderen.

1. Selecteer **Resourcegroep** in het linkermenu van Azure Portal.
2. Gebruik het veld **Filteren op naam** om u te beperken tot de resourcegroepen die u in deze zelfstudie hebt gemaakt. Er zijn er 3-4:

    * **&lt;projectName > RG**: bevat de Deployment Manager resources.
    * **&lt;projectName > ServiceWUSrg**: bevat de resources die zijn gedefinieerd door ServiceWUS.
    * **&lt;projectName > ServiceEUSrg**: bevat de resources die zijn gedefinieerd door ServiceEUS.
    * De resourcegroep voor de door de gebruiker gedefinieerde beheerde identiteit.
3. Selecteer de naam van de resourcegroep.
4. Selecteer **Resourcegroep verwijderen** in het bovenste menu.
5. Herhaal de laatste twee stappen als u andere resourcegroepen wilt verwijderen die zijn gemaakt in deze zelfstudie.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u Azure Deployment Manager gebruikt. Zie [zelf studie: status controle gebruiken in azure Deployment Manager](./deployment-manager-tutorial-health-check.md)voor het integreren van de status controle in azure Deployment Manager.

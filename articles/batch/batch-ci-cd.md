---
title: Azure Pipelines gebruiken om HPC-oplossingen & implementeren - Azure Batch te implementeren
description: Meer informatie over het implementeren van een build/release-pijplijn voor een HPC-toepassing die wordt uitgevoerd op Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533127"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Azure Pipelines gebruiken om HPC-oplossingen te bouwen en te implementeren

Azure DevOps-services bieden een reeks hulpprogramma's die worden gebruikt door ontwikkelteams bij het bouwen van een aangepaste toepassing. Tools van Azure DevOps kunnen zich vertalen in geautomatiseerdbouwen en testen van hoogwaardige compute-oplossingen. In dit artikel wordt uitgelegd hoe u een permanente integratie (CI) en een continue implementatie (CD) instellen met Azure Pipelines voor een krachtige compute-oplossing die is geïmplementeerd op Azure Batch.

Azure Pipelines biedt een reeks moderne CI/CD-processen voor het bouwen, implementeren, testen en bewaken van software. Deze processen versnellen uw softwarelevering, zodat u zich concentreren op uw code in plaats van infrastructuur en activiteiten te ondersteunen.

## <a name="create-an-azure-pipeline"></a>Een Azure-pijplijn maken

In dit voorbeeld maken we een build- en releasepijplijn om een Azure Batch-infrastructuur te implementeren en een toepassingspakket vrij te geven. Ervan uitgaande dat de code lokaal is ontwikkeld, is dit de algemene implementatiestroom:

![Diagram met de implementatiestroom in onze pijplijn](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Instellen

Als u de stappen in dit artikel wilt volgen, hebt u een Azure DevOps-organisatie en een teamproject nodig.

* [Een Azure DevOps-organisatie maken](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Een project maken in Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Broncontrole voor uw omgeving

Met bronbeheer kunnen teams wijzigingen in de codebase bijhouden en eerdere versies van de code inspecteren.

Meestal wordt aan broncontrole gedacht aan hand in hand met softwarecode. Hoe zit het met de onderliggende infrastructuur? Dit brengt ons bij Infrastructure as Code, waar we Azure Resource Manager-sjablonen of andere open-source alternatieven zullen gebruiken om onze onderliggende infrastructuur declaratief te definiëren.

Dit voorbeeld is sterk afhankelijk van een aantal Resource Manager-sjablonen (JSON-documenten) en bestaande binaire bestanden. U deze voorbeelden naar uw opslagplaats kopiëren en naar Azure DevOps pushen.

De codebasestructuur die in dit monster wordt gebruikt, lijkt op de volgende structuur;

* Een **map met armsjablonen** met een aantal Azure Resource Manager-sjablonen. De sjablonen worden uitgelegd in dit artikel.
* Een **map voor clienttoepassingen,** een kopie van het Azure Batch [.NET-bestandsverwerking met ffmpeg-voorbeeld.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Dit is niet nodig voor dit artikel.
* Een **hpc-applicatie** map, dat is de Windows 64-bits versie van [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Een **map met pijplijnen.** Dit bevat een YAML-bestand waarin ons buildproces wordt beschreven. Dit wordt besproken in het artikel.

In deze sectie wordt ervan uitgegaan dat u bekend bent met versiebeheer en het ontwerpen van Resource Manager-sjablonen. Als u niet bekend bent met deze concepten, raadpleegt u de volgende pagina's voor meer informatie.

* [Wat is broncontrole?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Informatie over de structuur en de syntaxis van Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

In dit voorbeeld worden meerdere Resource Manager-sjablonen gebruikt om onze oplossing te implementeren. Hiervoor gebruiken we een aantal capaciteitssjablonen (vergelijkbaar met eenheden of modules) die een specifiek stukje functionaliteit implementeren. We gebruiken ook een end-to-end oplossingssjabloon die verantwoordelijk is voor het samenbrengen van deze onderliggende mogelijkheden. Er zijn een paar voordelen aan deze aanpak:

* De onderliggende capaciteitssjablonen kunnen individueel worden getest.
* De onderliggende capaciteitssjablonen kunnen worden gedefinieerd als een standaard binnen een organisatie en worden hergebruikt in meerdere oplossingen.

In dit voorbeeld is er een end-to-end oplossingssjabloon (deployment.json) die drie sjablonen implementeert. De onderliggende sjablonen zijn capaciteitssjablonen die verantwoordelijk zijn voor het implementeren van een specifiek aspect van de oplossing.

![Voorbeeld van gekoppelde sjabloonstructuur met Azure Resource Manager-sjablonen](media/batch-ci-cd/ARMTemplateHierarchy.png)

De eerste sjabloon die we zullen bekijken is voor een Azure Storage-account. Onze oplossing vereist een opslagaccount om de toepassing te implementeren op onze Batch-account. Het is de moeite waard om op de hoogte te zijn van de [referentiegids resourcebeheer voor Microsoft.Storage-brontypen](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) bij het maken van Resource Manager-sjablonen voor opslagaccounts.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Vervolgens bekijken we de sjabloon Azure Batch-account. Het Azure Batch-account fungeert als een platform om tal van toepassingen in verschillende groepen (groeperingen van machines) uit te voeren. Het is de moeite waard om op de hoogte te zijn van de [naslaggids voor resourcebeheersjablonen voor Microsoft.Batch resourcetypen](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) bij het maken van Resource Manager-sjablonen voor Batch-accounts.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

De volgende sjabloon toont een voorbeeld van het maken van een Azure Batch Pool (de backendmachines om onze toepassingen te verwerken). Het is de moeite waard om op de hoogte te zijn van de [naslaggids voor resourcebeheersjablonen voor Microsoft.Batch resourcetypen](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) bij het bouwen van Resource Manager-sjablonen voor batchaccountgroepen.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Tot slot hebben we een sjabloon die lijkt op een orchestrator. Deze sjabloon is verantwoordelijk voor het implementeren van de capaciteitssjablonen.

U ook meer informatie vinden over [het maken van gekoppelde Azure Resource Manager-sjablonen](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) in een apart artikel.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>De HPC-oplossing

De infrastructuur en software kunnen worden gedefinieerd als code en worden ondergebracht in dezelfde repository.

Voor deze oplossing wordt de ffmpeg gebruikt als toepassingspakket. De ffmpeg pakket kan [hier](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)worden gedownload.

![Voorbeeld Git-repositorystructuur](media/batch-ci-cd/git-repository.jpg)

Er zijn vier hoofdsecties voor deze opslagplaats:

* De **map met armsjablonen** die onze infrastructuur als code opslaat
* De **hpc-applicatie** map die de binaries voor ffmpeg bevat
* De **map pijplijnen** die de definitie voor onze buildpijplijn bevat.
* **Optioneel:** de **map client-applicatie** waarmee code voor .NET-toepassing wordt opgeslagen. We gebruiken dit niet in het voorbeeld, maar in uw eigen project u de HPC Batch Application uitvoeren via een clientapplicatie.

> [!NOTE]
> Dit is slechts een voorbeeld van een structuur aan een codebase. Deze benadering wordt gebruikt om aan te tonen dat toepassing, infrastructuur en pijplijncode in dezelfde opslagplaats worden opgeslagen.

Nu de broncode is ingesteld, kunnen we beginnen met de eerste build.

## <a name="continuous-integration"></a>Continue integratie

[Azure Pipelines,](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)binnen Azure DevOps Services, helpt u bij het implementeren van een build-, test- en implementatiepijplijn voor uw toepassingen.

In deze fase van uw pijplijn worden tests meestal uitgevoerd om code te valideren en de juiste onderdelen van de software te bouwen. Het aantal en de soorten tests en eventuele extra taken die u uitvoert, zijn afhankelijk van uw bredere build- en releasestrategie.

## <a name="preparing-the-hpc-application"></a>Voorbereiding van de HPC-aanvraag

In dit voorbeeld zullen we ons richten op de **map hpc-applicatie.** De **map hpc-toepassingen** is de ffmpeg-software die wordt uitgevoerd vanuit het Azure Batch-account.

1. Navigeer naar het gedeelte Builds van Azure Pipelines in uw Azure DevOps-organisatie. Een **nieuwe pijplijn maken**.

    ![Een nieuwe buildpijplijn maken](media/batch-ci-cd/new-build-pipeline.jpg)

1. U hebt twee opties om een buildpijplijn te maken:

    a. [Met behulp van de Visual Designer](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Als u dit wilt gebruiken, klikt u op 'Gebruik de visuele ontwerper' op de pagina **Nieuwe pijplijn.**

    b. [YAML-builds gebruiken.](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops) U een nieuwe YAML-pijplijn maken door op de optie Azure Repos of GitHub te klikken op de pagina Nieuwe pijplijn. U het onderstaande voorbeeld ook opslaan in uw bronbeheer en een bestaand YAML-bestand verwijzen door op Visual Designer te klikken en vervolgens de YAML-sjabloon te gebruiken.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Zodra de build zo nodig is geconfigureerd, selecteert u **& wachtrij opslaan**. Als u continue integratie hebt ingeschakeld (in de sectie **Triggers),** wordt de build automatisch geactiveerd wanneer een nieuwe commit naar de repository wordt gemaakt, die voldoet aan de voorwaarden die in de build zijn ingesteld.

    ![Voorbeeld van een bestaande buildpijplijn](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Bekijk live updates over de voortgang van uw build in Azure DevOps door te navigeren naar het gedeelte **Bouwen** van Azure Pipelines. Selecteer de juiste build in uw builddefinitie.

    ![Bekijk live-uitgangen van je build](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Als u een clienttoepassing gebruikt om uw HPC Batch-toepassing uit te voeren, moet u een afzonderlijke builddefinitie voor die toepassing maken. U een aantal handleidingen voor [handleidingen](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) vinden in de Azure Pipelines-documentatie.

## <a name="continuous-deployment"></a>Doorlopende implementatie

Azure Pipelines worden ook gebruikt om uw toepassing en onderliggende infrastructuur te implementeren. [Releasepipelines](https://docs.microsoft.com/azure/devops/pipelines/release) is het onderdeel dat continue implementatie mogelijk maakt en uw releaseproces automatiseert.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Uw toepassing en onderliggende infrastructuur implementeren

Er zijn een aantal stappen betrokken bij de implementatie van de infrastructuur. Aangezien we [gekoppelde sjablonen](../azure-resource-manager/templates/linked-templates.md)hebben gebruikt, moeten deze sjablonen toegankelijk zijn vanaf een openbaar eindpunt (HTTP of HTTPS). Dit kan een opslagplaats zijn op GitHub, een Azure Blob Storage Account of een andere opslaglocatie. De geüploade sjabloonartefacten kunnen veilig blijven, omdat ze in een privémodus kunnen worden bewaard, maar toegankelijk zijn met een SAS-token (Shared Access Signature). In het volgende voorbeeld wordt uitgelegd hoe u een infrastructuur met sjablonen van een Azure Storage-blob implementeert.

1. Maak een **nieuwe releasedefinitie**en selecteer een lege definitie. Vervolgens moeten we de nieuw gecreëerde omgeving hernoemen naar iets dat relevant is voor onze pijplijn.

    ![Eerste releasepijplijn](media/batch-ci-cd/Release-0.jpg)

1. Maak een afhankelijkheid van de Build Pipeline om de uitvoer voor onze HPC-toepassing te krijgen.

    > [!NOTE]
    > Noteer nogmaals de **Bronalias**, omdat dit nodig is wanneer taken worden gemaakt binnen de releasedefinitie.

    ![Een artefactkoppeling maken naar het HPCApplicationPackage in de juiste buildpijplijn](media/batch-ci-cd/Release-1.jpg)

1. Maak een koppeling naar een ander artefact, deze keer een Azure Repo. Dit is vereist om toegang te krijgen tot de Resource Manager-sjablonen die zijn opgeslagen in uw opslagplaats. Omdat Resource Manager-sjablonen geen compilatie vereisen, hoeft u ze niet door een buildpijplijn te duwen.

    > [!NOTE]
    > Noteer nogmaals de **Bronalias**, omdat dit nodig is wanneer taken worden gemaakt binnen de releasedefinitie.

    ![Een artefactkoppeling maken naar de Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Navigeer naar de sectie **variabelen.** Het wordt aanbevolen om een aantal variabelen in uw pijplijn te maken, zodat u niet dezelfde informatie in meerdere taken invoert. Dit zijn de variabelen die in dit voorbeeld worden gebruikt en hoe deze van invloed zijn op de implementatie.

    * **applicationStorageAccountName**: Naam van het opslagaccount om HPC-toepassingsbinaries te houden
    * **batchAccountApplicationName**: Naam van de toepassing in het Azure Batch-account
    * **batchAccountName:** Naam van het Azure Batch-account
    * **batchAccountPoolName:** Naam van de pool van VM's die de verwerking doen
    * **batchApplicationId:** unieke id voor de Azure Batch-toepassing
    * **batchApplicationVersion:** Semantische versie van uw batchtoepassing (dat wil zeggen de ffmpeg-binaries)
    * **locatie:** locatie voor de Azure Resources die moeten worden geïmplementeerd
    * **resourceGroupName:** Naam van de te maken resourcegroep en waar uw resources worden geïmplementeerd
    * **storageAccountName:** Naam van het opslagaccount om gekoppelde Resource Beheer-sjablonen vast te houden

    ![Voorbeeld van variabelen die zijn ingesteld voor de release van Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Navigeer naar de taken voor de Dev-omgeving. In de onderstaande momentopname ziet u zes taken. Deze taken zullen: download de rits ffmpeg-bestanden, implementeren van een opslagaccount om de geneste Resource Manager-sjablonen te hosten, kopieer deze Resource Manager-sjablonen naar het opslagaccount, implementeer het batchaccount en vereiste afhankelijkheden, maak een toepassing in het Azure Batch-account en upload het toepassingspakket naar het Azure Batch-account.

    ![Voorbeeld van de taken die worden gebruikt om de HPC-toepassing vrij te geven aan Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Voeg de taak **Artefact voor pijplijnartefact downloaden (voorbeeld)** toe en stel de volgende eigenschappen in:
    * **Weergavenaam:** ApplicationPackage downloaden naar agent
    * **De naam van het artefact te downloaden:** hpc-applicatie
    * **Pad om te downloaden naar**: $(System.DefaultWorkingDirectory)

1. Maak een opslagaccount om je artefacten op te slaan. Een bestaand opslagaccount van de oplossing kan worden gebruikt, maar voor het op zichzelf staande voorbeeld en isolatie van inhoud maken we een speciaal opslagaccount voor onze artefacten (met name de Resource Manager-sjablonen).

    Voeg de **taak Implementatie van Azure Resource Group** toe en stel de volgende eigenschappen in:
    * **Weergavenaam:** Storage Account for Resource Manager-sjablonen implementeren
    * **Azure-abonnement:** Het juiste Azure-abonnement selecteren
    * **Actie:** Resourcegroep maken of bijwerken
    * **Resourcegroep:**$(resourceGroupName)
    * **Locatie**: $(locatie)
    * **Sjabloon**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Sjabloonparameters overschrijven**: -accountName $(storageAccountName)

1. Upload de artefacten uit het bronbeheer naar het opslagaccount. Er is een Azure Pipeline-taak om dit uit te voeren. Als onderdeel van deze taak kunnen de URL van de opslagaccountcontainer en het SAS-token worden uitgevoerd naar een variabele in Azure-pijplijnen. Dit betekent dat het kan worden hergebruikt gedurende deze agentfase.

    Voeg de taak **Azure File Copy toe** en stel de volgende eigenschappen in:
    * **Bron:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Azure Connection Type**: Azure Resource Manager
    * **Azure-abonnement:** Het juiste Azure-abonnement selecteren
    * **Doeltype:** Azure Blob
    * **RM-opslagaccount:**$(storageAccountName)
    * **Containernaam:** sjablonen
    * **Opslagcontainer URI**: sjabloonContainerUri
    * **Storage Container SAS Token**: templateContainerSasToken

1. Implementeer de orchestratorsjabloon. Herinner de orchestrator-sjabloon van eerder terug, u zult merken dat er parameters waren voor de URL van de opslagaccountcontainer, naast het SAS-token. U moet opmerken dat de variabelen die vereist zijn in de sjabloon Resourcebeheer worden bewaard in het gedeelte variabelen van de releasedefinitie of zijn ingesteld vanuit een andere Azure Pipelines-taak (bijvoorbeeld een deel van de taak Azure Blob Copy).

    Voeg de **taak Implementatie van Azure Resource Group** toe en stel de volgende eigenschappen in:
    * **Weergavenaam:** Azure-batch implementeren
    * **Azure-abonnement:** Het juiste Azure-abonnement selecteren
    * **Actie:** Resourcegroep maken of bijwerken
    * **Resourcegroep:**$(resourceGroupName)
    * **Locatie**: $(locatie)
    * **Sjabloon**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Sjabloonparameters overschrijven:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Een gangbare praktijk is het gebruik van Azure Key Vault-taken. Als de Service Principal (verbinding met uw Azure-abonnement) een geschikt toegangsbeleid heeft ingesteld, kan deze geheimen uit een Azure Key Vault downloaden en worden gebruikt als variabelen in uw pijplijn. De naam van het geheim wordt ingesteld met de bijbehorende waarde. Een geheim van sshPassword kan bijvoorbeeld worden verwezen met $(sshPassword) in de releasedefinitie.

1. In de volgende stappen wordt de Azure CLI aanroepen. De eerste wordt gebruikt om een toepassing te maken in Azure Batch. en upload en upload bijbehorende pakketten.

    Voeg de **Azure CLI-taak** toe en stel de volgende eigenschappen in:
    * **Weergavenaam:** Toepassing maken in Azure Batch-account
    * **Azure-abonnement:** Het juiste Azure-abonnement selecteren
    * **Scriptlocatie:** Inline Script
    * **Inline Script:**```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. De tweede stap wordt gebruikt om bijbehorende pakketten naar de toepassing te uploaden. In ons geval, de ffmpeg bestanden.

    Voeg de **Azure CLI-taak** toe en stel de volgende eigenschappen in:
    * **Weergavenaam:** Pakket uploaden naar Azure Batch-account
    * **Azure-abonnement:** Het juiste Azure-abonnement selecteren
    * **Scriptlocatie:** Inline Script
    * **Inline Script:**```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Het versienummer van het toepassingspakket is ingesteld op een variabele. Dit is handig als het overschrijven van eerdere versies van het pakket voor u werkt en als u handmatig het versienummer van het pakket wilt beheren dat naar Azure Batch wordt geschoven.

1. Maak een nieuwe release door **Release > Een nieuwe release maken**te selecteren. Zodra deze is geactiveerd, selecteert u de koppeling naar uw nieuwe versie om de status weer te geven.

1. U de live-uitvoer van de agent bekijken door de knop **Logboeken** onder uw omgeving te selecteren.

    ![De status van uw release weergeven](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Het testen van de omgeving

Zodra de omgeving is ingesteld, bevestigt u dat de volgende tests met succes kunnen worden voltooid.

Maak verbinding met het nieuwe Azure Batch-account met behulp van de Azure CLI vanuit een PowerShell-opdrachtprompt.

* Meld u aan bij `az login` uw Azure-account en volg de instructies om te verifiëren.
* Verifieer nu het Batch-account:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>De beschikbare toepassingen weergeven

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Controleer of de pool geldig is

```azurecli
az batch pool list
```

Let op `currentDedicatedNodes` de waarde van de uitvoer van deze opdracht. Deze waarde wordt in de volgende test aangepast.

#### <a name="resize-the-pool"></a>Het formaat van het zwembad wijzigen

Het formaat van de groep wijzigen zodat er rekenknooppunten beschikbaar zijn voor taak- en taaktests, controleer de opdracht de opdracht lijst met groep om de huidige status te bekijken totdat het formaat is voltooid en er beschikbare knooppunten beschikbaar zijn

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Volgende stappen

Naast dit artikel zijn er twee tutorials die ffmpeg gebruiken, met behulp van .NET en Python. Zie deze tutorials voor meer informatie over hoe om te gaan met een Batch-account via een eenvoudige toepassing.

* [Een parallelle workload uitvoeren met Azure Batch met behulp van de Python API](tutorial-parallel-python.md)
* [een parallelle workload uitvoeren met Azure Batch met behulp van de .NET API](tutorial-parallel-dotnet.md)

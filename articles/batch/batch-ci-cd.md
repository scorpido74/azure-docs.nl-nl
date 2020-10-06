---
title: Gebruik Azure-pijp lijnen om & te bouwen HPC-oplossingen
description: Meer informatie over het implementeren van een build/release-pijp lijn voor een HPC-toepassing die wordt uitgevoerd op Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: 2ad148579daa30d62da01aded0a01ace56f3dcbc
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760560"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Gebruik Azure-pijp lijnen om HPC-oplossingen te bouwen en te implementeren

Azure DevOps Services biedt een reeks hulpprogram ma's die worden gebruikt door ontwikkel teams bij het bouwen van een aangepaste toepassing. Hulpprogram ma's van Azure DevOps kunnen worden omgezet in geautomatiseerd bouwen en testen van high performance Compute-oplossingen. In dit artikel wordt beschreven hoe u een doorlopende integratie (CI) en doorlopende implementatie (CD) kunt instellen met behulp van Azure-pijp lijnen voor een high performance Compute-oplossing die is geïmplementeerd op Azure Batch.

Azure-pijp lijnen bieden een scala aan moderne CI/CD-processen voor het bouwen, implementeren, testen en bewaken van software. Deze processen versnellen uw software levering, zodat u zich kunt richten op uw code in plaats van de infra structuur en bewerkingen te ondersteunen.

## <a name="create-an-azure-pipeline"></a>Een Azure-pijp lijn maken

In dit voor beeld maken we een pijp lijn voor Build en release om een Azure Batch-infra structuur te implementeren en een toepassings pakket te publiceren. Ervan uitgaande dat de code lokaal is ontwikkeld, is dit de algemene implementatie stroom:

![Diagram van de stroom van de implementatie in onze pijp lijn](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Instellen

Als u de stappen in dit artikel wilt volgen, hebt u een Azure DevOps-organisatie en een team project nodig.

* [Een Azure DevOps-organisatie maken](/azure/devops/organizations/accounts/create-organization)
* [Een project maken in azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Broncode beheer voor uw omgeving

Met broncode beheer kunnen teams wijzigingen bijhouden die zijn aangebracht in de code basis en de vorige versies van het programma controleren.

Normaal gesp roken wordt het broncode beheer hand matig met software code beschouwd. Hoe zit het met de onderliggende infra structuur? Dit brengt ons de infra structuur als code, waarbij we Azure Resource Manager-sjablonen of andere open-source alternatieven gebruiken om de onderliggende infra structuur op een declaratieve manier te definiëren.

Dit voor beeld is intensief afhankelijk van een aantal Resource Manager-sjablonen (JSON-documenten) en bestaande binaire bestanden. U kunt deze voor beelden naar uw opslag plaats kopiëren en deze naar Azure DevOps pushen.

De code basis structuur die in dit voor beeld wordt gebruikt, lijkt op het volgende.

* Een **arm-sjablonen-** map met een aantal Azure Resource Manager sjablonen. De sjablonen worden beschreven in dit artikel.
* Een map van de **client-toepassing** , een kopie van de [Azure batch .net-bestands verwerking met ffmpeg-voor](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) beeld. Dit is niet nodig voor dit artikel.
* Een **HPC-toepassingsmap** , de Windows 64-bits versie van [ffmpeg 4.3.1](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-4.3.1-2020-09-21-full_build.zip).
* Een **pipeline** -map. Dit bevat een YAML-bestand met een overzicht van het bouw proces. Dit wordt behandeld in het artikel.

In deze sectie wordt ervan uitgegaan dat u bekend bent met versie beheer en het ontwerpen van Resource Manager-sjablonen. Als u niet bekend bent met deze concepten, raadpleegt u de volgende pagina's voor meer informatie.

* [Wat is broncode beheer?](/azure/devops/user-guide/source-control)
* [Inzicht in de structuur en syntaxis van Azure Resource Manager sjablonen](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sjablonen

Dit voor beeld maakt gebruik van meerdere Resource Manager-sjablonen voor het implementeren van onze oplossing. Hiervoor gebruiken we een aantal mogelijkheden sjablonen (vergelijkbaar met eenheden of modules) waarmee een specifiek onderdeel van de functionaliteit wordt geïmplementeerd. We gebruiken ook een end-to-end oplossings sjabloon die verantwoordelijk is voor het samen brengen van deze onderliggende mogelijkheden. Deze aanpak heeft een aantal voor delen:

* De onderliggende sjablonen voor de mogelijkheid van een afzonderlijke eenheid kunnen worden getest.
* De onderliggende sjablonen voor mogelijkheden kunnen worden gedefinieerd als een standaard binnen een organisatie en moeten opnieuw worden gebruikt in meerdere oplossingen.

Voor dit voor beeld is er een end-to-end oplossings sjabloon (deployment.jsaan) die drie sjablonen implementeert. De onderliggende sjablonen zijn mogelijkheden sjablonen, die verantwoordelijk zijn voor het implementeren van een specifiek aspect van de oplossing.

![Voor beeld van een gekoppelde sjabloon structuur met behulp van Azure Resource Manager sjablonen](media/batch-ci-cd/ARMTemplateHierarchy.png)

De eerste sjabloon die we gaan bekijken, is voor een Azure Storage-account. Onze oplossing vereist een opslag account om de toepassing te implementeren in het batch-account. Het is belang rijk dat u de [Naslag Gids voor de Resource Manager-sjabloon voor micro soft. Storage-Resource typen](/azure/templates/microsoft.storage/allversions) maakt bij het maken van Resource Manager-sjablonen voor opslag accounts.

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

Vervolgens kijken we naar de sjabloon voor het Azure Batch-account. Het Azure Batch-account fungeert als een platform voor het uitvoeren van talloze toepassingen in Pools (groepen van machines). Het is belang rijk dat u de [naslag handleiding voor Resource Manager-sjablonen voor Microsoft.BatCH-resource typen](/azure/templates/microsoft.batch/allversions) bij het maken van Resource Manager-sjablonen voor batch-accounts.

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

De volgende sjabloon toont een voor beeld van het maken van een Azure Batch groep (de back-endservers voor het verwerken van onze toepassingen). Het is belang rijk dat u de [naslag handleiding voor Resource Manager-sjablonen voor Microsoft.BatCH-resource typen](/azure/templates/microsoft.batch/allversions) bij het maken van Resource Manager-sjablonen voor batch-account groepen.

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

Ten slotte hebben we een sjabloon die vergelijkbaar is met een Orchestrator. Deze sjabloon is verantwoordelijk voor het implementeren van de mogelijkheden sjablonen.

U kunt ook meer te weten komen over [het maken van gekoppelde Azure Resource Manager sjablonen](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) in een afzonderlijk artikel.

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

De infra structuur en software kunnen worden gedefinieerd als code en worden opgenomen in dezelfde opslag plaats.

Voor deze oplossing wordt het ffmpeg gebruikt als het toepassings pakket. Het ffmpeg-pakket kan [hier](https://www.videohelp.com/software?d=ffmpeg-3.3.4-win64-static.zip)worden gedownload.

![Voor beeld van Git-opslag structuur](media/batch-ci-cd/git-repository.jpg)

Er zijn vier hoofd secties voor deze opslag plaats:

* De map **arm-sjablonen** die onze infra structuur opslaat als code
* De map **HPC-toepassing** die de binaire bestanden voor ffmpeg bevat
* De map **pijp lijnen** met de definitie voor onze build-pijp lijn.
* **Optioneel**: de map van de **client-toepassing** die code opslaat voor .NET-toepassing. Dit wordt niet gebruikt in het voor beeld, maar in uw eigen project wilt u mogelijk uitvoeringen uitvoeren van de HPC batch-toepassing via een client toepassing.

> [!NOTE]
> Dit is slechts één voor beeld van een structuur voor een code base. Deze methode wordt gebruikt om aan te tonen dat de toepassing, de infra structuur en de pijplijn code worden opgeslagen in dezelfde opslag plaats.

Nu de bron code is ingesteld, kunnen we beginnen met de eerste build.

## <a name="continuous-integration"></a>Continue integratie

[Azure-pijp lijnen](/azure/devops/pipelines/get-started/), binnen Azure DevOps Services, helpt u bij het implementeren van een pijp lijn voor bouwen, testen en implementatie voor uw toepassingen.

In deze fase van uw pijp lijn worden tests meestal uitgevoerd om code te valideren en de juiste onderdelen van de software te bouwen. Het aantal en de typen tests, en eventuele extra taken die u uitvoert, zijn afhankelijk van uw bredere build-en release strategie.

## <a name="preparing-the-hpc-application"></a>De HPC-toepassing voorbereiden

In dit voor beeld wordt de focus gelegd op de map **HPC-Application** . De map **HPC-Application** is de ffmpeg-software die vanuit het Azure batch-account wordt uitgevoerd.

1. Navigeer naar het gedeelte builds van Azure pipelines in uw Azure DevOps-organisatie. Maak een **nieuwe pijp lijn**.

    ![Een nieuwe build-pijp lijn maken](media/batch-ci-cd/new-build-pipeline.jpg)

1. U hebt twee opties voor het maken van een build-pijp lijn:

    a. [Visual Designer gebruiken](/azure/devops/pipelines/get-started-designer). Als u dit wilt gebruiken, klikt u op de Visual Designer gebruiken op de pagina **nieuwe pijp lijn** .

    b. [Yaml-builds gebruiken](/azure/devops/pipelines/get-started-yaml). U kunt een nieuwe YAML-pijp lijn maken door te klikken op de optie Azure opslag plaatsen of GitHub op de pagina nieuwe pijp lijn. U kunt het voor beeld hieronder ook opslaan in het bron beheer en verwijzen naar een bestaand YAML-bestand door te klikken op Visual Designer en vervolgens de YAML-sjabloon te gebruiken.

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

1. Wanneer de build zo nodig is geconfigureerd, selecteert u **& wachtrij opslaan**. Als u doorlopende integratie hebt ingeschakeld (in de sectie **Triggers** ), wordt de build automatisch geactiveerd wanneer een nieuwe door Voer van de opslag plaats wordt gemaakt en voldoen aan de voor waarden die zijn ingesteld in de build.

    ![Voor beeld van een bestaande build-pijp lijn](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Bekijk actuele updates over de voortgang van uw build in azure DevOps door te navigeren naar de sectie **Build** van Azure pipelines. Selecteer de juiste build uit uw build-definitie.

    ![Live uitvoer van uw build weer geven](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Als u een-client toepassing gebruikt om uw HPC-batch toepassing uit te voeren, moet u een afzonderlijke build-definitie maken voor die toepassing. U kunt een aantal hand leidingen vinden in de documentatie over [Azure-pijp lijnen](/azure/devops/pipelines/get-started/index) .

## <a name="continuous-deployment"></a>Doorlopende implementatie

Azure-pijp lijnen worden ook gebruikt voor het implementeren van uw toepassing en de onderliggende infra structuur. [Release pijplijnen](/azure/devops/pipelines/release) is het onderdeel dat doorlopende implementatie mogelijk maakt en uw release proces automatiseert.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Uw toepassing en de onderliggende infra structuur implementeren

Er zijn een aantal stappen voor het implementeren van de-infra structuur. Omdat we [gekoppelde sjablonen](../azure-resource-manager/templates/linked-templates.md)hebben gebruikt, moeten deze sjablonen toegankelijk zijn vanaf een openbaar eind punt (http of https). Dit kan een opslag plaats op GitHub, een Azure Blob Storage-account of een andere opslag locatie zijn. De geüploade sjabloon artefacten kunnen veilig blijven, omdat ze kunnen worden ondergebracht in een persoonlijke modus, maar via een van de volgende SAS-tokens (Shared Access Signature) worden geopend. In het volgende voor beeld ziet u hoe u een infra structuur implementeert met sjablonen van een Azure Storage-blob.

1. Maak een **nieuwe release definitie**en selecteer een lege definitie. Vervolgens moet u de nieuwe omgeving een andere naam geven die relevant is voor de pijp lijn.

    ![Pijp lijn voor de oorspronkelijke release](media/batch-ci-cd/Release-0.jpg)

1. Maak een afhankelijkheid van de build-pijp lijn om de uitvoer voor onze HPC-toepassing op te halen.

    > [!NOTE]
    > Noteer opnieuw de **bron alias**, omdat dit nodig is wanneer taken worden gemaakt in de release definitie.

    ![Maak een artefact koppeling naar de HPCApplicationPackage in de juiste build-pijp lijn](media/batch-ci-cd/Release-1.jpg)

1. Maak een koppeling naar een ander artefact, deze keer een Azure-opslag plaats. Dit is vereist voor toegang tot de Resource Manager-sjablonen die zijn opgeslagen in uw opslag plaats. Als Resource Manager-sjablonen geen compilatie vereisen, hoeft u deze niet te pushen via een build-pijp lijn.

    > [!NOTE]
    > Noteer opnieuw de **bron alias**, omdat dit nodig is wanneer taken worden gemaakt in de release definitie.

    ![Een artefact koppeling maken naar de Azure-opslag plaatsen](media/batch-ci-cd/Release-2.jpg)

1. Navigeer naar de sectie **Varia bles** . Het is raadzaam om een aantal variabelen in uw pijp lijn te maken, zodat u niet dezelfde informatie in meerdere taken hoeft op te zetten. Dit zijn de variabelen die in dit voor beeld worden gebruikt en hoe deze van invloed zijn op de implementatie.

    * **applicationStorageAccountName**: naam van het opslag account voor het opslaan van binaire bestanden van HPC-toepassingen
    * **batchAccountApplicationName**: naam van de toepassing in het Azure batch-account
    * **batchAccountName**: naam van het Azure batch-account
    * **batchAccountPoolName**: naam van de pool van vm's die de verwerking uitvoeren
    * **batchApplicationId**: unieke id voor de Azure batch toepassing
    * **batchApplicationVersion**: semantische versie van uw batch-toepassing (dat wil zeggen de binaire bestanden van ffmpeg)
    * **locatie**: locatie van de Azure-resources die moeten worden geïmplementeerd
    * **resourceGroupName**: de naam van de resource groep die moet worden gemaakt en waar uw resources worden geïmplementeerd
    * **storageAccountName**: naam van het opslag account dat gekoppelde Resource Manager-sjablonen moet bevatten

    ![Voor beeld van variabelen die zijn ingesteld voor de release van de Azure-pijp lijnen](media/batch-ci-cd/Release-4.jpg)

1. Navigeer naar de taken voor de ontwikkel omgeving. In de onderstaande moment opname ziet u zes taken. Deze taken: de gezipte ffmpeg-bestanden downloaden, een opslag account implementeren om de geneste Resource Manager-sjablonen te hosten, deze resource manager-sjablonen kopiëren naar het opslag account, het batch-account en de vereiste afhankelijkheden implementeren, een toepassing maken in het Azure Batch-account en het toepassings pakket uploaden naar het Azure Batch-account.

    ![Voor beeld van de taken die worden gebruikt voor het vrijgeven van de HPC-toepassing naar Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Voeg de taak **pijplijn artefact downloaden (preview)** toe en stel de volgende eigenschappen in:
    * **Weergave naam:** ApplicationPackage downloaden naar agent
    * **De naam van het artefact dat moet worden gedownload:** HPC-toepassing
    * **Pad om te downloaden naar**: $ (System. DefaultWorkingDirectory)

1. Maak een opslag account om uw artefacten op te slaan. Een bestaand opslag account van de oplossing kan worden gebruikt, maar voor het eigen voor beeld en de isolatie van inhoud, maken we een speciaal opslag account voor onze artefacten (met name de Resource Manager-sjablonen).

    Voeg de implementatie taak voor de **Azure-resource groep** toe en stel de volgende eigenschappen in:
    * **Weergave naam:** Opslag account voor Resource Manager-sjablonen implementeren
    * **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    * **Actie**: resource groep maken of bijwerken
    * **Resource groep**: $ (resourceGroupName)
    * **Locatie**: $ (locatie)
    * **Sjabloon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.jsop
    * **Sjabloon parameters negeren**:-AccountName $ (storageAccountName)

1. Upload de artefacten van het bron beheer naar het opslag account. Er is een Azure pipeline-taak om dit uit te voeren. Als onderdeel van deze taak kunnen de URL van de opslag account container en het SAS-token worden gegenereerd naar een variabele in azure-pijp lijnen. Dit betekent dat deze tijdens deze agent fase opnieuw kan worden gebruikt.

    Voeg de **Azure** -taak voor het kopiëren van bestanden toe en stel de volgende eigenschappen in:
    * **Bron:** $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Type Azure-verbinding**: Azure Resource Manager
    * **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    * **Doel type**: Azure Blob
    * **RM-opslag account**: $ (storageAccountName)
    * **Container naam**: sjablonen
    * **URI van opslag container**: templateContainerUri
    * **SAS-token van opslag container**: templateContainerSasToken

1. De Orchestrator-sjabloon implementeren. Als u de Orchestrator-sjabloon eerder hebt ingetrokken, ziet u dat er para meters zijn voor de container-URL van het opslag account, naast het SAS-token. Houd er rekening mee dat de variabelen die vereist zijn in de Resource Manager-sjabloon, worden vastgehouden in de sectie Varia bles van de release definitie of zijn ingesteld vanaf een andere Azure pipeline-taak (bijvoorbeeld onderdeel van de Azure Blob Copy-taak).

    Voeg de implementatie taak voor de **Azure-resource groep** toe en stel de volgende eigenschappen in:
    * **Weergave naam:** Azure Batch implementeren
    * **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    * **Actie**: resource groep maken of bijwerken
    * **Resource groep**: $ (resourceGroupName)
    * **Locatie**: $ (locatie)
    * **Sjabloon**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.jsop
    * **Sjabloon parameters overschrijven**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Een veelvoorkomende procedure is het gebruik van Azure Key Vault taken. Als voor de Service-Principal (verbinding met uw Azure-abonnement) een geschikt toegangs beleid is ingesteld, kunnen er geheimen van een Azure Key Vault worden gedownload en als variabelen worden gebruikt in uw pijp lijn. De naam van het geheim wordt ingesteld met de bijbehorende waarde. In de release definitie kan bijvoorbeeld naar een geheim van sshPassword worden verwezen met $ (sshPassword).

1. Met de volgende stappen wordt de Azure CLI aangeroepen. De eerste wordt gebruikt om een toepassing te maken in Azure Batch. en het uploaden van gekoppelde pakketten.

    Voeg de **Azure cli** -taak toe en stel de volgende eigenschappen in:
    * **Weergave naam:** Toepassing maken in Azure Batch-account
    * **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    * **Script locatie**: inline-script
    * **Inline-script**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. De tweede stap wordt gebruikt voor het uploaden van gekoppelde pakketten naar de toepassing. In ons geval worden de ffmpeg-bestanden.

    Voeg de **Azure cli** -taak toe en stel de volgende eigenschappen in:
    * **Weergave naam:** Pakket uploaden naar Azure Batch-account
    * **Azure-abonnement:** Selecteer het juiste Azure-abonnement
    * **Script locatie**: inline-script
    * **Inline-script**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Het versie nummer van het toepassings pakket is ingesteld op een variabele. Dit is handig als eerdere versies van het pakket worden overschreven, en als u het versie nummer van het pakket dat is gepusht naar Azure Batch, hand matig wilt beheren.

1. Maak een nieuwe release door **release > een nieuwe release maken**te selecteren. Zodra deze is geactiveerd, selecteert u de koppeling naar uw nieuwe release om de status weer te geven.

1. U kunt de live uitvoer van de agent weer geven door de knop **logs** onder uw omgeving te selecteren.

    ![De status van uw release weer geven](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>De omgeving testen

Nadat de omgeving is ingesteld, bevestigt u dat de volgende tests kunnen worden voltooid.

Maak verbinding met het nieuwe Azure Batch-account met behulp van de Azure CLI vanuit een Power shell-opdracht prompt.

* Meld u aan bij uw Azure-account `az login` en volg de instructies om te verifiëren.
* Verifieer nu het batch-account: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>De beschik bare toepassingen weer geven

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Controleer of de groep geldig is

```azurecli
az batch pool list
```

Noteer de waarde van van `currentDedicatedNodes` de uitvoer van deze opdracht. Deze waarde wordt bij de volgende test aangepast.

#### <a name="resize-the-pool"></a>Grootte van de pool wijzigen

Wijzig de grootte van de pool zodat er reken knooppunten beschikbaar zijn voor taak-en taak tests, Controleer met de opdracht pool lijst om de huidige status weer te geven totdat het formaat is voltooid en er beschik bare knoop punten zijn

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Volgende stappen

Naast dit artikel zijn er twee zelf studies die gebruikmaken van ffmpeg, met behulp van .NET en python. Raadpleeg deze zelf studies voor meer informatie over het gebruik van een batch-account via een eenvoudige toepassing.

* [Een parallelle workload uitvoeren met Azure Batch met behulp van de Python API](tutorial-parallel-python.md)
* [een parallelle workload uitvoeren met Azure Batch met behulp van de .NET API](tutorial-parallel-dotnet.md)

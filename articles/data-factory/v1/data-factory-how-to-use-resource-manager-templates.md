---
title: Resourcebeheersjablonen gebruiken in Gegevensfabriek
description: Meer informatie over het maken en gebruiken van Azure Resource Manager-sjablonen om entiteiten in gegevensfabriek te maken.
services: data-factory
documentationcenter: ''
author: djpmsft
ms.author: daperlov
manager: jroth
ms.reviewer: maghan
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 930a3f0cf629c99fc03a84c701bcf7b2807c77c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972861"
---
# <a name="use-templates-to-create-azure-data-factory-entities"></a>Sjablonen gebruiken om Entiteiten van Azure Data Factory te maken
> [!NOTE]
> Dit artikel is van toepassing op versie 1 van Data Factory. 

## <a name="overview"></a>Overzicht
Tijdens het gebruik van Azure Data Factory voor uw behoeften op het gebied van gegevensintegratie, u merken dat u hetzelfde patroon in verschillende omgevingen hergebruikt of dezelfde taak herhaaldelijk implementeert binnen dezelfde oplossing. Sjablonen helpen u deze scenario's eenvoudig te implementeren en te beheren. Sjablonen in Azure Data Factory zijn ideaal voor scenario's die herbruikbaarheid en herhaling inhouden.

Denk aan de situatie waarin een organisatie heeft 10 fabrieken over de hele wereld. De logboeken van elke installatie worden opgeslagen in een afzonderlijke on-premises SQL Server-database. Het bedrijf wil één datawarehouse bouwen in de cloud voor ad hoc analytics. Het wil ook dezelfde logica hebben, maar verschillende configuraties voor ontwikkelings-, test- en productieomgevingen.

In dit geval moet een taak worden herhaald binnen dezelfde omgeving, maar met verschillende waarden in de 10 datafabrieken voor elke productie-installatie. In feite is **herhaling** aanwezig. Templating maakt de abstractie van deze generieke stroom mogelijk (dat wil zeggen pijpleidingen met dezelfde activiteiten in elke gegevensfabriek), maar gebruikt een afzonderlijk parameterbestand voor elke productie-installatie.

Bovendien, omdat de organisatie deze 10 datafabrieken meerdere keren in verschillende omgevingen wil implementeren, kunnen sjablonen deze **herbruikbaarheid** gebruiken door gebruik te maken van afzonderlijke parameterbestanden voor ontwikkelings-, test- en productieomgevingen.

## <a name="templating-with-azure-resource-manager"></a>Templating met Azure Resource Manager
[Azure Resource Manager-sjablonen](../../azure-resource-manager/templates/overview.md) zijn een geweldige manier om templating in Azure Data Factory te bereiken. Resourcebeheersjablonen definiëren de infrastructuur en configuratie van uw Azure-oplossing via een JSON-bestand. Omdat Azure Resource Manager-sjablonen werken met alle/de meeste Azure-services, kan deze op grote schaal worden gebruikt om eenvoudig alle resources van uw Azure-assets te beheren. Zie [Sjablonen voor Azure Resource Manager ontwerpen](../../azure-resource-manager/templates/template-syntax.md) voor meer informatie over de Resource Manager-sjablonen in het algemeen.

## <a name="tutorials"></a>Zelfstudies
Zie de volgende zelfstudies voor stapsgewijze instructies om entiteiten in gegevensfabriek te maken met behulp van Resource Manager-sjablonen:

* [Zelfstudie: Een pijplijn maken om gegevens te kopiëren met de sjabloon Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
* [Zelfstudie: Een pijplijn maken om gegevens te verwerken met de sjabloon Azure Resource Manager](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Gegevensfabrieksjablonen op GitHub
Bekijk de volgende Azure-sjablonen voor snel starten op GitHub:

* [Een gegevensfabriek maken om gegevens uit Azure Blob Storage naar Azure SQL-database te kopiëren](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
* [Een gegevensfabriek maken met Hive-activiteit op azure HDInsight-cluster](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
* [Een gegevensfabriek maken om gegevens van Salesforce naar Azure Blobs te kopiëren](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)
* [Een gegevensfabriek maken die activiteiten ketent: kopieert gegevens van een FTP-server naar Azure Blobs, roept een hivescript op een on-demand HDInsight-cluster om de gegevens te transformeren en kopieert het resultaat naar Azure SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-data-factory-ftp-hive-blob)

Voel je vrij om uw Azure Data Factory-sjablonen te delen bij [Azure Quick start.](https://azure.microsoft.com/documentation/templates/) Raadpleeg de [bijdragegids](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) tijdens het ontwikkelen van sjablonen die via deze repository kunnen worden gedeeld.

In de volgende secties vindt u informatie over het definiëren van gegevensfabrieksbronnen in een resourcemanagersjabloon.

## <a name="defining-data-factory-resources-in-templates"></a>Gegevensfabriekresources definiëren in sjablonen
De sjabloon op het hoogste niveau voor het definiëren van een gegevensfabriek is:

```JSON
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
"contentVersion": "1.0.0.0",
"parameters": { ...
},
"variables": { ...
},
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
    "resources": [
    { "type": "linkedservices",
        ...
    },
    {"type": "datasets",
        ...
    },
    {"type": "dataPipelines",
        ...
    }
}
```

### <a name="define-data-factory"></a>Een gegevensfactory definiëren
U definieert een gegevensfactory in de Resource Manager-sjabloon zoals in het volgende voorbeeld wordt weergegeven:

```JSON
"resources": [
{
    "name": "[variables('<mydataFactoryName>')]",
    "apiVersion": "2015-10-01",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "East US"
}
```
De dataFactoryName wordt gedefinieerd in "variabelen" als:

```JSON
"dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",
```

### <a name="define-linked-services"></a>Gekoppelde services definiëren

```JSON
"type": "linkedservices",
"name": "[variables('<LinkedServiceName>')]",
"apiVersion": "2015-10-01",
"dependsOn": [ "[variables('<dataFactoryName>')]" ],
"properties": {
    ...
}
```

Zie [Storage Linked Service](data-factory-azure-blob-connector.md#azure-storage-linked-service) of Compute Linked [Services](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) voor meer informatie over de JSON-eigenschappen voor de specifieke gekoppelde service die u wilt implementeren. De parameter 'dependsOn' geeft de naam van de bijbehorende gegevensfabriek aan. Een voorbeeld van het definiëren van een gekoppelde service voor Azure Storage wordt weergegeven in de volgende JSON-definitie:

### <a name="define-datasets"></a>Gegevenssets definiëren

```JSON
"type": "datasets",
"name": "[variables('<myDatasetName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<myDatasetLinkedServiceName>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    ...
}
```
Raadpleeg [Ondersteunde gegevensarchieven](data-factory-data-movement-activities.md#supported-data-stores-and-formats) voor meer informatie over de JSON-eigenschappen voor het specifieke gegevenssettype dat u wilt implementeren. Let op: de parameter 'dependsOn' geeft de naam op van de bijbehorende gegevensfabriek en opslaggekoppelde service. Een voorbeeld van het definiëren van het type Azure blob-opslag wordt weergegeven in de volgende JSON-definitie:

```JSON
"type": "datasets",
"name": "[variables('storageDataset')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('storageLinkedServiceName')]"
],
"apiVersion": "2015-10-01",
"properties": {
"type": "AzureBlob",
"linkedServiceName": "[variables('storageLinkedServiceName')]",
"typeProperties": {
    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
    "fileName": "[parameters('sourceBlobName')]",
    "format": {
        "type": "TextFormat"
    }
},
"availability": {
    "frequency": "Hour",
    "interval": 1
}
```

### <a name="define-pipelines"></a>Pijplijnen definiëren

```JSON
"type": "dataPipelines",
"name": "[variables('<mypipelineName>')]",
"dependsOn": [
    "[variables('<dataFactoryName>')]",
    "[variables('<inputDatasetLinkedServiceName>')]",
    "[variables('<outputDatasetLinkedServiceName>')]",
    "[variables('<inputDataset>')]",
    "[variables('<outputDataset>')]"
],
"apiVersion": "2015-10-01",
"properties": {
    activities: {
        ...
    }
}
```

Raadpleeg [het definiëren van pijplijnen](data-factory-create-pipelines.md#pipeline-json) voor meer informatie over de JSON-eigenschappen voor het definiëren van de specifieke pijplijn en activiteiten die u wilt implementeren. Let op de parameter 'dependsOn' geeft de naam van de gegevensfabriek en de bijbehorende gekoppelde services of gegevenssets op. In het volgende JSON-fragment wordt een voorbeeld van een pijplijn weergegeven die gegevens kopieert van Azure Blob Storage naar Azure SQL Database:

```JSON
"type": "datapipelines",
"name": "[variables('pipelineName')]",
"dependsOn": [
    "[variables('dataFactoryName')]",
    "[variables('azureStorageLinkedServiceName')]",
    "[variables('azureSqlLinkedServiceName')]",
    "[variables('blobInputDatasetName')]",
    "[variables('sqlOutputDatasetName')]"
],
"apiVersion": "2015-10-01",
"properties": {
    "activities": [
    {
        "name": "CopyFromAzureBlobToAzureSQL",
        "description": "Copy data frm Azure blob to Azure SQL",
        "type": "Copy",
        "inputs": [
            {
                "name": "[variables('blobInputDatasetName')]"
            }
        ],
        "outputs": [
            {
                "name": "[variables('sqlOutputDatasetName')]"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "BlobSource"
            },
            "sink": {
                "type": "SqlSink",
                "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
            },
            "translator": {
                "type": "TabularTranslator",
                "columnMappings": "Column0:FirstName,Column1:LastName"
            }
        },
        "Policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 3,
            "timeout": "01:00:00"
        }
    }
    ],
    "start": "2016-10-03T00:00:00Z",
    "end": "2016-10-04T00:00:00Z"
}
```
## <a name="parameterizing-data-factory-template"></a>Sjabloon Gegevensfabriek parameteriseren
Zie [Aanbevolen procedures voor het maken van Azure Resource Manager-sjablonen voor](../../azure-resource-manager/resource-manager-template-best-practices.md)aanbevolen procedures voor aanbevolen procedures. In het algemeen moet het gebruik van parameters worden geminimaliseerd, vooral als variabelen in plaats daarvan kunnen worden gebruikt. Geef alleen parameters op in de volgende scenario's:

* Instellingen verschillen per omgeving (bijvoorbeeld ontwikkeling, test en productie)
* Geheimen (zoals wachtwoorden)

Als u geheimen uit [Azure Key Vault](../../key-vault/key-vault-overview.md) moet halen bij het implementeren van Azure Data Factory-entiteiten met behulp van sjablonen, geeft u de **sleutelkluis** en **de geheime naam** op zoals weergegeven in het volgende voorbeeld:

```JSON
"parameters": {
    "storageAccountKey": {
        "reference": {
            "keyVault": {
                "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
             },
            "secretName": "<secretName>"
           },
       },
       ...
}
```

> [!NOTE]
> Hoewel het exporteren van sjablonen voor bestaande gegevensfabrieken momenteel nog niet wordt ondersteund, is het in de maak.
>
>

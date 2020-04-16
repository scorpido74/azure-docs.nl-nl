---
title: Gegevens transformeren met Databricks Notebook
description: Meer informatie over het verwerken of transformeren van gegevens door een Databricks-notitieblok uit te voeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
manager: shwang
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 6d3c9f0df0d834ffe75d0b56e3c80a432c27ea38
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81419014"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Gegevens transformeren door een Databricks-notitieblok uit te voeren
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Met de Azure Databricks-notitieblokactiviteit in een [datafabriekpijplijn](concepts-pipelines-activities.md) wordt een Databricks-notitieblok uitgevoerd in uw Azure Databricks-werkruimte. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Gegevensbricks-activiteitsdefinitie

Hier is de voorbeeld-JSON-definitie van een Databricks-notitieblokactiviteit:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Activiteitseigenschappen Databricks Notebook

In de volgende tabel worden de JSON-eigenschappen beschreven die worden gebruikt in de JSON-definitie:

|Eigenschap|Beschrijving|Vereist|
|---|---|---|
|name|Naam van de activiteit in de pijplijn.|Ja|
|description|Tekst waarin wordt beschreven wat de activiteit doet.|Nee|
|type|Voor Gegevensbricks-notitieblokactiviteit is het activiteitstype DatabricksNotebook.|Ja|
|linkedServiceName|Naam van de Databricks Linked Service waarop het Databricks-notitieblok wordt uitgevoerd. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service.|Ja|
|notitieblokPad|Het absolute pad van het notitieblok dat moet worden uitgevoerd in de Databricks Workspace. Dit pad moet beginnen met een slash.|Ja|
|basisparameters|Een array van sleutelwaardeparen. Basisparameters kunnen worden gebruikt voor elke activiteitsrun. Als het notitieblok een parameter neemt die niet is opgegeven, wordt de standaardwaarde van het notitieblok gebruikt. Meer informatie over parameters in [Databricks Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Nee|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd en die de taak uitvoeren. Het kan een \<array van string, object>.|Nee|


## <a name="supported-libraries-for-databricks-activities"></a>Ondersteunde bibliotheken voor Databricks-activiteiten

In de bovenstaande Databricks activiteitsdefinitie geeft u deze bibliotheektypen op: *pot,* *ei,* *whl,* *maven,* *pypi*, *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
    {
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Zie de documentatie [Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) voor bibliotheektypen voor meer informatie.

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Parameters doorgeven tussen notitieblokken en gegevensfabriek

U parameters van de gegevensfabriek doorgeven aan notitieblokken met de eigenschap *baseParameters* in databricks-activiteit. 

In bepaalde gevallen moet u mogelijk bepaalde waarden van notebook terugnaar gegevensfabriek, die kunnen worden gebruikt voor controlestroom (voorwaardelijke controles) in de gegevensfabriek of worden verbruikt door downstream-activiteiten (groottelimiet is 2MB). 

1. In uw notitieblok u [dbutils.notebook.exit('returnValue')](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-workflows.html#notebook-workflows-exit) bellen en wordt de bijbehorende 'returnValue' teruggestuurd naar de gegevensfabriek.

2. U de uitvoer in de gegevensfabriek gebruiken met expressie zoals `'@activity('databricks notebook activity name').output.runOutput'`. 

   > [!IMPORTANT]
   > Als u het JSON-object passeert, u waarden ophalen door eigenschapsnamen toe te schrijven. Voorbeeld: `'@activity('databricks notebook activity name').output.runOutput.PropertyName'`

## <a name="how-to-upload-a-library-in-databricks"></a>Een bibliotheek uploaden in Databricks

#### <a name="using-databricks-workspace-ui"></a>[Gebruikersinterface van databricks-werkruimte gebruiken](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Als u het dbfs-pad van de bibliotheek wilt verkrijgen dat is toegevoegd met behulp van ui, u de [Databricks CLI (installatie)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)gebruiken. 

Doorgaans worden de Jar-bibliotheken opgeslagen onder dbfs:/FileStore/jars tijdens het gebruik van de gebruikersinterface. U een lijst maken van alle via de CLI: *databricks fs ls dbfs:/FileStore/jars*.



#### <a name="copy-library-using-databricks-cli"></a>[Bibliotheek kopiëren met Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Voorbeeld: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*

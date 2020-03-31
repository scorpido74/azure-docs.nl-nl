---
title: Gegevens transformeren met Databricks Python
description: Meer informatie over het verwerken of transformeren van gegevens door een Databricks Python uit te voeren.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/15/2018
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: anandsub
ms.openlocfilehash: be2e389a0f103983a566a3f74d201e5589d84586
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74926725"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Gegevens transformeren door een Python-activiteit uit te voeren in Azure Databricks

Met de Azure Databricks Python-activiteit in een [datafabriekwordt](concepts-pipelines-activities.md) een Python-bestand uitgevoerd in uw Azure Databricks-cluster. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

Bekijk de volgende video voor een inleiding en demonstratie van deze functie van 11 minuten:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks Python-activiteitsdefinitie

Hier is de voorbeeld JSON-definitie van een Databricks Python-activiteit:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Activiteitseigenschappen Van Databricks Python

In de volgende tabel worden de JSON-eigenschappen beschreven die worden gebruikt in de JSON-definitie:

|Eigenschap|Beschrijving|Vereist|
|---|---|---|
|name|Naam van de activiteit in de pijplijn.|Ja|
|description|Tekst waarin wordt beschreven wat de activiteit doet.|Nee|
|type|Voor Databricks Python Activity is het activiteitstype DatabricksSparkPython.|Ja|
|linkedServiceName|Naam van de Databricks Linked Service waarop de Python-activiteit wordt uitgevoerd. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service.|Ja|
|pythonFile|De URI van het Python-bestand dat moet worden uitgevoerd. Alleen DBFS-paden worden ondersteund.|Ja|
|parameters|Opdrachtregelparameters die worden doorgegeven aan het Python-bestand. Dit is een scala aan snaren.|Nee|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd en die de taak uitvoeren. Het kan een array van <string zijn, object>|Nee|

## <a name="supported-libraries-for-databricks-activities"></a>Ondersteunde bibliotheken voor databricks-activiteiten

In de bovenstaande Databricks activiteitsdefinitie geeft u deze bibliotheektypen op: *pot,* *ei,* *maven,* *pypi*, *cran*.

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

Voor meer details verwijs [Gegevensbricks documentatie](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) voor bibliotheektypes.

## <a name="how-to-upload-a-library-in-databricks"></a>Een bibliotheek uploaden in Databricks

#### <a name="using-databricks-workspace-ui"></a>[Gebruikersinterface van databricks-werkruimte gebruiken](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Als u het dbfs-pad van de bibliotheek wilt verkrijgen dat is toegevoegd met behulp van ui, u [Databricks CLI (installatie)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli)gebruiken. 

Meestal worden de Jar-bibliotheken opgeslagen onder dbfs:/FileStore/jars tijdens het gebruik van de gebruikersinterface. U een lijst maken van de CLI: *databricks fs ls dbfs:/FileStore/jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Bibliotheek kopiëren met Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)

Voorbeeld: *databricks fs cp SparkPi-assembly-0.1.jar dbfs:/FileStore/jars*
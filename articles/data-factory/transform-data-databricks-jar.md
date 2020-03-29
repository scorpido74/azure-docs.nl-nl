---
title: Gegevens transformeren met Databricks Jar
description: Meer informatie over het verwerken of transformeren van gegevens door een Databricks Jar uit te voeren.
services: data-factory
documentationcenter: ''
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
manager: shwang
ms.date: 03/15/2018
ms.openlocfilehash: 20858069b745beeaf64951c4ef23c2eb85251985
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74929112"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Gegevens transformeren door een Jar-activiteit uit te voeren in Azure Databricks

Met de Azure Databricks Jar-activiteit in een [datafabriekwordt](concepts-pipelines-activities.md) een Spark Jar uitgevoerd in uw Azure Databricks-cluster. Dit artikel bouwt voort op het artikel [over gegevenstransformatieactiviteiten,](transform-data.md) dat een algemeen overzicht geeft van gegevenstransformatie en de ondersteunde transformatieactiviteiten.Azure Databricks is een beheerd platform voor het uitvoeren van Apache Spark.

Bekijk de volgende video voor een inleiding en demonstratie van deze functie van 11 minuten:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Databricks Jar activiteit definitie

Hier is de voorbeeld JSON definitie van een Databricks Jar Activiteit:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Activiteitseigenschappen databricks jar

In de volgende tabel worden de JSON-eigenschappen beschreven die worden gebruikt in de JSON-definitie:

|Eigenschap|Beschrijving|Vereist|
|:--|---|:-:|
|name|Naam van de activiteit in de pijplijn.|Ja|
|description|Tekst waarin wordt beschreven wat de activiteit doet.|Nee|
|type|Voor Databricks Jar Activity is het activiteitstype DatabricksSparkJar.|Ja|
|linkedServiceName|Naam van de Databricks Linked Service waarop de Jar-activiteit wordt uitgevoerd. Zie Artikel [Gekoppelde services](compute-linked-services.md) berekenen voor meer informatie over deze gekoppelde service.|Ja|
|mainClassName|De volledige naam van de klasse met de belangrijkste uit te voeren methode. Deze klasse moet worden opgenomen in een JAR die als bibliotheek wordt verstrekt.|Ja|
|parameters|Parameters die worden doorgegeven aan de hoofdmethode.  Dit is een scala aan snaren.|Nee|
|bibliotheken|Een lijst met bibliotheken die op het cluster moeten worden geïnstalleerd en die de taak uitvoeren. Het kan een array van <string zijn, object>|Ja (ten minste één met de methode mainClassName)|

> [!NOTE]
> **Bekend probleem** - Bij het gebruik van hetzelfde [interactieve cluster](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) voor het uitvoeren van gelijktijdige Databricks Jar-activiteiten (zonder clusteropnieuw opstarten), is er een bekend probleem in Databricks waar in parameters van de 1e activiteit ook door volgende activiteiten worden gebruikt. Vandaar dat onjuiste parameters worden doorgegeven aan de volgende taken. Gebruik in plaats daarvan een [taakcluster](compute-linked-services.md#example---using-new-job-cluster-in-databricks) om dit te beperken. 

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

Meestal worden de Jar-bibliotheken opgeslagen onder dbfs:/FileStore/jars tijdens het gebruik van de gebruikersinterface. U alle lijst via de CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-cli"></a>[Bibliotheek kopiëren met Databricks CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Gebruik Databricks CLI [(installatiestappen)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Voorbeeld - kopiëren JAR naar dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*

---
title: Gegevensopname met Azure Data Factory
titleSuffix: Azure Machine Learning
description: Meer informatie over het bouwen van een pijp lijn voor gegevens opname met Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ad04566699b2eebb0cbd7a9f242de38bc75e2015
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986405"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Gegevensopname met Azure Data Factory

In dit artikel leert u hoe u een pijp lijn voor gegevens opname bouwt met Azure Data Factory (ADF). Deze pijp lijn wordt gebruikt om gegevens op te nemen voor gebruik met Azure Machine Learning. Met Azure Data Factory kunt u gemakkelijk ETL-gegevens ophalen, transformeren en laden. Zodra de gegevens zijn getransformeerd en in opslag zijn geladen, kunt u deze gebruiken om uw machine learning modellen te trainen.

Eenvoudige gegevens transformatie kan worden afgehandeld met systeem eigen ADF-activiteiten en-instrumenten zoals de [gegevens stroom](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity). Wanneer het gaat om complexere scenario's, kunnen de gegevens worden verwerkt met een aantal aangepaste code. Bijvoorbeeld python of R-code.

Er zijn verschillende algemene technieken van het gebruik van Azure Data Factory voor het transformeren van gegevens tijdens opname. Elke techniek heeft voor-en nadelen die bepalen of het geschikt is voor een specifieke use-case:

| Techniek | Voordelen | Nadelen |
| ----- | ----- | ----- |
| ADF + Azure Functions | Lage latentie, serverloze compute</br>Stateful functies</br>Herbruikbare functies | Alleen geschikt voor korte actieve verwerking |
| ADF + aangepast onderdeel | Grootschalige parallelle computing</br>Geschikt voor zware algoritmen | Code terugloop in een uitvoerbaar bestand</br>Complexiteit van de afhandelings afhankelijkheden en IO |
| ADF + Azure Databricks notebook | Apache Spark</br>Systeem eigen python-omgeving | Kan duur zijn</br>Het maken van clusters wordt in eerste instantie tijd en latentie toegevoegd

## <a name="adf-with-azure-functions"></a>ADF met Azure functions

![Diagram toont een Azure Data Factory pijp lijn, met Azure function en run M L-pijp lijn, en een Azure Machine Learning pijp lijn, met Train model, en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens.](media/how-to-data-ingest-adf/adf-function.png)

Met Azure Functions kunt u kleine stukjes code (functies) uitvoeren zonder dat u zich zorgen hoeft te maken over de infra structuur van de toepassing. In deze optie worden de gegevens verwerkt met aangepaste python-code die is ingepakt in een Azure-functie. 

De functie wordt aangeroepen met de [activiteit ADF Azure function](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Deze methode is een goede optie voor licht gewicht gegevens transformaties. 

* -Professionals
    * De gegevens worden verwerkt op een serverloze Compute met een relatief lage latentie
    * ADF-pijp lijn kan een [duurzame Azure-functie](/azure/azure-functions/durable/durable-functions-overview) aanroepen waarmee een geavanceerde gegevens transformatie stroom kan worden geïmplementeerd 
    * De details van de gegevens transformatie worden abstract gemaakt door de Azure-functie die opnieuw kan worden gebruikt en vanaf andere locaties kan worden aangeroepen.
* Nadelen
    * De Azure Functions moet worden gemaakt voor gebruik met ADF
    * Azure Functions is alleen geschikt voor het verwerken van langlopende gegevens

## <a name="adf-with-custom-component-activity"></a>ADF met aangepaste onderdeel activiteit

![In het diagram ziet u een Azure Data Factory pijp lijn, met een aangepast onderdeel en een pijp lijn met M L, en een Azure Machine Learning pijp lijn, met Train model, en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens.](media/how-to-data-ingest-adf/adf-customcomponent.png)

In deze optie worden de gegevens verwerkt met aangepaste python-code die is ingepakt in een uitvoerbaar bestand. Deze wordt aangeroepen met een [aangepaste ADF-onderdeel activiteit](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Deze aanpak is beter geschikt voor grote gegevens dan de vorige techniek.

* -Professionals
    * De gegevens worden verwerkt op [Azure batch](https://docs.microsoft.com/azure/batch/batch-technical-overview) pool, waardoor grootschalige parallelle en High-Performance Computing worden geboden
    * Kan worden gebruikt om zware algoritmen uit te voeren en grote hoeveel heden gegevens te verwerken
* Nadelen
    * Azure Batch groep moet worden gemaakt voor gebruik met ADF
    * Over technische technici met betrekking tot het inpakken van python-code naar een uitvoerbaar bestand Complexiteit van het verwerken van afhankelijkheden en para meters voor invoer/uitvoer

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF met Azure Databricks python-notebook

![Diagram toont een Azure Data Factory pijp lijn, met Azure Databricks python, een pijp lijn met M L en een Azure Machine Learning pijp lijn, met Train model, en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens.](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) is een op Apache Spark gebaseerd analyse platform in de micro soft Cloud.

In deze techniek wordt de gegevens transformatie uitgevoerd door een [python-notebook](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook), dat wordt uitgevoerd op een Azure Databricks cluster. Dit is waarschijnlijk de meest voorkomende benadering die gebruikmaakt van de volledige kracht van een Azure Databricks service. Het is ontworpen voor de verwerking van gedistribueerde gegevens op schaal.

* -Professionals
    * De gegevens worden getransformeerd op de krach tigste Azure-service voor gegevens verwerking, waarvan een back-up wordt gemaakt door Apache Spark omgeving
    * Systeem eigen ondersteuning van python samen met data Science frameworks en Bibliotheken, waaronder tensor flow, PyTorch en scikit-Learn
    * Het is niet nodig om de python-code op te slaan in functions of uitvoer bare modules. De code werkt als volgt.
* Nadelen
    * Azure Databricks-infra structuur moet worden gemaakt voor gebruik met ADF
    * Kan duur zijn afhankelijk van Azure Databricks configuratie
    * Het draaien van reken clusters van de modus koud duurt enige tijd die hoge latentie voor de oplossing biedt 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Gegevens in Azure Machine Learning pijp lijnen gebruiken

![Diagram toont een Azure Data Factory pijp lijn en een Azure Machine Learning pijp lijn en hoe ze communiceren met onbewerkte gegevens en voor bereide gegevens. Met de Data Factory pijp lijn worden gegevens naar de voor bereide data base gevoed, waarmee een gegevens archief wordt gevoed, wat gegevens sets in de werk ruimte Machine Learning.](media/how-to-data-ingest-adf/aml-dataset.png)

De getransformeerde gegevens van de ADF-pijp lijn worden opgeslagen in de gegevens opslag (zoals Azure Blob). Azure Machine Learning heeft toegang tot deze gegevens via [data stores](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) en [gegevens sets](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets).

Telkens wanneer de ADF-pijp lijn wordt uitgevoerd, worden de gegevens opgeslagen op een andere locatie in de opslag. De ADF-pijp lijn roept een Azure Machine Learning pijp lijn aan om de locatie door te geven aan Azure Machine Learning. Bij het aanroepen van de ML pijp lijn worden de gegevens locatie en run-ID verzonden als para meters. Met de ML-pijp lijn kan vervolgens een gegevens opslag/gegevensset worden gemaakt met behulp van de locatie van de informatie. 

> [!TIP]
> Gegevens sets [ondersteunen versie beheer](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets), zodat de ml-pijp lijn een nieuwe versie van de gegevensset kan registreren die verwijst naar de meest recente gegevens van de ADF-pijp lijn.

Zodra de gegevens toegankelijk zijn via een gegevens opslag of gegevensset, kunt u deze gebruiken om een ML-model te trainen. Het trainings proces kan deel uitmaken van dezelfde ML-pijp lijn die wordt aangeroepen vanuit ADF. Het is ook mogelijk dat het een afzonderlijk proces is, zoals experimenten in een Jupyter-notebook.

Omdat gegevens sets ondersteuning bieden voor versie beheer en elke uitvoering van de pijp lijn een nieuwe versie maakt, is het eenvoudig om te begrijpen welke versie van de gegevens is gebruikt voor het trainen van een model.

## <a name="next-steps"></a>Volgende stappen

* [Een Databricks-notebook uitvoeren in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Toegang tot gegevens in azure Storage-services](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Modellen trainen met gegevens sets in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps voor een pijp lijn voor gegevens opname](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)


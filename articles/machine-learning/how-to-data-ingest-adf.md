---
title: Gegevensopname met Azure Data Factory
titleSuffix: Azure Machine Learning
description: Meer informatie over het maken van een pijplijn voor het innemen van gegevens met Azure Data Factory.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 03/01/2020
ms.openlocfilehash: 5b4ed40048aab815397c9726098880b2125b732c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78274786"
---
# <a name="data-ingestion-with-azure-data-factory"></a>Gegevensopname met Azure Data Factory

In dit artikel leert u hoe u een pijplijn voor gegevensopname maken met Azure Data Factory (ADF). Deze pijplijn wordt gebruikt om gegevens in te nemen voor gebruik met Azure Machine Learning. Met Azure Data Factory u eenvoudig ETL-gegevens extraheren, transformeren en laden. Zodra de gegevens zijn getransformeerd en geladen in opslag, kan het worden gebruikt om uw machine learning-modellen te trainen.

Eenvoudige gegevenstransformatie kan worden afgehandeld met native ADF-activiteiten en -instrumenten, zoals [gegevensstroom.](https://docs.microsoft.com/azure/data-factory/control-flow-execute-data-flow-activity) Als het gaat om meer gecompliceerde scenario's, kunnen de gegevens worden verwerkt met een aangepaste code. Bijvoorbeeld Python- of R-code.

Er zijn verschillende veelgebruikte technieken om Azure Data Factory te gebruiken om gegevens te transformeren tijdens inname. Elke techniek heeft voor- en nadelen die bepalen of het een goede pasvorm is voor een specifieke use case:

| Techniek | Voordelen | Nadelen |
| ----- | ----- | ----- |
| ADF + Azure-functies | Lage latentie, serverloze rekenkracht</br>Stateful functies</br>Herbruikbare functies | Alleen goed voor korte lopende verwerking |
| ADF + aangepaste component | Grootschalige parallel computing</br>Geschikt voor zware algoritmen | Metende code in een uitvoerbare</br>Complexiteit van het omgaan met afhankelijkheden en IO |
| ADF + Azure Databricks-notitieblok | Apache Spark</br>Native Python-omgeving | Kan duur zijn</br>Clusters maken kost in eerste instantie tijd en voegt latentie toe

## <a name="adf-with-azure-functions"></a>ADF met Azure-functies

![adf-functie](media/how-to-data-ingest-adf/adf-function.png)

Met Azure Functions u kleine stukjes code (functies) uitvoeren zonder u zorgen te maken over de toepassingsinfrastructuur. In deze optie worden de gegevens verwerkt met aangepaste Python-code verpakt in een Azure-functie. 

De functie wordt aangeroepen met de [ADF Azure-functieactiviteit](https://docs.microsoft.com/azure/data-factory/control-flow-azure-function-activity). Deze aanpak is een goede optie voor lichtgewicht gegevenstransformaties. 

* Pros:
    * De gegevens worden verwerkt op een serverloze compute met een relatief lage latentie
    * ADF-pijplijn kan een [duurzame Azure-functie](/azure/azure-functions/durable/durable-functions-overview) aanroepen die een geavanceerde gegevenstransformatiestroom kan implementeren 
    * De details van de gegevenstransformatie worden verwijderd door de Azure-functie die kan worden hergebruikt en aangeroepen vanuit andere plaatsen
* Tegens:
    * De Azure-functies moeten worden gemaakt voordat u met ADF wordt gebruikt
    * Azure Functions is alleen goed voor kortlopende gegevensverwerking

## <a name="adf-with-custom-component-activity"></a>ADF met aangepaste componentactiviteit

![adf-customcomponent](media/how-to-data-ingest-adf/adf-customcomponent.png)

In deze optie worden de gegevens verwerkt met aangepaste Python-code verpakt in een uitvoerbare code. Het wordt aangeroepen met een [ADF Custom Component-activiteit](https://docs.microsoft.com/azure/data-factory/transform-data-using-dotnet-custom-activity). Deze aanpak past beter bij grote data dan de vorige techniek.

* Pros:
    * De gegevens worden verwerkt op [Azure Batch-groep,](https://docs.microsoft.com/azure/batch/batch-technical-overview) die grootschalige parallelle en krachtige computing biedt
    * Kan worden gebruikt om zware algoritmen uit te voeren en aanzienlijke hoeveelheden gegevens te verwerken
* Tegens:
    * Azure Batch-groep moet worden gemaakt voordat u met ADF wordt gebruikt
    * Over engineering met betrekking tot het verpakken van Python-code in een uitvoerbare. Complexiteit van de verwerking van afhankelijkheden en invoer-/uitvoerparameters

## <a name="adf-with-azure-databricks-python-notebook"></a>ADF met Azure Databricks Python-notitieblok

![adf-databricks](media/how-to-data-ingest-adf/adf-databricks.png)

[Azure Databricks](https://azure.microsoft.com/services/databricks/) is een op Apache Spark gebaseerd analyseplatform in de Microsoft-cloud.

In deze techniek wordt de gegevenstransformatie uitgevoerd door een [Python-notitieblok,](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)uitgevoerd op een Azure Databricks-cluster. Dit is waarschijnlijk de meest voorkomende aanpak die gebruikmaakt van de volledige kracht van een Azure Databricks-service. Het is ontworpen voor gedistribueerde gegevensverwerking op schaal.

* Pros:
    * De gegevens worden getransformeerd op de krachtigste Azure-service voor gegevensverwerking, waarvan wordt ondersteund door de Apache Spark-omgeving
    * Native ondersteuning van Python, samen met data science frameworks en bibliotheken, waaronder TensorFlow, PyTorch en scikit-learn
    * Het is niet nodig om de Python-code in te wikkelen in functies of uitvoerbare modules. De code werkt zoals het is.
* Tegens:
    * Azure Databricks-infrastructuur moet worden gemaakt voordat u met ADF wordt gebruikt
    * Kan duur zijn, afhankelijk van de configuratie van Azure Databricks
    * Het upupen van compute clusters vanuit de "koude" modus neemt enige tijd in beslag dat hoge latentie naar de oplossing brengt 
    

## <a name="consuming-data-in-azure-machine-learning-pipelines"></a>Gegevens verbruiken in Azure Machine Learning-pijplijnen

![aml-dataset](media/how-to-data-ingest-adf/aml-dataset.png)

De getransformeerde gegevens uit de ADF-pijplijn worden opgeslagen in gegevensopslag (zoals Azure Blob). Azure Machine Learning heeft toegang tot deze gegevens via [datastores](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores) en [gegevenssets.](https://docs.microsoft.com/azure/machine-learning/how-to-create-register-datasets)

Telkens wanneer de ADF-pijplijn wordt uitgevoerd, worden de gegevens opgeslagen op een andere locatie in de opslag. Als u de locatie wilt doorgeven aan Azure Machine Learning, wordt in de ADF-pijplijn een Azure Machine Learning-pijplijn aangestoofd. Wanneer u de ML-pijplijn aanroept, worden de gegevenslocatie en de run-id als parameters verzonden. De ML-pijplijn kan vervolgens een gegevensarchief/gegevensset maken met behulp van de gegevenslocatie. 

> [!TIP]
> Gegevenssets [ondersteunen versiebeheer,](https://docs.microsoft.com/azure/machine-learning/how-to-version-track-datasets)zodat de ML-pijplijn een nieuwe versie van de gegevensset kan registreren die verwijst naar de meest recente gegevens uit de ADF-pijplijn.

Zodra de gegevens toegankelijk zijn via een datastore of gegevensset, u deze gebruiken om een ML-model te trainen. Het trainingsproces maakt mogelijk deel uit van dezelfde ML-pijplijn die wordt aangeroepen vanuit ADF. Of het kan een apart proces zijn, zoals experimenteren in een Jupyter-notitieblok.

Aangezien gegevenssets versiebeheer ondersteunen en elke run vanaf de pijplijn een nieuwe versie maakt, is het gemakkelijk te begrijpen welke versie van de gegevens is gebruikt om een model te trainen.

## <a name="next-steps"></a>Volgende stappen

* [Een Databricks-notitieblok uitvoeren in Azure Data Factory](https://docs.microsoft.com/azure/data-factory/transform-data-using-databricks-notebook)
* [Toegang tot gegevens in Azure-opslagservices](https://docs.microsoft.com/azure/machine-learning/how-to-access-data#create-and-register-datastores)
* [Modellen trainen met gegevenssets in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-train-with-datasets)
* [DevOps voor een pijplijn voor gegevensopname](https://docs.microsoft.com/azure/machine-learning/how-to-cicd-data-ingestion)


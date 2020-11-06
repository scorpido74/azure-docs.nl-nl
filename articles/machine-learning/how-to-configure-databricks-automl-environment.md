---
title: Ontwikkelen met autoML & Azure Databricks
titleSuffix: Azure Machine Learning
description: Meer informatie over het instellen van een ontwikkel omgeving in Azure Machine Learning en Azure Databricks. Gebruik de Azure ML-Sdk's voor Databricks en Databricks met autoML.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.date: 10/21/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: ef8ee7718aabb443fda6cd7b276ee53472261913
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424352"
---
# <a name="set-up-a-development-environment-with-azure-databricks-and-automl-in-azure-machine-learning"></a>Stel een ontwikkel omgeving in met Azure Databricks en autoML in Azure Machine Learning 

Meer informatie over het configureren van een ontwikkel omgeving in Azure Machine Learning die gebruikmaakt van Azure Databricks en geautomatiseerd ML.

Azure Databricks is ideaal voor het uitvoeren van grootschalige intensieve machine learning werk stromen op het schaal bare Apache Spark platform in de Azure-Cloud. Het biedt een samenwerkings omgeving op basis van een laptop met een CPU of een reken cluster op basis van GPU.

Zie [een python-ontwikkel omgeving instellen](how-to-configure-environment.md)voor meer informatie over andere machine learning ontwikkel omgevingen.


## <a name="prerequisite"></a>Vereiste

Azure Machine Learning werk ruimte. Als u er geen hebt, kunt u een Azure Machine Learning-werk ruimte maken met behulp van de [Azure Portal](how-to-manage-workspace.md)-, [Azure CLI](how-to-manage-workspace-cli.md#create-a-workspace)-en [Azure Resource Manager-sjablonen](how-to-create-workspace-template.md).


## <a name="azure-databricks-with-azure-machine-learning-and-automl"></a>Azure Databricks met Azure Machine Learning en autoML

Azure Databricks integreert met Azure Machine Learning en de autoML-mogelijkheden. 

U kunt Azure Databricks gebruiken:

+ Een model trainen met Spark MLlib en het model implementeren in ACI/AKS.
+ Met [geautomatiseerde machine learning](concept-automated-ml.md) mogelijkheden met behulp van een Azure ml-SDK.
+ Als reken doel van een [Azure machine learning-pijp lijn](concept-ml-pipelines.md).

## <a name="set-up-a-databricks-cluster"></a>Een Databricks-cluster instellen

Maak een [Databricks-cluster](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal). Sommige instellingen zijn alleen van toepassing als u de SDK installeert voor automatische machine learning op Databricks.

**Het duurt enkele minuten om het cluster te maken.**

Gebruik deze instellingen:

| Instelling |Van toepassing op| Waarde |
|----|---|---|
| Clusternaam |altijd| yourclustername |
| Databricks Runtime versie |altijd| Runtime 7,1 (scala 2,21, Spark 3.0.0)-niet ML|
| Python-versie |altijd| 3 |
| Type werk nemer <br>(bepaalt het maximum aantal gelijktijdige iteraties) |Geautomatiseerde machine learning<br>alleen| Voorkeurs-VM geoptimaliseerd voor geheugen |
| IT |altijd| 2 of hoger |
| Automatisch schalen inschakelen |Geautomatiseerde machine learning<br>alleen| Uitschakelen |

Wacht totdat het cluster wordt uitgevoerd voordat u doorgaat.

## <a name="add-the-azure-ml-sdk-to-databricks"></a>De Azure ML SDK toevoegen aan Databricks

Zodra het cluster wordt uitgevoerd, [maakt u een bibliotheek](https://docs.databricks.com/user-guide/libraries.html#create-a-library) om het juiste Azure machine learning SDK-pakket aan uw cluster toe te voegen. 

Als u automatische ML wilt gebruiken, gaat u verder met [het toevoegen van de Azure ml SDK met AutoML](#add-the-azure-ml-sdk-with-automl-to-databricks).


1. Klik met de rechter muisknop op de huidige werkruimte map waar u de bibliotheek wilt opslaan. Selecteer **Create**  >  **bibliotheek** maken.
    
    > [!TIP]
    > Als u een oude SDK-versie hebt, schakelt u deze uit in de geïnstalleerde bibliotheken van het cluster en gaat u naar de Prullenbak. Installeer de nieuwe SDK-versie en start het cluster opnieuw op. Als er een probleem is nadat de computer opnieuw is opgestart, ontkoppelt u het cluster en koppelt u het opnieuw.

1. Kies de volgende optie (er worden geen andere SDK-installaties ondersteund)

   |SDK- &nbsp; pakket &nbsp; extra's|Bron|PyPi- &nbsp; naam&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Voor Databricks| Python-ei of PyPI uploaden | azureml-SDK [databricks]|

   > [!WARNING]
   > Er kunnen geen andere SDK-extra's worden geïnstalleerd. Kies alleen de `databricks` optie [].

   * Selecteer niet **automatisch koppelen aan alle clusters**.
   * Selecteer  **koppelen** naast de naam van uw cluster.

1. Controleer op fouten totdat de status is gewijzigd in **bijgevoegd**. Dit kan enkele minuten duren.  Als deze stap mislukt:

   Probeer het cluster opnieuw te starten door:
   1. Selecteer **clusters** in het linkerdeel venster.
   1. Selecteer de naam van uw cluster in de tabel.
   1. Selecteer **opnieuw opstarten** op het tabblad **tape wisselaars** .

   Een geslaagde installatie ziet er ongeveer als volgt uit: 

  ![Azure Machine Learning SDK voor Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg) 

## <a name="add-the-azure-ml-sdk-with-automl-to-databricks"></a>Voeg de Azure ML SDK met AutoML toe aan Databricks
Als het cluster is gemaakt met Databricks Runtime 7,1 of hoger ( *niet* ml), voert u de volgende opdracht uit in de eerste cel van uw notitie blok om de AML-SDK te installeren.

```
%pip install --upgrade --force-reinstall -r https://aka.ms/automl_linux_requirements.txt
```
Voor Databricks Runtime 7,0 en lager installeert u de Azure Machine Learning SDK met het [init-script](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/automl/README.md).

### <a name="automl-config-settings"></a>AutoML-configuratie-instellingen

Bij het gebruik van Azure Databricks de volgende para meters toevoegen in AutoML config:

- ```max_concurrent_iterations``` is gebaseerd op het aantal worker-knoop punten in uw cluster.
- ```spark_context=sc``` is gebaseerd op de standaard Spark-context.

## <a name="ml-notebooks-that-work-with-azure-databricks"></a>ML-notebooks die samen werken met Azure Databricks

Uitproberen:
+ Er zijn veel voorbeeld notitieblokken beschikbaar, **maar alleen [deze voorbeeld notitieblokken](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) werken met Azure Databricks.**

+ Importeer deze voor beelden rechtstreeks vanuit uw werk ruimte. Zie hieronder: ![ Selecteer import ](./media/how-to-configure-environment/azure-db-screenshot.png)
 ![ paneel importeren](./media/how-to-configure-environment/azure-db-import.png)

+ Meer informatie over het [maken van een pijp lijn met Databricks als de trainings Compute](how-to-create-your-first-pipeline.md).

## <a name="next-steps"></a>Volgende stappen

- [Train een model](tutorial-train-models-with-aml.md) op Azure machine learning met de MNIST-gegevensset.
- Zie de [Naslag informatie voor de Azure machine learning SDK voor python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py).

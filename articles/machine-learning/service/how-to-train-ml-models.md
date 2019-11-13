---
title: Train ML-modellen met schattingen
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van een enkele en gedistribueerde training van traditionele machine learning en diepe leer modellen met behulp van Azure Machine Learning Estimator-klasse
ms.author: maxluk
author: maxluk
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: sgilley
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 9bb22a564f52dfcdb3fbec6d842e452ca416059f
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961702"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellen trainen met Azure Machine Learning met behulp van estimator
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met Azure Machine Learning kunt u eenvoudig uw trainings script naar [verschillende reken doelen](how-to-set-up-training-targets.md#compute-targets-for-training)verzenden met behulp van een [RunConfiguration-object](how-to-set-up-training-targets.md#whats-a-run-configuration) en een ScriptRunConfig- [object](how-to-set-up-training-targets.md#submit). Met dit patroon kunt u veel flexibiliteit en maximale controle bieden.

De Azure Machine Learning python SDK biedt een alternatieve abstractie van een hoger niveau, de Estimator-klasse, waarmee gebruikers eenvoudig uitvoer configuraties kunnen bouwen, om de training van een grondige leer model te vergemakkelijken. U kunt een algemene [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainings script te verzenden met behulp van een door u gekozen trainings raamwerk (zoals scikit-informatie) op elk reken doel dat u kiest, of het nu uw lokale computer, één virtuele machine in azure of een GPU-cluster in Azure is. Voor PyTorch-, tensor flow-en Chainer-taken biedt Azure Machine Learning ook de respectieve [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -schattingen om het gebruik van deze frameworks te vereenvoudigen.

## <a name="train-with-an-estimator"></a>Trainen met een estimator

Als u hebt gemaakt uw [werkruimte](concept-workspace.md) en stel uw [ontwikkelomgeving](how-to-configure-environment.md), trainen van een model in Azure Machine Learning omvat de volgende stappen:  
1. Een [extern Compute-doel](how-to-set-up-training-targets.md) maken (Opmerking: u kunt ook een lokale computer als reken doel gebruiken)
2. Uw [trainings gegevens](how-to-access-data.md) uploaden naar de Data Store (optioneel)
3. Maak uw [trainingsscript](tutorial-train-models-with-aml.md#create-a-training-script)
4. Maak een `Estimator` object
5. De Estimator naar een experiment object verzenden in de werk ruimte

In dit artikel richt zich op de stappen 4 en 5. Voor stappen 1-3, naar verwijzen de [zelfstudie: een model trainen](tutorial-train-models-with-aml.md) voor een voorbeeld.

### <a name="single-node-training"></a>Training voor één knooppunt

Gebruik een `Estimator` voor de training van een één knooppunt worden uitgevoerd op externe compute in Azure voor een scikit-model meer. U moet al hebt gemaakt uw [compute-doel](how-to-set-up-training-targets.md#amlcompute) object `compute_target` en uw [gegevensopslag](how-to-access-data.md) object `ds`.

```Python
from azureml.train.estimator import Estimator

script_params = {
    # to mount files referenced by mnist dataset
    '--data-folder': ds.as_named_input('mnist').as_mount(),
    '--regularization': 0.8
}

sk_est = Estimator(source_directory='./my-sklearn-proj',
                   script_params=script_params,
                   compute_target=compute_target,
                   entry_script='train.py',
                   conda_packages=['scikit-learn'])
```

Dit codefragment bevat de volgende parameters voor de `Estimator` constructor.

Parameter | Beschrijving
--|--
`source_directory`| Lokale map waarin alle uw code die nodig zijn voor de trainingstaak. Deze map wordt van uw lokale computer naar de externe Compute gekopieerd.
`script_params`| Dictionary waarmee de opdracht regel argumenten worden opgegeven die moeten worden door gegeven aan uw trainings script `entry_script`, in de vorm van `<command-line argument, value>`-paren. Als u een uitgebreide vlag in `script_params`wilt opgeven, gebruikt u `<command-line argument, "">`.
`compute_target`| Het externe Compute-doel waarvoor uw trainings script wordt uitgevoerd, in dit geval een[AmlCompute](how-to-set-up-training-targets.md#amlcompute)-cluster (Azure machine learning Compute). (Houd er rekening mee dat het AmlCompute-cluster het doel is dat het vaakst wordt gebruikt. het is ook mogelijk om andere berekenings doel typen te kiezen, zoals virtuele Azure-machines of zelfs lokale computers.)
`entry_script`| FilePath (relatief aan de `source_directory`) van het trainingsscript in de berekening die extern worden uitgevoerd. Dit bestand en eventuele extra bestanden waarvan het afhankelijk is, moeten zich in deze map bevinden.
`conda_packages`| Lijst met Python-pakketten worden geïnstalleerd via conda die nodig zijn voor uw trainingsscript.  

De constructor heeft een andere para meter met de naam `pip_packages` die u voor de benodigde PIP-pakketten gebruikt.

Nu dat u hebt uw `Estimator` object, verzenden van de trainingstaak om te worden uitgevoerd op de externe compute met een aanroep naar de `submit` functioneren in uw [Experiment](concept-azure-machine-learning-architecture.md#experiments) object `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciale mappen** twee mappen *levert* en *logboeken*, speciale behandeling door Azure Machine Learning. Tijdens de training, bij het schrijven van bestanden in mappen met de naam *levert* en *logboeken* die zijn ten opzichte van de hoofdmap (`./outputs` en `./logs`respectievelijk), de bestanden worden automatisch uploaden naar uw uitvoeringsgeschiedenis zodat u de toegang tot hebben als uw uitvoering is voltooid.
>
> Artefacten tijdens de training (zoals modelbestanden, controlepunten, gegevensbestanden of uitgezette afbeeldingen) schrijven om te maken aan de `./outputs` map.
>
> Op dezelfde manier kunt u de logboeken van uw trainings uitvoering naar de map `./logs` schrijven. Gebruikmaken van Azure Machine Learning [TensorBoard integratie](https://aka.ms/aml-notebook-tb) Zorg ervoor dat u uw logboeken TensorBoard schrijven naar deze map. Terwijl uw uitvoering uitgevoerd wordt, kunt u zich TensorBoard starten en deze logboeken streamen.  Later, ook mogelijk om terug te zetten van de logboeken van een van uw eerdere uitvoeringen.
>
> Bijvoorbeeld, voor het downloaden van een bestand geschreven naar de *levert* map op uw lokale computer na uw externe training uitvoeren: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Gedistribueerde trainings- en aangepaste Docker-installatiekopieën

Er zijn twee aanvullende scenario's u met uitvoeren kunt de `Estimator`:
* Met behulp van een aangepaste Docker-installatiekopie
* Gedistribueerde training op een cluster met meerdere knooppunten

De volgende code laat zien hoe u gedistribueerde training kunt uitvoeren voor een Keras-model. In plaats van de standaard-Azure Machine Learning installatie kopieën te gebruiken, wordt er bovendien een aangepaste docker-installatie kopie van docker hub opgegeven `continuumio/miniconda` voor training.

U moet al hebt gemaakt uw [compute-doel](how-to-set-up-training-targets.md#amlcompute) object `compute_target`. U maken als volgt de estimator:

```Python
from azureml.train.estimator import Estimator
from azureml.core.runconfig import MpiConfiguration

estimator = Estimator(source_directory='./my-keras-proj',
                      compute_target=compute_target,
                      entry_script='train.py',
                      node_count=2,
                      process_count_per_node=1,
                      distributed_training=MpiConfiguration(),        
                      conda_packages=['tensorflow', 'keras'],
                      custom_docker_image='continuumio/miniconda')
```

De bovenstaande code wordt aangegeven dat de volgende nieuwe parameters voor de `Estimator` constructor:

Parameter | Beschrijving | Standaard
--|--|--
`custom_docker_image`| Naam van de installatiekopie die u wilt gebruiken. Geef alleen installatiekopieën die beschikbaar zijn in de openbare docker-opslagplaatsen (in dit geval Docker Hub). Als u een afbeelding uit een privé-docker-opslag plaats wilt gebruiken, gebruikt u in plaats daarvan de para meter `environment_definition` van de constructor. [Zie voor beeld](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Het aantal knooppunten moet worden gebruikt voor de trainingstaak. | `1`
`process_count_per_node`| Het aantal processen (of 'werknemers') om uit te voeren op elk knooppunt. In dit geval gebruikt u de `2` GPU's die beschikbaar zijn op elk knooppunt.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) -object voor het starten van gedistribueerde training met MPI-back-end.  | `None`


Ten slotte verzendt u de trainingstaak:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="github-tracking-and-integration"></a>GitHub bijhouden en integreren

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

## <a name="examples"></a>Voorbeelden
Voor een notitie blok waarin de basis beginselen van een Estimator patroon worden weer gegeven, raadpleegt u:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Voor een notebook die een scikit-leer model traint met behulp van Estimator, zie:
* [zelfstudies/img-classificatie-deel 1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Voor notebooks op trainings modellen met behulp van diep gaande leren Framework-specifieke schattingen raadpleegt u:

* [procedures voor het gebruik van azureml/ml-Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Metrische gegevens over uitvoeren tijdens de training bijhouden](how-to-track-experiments.md)
* [PyTorch-modellen trainen](how-to-train-pytorch.md)
* [TensorFlow-modellen trainen](how-to-train-tensorflow.md)
* [Afstemmen van hyperparameters](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)

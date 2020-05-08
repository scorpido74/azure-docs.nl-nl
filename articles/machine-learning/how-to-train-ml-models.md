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
ms.date: 03/09/2020
ms.custom: seodec18
ms.openlocfilehash: 3c96ba3496f4542658878518207b2033342e33f5
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82628756"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellen trainen met Azure Machine Learning met behulp van estimator
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met Azure Machine Learning kunt u eenvoudig uw trainings script naar [verschillende reken doelen](how-to-set-up-training-targets.md#compute-targets-for-training)verzenden met behulp van een [RunConfiguration-object](how-to-set-up-training-targets.md#whats-a-run-configuration) en een ScriptRunConfig- [object](how-to-set-up-training-targets.md#submit). Met dit patroon kunt u veel flexibiliteit en maximale controle bieden.

De Azure Machine Learning python SDK biedt een alternatieve abstractie van een hoger niveau, de Estimator-klasse, waarmee gebruikers eenvoudig uitvoer configuraties kunnen bouwen, om de training van een grondige leer model te vergemakkelijken. U kunt een algemene [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainings script te verzenden met behulp van een door u gekozen trainings raamwerk (zoals scikit-informatie) op elk reken doel dat u kiest, of het nu uw lokale computer, één virtuele machine in azure of een GPU-cluster in Azure is. Voor PyTorch-, tensor flow-en Chainer-taken biedt Azure Machine Learning ook de respectieve [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -schattingen om het gebruik van deze frameworks te vereenvoudigen.

## <a name="train-with-an-estimator"></a>Train met een estimator

Nadat u uw [werk ruimte](concept-workspace.md) hebt gemaakt en uw [ontwikkel omgeving](how-to-configure-environment.md)hebt ingesteld, moet u de volgende stappen uitvoeren voor het trainen van een model in azure machine learning:  
1. Een [extern Compute-doel](how-to-set-up-training-targets.md) maken (Opmerking: u kunt ook een lokale computer als reken doel gebruiken)
2. Uw [trainings gegevens](how-to-access-data.md) uploaden naar de Data Store (optioneel)
3. Uw [trainings script](tutorial-train-models-with-aml.md#create-a-training-script) maken
4. Maak een `Estimator`-object
5. De Estimator naar een experiment object verzenden in de werk ruimte

Dit artikel richt zich op stap 4-5. Raadpleeg voor stap 1-3 de [zelf studie een model trainen](tutorial-train-models-with-aml.md) voor een voor beeld.

### <a name="single-node-training"></a>Training met één knoop punt

Gebruik een `Estimator` voor training met één knoop punt op externe Compute in azure voor een scikit-leer model. U moet al uw [berekenings doel](how-to-set-up-training-targets.md#amlcompute) object `compute_target` en uw [FileDataset](how-to-create-register-datasets.md) - `ds`object hebben gemaakt.

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

Dit code fragment bevat de volgende para meters `Estimator` voor de constructor.

Parameter | Beschrijving
--|--
`source_directory`| Lokale map met alle code die nodig is voor de trainings taak. Deze map wordt van uw lokale computer naar de externe Compute gekopieerd.
`script_params`| Dictionary waarmee de opdracht regel argumenten worden opgegeven die moeten worden door gegeven `entry_script`aan uw trainings script, `<command-line argument, value>` in de vorm van paren. Als u een uitgebreide vlag in `script_params`wilt opgeven, `<command-line argument, "">`gebruikt u.
`compute_target`| Het externe Compute-doel waarvoor uw trainings script wordt uitgevoerd, in dit geval een[AmlCompute](how-to-set-up-training-targets.md#amlcompute)-cluster (Azure machine learning Compute). (Houd er rekening mee dat het AmlCompute-cluster het doel is dat het vaakst wordt gebruikt. het is ook mogelijk om andere berekenings doel typen te kiezen, zoals virtuele Azure-machines of zelfs lokale computers.)
`entry_script`| Het bestandspad (ten opzichte van het `source_directory`) van het trainings script dat moet worden uitgevoerd op de externe compute. Dit bestand en eventuele extra bestanden waarvan het afhankelijk is, moeten zich in deze map bevinden.
`conda_packages`| Lijst met Python-pakketten die moeten worden geïnstalleerd via Conda die nodig zijn voor uw trainings script.  

De constructor heeft een andere para meter `pip_packages` met de naam die u gebruikt voor de benodigde PIP-pakketten.

Nu u `Estimator` uw object hebt gemaakt, moet u de trainings taak verzenden om te worden uitgevoerd op de externe computer met een aanroep `submit` van de functie op uw `experiment`object [experiment](concept-azure-machine-learning-architecture.md#experiments) . 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciale mappen** Twee mappen, *uitvoer* en *Logboeken*, een speciale behandeling ontvangen door Azure machine learning. Wanneer u tijdens de training bestanden schrijft naar mappen met de naam *outputs* en *Logboeken* die relatief zijn ten opzichte van de hoofdmap (`./outputs` en `./logs`respectievelijk), worden de bestanden automatisch geüpload naar de uitvoerings geschiedenis, zodat u er toegang tot hebt zodra de uitvoering is voltooid.
>
> Als u artefacten wilt maken tijdens de training (zoals model bestanden, controle punten, gegevens bestanden of geplote afbeeldingen), schrijft `./outputs` u deze naar de map.
>
> Op dezelfde manier kunt u de logboeken van uw trainings uitvoering naar `./logs` de map schrijven. Als u de TensorBoard- [integratie](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/export-run-history-to-tensorboard/export-run-history-to-tensorboard.ipynb) van Azure machine learning wilt gebruiken, moet u ervoor zorgen dat u uw TensorBoard-logboeken naar deze map schrijft. Tijdens het uitvoeren van de uitvoering kunt u TensorBoard starten en deze logboeken streamen.  Later kunt u de logboeken ook herstellen vanuit een van de vorige uitvoeringen.
>
> Als u bijvoorbeeld een bestand wilt downloaden naar de map *uitvoer* naar de lokale computer nadat de externe training is uitgevoerd:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Gedistribueerde training en aangepaste docker-installatie kopieën

Er zijn twee aanvullende trainings scenario's die u kunt uitvoeren met `Estimator`:
* Een aangepaste docker-installatie kopie gebruiken
* Gedistribueerde training op een cluster met meerdere knoop punten

De volgende code laat zien hoe u gedistribueerde training kunt uitvoeren voor een Keras-model. In plaats van de standaard-Azure Machine Learning installatie kopieën te gebruiken, wordt er bovendien een aangepaste docker-installatie kopie `continuumio/miniconda` van docker hub voor training opgegeven.

U moet uw [reken doel](how-to-set-up-training-targets.md#amlcompute) object `compute_target`al hebben gemaakt. U maakt de Estimator als volgt:

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

Met de bovenstaande code worden de volgende nieuwe para meters `Estimator` voor de constructor getoond:

Parameter | Beschrijving | Standaard
--|--|--
`custom_docker_image`| De naam van de installatie kopie die u wilt gebruiken. Bied alleen installatie kopieën beschikbaar in open bare docker-opslag plaatsen (in dit geval docker hub). Als u een afbeelding uit een privé-docker-opslag plaats wilt gebruiken, `environment_definition` gebruikt u in plaats daarvan de constructor-para meter. [Zie voor beeld](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Het aantal knoop punten dat voor uw trainings taak moet worden gebruikt. | `1`
`process_count_per_node`| Het aantal processen (of werk nemers) dat op elk knoop punt moet worden uitgevoerd. In dit geval gebruikt u de `2` gpu's die op elk knoop punt beschikbaar zijn.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) -object voor het starten van gedistribueerde training met MPI-back-end.  | `None`


Dien ten slotte de trainings taak in:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Een model registreren

Zodra u het model hebt getraind, kunt u het opslaan en registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

Als u de volgende code uitvoert, wordt het model geregistreerd in uw werk ruimte en wordt het beschikbaar gemaakt om te verwijzen op naam in externe Compute-contexten of implementatie scripts. Zie [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) de naslag documentatie voor meer informatie en aanvullende para meters.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub bijhouden en integreren

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

## <a name="examples"></a>Voorbeelden
Voor een notitie blok waarin de basis beginselen van een Estimator patroon worden weer gegeven, raadpleegt u:
* [procedures voor het gebruik van azureml/training-met-diep leren/How-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Voor een notebook die een scikit-leer model traint met behulp van Estimator, zie:
* [zelf studies/img-Classification-part1-training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/image-classification-mnist-data/img-classification-part1-training.ipynb)

Voor notebooks op trainings modellen met behulp van diep gaande leren Framework-specifieke schattingen raadpleegt u:

* [procedures voor het gebruik van azureml/ml-Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [PyTorch-modellen trainen](how-to-train-pytorch.md)
* [TensorFlow-modellen trainen](how-to-train-tensorflow.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)
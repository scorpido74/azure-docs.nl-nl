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
ms.openlocfilehash: 678af1855baf52efa727444236de8a1724a7d0b0
ms.sourcegitcommit: 72c2da0def8aa7ebe0691612a89bb70cd0c5a436
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "79078482"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellen trainen met Azure Machine Learning met behulp van estimator
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met Azure Machine Learning kunt u eenvoudig uw trainings script naar [verschillende reken doelen](how-to-set-up-training-targets.md#compute-targets-for-training)verzenden met behulp van een [RunConfiguration-object](how-to-set-up-training-targets.md#whats-a-run-configuration) en een ScriptRunConfig- [object](how-to-set-up-training-targets.md#submit). Met dit patroon kunt u veel flexibiliteit en maximale controle bieden.

De Azure Machine Learning python SDK biedt een alternatieve abstractie van een hoger niveau, de Estimator-klasse, waarmee gebruikers eenvoudig uitvoer configuraties kunnen bouwen, om de training van een grondige leer model te vergemakkelijken. U kunt een algemene [Estimator](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainings script te verzenden met behulp van een door u gekozen trainings raamwerk (zoals scikit-informatie) op elk reken doel dat u kiest, of het nu uw lokale computer, één virtuele machine in azure of een GPU-cluster in Azure is. Voor PyTorch-, tensor flow-en Chainer-taken biedt Azure Machine Learning ook de respectieve [PyTorch](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py), [tensor flow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) -schattingen om het gebruik van deze frameworks te vereenvoudigen.

## <a name="train-with-an-estimator"></a>Trainen met een estimator

Nadat u uw [werk ruimte](concept-workspace.md) hebt gemaakt en uw [ontwikkel omgeving](how-to-configure-environment.md)hebt ingesteld, moet u de volgende stappen uitvoeren voor het trainen van een model in azure machine learning:  
1. Een [extern Compute-doel](how-to-set-up-training-targets.md) maken (Opmerking: u kunt ook een lokale computer als reken doel gebruiken)
2. Uw [trainings gegevens](how-to-access-data.md) uploaden naar de Data Store (optioneel)
3. Uw [trainings script](tutorial-train-models-with-aml.md#create-a-training-script) maken
4. Een `Estimator`-object maken
5. De Estimator naar een experiment object verzenden in de werk ruimte

In dit artikel richt zich op de stappen 4 en 5. Raadpleeg voor stap 1-3 de [zelf studie een model trainen](tutorial-train-models-with-aml.md) voor een voor beeld.

### <a name="single-node-training"></a>Training voor één knooppunt

Gebruik een `Estimator` voor een training met één knoop punt die wordt uitgevoerd op externe Compute in azure voor een scikit-leer model. U moet uw [berekenings doel](how-to-set-up-training-targets.md#amlcompute) object al hebben gemaakt `compute_target` en uw [FileDataset](how-to-create-register-datasets.md) -object `ds`.

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

Dit code fragment bevat de volgende para meters voor de `Estimator`-constructor.

Parameter | Beschrijving
--|--
`source_directory`| Lokale map waarin alle uw code die nodig zijn voor de trainingstaak. Deze map wordt van uw lokale computer naar de externe Compute gekopieerd.
`script_params`| Dictionary waarmee de opdracht regel argumenten worden opgegeven die moeten worden door gegeven aan uw trainings script `entry_script`, in de vorm van `<command-line argument, value>`-paren. Als u een uitgebreide vlag in `script_params`wilt opgeven, gebruikt u `<command-line argument, "">`.
`compute_target`| Het externe Compute-doel waarvoor uw trainings script wordt uitgevoerd, in dit geval een[AmlCompute](how-to-set-up-training-targets.md#amlcompute)-cluster (Azure machine learning Compute). (Houd er rekening mee dat het AmlCompute-cluster het doel is dat het vaakst wordt gebruikt. het is ook mogelijk om andere berekenings doel typen te kiezen, zoals virtuele Azure-machines of zelfs lokale computers.)
`entry_script`| Filepath (ten opzichte van de `source_directory`) van het trainings script dat moet worden uitgevoerd op de externe compute. Dit bestand en eventuele extra bestanden waarvan het afhankelijk is, moeten zich in deze map bevinden.
`conda_packages`| Lijst met Python-pakketten worden geïnstalleerd via conda die nodig zijn voor uw trainingsscript.  

De constructor heeft een andere para meter met de naam `pip_packages` die u voor de benodigde PIP-pakketten gebruikt.

Nu u uw `Estimator`-object hebt gemaakt, moet u de trainings taak verzenden om te worden uitgevoerd op de externe computer met een aanroep van de functie `submit` op uw object [Experiment](concept-azure-machine-learning-architecture.md#experiments) `experiment`. 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciale mappen** Twee mappen, *uitvoer* en *Logboeken*, een speciale behandeling ontvangen door Azure machine learning. Wanneer u tijdens de training bestanden schrijft naar mappen met de naam *outputs* en *Logboeken* die zijn gerelateerd aan de hoofdmap (respectievelijk`./outputs` en `./logs`), worden de bestanden automatisch geüpload naar de uitvoerings geschiedenis, zodat u er toegang tot hebt zodra de uitvoering is voltooid.
>
> Als u artefacten wilt maken tijdens de training (zoals model bestanden, controle punten, gegevens bestanden of geplote afbeeldingen), schrijft u deze naar de map `./outputs`.
>
> Op dezelfde manier kunt u de logboeken van uw trainings uitvoering naar de map `./logs` schrijven. Als u de TensorBoard- [integratie](https://aka.ms/aml-notebook-tb) van Azure machine learning wilt gebruiken, moet u ervoor zorgen dat u uw TensorBoard-logboeken naar deze map schrijft. Terwijl uw uitvoering uitgevoerd wordt, kunt u zich TensorBoard starten en deze logboeken streamen.  Later, ook mogelijk om terug te zetten van de logboeken van een van uw eerdere uitvoeringen.
>
> Als u bijvoorbeeld een bestand wilt downloaden naar de map *uitvoer* naar uw lokale computer nadat de uitvoering van de externe training is uitgevoerd: `run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Gedistribueerde trainings- en aangepaste Docker-installatiekopieën

Er zijn twee aanvullende trainings scenario's die u kunt uitvoeren met de `Estimator`:
* Met behulp van een aangepaste Docker-installatiekopie
* Gedistribueerde training op een cluster met meerdere knooppunten

De volgende code laat zien hoe u gedistribueerde training kunt uitvoeren voor een Keras-model. In plaats van de standaard-Azure Machine Learning installatie kopieën te gebruiken, wordt er bovendien een aangepaste docker-installatie kopie van docker hub opgegeven `continuumio/miniconda` voor training.

U moet uw [berekenings doel](how-to-set-up-training-targets.md#amlcompute) object al hebben gemaakt `compute_target`. U maken als volgt de estimator:

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

Met de bovenstaande code worden de volgende nieuwe para meters voor de `Estimator`-constructor getoond:

Parameter | Beschrijving | Standaard
--|--|--
`custom_docker_image`| Naam van de installatiekopie die u wilt gebruiken. Geef alleen installatiekopieën die beschikbaar zijn in de openbare docker-opslagplaatsen (in dit geval Docker Hub). Als u een afbeelding uit een privé-docker-opslag plaats wilt gebruiken, gebruikt u in plaats daarvan de para meter `environment_definition` van de constructor. [Zie voor beeld](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Het aantal knooppunten moet worden gebruikt voor de trainingstaak. | `1`
`process_count_per_node`| Het aantal processen (of 'werknemers') om uit te voeren op elk knooppunt. In dit geval gebruikt u de `2` Gpu's die op elk knoop punt beschikbaar zijn.| `1`
`distributed_training`| [MPIConfiguration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) -object voor het starten van gedistribueerde training met MPI-back-end.  | `None`


Ten slotte verzendt u de trainingstaak:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Een model registreren

Zodra u het model hebt getraind, kunt u het opslaan en registreren in uw werk ruimte. Met model registratie kunt u uw modellen in uw werk ruimte opslaan en versieren om het [model beheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

Als u de volgende code uitvoert, wordt het model geregistreerd in uw werk ruimte en wordt het beschikbaar gemaakt om te verwijzen op naam in externe Compute-contexten of implementatie scripts. Zie [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) in de naslag documentatie voor meer informatie en aanvullende para meters.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub bijhouden en integreren

Wanneer u begint met het uitvoeren van een training waarbij de bronmap een lokale Git-opslag plaats is, wordt informatie over de opslag plaats opgeslagen in de uitvoerings geschiedenis. Zie [Git-integratie voor Azure machine learning](concept-train-model-git-integration.md)voor meer informatie.

## <a name="examples"></a>Voorbeelden
Voor een notitie blok waarin de basis beginselen van een Estimator patroon worden weer gegeven, raadpleegt u:
* [procedures voor het gebruik van azureml/training-met-diep leren/How-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Voor een notebook die een scikit-leer model traint met behulp van Estimator, zie:
* [zelf studies/img-Classification-part1-training. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Voor notebooks op trainings modellen met behulp van diep gaande leren Framework-specifieke schattingen raadpleegt u:

* [procedures voor het gebruik van azureml/ml-Frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Metrische uitvoerings gegevens tijdens de training volgen](how-to-track-experiments.md)
* [PyTorch-modellen trainen](how-to-train-pytorch.md)
* [Tensor flow-modellen trainen](how-to-train-tensorflow.md)
* [Hyper parameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)
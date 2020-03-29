---
title: Train ML-modellen met schatters
titleSuffix: Azure Machine Learning
description: Meer informatie over het uitvoeren van single-node en gedistribueerde training van traditionele machine learning- en deep learning-modellen met azure machine learning-classificatieklasse
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79078482"
---
# <a name="train-models-with-azure-machine-learning-using-estimator"></a>Modellen trainen met Azure Machine Learning met behulp van schatter
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Met Azure Machine Learning u uw trainingsscript eenvoudig indienen bij [verschillende rekendoelen,](how-to-set-up-training-targets.md#compute-targets-for-training)met behulp van een [runconfiguration-object](how-to-set-up-training-targets.md#whats-a-run-configuration) en een [ScriptRunConfig-object.](how-to-set-up-training-targets.md#submit) Dat patroon geeft je veel flexibiliteit en maximale controle.

Om training van deep learning-modellen te vergemakkelijken, biedt de Azure Machine Learning Python SDK een alternatieve abstractie op een hoger niveau, de schatterklasse, waarmee gebruikers eenvoudig configuraties kunnen uitvoeren. U een algemeen [schatter](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.estimator?view=azure-ml-py) maken en gebruiken om trainingsscript in te dienen met behulp van een leerkader dat u kiest (zoals scikit-learn) op elk berekend doel dat u kiest, of het nu uw lokale machine, een enkele VM in Azure of een GPU-cluster in Azure is. Voor PyTorch-, TensorFlow- en Chainer-taken biedt Azure Machine Learning ook respectievelijk [PyTorch,](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.pytorch?view=azure-ml-py) [TensorFlow](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.tensorflow?view=azure-ml-py)en [Chainer-schatters](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.dnn.chainer?view=azure-ml-py) om het gebruik van deze frameworks te vereenvoudigen.

## <a name="train-with-an-estimator"></a>Trein met een schatter

Zodra u uw [werkruimte](concept-workspace.md) hebt gemaakt en uw [ontwikkelomgeving](how-to-configure-environment.md)hebt ingesteld, omvat het trainen van een model in Azure Machine Learning de volgende stappen:  
1. Een [extern rekendoel maken](how-to-set-up-training-targets.md) (opmerking u ook lokale computer gebruiken als rekendoel)
2. Uw [trainingsgegevens uploaden](how-to-access-data.md) naar datastore (optioneel)
3. Uw [trainingsscript maken](tutorial-train-models-with-aml.md#create-a-training-script)
4. Maak een `Estimator`-object
5. De schatter verzenden naar een experimentobject onder de werkruimte

Dit artikel richt zich op stappen 4-5. Voor stap 1-3, verwijzen naar de [trein een model tutorial](tutorial-train-models-with-aml.md) voor een voorbeeld.

### <a name="single-node-training"></a>Training met één knooppunt

Gebruik `Estimator` een training met één knooppunt die wordt uitgevoerd op externe gegevens in Azure voor een scikit-learn-model. U had al uw compute `compute_target` [target](how-to-set-up-training-targets.md#amlcompute) object `ds`en het [object FileDataset](how-to-create-register-datasets.md) moeten hebben gemaakt.

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

In dit codefragment worden `Estimator` de volgende parameters aan de constructor opgegeven.

Parameter | Beschrijving
--|--
`source_directory`| Lokale map die al uw code bevat die nodig is voor de trainingstaak. Deze map wordt gekopieerd van uw lokale machine naar de externe compute.
`script_params`| Woordenboek waarin de opdrachtregelargumenten worden opgegeven `entry_script`die moeten worden `<command-line argument, value>` doorgegeven aan uw trainingsscript, in de vorm van paren. Gebruik . `script_params` `<command-line argument, "">`
`compute_target`| Remote compute target waarop uw trainingsscript wordt uitgevoerd, in dit geval een Azure Machine Learning Compute[(AmlCompute)](how-to-set-up-training-targets.md#amlcompute)cluster. (Let op, hoewel AmlCompute-cluster het veelgebruikte doel is, is het ook mogelijk om andere compute-doeltypen te kiezen, zoals Azure VM's of zelfs lokale computer.)
`entry_script`| Filepath (ten `source_directory`opzichte van het trainingsscript dat moet worden uitgevoerd op de externe compute. Dit bestand en eventuele extra bestanden waarvan het afhankelijk is, moeten zich in deze map bevinden.
`conda_packages`| Lijst van Python-pakketten die moeten worden geïnstalleerd via conda die nodig is voor uw trainingsscript.  

De constructeur heeft `pip_packages` een andere parameter genaamd die u gebruikt voor alle pip pakketten die nodig zijn.

Nu u `Estimator` uw object hebt gemaakt, dient u de trainingstaak in die `submit` moet worden uitgevoerd op de externe gegevensberekening met een aanroep naar de functie op het object `experiment` [Experiment.](concept-azure-machine-learning-architecture.md#experiments) 

```Python
run = experiment.submit(sk_est)
print(run.get_portal_url())
```

> [!IMPORTANT]
> **Speciale mappen** Twee mappen, *uitvoer en logboeken* , ontvangen een speciale behandeling door Azure Machine Learning. *logs* Tijdens de training worden de bestanden automatisch geüpload naar uw rungeschiedenis, zodat u er toegang toe hebt zodra uw run is voltooid, wanneer u bestanden schrijft met de naam uitvoer en `./logs` *logboeken* die ten opzichte van de hoofdmap zijn vermeld (`./outputs` en , respectievelijk) wanneer u bestanden naar uw hardloopgeschiedenis stuurt. *outputs*
>
> Als u artefacten wilt maken tijdens de training (zoals modelbestanden, controlepunten, `./outputs` gegevensbestanden of uitgezette afbeeldingen) schrijft u deze naar de map.
>
> Op dezelfde manier u logboeken van `./logs` uw trainingsrun naar de map schrijven. Als u de [TensorBoard-integratie](https://aka.ms/aml-notebook-tb) van Azure Machine Learning wilt gebruiken, moet u uw TensorBoard-logboeken naar deze map schrijven. Terwijl je run aan de gang is, kun je TensorBoard starten en deze logboeken streamen.  Later u ook de logboeken van een van uw vorige runs herstellen.
>
> Bijvoorbeeld als u een bestand wilt downloaden dat naar de *uitvoermap* naar uw lokale machine is geschreven nadat u op afstand bent getraind:`run.download_file(name='outputs/my_output_file', output_file_path='my_destination_path')`

### <a name="distributed-training-and-custom-docker-images"></a>Gedistribueerde trainings- en aangepaste Docker-afbeeldingen

Er zijn twee aanvullende trainingsscenario's `Estimator`die u uitvoeren met de:
* Een aangepaste Docker-afbeelding gebruiken
* Gedistribueerde training op een cluster met meerdere nodes

De volgende code laat zien hoe je gedistribueerde training voor een Keras-model uitvoeren. In plaats van de standaard Azure Machine Learning-afbeeldingen te gebruiken, wordt `continuumio/miniconda` bovendien een aangepaste dockerafbeelding van Docker Hub opgegeven voor training.

U had uw [compute](how-to-set-up-training-targets.md#amlcompute) target `compute_target`object al moeten maken. U maakt de schatter als volgt:

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

De bovenstaande code stelt de volgende `Estimator` nieuwe parameters bloot aan de constructeur:

Parameter | Beschrijving | Standaard
--|--|--
`custom_docker_image`| Naam van de afbeelding die u wilt gebruiken. Geef alleen afbeeldingen die beschikbaar zijn in openbare dockerrepositories (in dit geval Docker Hub). Als u een afbeelding uit een opslagplaats voor privédocker wilt gebruiken, gebruikt u in plaats daarvan de parameter van `environment_definition` de constructor. [Zie voorbeeld](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb). | `None`
`node_count`| Aantal knooppunten dat u gebruiken voor uw trainingstaak. | `1`
`process_count_per_node`| Aantal processen (of "werknemers") dat op elk knooppunt moet worden uitgevoerd. In dit geval gebruikt `2` u de GPU's die beschikbaar zijn op elk knooppunt.| `1`
`distributed_training`| [MPIConfiguration-object](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfig.mpiconfiguration?view=azure-ml-py) voor het starten van gedistribueerde training met MPI-backend.  | `None`


Tot slot, dien de opleiding baan:
```Python
run = experiment.submit(estimator)
print(run.get_portal_url())
```

## <a name="registering-a-model"></a>Een model registreren

Zodra u het model hebt getraind, u het opslaan en registreren op uw werkruimte. Met modelregistratie u uw modellen opslaan en in uw werkruimte gebruiken om [het modelbeheer en de implementatie](concept-model-management-and-deployment.md)te vereenvoudigen.

Als u de volgende code uitvoert, wordt het model geregistreerd in uw werkruimte en wordt het beschikbaar gemaakt voor verwijzing op naam in externe rekencontexten of implementatiescripts. Zie [`register_model`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#register-model-model-name--model-path-none--tags-none--properties-none--model-framework-none--model-framework-version-none--description-none--datasets-none--sample-input-dataset-none--sample-output-dataset-none--resource-configuration-none----kwargs-) in de referentiedocumenten voor meer informatie en aanvullende parameters.

```python
model = run.register_model(model_name='sklearn-sample', model_path=None)
```

## <a name="github-tracking-and-integration"></a>GitHub tracking en integratie

Wanneer u een trainingsrun start waarbij de bronmap een lokale Git-opslagplaats is, wordt informatie over de opslagplaats opgeslagen in de rungeschiedenis. Zie [Git-integratie voor Azure Machine Learning voor](concept-train-model-git-integration.md)meer informatie.

## <a name="examples"></a>Voorbeelden
Zie voor een notitieblok met de basisprincipes van een schatterpatroon:
* [how-to-use-azureml/training-with-deep-learning/how-to-use-estimator](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training-with-deep-learning/how-to-use-estimator/how-to-use-estimator.ipynb)

Zie voor een notebook die een scikit-learn-model traint met behulp van schatter:
* [tutorials/img-classification-part1-training.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/img-classification-part1-training.ipynb)

Zie voor notitieblokken op trainingsmodellen met behulp van deep-learning-framework specifieke schatters:

* [how-to-use-azureml/ml-frameworks](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/ml-frameworks)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Volgende stappen

* [Hardloopstatistieken bijhouden tijdens de training](how-to-track-experiments.md)
* [PyTorch-modellen trainen](how-to-train-pytorch.md)
* [TensorFlow-modellen trainen](how-to-train-tensorflow.md)
* [Hyperparameters afstemmen](how-to-tune-hyperparameters.md)
* [Een getraind model implementeren](how-to-deploy-and-where.md)
* [Omgevingen maken en beheren voor training en implementatie](how-to-use-environments.md)
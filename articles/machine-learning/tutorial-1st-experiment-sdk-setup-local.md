---
title: 'Zelfstudie:  Aan de slag met machine learning - Python'
titleSuffix: Azure Machine Learning
description: In deze zelfstudie gaat u aan de slag met de Python SDK voor Azure Machine Learning die wordt uitgevoerd op uw persoonlijke ontwikkelsomgeving.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: aminsaied
ms.author: amsaied
ms.reviewer: sgilley
ms.date: 09/15/2020
ms.custom: devx-track-python
ms.openlocfilehash: 48edc138e7ab16e712339d6291db52a4a2b36b32
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90929330"
---
# <a name="tutorial-get-started-with-azure-machine-learning-on-your-development-environment-part-1-of-4"></a>Zelfstudie: Aan de slag met Azure Machine Learning in uw ontwikkelomgeving (deel 1 van 4)

In deze **vierdelige zelfstudiereeks** leert u de grondbeginselen van Azure Machine Learning en kunt u op taken gebaseerde Python ML-taken uitvoeren in de Azure-cloud, waaronder:

1. Een werkruimte en uw lokale machine learning-ontwikkelomgeving instellen.
2. Code uitvoeren in de cloud met behulp van de Python-SDK van Azure Machine Learning.
3. De Python-omgeving beheren die u gebruikt voor modeltraining.
4. Gegevens uploaden naar Azure en die gegevens gebruiken in de training.

In **deel 1 van deze zelfstudiereeks** doet u het volgende:

> [!div class="checklist"]
> * De Azure Machine Learning-SDK installeren
> * Mapstructuur voor code instellen
> * Een Azure Machine Learning-werkruimte maken
> * Uw lokale ontwikkelomgeving configureren
> * Een rekencluster instellen

>[!NOTE]
> Deze zelfstudiereeks is gericht op de Azure Machine Learning-concepten die zijn afgestemd op __op taken gebaseerde__ machine learning-taken van Python die rekenintensief zijn en/of reproduceerbaarheid vereisen. Als uw machine learning-taken niet in dit profiel passen, gebruikt u de functionaliteit [Jupyter of RStudio op een Azure Machine Learning-rekeninstantie](tutorial-1st-experiment-sdk-setup.md) om de onboarding naar Azure Machine Learning uit te voeren.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement. Als u geen Azure-abonnement hebt, maakt u een gratis account voordat u begint. Probeer [Azure Machine Learning](https://aka.ms/AMLFree) vandaag nog.
- Vertrouwd met Python en [Machine Learning-concepten](concept-azure-machine-learning-architecture.md). Bijvoorbeeld omgevingen, training, scoren, enzovoort.
- Een lokale ontwikkelomgeving: een laptop met Python geïnstalleerd en uw favoriete IDE (bijvoorbeeld: VSCode, Pycharm, Jupyter, enzovoort).

## <a name="install-the-azure-machine-learning-sdk"></a>De Azure Machine Learning-SDK installeren

In deze zelfstudie maken we gebruik van de Azure Machine Learning Python-SDK.

U kunt de hulpprogramma's die u het beste kent, gebruiken, bijvoorbeeld: conda, pip, enzovoort, om een omgeving in te stellen om in deze zelfstudie te gebruiken. Installeer de Azure Machine Learning Python-SDK via pip in de omgeving:

```bash
pip install azureml-sdk
```

## <a name="create-directory-structure-for-code"></a>Mapstructuur voor code maken
We raden aan dat u de volgende eenvoudige map instelt voor deze zelfstudie:

```markdown
tutorial
└──.azureml
```

- **zelfstudie** (map op het hoogste niveau van het project)
- **. azureml** (verborgen submap van zelfstudie):  De map `.azureml` wordt gebruikt om Azure Machine Learning-configuratiebestanden op te slaan.

## <a name="create-an-azure-machine-learning-workspace"></a>Een Azure Machine Learning-werkruimte maken

Een werkruimte is een resource op het hoogste niveau voor Azure Machine Learning en is een centrale locatie voor het volgende:

- Resources zoals Compute beheren
- Assets opslaan zoals notebooks, omgevingen, gegevenssets, pijplijnen, modellen, eindpunten, enzovoort
- Samenwerken met andere teamleden

In de bovenliggende map - `tutorial` - voegt u een nieuw Python-bestand met de naam `01-create-workspace.py` met de onderstaande code toe. Pas de parameters (naam, abonnements-id, resourcegroep en [locatie](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service) aan met uw voorkeuren.

U kunt de code uitvoeren in een interactieve sessie of als een python-bestand.

>[!NOTE]
> Wanneer u een lokale ontwikkelomgeving (zoals een laptop) gebruikt, wordt u gevraagd om u te verifiëren bij uw werkruimte met behulp van een *apparaatcode* de eerste keer dat u de onderstaande code uitvoert. Volg de instructies op het scherm.

```python
# tutorial/01-create-workspace.py
from azureml.core import Workspace

ws = Workspace.create(name='<my_workspace_name>', # provide a name for your workspace
                      subscription_id='<azure-subscription-id>', # provide your subscription ID
                      resource_group='<myresourcegroup>', # provide a resource group name
                      create_resource_group=True,
                      location='<NAME_OF_REGION>') # For example: 'westeurope' or 'eastus2' or 'westus2' or 'southeastasia'.

# write out the workspace details to a configuration file: .azureml/config.json
ws.write_config(path='.azureml')
```

Voer deze code uit vanuit de map `tutorial`:

```bash
cd <path/to/tutorial>
python ./01-create-workspace.py
```

Zodra het bovenstaande codefragment is uitgevoerd, ziet uw mapstructuur er als volgt uit:

```markdown
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
```

Het bestand `.azureml/config.json` bevat de metagegevens die nodig zijn om verbinding te maken met uw Azure Machine Learning-werkruimte, namelijk uw abonnements-id, resourcegroep en werkruimtenaam. 

> [!NOTE]
> De inhoud van `config.json` is geen geheim; het is zeer geschikt voor het delen van deze gegevens.
> Verificatie is wel nog vereist om te communiceren met uw Azure Machine Learning-werkruimte.

## <a name="create-an-azure-machine-learning-compute-cluster"></a>Een Azure Machine Learning-rekencluster maken

Maak een python-script in de map `tutorial` op het hoogste niveau met de naam `02-create-compute.py` en vul de volgende code in om een Azure Machine Learning-rekencluster te maken dat automatisch wordt geschaald tussen nul en vier knooppunten:

```python
# tutorial/02-create-compute.py
from azureml.core import Workspace
from azureml.core.compute import ComputeTarget, AmlCompute
from azureml.core.compute_target import ComputeTargetException

ws = Workspace.from_config() # this automatically looks for a directory .azureml

# Choose a name for your CPU cluster
cpu_cluster_name = "cpu-cluster"

# Verify that cluster does not exist already
try:
    cpu_cluster = ComputeTarget(workspace=ws, name=cpu_cluster_name)
    print('Found existing cluster, use it.')
except ComputeTargetException:
    compute_config = AmlCompute.provisioning_configuration(vm_size='STANDARD_D2_V2',
                                                            max_nodes=4, 
                                                            idle_seconds_before_scaledown=2400)
    cpu_cluster = ComputeTarget.create(ws, cpu_cluster_name, compute_config)

cpu_cluster.wait_for_completion(show_output=True)
```

Het Python-bestand uitvoeren:

```bash
python ./02-create-compute.py
```


> [!NOTE]
> Wanneer het cluster is gemaakt, zijn er 0 knooppunten ingericht. Daarom worden voor het cluster **geen** kosten in rekening gebracht totdat u een taak verzendt. Dit cluster wordt omlaag geschaald wanneer het gedurende 2400 seconden inactief is geweest (40 minuten).

De mapstructuur ziet er nu als volgt uit:

```bash
tutorial
└──.azureml
|  └──config.json
└──01-create-workspace.py
└──02-create-compute.py
```

## <a name="next-steps"></a>Volgende stappen

In deze installatiezelfstudie hebt u het volgende gedaan:

- Een Azure Machine Learning-werkruimte gemaakt
- De lokale ontwikkelomgeving instellen
- Een Azure Machine Learning-rekencluster gemaakt.

In de volgende zelfstudie gaat u een script verzenden naar het Azure Machine Learning-rekencluster.

> [!div class="nextstepaction"]
> [Zelfstudie: Het Python-script 'Hallo wereld' uitvoeren op Azure](tutorial-1st-experiment-hello-world.md)

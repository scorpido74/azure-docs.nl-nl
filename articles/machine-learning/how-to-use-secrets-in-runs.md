---
title: Gebruik geheimen in trainingsruns
titleSuffix: Azure Machine Learning
description: Geef geheimen door aan trainingsruns op veilige manier met Workspace Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/09/2020
ms.openlocfilehash: d877794abf12b8b412cd1ecf4efd72fd1179d768
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942258"
---
# <a name="use-secrets-in-training-runs"></a>Gebruik geheimen in trainingsruns
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u geheimen veilig gebruiken in trainingsruns. Verificatiegegevens zoals uw gebruikersnaam en wachtwoord zijn geheimen. Als u bijvoorbeeld verbinding maakt met een externe database om trainingsgegevens op te vragen, moet u uw gebruikersnaam en wachtwoord doorgeven aan de context van externe run. Het coderen van dergelijke waarden in trainingsscripts in cleartext is onzeker omdat het het geheim zou blootleggen. 

In plaats daarvan heeft uw Azure Machine Learning-werkruimte een bijbehorende bron, een [Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/key-vault-overview) Gebruik deze Key Vault om geheimen door te geven aan externe activiteiten die veilig worden uitgevoerd via een set API's in de Azure Machine Learning Python SDK.

De basisstroom voor het gebruik van geheimen is:
 1. Meld u op de lokale computer aan bij Azure en maakt verbinding met uw werkruimte.
 2. Stel op de lokale computer een geheim in in Workspace Key Vault.
 3. Dien een externe run in.
 4. Binnen de remote run, haal het geheim van Key Vault en gebruik het.

## <a name="set-secrets"></a>Geheimen instellen

In de klasse Azure Machine Learning bevat de klasse [Keyvault](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) methoden voor het instellen van geheimen. In uw lokale Python-sessie krijgt u eerst een verwijzing [`set_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) naar uw werkruimte Key Vault en gebruikt u de methode om een geheim in te stellen op naam en waarde. De __set_secret__ methode werkt de geheime waarde bij als de naam al bestaat.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Zet de geheime waarde niet in uw Python-code omdat deze onveilig is om deze als cleartext in het bestand op te slaan. Verkrijg in plaats daarvan de geheime waarde uit een omgevingsvariabele, bijvoorbeeld Azure DevOps, of uit interactieve gebruikersinvoer.

U geheime namen [`list_secrets()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) vermelden met behulp van de methode en er is ook een batchversie,[set_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) waarmee u meerdere geheimen tegelijk instellen.

## <a name="get-secrets"></a>Krijg geheimen

In uw lokale code kunt[`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) u de methode gebruiken om de geheime waarde op naam te krijgen.

Voor ingezonden [`Experiment.submit`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-) uitvoeringen [`get_secret()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) de [`Run`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py) , gebruik de methode met de klasse. Omdat een ingediende run op de hoogte is van de werkruimte, wordt met deze methode de instantiatie van de werkruimte geleid en wordt de geheime waarde rechtstreeks geretourneerd.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Wees voorzichtig niet om de geheime waarde bloot door het schrijven of afdrukken van het uit.

Er is ook een batch-versie, [get_secrets()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) voor toegang tot meerdere geheimen tegelijk.

## <a name="next-steps"></a>Volgende stappen

 * [Voorbeeldnotitieblok weergeven](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Meer informatie over bedrijfsbeveiliging met Azure Machine Learning](concept-enterprise-security.md)

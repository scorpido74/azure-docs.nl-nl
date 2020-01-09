---
title: Geheimen gebruiken in trainings uitvoeringen
titleSuffix: Azure Machine Learning
description: Geheimen op beveiligde wijze door geven aan trainings uitvoeringen met behulp van werkruimte Key Vault
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: 722e329b1a2d069b614a301a5eb2f8642b697295
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2019
ms.locfileid: "75538707"
---
# <a name="use-secrets-in-training-runs"></a>Geheimen gebruiken in trainings uitvoeringen
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u geheimen kunt gebruiken in de training die veilig worden uitgevoerd. Als u bijvoorbeeld verbinding wilt maken met een externe data base om een query uit te voeren op trainings gegevens, moet u uw gebruikers naam en wacht woord door geven aan de context voor externe uitvoering. Het coderen van dergelijke waarden in trainings scripts is onveilig, omdat het geheim zou worden weer gegeven. 

In plaats daarvan heeft uw Azure Machine Learning-werkruimte [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview) als gekoppelde resource. Deze Key Vault kan worden gebruikt voor het door geven van geheimen aan externe uitvoeringen veilig via een set Api's in Azure Machine Learning python SDK.

De basis stroom voor het gebruik van geheimen is:
 1. Meld u op de lokale computer aan bij Azure en maak verbinding met uw werk ruimte.
 2. Stel op lokale computer een geheim in de werk ruimte Key Vault in.
 3. Een externe uitvoering verzenden.
 4. Haal binnen de externe uitvoering het geheim op uit de sleutel waarde en gebruik het.

## <a name="set-secrets"></a>Geheimen instellen

In de Azure Machine Learning python-SDK bevat de klasse sleutel [kluis](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py) methoden voor het instellen van geheimen. In uw lokale python-sessie moet u eerst een verwijzing naar de werkruimte Key Vault verkrijgen en vervolgens [set_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secret-name--value-) methode gebruiken om een geheim in te stellen op naam en waarde.

```python
from azureml.core import Workspace
import os

ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

Plaats de geheime waarde niet in uw Python-code omdat deze onveilig is om deze in het bestand op te slaan als een lees bare tekst. Haal in plaats daarvan de geheime waarde op uit een omgevings variabele, bijvoorbeeld Azure DevOps build Secret of van interactieve gebruikers invoer.

U kunt de naam van een geheim vermelden met behulp van de [list_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#list-secrets--) methode. De __set_secret__ methode werkt de geheime waarde bij als de naam al bestaat.

## <a name="get-secrets"></a>Geheimen ophalen

In uw lokale code kunt u de methode sleutel[kluis. get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#get-secret-name-) gebruiken om de geheime waarde op naam op te halen.

Gebruik de methode [Run. get_secret](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secret-name-) in uitvoeringen die zijn verzonden met behulp van [experiment. Submit](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py#submit-config--tags-none----kwargs-). Omdat een ingediende uitvoering op de hoogte is van de werk ruimte, wordt met deze methode de activering van de werk ruimte versneld en wordt de geheime waarde direct geretourneerd.

```python
# Code in submitted run
from azureml.core import Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

Zorg ervoor dat u de geheime waarde niet beschikbaar maakt door deze te schrijven of af te drukken.

De methoden set en Get hebben ook batch versies [set_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.keyvault.keyvault?view=azure-ml-py#set-secrets-secrets-batch-) en [get_secrets](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#get-secrets-secrets-) voor het openen van meerdere geheimen tegelijk.

## <a name="next-steps"></a>Volgende stappen

 * [Voorbeeld notitieblok weer geven](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Meer informatie over Enter prise Security met Azure Machine Learning](concept-enterprise-security.md)

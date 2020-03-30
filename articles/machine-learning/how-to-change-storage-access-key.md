---
title: Toegangssleutels voor opslagaccount wijzigen
titleSuffix: Azure Machine Learning
description: Meer informatie over het wijzigen van de toegangssleutels voor het Azure Storage-account dat door uw werkruimte wordt gebruikt. Azure Machine Learning gebruikt een Azure Storage-account om gegevens en modellen op te slaan.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296920"
---
# <a name="regenerate-storage-account-access-keys"></a>Toegangssleutels voor opslagaccount regenereren
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Meer informatie over het wijzigen van de toegangssleutels voor Azure Storage-accounts die worden gebruikt door Azure Machine Learning. Azure Machine Learning kan opslagaccounts gebruiken om gegevens of getrainde modellen op te slaan.

Voor beveiligingsdoeleinden moet u mogelijk de toegangssleutels voor een Azure Storage-account wijzigen. Wanneer u de toegangssleutel regenereert, moet Azure Machine Learning worden bijgewerkt om de nieuwe sleutel te gebruiken. Azure Machine Learning gebruikt mogelijk het opslagaccount voor zowel modelopslag als als gegevensarchief.

## <a name="prerequisites"></a>Vereisten

* Een Azure Machine Learning-werkruimte. Zie het artikel [Een werkruimte maken](how-to-manage-workspace.md) voor meer informatie.

* De [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* De [Azure Machine Learning CLI-extensie](reference-azure-machine-learning-cli.md).

> [!NOTE]
> De codefragmenten in dit document zijn getest met versie 1.0.83 van de Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Wat moet worden bijgewerkt

Opslagaccounts kunnen worden gebruikt door de Azure Machine Learning-werkruimte (logboeken opslaan, modellen, momentopnamen, enz.) en als gegevensarchief. Het proces voor het bijwerken van de werkruimte is één Azure CLI-opdracht en kan worden uitgevoerd na het bijwerken van de opslagsleutel. Het proces van het bijwerken van datastores is meer betrokken, en vereist het ontdekken van welke datastores momenteel het opslagaccount gebruiken en deze vervolgens opnieuw registreren.

> [!IMPORTANT]
> Werk de werkruimte tegelijkertijd bij met de Azure CLI en de gegevenswinkels met Python. Het bijwerken van slechts de ene of de andere is niet voldoende en kan fouten veroorzaken totdat beide zijn bijgewerkt.

Als u de opslagaccounts wilt ontdekken die door uw gegevenswinkels worden gebruikt, gebruikt u de volgende code:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Deze code zoekt naar geregistreerde gegevenswinkels die Azure Storage gebruiken en bevat de volgende gegevens:

* Naam datastore: de naam van het gegevensarchief waarop het opslagaccount is geregistreerd.
* Naam van het opslagaccount: de naam van het Azure Storage-account.
* Container: De container in de opslagrekening die door deze registratie wordt gebruikt.

Het geeft ook aan of de datastore voor een Azure Blob of een Azure File-share is, omdat er verschillende methoden zijn om elk type gegevensarchief opnieuw te registreren.

Als er een item bestaat voor het opslagaccount waarvoor u toegangssleutels regenereert, slaat u de naam van het gegevensarchief, de naam van het opslagaccount en de naam van de container op.

## <a name="update-the-access-key"></a>De toegangssleutel bijwerken

Als u Azure Machine Learning wilt bijwerken om de nieuwe sleutel te gebruiken, gebruikt u de volgende stappen:

> [!IMPORTANT]
> Voer alle stappen uit, werk zowel de werkruimte bij met de CLI als gegevenswinkels met Python. Het bijwerken van slechts de ene of de andere kan fouten veroorzaken totdat beide zijn bijgewerkt.

1. Regenereerde de sleutel. Zie Toegangssleutels voor [opslagaccount](../storage/common/storage-account-keys-manage.md)beheren voor informatie over het regenereren van een toegangssleutel. Bewaar de nieuwe sleutel.

1. Als u de werkruimte wilt bijwerken om de nieuwe sleutel te gebruiken, gebruikt u de volgende stappen:

    1. U zich aanmelden bij het Azure-abonnement dat uw werkruimte bevat met de volgende opdracht Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Als u de werkruimte wilt bijwerken om de nieuwe sleutel te gebruiken, gebruikt u de volgende opdracht. Vervang `myworkspace` de naam van uw Azure `myresourcegroup` Machine Learning-werkruimte en vervang de naam van de Azure-brongroep die de werkruimte bevat.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Met deze opdracht worden de nieuwe sleutels voor het Azure-opslagaccount dat door de werkruimte wordt gebruikt, automatisch gesynchroniseerd.

1. Als u gegevensarchief(s) die het opslagaccount gebruiken opnieuw wilt registreren, gebruikt u de waarden uit de sectie [Wat moet worden bijgewerkt](#whattoupdate) en de sleutel van stap 1 met de volgende code:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Aangezien `overwrite=True` is opgegeven, deze code overschrijft de bestaande registratie en updates om de nieuwe sleutel te gebruiken.

## <a name="next-steps"></a>Volgende stappen

Zie de [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) klassereferentie voor meer informatie over het registreren van datastores.

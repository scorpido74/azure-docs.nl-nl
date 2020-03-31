---
title: Werkruimten maken met Azure CLI
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Azure CLI om een nieuwe Azure Machine Learning-werkruimte te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 03/05/2020
ms.openlocfilehash: 64c2e77ffc43ec98c13ce8c0cad002cdb2053241
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296905"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Een werkruimte maken voor Azure Machine Learning met Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een Azure Machine Learning-werkruimte maakt met de Azure CLI. De Azure CLI biedt opdrachten voor het beheer van Azure-resources. De machine learning-extensie voor de CLI biedt opdrachten voor het werken met Azure Machine Learning-resources.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er geen hebt, probeert u de [gratis of betaalde versie van Azure Machine Learning.](https://aka.ms/AMLFree)

* Als u de CLI-opdrachten in dit document vanuit uw **lokale omgeving**wilt gebruiken, hebt u de [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

    Als u de [Azure Cloud Shell](https://azure.microsoft.com//features/cloud-shell/)gebruikt, wordt de CLI geopend via de browser en leeft deze in de cloud.

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI verbinden met uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, u deze sectie overslaan. De cloudshell verifieert u automatisch met het account dat u aanmeldt bij uw Azure-abonnement.

Er zijn verschillende manieren waarop u vanaf de CLI verifiëren voor uw Azure-abonnement. De meest elementaire is om interactief te verifiëren met behulp van een browser. Als u interactief wilt verifiëren, opent u een opdrachtregel of terminal en gebruikt u de volgende opdracht:

```azurecli-interactive
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdrachtregel volgen. De instructies omvatten [https://aka.ms/devicelogin](https://aka.ms/devicelogin) browsen naar en het invoeren van een autorisatiecode.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

Zie [Aanmelden met Azure CLI](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)voor andere methoden voor het verifiëren.

## <a name="install-the-machine-learning-extension"></a>De machine learning-extensie installeren

Als u de machine learning-extensie wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Een werkruimte maken

De Azure Machine Learning-werkruimte is gebaseerd op de volgende Azure-services of -entiteiten:

> [!IMPORTANT]
> Als u geen bestaande Azure-service opgeeft, wordt er automatisch een gemaakt tijdens het maken van werkruimten. U moet altijd een resourcegroep opgeven.

| Service | Parameter om een bestaande instantie op te geven |
| ---- | ---- |
| **Azure-resourcegroep** | `-g <resource-group-name>`
| **Azure-opslagaccount** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure-containerregister** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De Azure Machine Learning-werkruimte moet worden gemaakt in een resourcegroep. U een bestaande resourcegroep gebruiken of een nieuwe groep maken. Als __u een nieuwe resourcegroep wilt maken,__ gebruikt u de volgende opdracht. Vervang `<resource-group-name>` de naam die u voor deze resourcegroep wilt gebruiken. Vervang `<location>` het Azure-gebied dat u voor deze brongroep wilt gebruiken:

> [!TIP]
> U moet een regio selecteren waar Azure Machine Learning beschikbaar is. Zie Producten [die per regio beschikbaar zijn voor](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)informatie .

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Het antwoord van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Zie [az group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)voor meer informatie over het werken met resourcegroepen.

### <a name="automatically-create-required-resources"></a>De vereiste resources automatisch maken

Als u een nieuwe werkruimte wilt maken waar de __services automatisch worden gemaakt,__ gebruikt u de volgende opdracht:

> [!TIP]
> Met de opdrachten in deze sectie wordt een werkruimte voor de basiseditie gemaakt. Als u een werkruimte `--sku enterprise` voor een `az ml workspace create` onderneming wilt maken, gebruikt u de schakelaar met de opdracht. Zie Wat is Azure Machine Learning voor meer informatie over Azure Machine [Learning-edities.](overview-what-is-azure-ml.md#sku)

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

> [!NOTE]
> De naam van de werkruimte is hoofdletters ongevoelig.

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

### <a name="use-existing-resources"></a>Bestaande bronnen gebruiken

Als u een werkruimte wilt maken die bestaande bronnen gebruikt, moet u de id voor de resources opgeven. Gebruik de volgende opdrachten om de ID voor de services op te halen:

> [!IMPORTANT]
> U hoeft niet alle bestaande resources op te geven. U er een of meer opgeven. U bijvoorbeeld een bestaand opslagaccount opgeven en de werkruimte maakt de andere bronnen.

+ **Azure Storage-account:**`az storage account show --name <storage-account-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor uw opslagaccount:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure-toepassingsinzichten:**

    1. Installeer de extensie toepassingsinzichten:

        ```azurecli-interactive
        az extension add -n application-insights
        ```

    2. Haal de id van uw applicatie-insight service:

        ```azurecli-interactive
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor uw service voor toepassingsinzichten:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault:**`az keyvault show --name <key-vault-name> --query "ID"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor uw sleutelkluis:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure Container Registry:**`az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de id voor het containerregister:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Het containerregister moet het [beheerdersaccount](/azure/container-registry/container-registry-authentication#admin-account) hebben ingeschakeld voordat het kan worden gebruikt met een Azure Machine Learning-werkruimte.

Zodra u de id's voor de resource(s) hebt die u `az workspace create -w <workspace-name> -g <resource-group-name>` met de werkruimte wilt gebruiken, gebruikt u de basisopdracht en voegt u de parameter(s) en ID(s) toe voor de bestaande resources. Met de volgende opdracht wordt bijvoorbeeld een werkruimte gemaakt die een bestaand containerregister gebruikt:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name> --container-registry "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="list-workspaces"></a>Werkruimten weergeven

Als u alle werkruimten voor uw Azure-abonnement wilt weergeven, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace list
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
[
  {
    "resourceGroup": "myresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "myml"
  },
  {
    "resourceGroup": "anotherresourcegroup",
    "subscriptionId": "<subscription-id>",
    "workspaceName": "anotherml"
  }
]
```

Zie voor meer informatie de documentatie van de [az ml-werkruimtelijst.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list)

## <a name="get-workspace-information"></a>Werkruimtegegevens verzamelen

Als u informatie over een werkruimte wilt opvragen, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace show -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Zie voor meer informatie de documentatie van de [AZ ML-werkruimte.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show)

## <a name="update-a-workspace"></a>Een werkruimte bijwerken

Als u een werkruimte wilt bijwerken, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace update -w <workspace-name> -g <resource-group-name>
```

De uitvoer van deze opdracht is vergelijkbaar met de volgende JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/application-insight-name>",
  "creationTime": "2019-08-30T18:55:03.1807976+00:00",
  "description": "",
  "friendlyName": "",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "tags": {},
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

Zie voor meer informatie de updatedocumentatie van de [AZ ML Workspace.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update)

## <a name="share-a-workspace-with-another-user"></a>Een werkruimte delen met een andere gebruiker

Als u een werkruimte wilt delen met een andere gebruiker in uw abonnement, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Zie [Gebruikers en rollen beheren](how-to-assign-roles.md)voor meer informatie over op rollen gebaseerde toegangscontrole (RBAC).

Zie voor meer informatie de documentatie [voor het delen van az ml-werkruimtes.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share)

## <a name="sync-keys-for-dependent-resources"></a>Sneltoetsen synchroniseren voor afhankelijke resources

Als u toegangssleutels wijzigt voor een van de bronnen die door uw werkruimte worden gebruikt, gebruikt u de volgende opdracht om de nieuwe toetsen met de werkruimte te synchroniseren:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Zie [Toegangssleutels voor opslagopnieuw genereren](how-to-change-storage-access-key.md)voor meer informatie over het wijzigen van sleutels.

Zie voor meer informatie de documentatie [over de synchronisatietoetsen van az ml-werkruimte.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys)

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Als u een werkruimte wilt verwijderen nadat deze niet meer nodig is, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Als u een werkruimte verwijdert, worden het toepassingsinzicht, het opslagaccount, de sleutelkluis of het containerregister dat door de werkruimte wordt gebruikt, niet verwijderd.

U ook de brongroep verwijderen, die de werkruimte en alle andere Azure-bronnen in de brongroep verwijdert. Als u de brongroep wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Zie voor meer informatie de documentatie voor het verwijderen van de [AZ ML-werkruimte.](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete)

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten in resourceprovider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

### <a name="moving-the-workspace"></a>De werkruimte verplaatsen

> [!WARNING]
> Het verplaatsen van uw Azure Machine Learning-werkruimte naar een ander abonnement of het verplaatsen van het eigen abonnement naar een nieuwe tenant wordt niet ondersteund. Dit kan fouten veroorzaken.

### <a name="deleting-the-azure-container-registry"></a>Het Azure-containerregister verwijderen

De Azure Machine Learning-werkruimte gebruikt Azure Container Registry (ACR) voor sommige bewerkingen. Het zal automatisch een ACR-exemplaar maken wanneer het er voor het eerst een nodig heeft.

[!INCLUDE [machine-learning-delete-acr](../../includes/machine-learning-delete-acr.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de [az ml-documentatie](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) voor meer informatie over de Azure CLI-extensie voor machine learning.

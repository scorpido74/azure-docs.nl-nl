---
title: Werk ruimten maken met Azure CLI
titleSuffix: Azure Machine Learning
description: Meer informatie over het gebruik van de Azure CLI om een nieuwe Azure Machine Learning-werk ruimte te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 11/05/2019
ms.openlocfilehash: f22145541369093f9cb8a2e9d06b705b6b2121df
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289863"
---
# <a name="create-a-workspace-for-azure-machine-learning-with-azure-cli"></a>Een werk ruimte maken voor Azure Machine Learning met Azure CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In dit artikel leert u hoe u een Azure Machine Learning-werk ruimte maakt met behulp van de Azure CLI. De Azure CLI bevat opdrachten voor het beheer van Azure-resources. De machine learning-extensie voor de CLI bevat opdrachten voor het werken met Azure Machine Learning-resources.

## <a name="prerequisites"></a>Vereisten

* Een **Azure-abonnement**. Als u er nog geen hebt, probeer [dan de gratis of betaalde versie van Azure machine learning](https://aka.ms/AMLFree).

* Als u de CLI-opdrachten in dit document vanuit uw **lokale omgeving**wilt gebruiken, hebt u de [Azure cli](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)nodig.

    Als u de [Azure Cloud shell](https://azure.microsoft.com//features/cloud-shell/)gebruikt, wordt de CLI geopend via de browser en in de Cloud.

## <a name="connect-the-cli-to-your-azure-subscription"></a>De CLI koppelen aan uw Azure-abonnement

> [!IMPORTANT]
> Als u de Azure Cloud Shell gebruikt, kunt u deze sectie overs Laan. De Cloud shell verifieert u automatisch met het account dat u hebt aangemeld bij uw Azure-abonnement.

Er zijn verschillende manieren waarop u kunt verifiëren bij uw Azure-abonnement vanuit de CLI. De meeste basis is om interactief te verifiëren met behulp van een browser. Als u interactief wilt verifiëren, opent u een opdracht regel of Terminal en gebruikt u de volgende opdracht:

```azurecli
az login
```

Als de CLI uw standaardbrowser kan openen, gebeurt dat ook en wordt er een aanmeldingspagina gedownload. Anders moet u een browser openen en de instructies op de opdracht regel volgen. De instructies moeten bladeren naar [https://aka.ms/devicelogin](https://aka.ms/devicelogin) en een autorisatie code invoeren.

Zie [Aanmelden met Azure cli](https://docs.microsoft.com/cli/azure/authenticate-azure-cli?view=azure-cli-latest)voor andere verificatie methoden.

## <a name="install-the-machine-learning-extension"></a>De machine learning-extensie installeren

Als u de extensie machine learning wilt installeren, gebruikt u de volgende opdracht:

```azurecli-interactive
az extension add -n azure-cli-ml
```

## <a name="create-a-workspace"></a>Een werkruimte maken

De Azure Machine Learning-werk ruimte is afhankelijk van de volgende Azure-Services of-entiteiten:

> [!IMPORTANT]
> Als u geen bestaande Azure-service opgeeft, wordt er automatisch een gemaakt tijdens het maken van de werk ruimte. U moet altijd een resource groep opgeven.

| Service | Para meter om een bestaand exemplaar op te geven |
| ---- | ---- |
| **Azure-resource groep** | `-g <resource-group-name>`
| **Azure Storage-account** | `--storage-account <service-id>` |
| **Azure Application Insights** | `--application-insights <service-id>` |
| **Azure Key Vault** | `--keyvault <service-id>` |
| **Azure Container Registry** | `--container-registry <service-id>` |

### <a name="create-a-resource-group"></a>Een resourcegroep maken

De Azure Machine Learning-werk ruimte moet binnen een resource groep worden gemaakt. U kunt een bestaande resource groep gebruiken of een nieuwe maken. Gebruik de volgende opdracht om __een nieuwe resource groep te maken__. Vervang `<resource-group-name>` door de naam die u voor deze resource groep wilt gebruiken. Vervang `<location>` door de Azure-regio die u voor deze resource groep wilt gebruiken:

> [!TIP]
> U moet een regio selecteren waar Azure Machine Learning beschikbaar is. Zie [producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)voor meer informatie.

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

Zie [AZ Group](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)(Engelstalig) voor meer informatie over het werken met resource groepen.

### <a name="automatically-create-required-resources"></a>Automatisch vereiste resources maken

Als u een nieuwe werk ruimte wilt maken waar de __Services automatisch worden gemaakt__, gebruikt u de volgende opdracht:

> [!TIP]
> Met de opdrachten in deze sectie wordt een Basic Edition-werk ruimte gemaakt. Als u een Enter prise-werk ruimte wilt maken, gebruikt u de `--sku enterprise` switch met de `az ml workspace create` opdracht. Zie [Wat is Azure machine learning](overview-what-is-azure-ml.md#sku)voor meer informatie over Azure machine learning-edities.

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
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

### <a name="use-existing-resources"></a>Bestaande resources gebruiken

Als u een werk ruimte wilt maken waarin bestaande resources worden gebruikt, moet u de ID voor de resources opgeven. Gebruik de volgende opdrachten om de ID voor de services op te halen:

> [!IMPORTANT]
> U hoeft niet alle bestaande resources op te geven. U kunt een of meer opgeven. U kunt bijvoorbeeld een bestaand opslag account opgeven en de andere resources worden gemaakt door de werk ruimte.

+ **Azure Storage account**: `az storage account show --name <storage-account-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor uw opslag account:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.Storage/storageAccounts/<storage-account-name>"`

+ **Azure-toepassing Insights**:

    1. Installeer de Application Insights-extensie:

        ```bash
        az extension add -n application-insights
        ```

    2. De ID ophalen van uw Application Insight-service:

        ```bash
        az monitor app-insights component show --app <application-insight-name> -g <resource-group-name> --query "id"
        ```

        Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor uw Application Insights-service:

        `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>"`

+ **Azure Key Vault**: `az keyvault show --name <key-vault-name> --query "ID"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor uw sleutel kluis:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.KeyVault/vaults/<key-vault-name>"`

+ **Azure container Registry**: `az acr show --name <acr-name> -g <resource-group-name> --query "id"`

    Het antwoord van deze opdracht is vergelijkbaar met de volgende tekst en is de ID voor het container register:

    `"/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<acr-name>"`

    > [!IMPORTANT]
    > Het [beheerders account](/azure/container-registry/container-registry-authentication#admin-account) moet zijn ingeschakeld voor het container register voordat het kan worden gebruikt met een Azure machine learning-werk ruimte.

Wanneer u de Id's hebt voor de resource (s) die u wilt gebruiken voor de werk ruimte, gebruikt u de basis `az workspace create -w <workspace-name> -g <resource-group-name>` opdracht en voegt u de para meter (s) en de ID ('s) toe voor de bestaande resources. Met de volgende opdracht maakt u bijvoorbeeld een werk ruimte die gebruikmaakt van een bestaand container register:

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

## <a name="list-workspaces"></a>Werk ruimten weer geven

Als u alle werk ruimten voor uw Azure-abonnement wilt weer geven, gebruikt u de volgende opdracht:

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

Zie de documentatie voor de [lijst AZ ml-werk ruimte](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-list) voor meer informatie.

## <a name="get-workspace-information"></a>Werkruimte gegevens ophalen

Gebruik de volgende opdracht om informatie over een werk ruimte op te halen:

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

Zie de documentatie van [AZ ml Workspace show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-show) voor meer informatie.

## <a name="update-a-workspace"></a>Een werk ruimte bijwerken

Als u een werk ruimte wilt bijwerken, gebruikt u de volgende opdracht:

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

Zie voor meer informatie de documentatie van [AZ ml Workspace update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-update) .

## <a name="share-a-workspace-with-another-user"></a>Een werk ruimte delen met een andere gebruiker

Als u een werk ruimte wilt delen met een andere gebruiker in uw abonnement, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace share -w <workspace-name> -g <resource-group-name> --user <user> --role <role>
```

Zie [gebruikers en rollen beheren](how-to-assign-roles.md)voor meer informatie over op rollen gebaseerd toegangs beheer (RBAC) met Azure machine learning.

Zie voor meer informatie de documentatie bij [AZ ml-werkruimte share](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-share) .

## <a name="sync-keys-for-dependent-resources"></a>Sleutels voor afhankelijke Resources synchroniseren

Als u de toegangs sleutels voor een van de resources die worden gebruikt door uw werk ruimte wijzigt, gebruikt u de volgende opdracht om de nieuwe sleutels te synchroniseren met de werk ruimte:

```azurecli-interactive
az ml workspace sync-keys -w <workspace-name> -g <resource-group-name>
```

Zie [toegangs sleutels voor opslag opnieuw genereren](how-to-change-storage-access-key.md)voor meer informatie over het wijzigen van sleutels.

Zie voor meer informatie de documentatie van de [werk ruimte synchronisatie van AZ ml-sleutels](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-sync-keys) .

## <a name="delete-a-workspace"></a>Een werkruimte verwijderen

Als u een werk ruimte wilt verwijderen nadat deze niet meer nodig is, gebruikt u de volgende opdracht:

```azurecli-interactive
az ml workspace delete -w <workspace-name> -g <resource-group-name>
```

> [!IMPORTANT]
> Als u een werk ruimte verwijdert, worden de toepassings inzichten, het opslag account, de sleutel kluis of het container register dat door de werk ruimte wordt gebruikt, niet verwijderd.

U kunt ook de resource groep verwijderen, waarmee de werk ruimte en alle andere Azure-resources in de resource groep worden verwijderd. Als u de resource groep wilt verwijderen, gebruikt u de volgende opdracht:

```azurecli-interactive
az group delete -g <resource-group-name>
```

Zie voor meer informatie de documentatie voor het verwijderen van de [werk ruimte AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-delete) .

## <a name="troubleshooting"></a>Problemen oplossen

### <a name="resource-provider-errors"></a>Fouten van de resource provider

[!INCLUDE [machine-learning-resource-provider](../../includes/machine-learning-resource-provider.md)]

## <a name="next-steps"></a>Volgende stappen

Zie de documentatie van [AZ ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest) voor meer informatie over de Azure cli-extensie voor machine learning.

---
title: Aad-identiteit gebruiken met uw webservice
titleSuffix: Azure Machine Learning
description: Gebruik AAD-identiteit met uw webservice in Azure Kubernetes Service om toegang te krijgen tot cloudbronnen tijdens het scoren.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: aashishb
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 02/10/2020
ms.openlocfilehash: f997aef59e91bed325b84af855a84f43cd639d83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77122842"
---
# <a name="use-azure-ad-identity-with-your-machine-learning-web-service-in-azure-kubernetes-service"></a>Azure AD-identiteit gebruiken met uw webservice voor machine learning in Azure Kubernetes-service

In deze how-to leert u hoe u een Azure Active Directory-identiteit (AAD) toewijst aan uw geïmplementeerde machine learning-model in Azure Kubernetes Service. Met het [AAD Pod Identity-project](https://github.com/Azure/aad-pod-identity) kunnen toepassingen veilig toegang krijgen tot cloudbronnen met AAD met behulp van een [Managed Identity](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) en Kubernetes-primitieven. Hierdoor heeft uw webservice veilig toegang tot uw Azure-bronnen zonder dat `score.py` u referenties hoeft in te sluiten of tokens rechtstreeks in uw script hoeft te beheren. In dit artikel worden de stappen uitgelegd voor het maken en installeren van een Azure Identity in uw Azure Kubernetes Service-cluster en wordt de identiteit toegewezen aan uw geïmplementeerde webservice.

## <a name="prerequisites"></a>Vereisten

- De [Azure CLI-extensie voor de Machine Learning-service](reference-azure-machine-learning-cli.md), de [Azure Machine Learning SDK voor Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)of de Azure Machine Learning Visual Studio [Code-extensie](tutorial-setup-vscode-extension.md).

- Toegang tot uw AKS-cluster met de `kubectl` opdracht. Zie [Verbinding maken met het cluster voor](https://docs.microsoft.com/azure/aks/kubernetes-walkthrough#connect-to-the-cluster) meer informatie

- Een Azure Machine Learning-webservice die is geïmplementeerd in uw AKS-cluster.

## <a name="create-and-install-an-azure-identity-in-your-aks-cluster"></a>Een Azure-identiteit maken en installeren in uw AKS-cluster

1. Als u wilt bepalen of uw AKS-cluster RBAC is ingeschakeld, gebruikt u de volgende opdracht:

    ```azurecli-interactive
    az aks show --name <AKS cluster name> --resource-group <resource group name> --subscription <subscription id> --query enableRbac
    ```

    Met deze opdracht `true` wordt een waarde geretourneerd van als RBAC is ingeschakeld. Deze waarde bepaalt de opdracht die moet worden gebruikt in de volgende stap.

1. Als u [AAD Pod Identity](https://github.com/Azure/aad-pod-identity#getting-started) wilt installeren in uw AKS-cluster, gebruikt u een van de volgende opdrachten:

    * Als RBAC in uw AKS-cluster **is ingeschakeld,** gebruikt u de volgende opdracht:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
        ```
    
    * Als RBAC op uw **AKS-cluster niet is ingeschakeld,** gebruikt u de volgende opdracht:
    
        ```azurecli-interactive
        kubectl apply -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
        ```
    
        De uitvoer van de opdracht is vergelijkbaar met de volgende tekst:

        ```text
        customresourcedefinition.apiextensions.k8s.io/azureassignedidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentitybindings.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azureidentities.aadpodidentity.k8s.io created
        customresourcedefinition.apiextensions.k8s.io/azurepodidentityexceptions.aadpodidentity.k8s.io created
        daemonset.apps/nmi created
        deployment.apps/mic created
        ```

1. [Een Azure-identiteit maken](https://github.com/Azure/aad-pod-identity#2-create-an-azure-identity) volgens de stappen die worden weergegeven op de projectpagina van AAD Pod Identity.

1. [Installeer de Azure Identity](https://github.com/Azure/aad-pod-identity#3-install-the-azure-identity) volgens de stappen die worden weergegeven op de projectpagina AAD Pod Identity.

1. [Installeer de Azure-identiteitsbinding](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding) volgens de stappen die worden weergegeven op de projectpagina AAD Pod Identity.

1. Als de Azure-identiteit die in de vorige stap is gemaakt, zich niet in dezelfde brongroep bevindt als uw AKS-cluster, volgt [u Machtigingen instellen voor MIC](https://github.com/Azure/aad-pod-identity#6-set-permissions-for-mic) volgens de stappen die worden weergegeven op de projectpagina AAD Pod Identity.

## <a name="assign-azure-identity-to-machine-learning-web-service"></a>Azure Identity toewijzen aan de webservice voor machine learning

De volgende stappen gebruiken de Azure Identity die in de vorige sectie is gemaakt en toewijzen aan uw AKS-webservice via een **selectorlabel**.

Identificeer eerst de naam en naamruimte van uw implementatie in uw AKS-cluster die u de Azure-identiteit wilt toewijzen. U deze informatie krijgen door de volgende opdracht uit te voeren. De naamruimten moeten de naam van uw Azure Machine Learning-werkruimte zijn en uw implementatienaam moet uw eindpuntnaam zijn, zoals in de portal wordt weergegeven.

```azurecli-interactive
kubectl get deployment --selector=isazuremlapp=true --all-namespaces --show-labels
```

Voeg het azure identity selector label toe aan uw implementatie door de implementatiespec te bewerken. De waarde van de kiezer moet de waarde zijn die u hebt gedefinieerd in stap 5 van [De Azure-identiteitsbinding installeren.](https://github.com/Azure/aad-pod-identity#5-install-the-azure-identity-binding)

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzureIdentityBinding
metadata:
  name: demo1-azure-identity-binding
spec:
  AzureIdentity: <a-idname>
  Selector: <label value to match>
```

Bewerk de implementatie om het azure identity selector-label toe te voegen. Ga naar de `/spec/template/metadata/labels`volgende sectie onder . U ziet waarden `isazuremlapp: “true”`zoals . Voeg het aad-pod-identiteitslabel toe zoals hieronder weergegeven.

```azurecli-interactive
    kubectl edit deployment/<name of deployment> -n azureml-<name of workspace>
```

```yaml
spec:
  template:
    metadata:
      labels:
      - aadpodidbinding: "<value of Selector in AzureIdentityBinding>"
      ...
```

Voer de volgende opdracht uit om te controleren of het label correct is toegevoegd.

```azurecli-interactive
   kubectl get deployment <name of deployment> -n azureml-<name of workspace> --show-labels
```

Voer de volgende opdracht uit om alle podstatussen te bekijken.

```azurecli-interactive
    kubectl get pods -n azureml-<name of workspace>
```

Zodra de pods operationeel zijn, kunnen de webservices voor deze implementatie nu toegang krijgen tot Azure-bronnen via uw Azure-identiteit zonder dat de referenties in uw code hoeven in te sluiten. 

## <a name="assign-the-appropriate-roles-to-your-azure-identity"></a>De juiste rollen toewijzen aan uw Azure-identiteit

[Wijs uw Azure Managed Identity toe met de juiste rollen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal) om toegang te krijgen tot andere Azure-bronnen. Controleer of de rollen die u toewijst, de juiste **gegevensacties hebben.** De rol [van de opslagblob-gegevenslezer](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-reader) heeft bijvoorbeeld leesmachtigingen voor uw Opslagblob, terwijl de algemene [lezersrol](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#reader) dat mogelijk niet doet.

## <a name="use-azure-identity-with-your-machine-learning-web-service"></a>Azure Identity gebruiken met uw webservice voor machine learning

Implementeer een model in uw AKS-cluster. Het `score.py` script kan bewerkingen bevatten die verwijzen naar de Azure-resources waartoe uw Azure Identity toegang heeft. Controleer of u de vereiste clientbibliotheekafhankelijkheden hebt geïnstalleerd voor de bron die u probeert te openen. Hieronder vindt u een paar voorbeelden van hoe u uw Azure-identiteit gebruiken om toegang te krijgen tot verschillende Azure-bronnen van uw service.

### <a name="access-key-vault-from-your-web-service"></a>Toegang tot Key Vault vanuit uw webservice

Als u uw Azure Identity leestoegang hebt gegeven tot `score.py` een geheim in een **Key Vault,** u deze openen met de volgende code.

```python
from azure.identity import DefaultAzureCredential
from azure.keyvault.secrets import SecretClient

my_vault_name = "yourkeyvaultname"
my_vault_url = "https://{}.vault.azure.net/".format(my_vault_name) 
my_secret_name = "sample-secret"

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
secret_client = SecretClient(
    vault_url=my_vault_url,
    credential=credential)
secret = secret_client.get_secret(my_secret_name)
```

### <a name="access-blob-from-your-web-service"></a>Blob openen vanuit uw webservice

Als u uw Azure Identity leestoegang hebt gegeven `score.py` tot gegevens in een **Opslagblob,** u deze openen met de volgende code.

```python
from azure.identity import DefaultAzureCredential
from azure.storage.blob import BlobServiceClient

my_storage_account_name = "yourstorageaccountname"
my_storage_account_url = "https://{}.blob.core.windows.net/".format(my_storage_account_name)

# This will use your Azure Managed Identity
credential = DefaultAzureCredential()
blob_service_client = BlobServiceClient(
    account_url=my_storage_account_url,
    credential=credential
)
blob_client = blob_service_client.get_blob_client(container="some-container", blob="some_text.txt")
blob_data = blob_client.download_blob()
blob_data.readall()
```

## <a name="next-steps"></a>Volgende stappen

* Zie de [opslagplaats](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/identity/azure-identity#azure-identity-client-library-for-python) op GitHub voor meer informatie over het gebruik van de Python Azure Identity-clientbibliotheek.
* Zie de [how-to](how-to-deploy-azure-kubernetes-service.md).
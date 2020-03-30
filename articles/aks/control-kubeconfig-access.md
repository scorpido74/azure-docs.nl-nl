---
title: Toegang tot kubeconfig beperken in Azure Kubernetes Service (AKS)
description: Meer informatie over het beheren van de toegang tot het Kubernetes-configuratiebestand (kubeconfig) voor clusterbeheerders en clustergebruikers
services: container-service
ms.topic: article
ms.date: 01/28/2020
ms.openlocfilehash: 25c710cce2855d6af985d3f46082f47573bbc101
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79259550"
---
# <a name="use-azure-role-based-access-controls-to-define-access-to-the-kubernetes-configuration-file-in-azure-kubernetes-service-aks"></a>Op Azure-rolegebaseerde toegangsbesturingselementen gebruiken om de toegang tot het Kubernetes-configuratiebestand te definiëren in Azure Kubernetes Service (AKS)

U communiceren met `kubectl` Kubernetes-clusters met behulp van de tool. De Azure CLI biedt een eenvoudige manier om de toegangsreferenties en configuratie-informatie te krijgen om verbinding te maken met uw AKS-clusters met behulp van `kubectl`. Om te beperken wie die Kubernetes-configuratie *(kubeconfig)* informatie kan krijgen en om de machtigingen die ze vervolgens hebben te beperken, u Op Azure-role-based access controls (RBAC) gebruik maken.

In dit artikel ziet u hoe u RBAC-rollen toewijzen die beperken wie de configuratiegegevens voor een AKS-cluster kan krijgen.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

In dit artikel moet u ook de Azure CLI-versie 2.0.65 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

## <a name="available-cluster-roles-permissions"></a>Beschikbare machtigingen voor clusterrollen

Wanneer u met het `kubectl` hulpprogramma interactie hebt met een AKS-cluster, wordt een configuratiebestand gebruikt dat clusterverbindingsgegevens definieert. Dit configuratiebestand wordt meestal opgeslagen in *~/.kube/config*. Meerdere clusters kunnen worden gedefinieerd in dit *kubeconfig-bestand.* U schakelt tussen clusters met behulp van de opdracht [kubectl config use-context.][kubectl-config-use-context]

Met de opdracht [AZ AKS get-credentials][az-aks-get-credentials] u de toegangsreferenties voor een AKS-cluster krijgen en deze samenvoegen tot het *kubeconfig-bestand.* U RBAC (Azure role-based access controls) gebruiken om de toegang tot deze referenties te beheren. Met deze Azure RBAC-rollen u bepalen wie het *kubeconfig-bestand* kan ophalen en welke machtigingen ze vervolgens binnen het cluster hebben.

De twee ingebouwde rollen zijn:

* **Azure Kubernetes Service Cluster Admin-rol**  
  * Hiermee u toegang krijgen tot *Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action* API-aanroep. Deze API-aanroep [bevat de clusterbeheerdersreferenties][api-cluster-admin].
  * Downloadt *kubeconfig* voor de *clusterAdmin-rol.*
* **Gebruikersrol Azure Kubernetes Service Cluster**
  * Hiermee u toegang krijgen tot *Microsoft.ContainerService/managedClusters/listClusterUserCredential/action* API-aanroep. Deze API-aanroep [bevat de referenties van de clustergebruiker][api-cluster-user].
  * Downloadt *kubeconfig* voor *clusterGebruikersrol.*

Deze RBAC-rollen kunnen worden toegepast op een Gebruiker of groep van Azure Active Directory (AD).

> ! [OPMERKING] Op clusters die Azure AD gebruiken, hebben gebruikers met de *clusterGebruikersrol* een leeg *kubeconfig-bestand* dat een aanmelding vraagt. Eenmaal ingelogd hebben gebruikers toegang op basis van hun Azure AD-gebruikers- of groepsinstellingen. Gebruikers met de *clusterAdmin-rol* hebben beheerderstoegang.
>
> Clusters die Azure AD niet gebruiken, gebruiken alleen de *clusterAdmin-rol.*

## <a name="assign-role-permissions-to-a-user-or-group"></a>Rolmachtigingen toewijzen aan een gebruiker of groep

Als u een van de beschikbare rollen wilt toewijzen, moet u de bron-id van het AKS-cluster en de id van het Azure AD-gebruikersaccount of -groep oppakken. De volgende voorbeeldopdrachten:

* Haal de clusterbron-id op met de opdracht [az aks show][az-aks-show] voor het cluster met de naam *myAKSCluster* in de *brongroep myResourceGroup.* Geef indien nodig uw eigen cluster- en resourcegroepnaam op.
* Gebruik de [az-accountshow][az-account-show] en [az-advertentiegebruikers tonen][az-ad-user-show] opdrachten om je gebruikersnaam te krijgen.
* Wijs ten slotte een rol toe met de opdracht [Az-roltoewijzing maken.][az-role-assignment-create]

In het volgende voorbeeld wordt de *Azure Kubernetes Service Cluster-beheerrol* aan een afzonderlijk gebruikersaccount toegevoegd:

```azurecli-interactive
# Get the resource ID of your AKS cluster
AKS_CLUSTER=$(az aks show --resource-group myResourceGroup --name myAKSCluster --query id -o tsv)

# Get the account credentials for the logged in user
ACCOUNT_UPN=$(az account show --query user.name -o tsv)
ACCOUNT_ID=$(az ad user show --id $ACCOUNT_UPN --query objectId -o tsv)

# Assign the 'Cluster Admin' role to the user
az role assignment create \
    --assignee $ACCOUNT_ID \
    --scope $AKS_CLUSTER \
    --role "Azure Kubernetes Service Cluster Admin Role"
```

> [!TIP]
> Als u machtigingen wilt toewijzen aan een Azure `--assignee` AD-groep, werkt u de parameter in het vorige voorbeeld bij met de object-id voor de *groep* in plaats van aan een *gebruiker*. Als u de object-id voor een groep wilt verkrijgen, gebruikt u de opdracht [az-advertentiegroep weergeven.][az-ad-group-show] In het volgende voorbeeld wordt de object-id voor de Azure AD-groep met de naam *appdev:*`az ad group show --group appdev --query objectId -o tsv`

U de vorige toewijzing naar behoefte wijzigen in de *clustergebruikersrol.*

In de volgende voorbeelduitvoerwordt weergegeven dat de roltoewijzing is gemaakt:

```
{
  "canDelegate": null,
  "id": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/providers/Microsoft.Authorization/roleAssignments/b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "name": "b2712174-5a41-4ecb-82c5-12b8ad43d4fb",
  "principalId": "946016dd-9362-4183-b17d-4c416d1f8f61",
  "resourceGroup": "myResourceGroup",
  "roleDefinitionId": "/subscriptions/<guid>/providers/Microsoft.Authorization/roleDefinitions/0ab01a8-8aac-4efd-b8c2-3ee1fb270be8",
  "scope": "/subscriptions/<guid>/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-and-verify-the-configuration-information"></a>De configuratiegegevens opvragen en verifiëren

Als RBAC-rollen zijn toegewezen, gebruikt u de opdracht [az aks-referenties][az-aks-get-credentials] om de *kubeconfig-definitie* voor uw AKS-cluster te krijgen. In het volgende voorbeeld worden de *---beheerdersreferenties* opgevraagd, die correct werken als de gebruiker de *clusterbeheerrol*heeft gekregen:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

U vervolgens de opdracht [kubectl config-weergave][kubectl-config-view] gebruiken om te controleren of de *context* voor het cluster aangeeft dat de beheerdersconfiguratiegegevens zijn toegepast:

```
$ kubectl config view

apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://myaksclust-myresourcegroup-19da35-4839be06.hcp.eastus.azmk8s.io:443
  name: myAKSCluster
contexts:
- context:
    cluster: myAKSCluster
    user: clusterAdmin_myResourceGroup_myAKSCluster
  name: myAKSCluster-admin
current-context: myAKSCluster-admin
kind: Config
preferences: {}
users:
- name: clusterAdmin_myResourceGroup_myAKSCluster
  user:
    client-certificate-data: REDACTED
    client-key-data: REDACTED
    token: e9f2f819a4496538b02cefff94e61d35
```

## <a name="remove-role-permissions"></a>Rolmachtigingen verwijderen

Als u roltoewijzingen wilt verwijderen, gebruikt u de opdracht [voor het verwijderen van az-rollentoewijzing.][az-role-assignment-delete] Geef de account-id en clusterbron-id op, zoals verkregen in de vorige opdrachten. Als u de rol toegewezen hebt aan een groep in plaats van aan `--assignee` een gebruiker, geeft u de juiste groepsobject-id op in plaats van accountobject-id voor de parameter:

```azurecli-interactive
az role assignment delete --assignee $ACCOUNT_ID --scope $AKS_CLUSTER
```

## <a name="next-steps"></a>Volgende stappen

Voor verbeterde beveiliging bij toegang tot AKS-clusters, [integreert u Azure Active Directory-verificatie.][aad-integration]

<!-- LINKS - external -->
[kubectl-config-use-context]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config
[kubectl-config-view]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#config

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[azure-rbac]: ../role-based-access-control/overview.md
[api-cluster-admin]: /rest/api/aks/managedclusters/listclusteradmincredentials
[api-cluster-user]: /rest/api/aks/managedclusters/listclusterusercredentials
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-role-assignment-delete]: /cli/azure/role/assignment#az-role-assignment-delete
[aad-integration]: azure-ad-integration.md
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show

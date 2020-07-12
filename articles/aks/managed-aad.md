---
title: Azure AD gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van Azure AD in azure Kubernetes service (AKS)
services: container-service
manager: gwallace
author: TomGeske
ms.topic: article
ms.date: 07/08/2020
ms.author: thomasge
ms.openlocfilehash: b30c5b0e81f4748d5e94c05d016be83163c1e78e
ms.sourcegitcommit: dabd9eb9925308d3c2404c3957e5c921408089da
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86251124"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>AKS-beheerde Azure Active Directory-integratie (preview-versie)

> [!NOTE]
> Bestaande AKS-clusters (Azure Kubernetes service) met Azure Active Directory-integratie (Azure AD) worden niet beïnvloed door de nieuwe, door AKS beheerde Azure AD-ervaring.

Azure AD-integratie met AKS is ontworpen om de Azure AD-integratie ervaring te vereenvoudigen, waar gebruikers eerder moesten een client-app, een server-app en de Azure AD-Tenant nodig hebben om Lees machtigingen te verlenen aan de Directory. In de nieuwe versie beheert de AKS-resource provider de client-en server-apps voor u.

## <a name="azure-ad-authentication-overview"></a>Overzicht van Azure AD-verificatie

Cluster beheerders kunnen op rollen gebaseerd toegangs beheer (RBAC) Kubernetes configureren op basis van de identiteit van een gebruiker of het lidmaatschap van de Directory groep. Azure AD-verificatie wordt geleverd voor AKS-clusters met OpenID Connect Connect. OpenID Connect Connect is een id-laag die boven op het OAuth 2,0-protocol is gebouwd. Voor meer informatie over OpenID Connect Connect raadpleegt u de [Open-ID Connect-documentatie][open-id-connect].

Meer informatie over de AAD-integratie stroom vindt u in de [documentatie over integratie concepten van Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

> [!IMPORTANT]
> AKS preview-functies zijn beschikbaar op self-service. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door de klant ondersteuning. Daarom zijn deze functies niet bedoeld voor productie gebruik. Zie de volgende ondersteunings artikelen voor meer informatie:
>
> - [AKS-ondersteunings beleid](support-policies.md)
> - [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

* Zoek de Tenant-ID van uw Azure-account door naar de Azure Portal te gaan en Azure Active Directory > eigenschappen te selecteren > Directory-ID

> [!Important]
> U moet Kubectl gebruiken met een minimum versie van 1,18

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.5.1 of hoger
- De 0.4.38-uitbrei ding AKS-preview
- Kubectl met een minimum versie van [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Gebruik de volgende Azure CLI-opdrachten om de uitbrei ding AKS-preview of later te installeren of bij te werken:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Als u kubectl wilt installeren, gebruikt u de volgende opdrachten:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Gebruik [deze instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl/) voor andere besturings systemen.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd**wordt weer gegeven. U kunt de registratie status controleren met behulp van de opdracht [AZ Feature List](/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Een AKS-cluster maken met Azure AD ingeschakeld

Maak een AKS-cluster met behulp van de volgende CLI-opdrachten.

Een Azure-resource groep maken:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

U kunt een bestaande Azure AD-groep gebruiken of een nieuwe maken. U hebt de object-ID voor uw Azure AD-groep nodig.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Gebruik de volgende opdracht om een nieuwe Azure AD-groep voor uw cluster beheerders te maken:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Een AKS-cluster maken en beheer toegang inschakelen voor uw Azure AD-groep

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Als u een Azure AD-cluster met AKS-beheer hebt gemaakt, is de volgende sectie in de antwoord tekst
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Het cluster wordt binnen een paar minuten gemaakt.

## <a name="access-an-azure-ad-enabled-cluster"></a>Toegang tot een Azure AD-cluster

U hebt de [Azure Kubernetes service-cluster gebruiker](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) ingebouwde rol nodig om de volgende stappen uit te voeren.

De gebruikers referenties ophalen voor toegang tot het cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Volg de instructies om u aan te melden.

Gebruik de opdracht kubectl Get nodes om knoop punten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Configureer [op rollen gebaseerde Access Control (RBAC)](./azure-ad-rbac.md) om extra beveiligings groepen voor uw clusters te configureren.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Problemen met toegang tot Azure AD oplossen

> [!Important]
> De normale Azure AD-groeps verificatie wordt omzeild met de stappen die hieronder worden beschreven. Gebruik ze alleen in een nood geval.

Als u permanent bent geblokkeerd door geen toegang te hebben tot een geldige Azure AD-groep die toegang heeft tot uw cluster, kunt u nog steeds de beheerders referenties verkrijgen voor toegang tot het cluster.

Als u deze stappen wilt uitvoeren, moet u toegang hebben tot de ingebouwde rol [Azure Kubernetes service cluster admin](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-sign-in-with-kubelogin"></a>Niet-interactief aanmelden met kubelogin

Er zijn een aantal niet-interactieve scenario's, zoals continue integratie pijplijnen, die momenteel niet beschikbaar zijn in kubectl. U kunt gebruiken [`kubelogin`](https://github.com/Azure/kubelogin) om toegang te krijgen tot het cluster met niet-interactieve Service-Principal-aanmelding.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [Azure RBAC-integratie voor Kubernetes-autorisatie][azure-rbac-integration]
* Meer informatie over [Azure AD-integratie met KUBERNETES RBAC][azure-ad-rbac].
* Gebruik [kubelogin](https://github.com/Azure/kubelogin) om toegang te krijgen tot functies voor Azure-verificatie die niet beschikbaar zijn in kubectl.
* Meer informatie over [AKS-en Kubernetes-identiteits concepten][aks-concepts-identity].
* Gebruik [Azure Resource Manager (arm)-Sjablonen][aks-arm-template] voor het maken van met AKS beheerde Azure AD-clusters.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

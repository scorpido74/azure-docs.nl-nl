---
title: Azure AD gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van Azure AD in azure Kubernetes service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: b121830192a2b88185bbbbc9a92934e51b32a61c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81114648"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Azure AD integreren in azure Kubernetes service (preview)

> [!Note]
> Bestaande AKS v1-clusters met AD-integratie worden niet beïnvloed door de nieuwe AKS v2-ervaring.

Azure AD-integratie met AKS v2 is ontworpen om de Azure AD-integratie met AKS v1-ervaring te vereenvoudigen, waar gebruikers moesten een client-app, een server-app en de Azure AD-Tenant nodig hebben om machtigingen voor het lezen van mappen te verlenen. In de nieuwe versie beheert de AKS-resource provider de client-en server-apps voor u.

## <a name="limitations"></a>Beperkingen

* U kunt momenteel geen upgrade uitvoeren van een bestaande Azure AD-AKS v1-cluster naar de v2-ervaring.

> [!IMPORTANT]
> AKS preview-functies zijn beschikbaar op self-service. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door de klant ondersteuning. Daarom zijn deze functies niet bedoeld voor productie gebruik. Zie de volgende ondersteunings artikelen voor meer informatie:
>
> - [AKS-ondersteunings beleid](support-policies.md)
> - [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.2.0 of hoger
- De 0.4.38-uitbrei ding AKS-preview
- Kubectl met een minimum versie van [1,18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Gebruik de volgende Azure CLI-opdrachten om de uitbrei ding AKS-preview of later te installeren of bij te werken:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Als u kubectl wilt installeren, gebruikt u het volgende:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Gebruik [deze instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl/) voor andere besturings systemen.

> [!CAUTION]
> Nadat u een functie voor een abonnement hebt geregistreerd, kunt u de registratie van die functie op dit moment niet ongedaan maken. Wanneer u een aantal preview-functies inschakelt, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die later in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik in plaats daarvan een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd**wordt weer gegeven. U kunt de registratie status controleren met behulp van de opdracht [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt `Microsoft.ContainerService` u de registratie van de resource provider met behulp van de opdracht [AZ provider REGI ster](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Een AKS-cluster maken met Azure AD ingeschakeld

U kunt nu een AKS-cluster maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-resource groep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Met de bovenstaande opdracht maakt u een AKS-cluster met drie knoop punten, maar de gebruiker die het cluster heeft gemaakt, is standaard geen lid van een groep die toegang heeft tot dit cluster. Deze gebruiker moet een Azure AD-groep maken, toevoegen als lid van de groep en vervolgens het cluster bijwerken zoals hieronder wordt weer gegeven. Volg de instructies [hier](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Zodra u een groep hebt gemaakt en uzelf (en andere) hebt toegevoegd als lid, kunt u het cluster met de Azure AD-groep bijwerken met de volgende opdracht

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Als u eerst een groep maakt en leden toevoegt, kunt u de Azure AD-groep op het moment van maken inschakelen met de volgende opdracht:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Als u een Azure AD v2-cluster hebt gemaakt, is de volgende sectie in de antwoord tekst
```
"Azure ADProfile": {
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
De beheerders referenties ophalen voor toegang tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Gebruik nu de kubectl Get nodes opdracht om knoop punten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

De gebruikers referenties voor toegang tot het cluster ophalen:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Volg de instructies om u aan te melden.

U ontvangt: **u moet zijn aangemeld bij de server (niet gemachtigd)**

De bovenstaande gebruiker krijgt een fout melding omdat de gebruiker geen deel uitmaakt van een groep die toegang heeft tot het cluster.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de [Azure AD-rol op basis van Access Control][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md

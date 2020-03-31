---
title: Azure AD gebruiken in Azure Kubernetes-service
description: Meer informatie over het gebruik van Azure AD in Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 03/24/2020
ms.openlocfilehash: 1ca4b70139ed5e0a136f6f5f2b0382b8c1688983
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80389406"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Azure AD integreren in Azure Kubernetes-service (voorbeeld)

> [!Note]
> Bestaande AKS v1-clusters met AD-integratie worden niet beïnvloed door de nieuwe AKS v2-ervaring.

Azure AD-integratie met AKS v2 is ontworpen om de Azure AD-integratie met AKS v1-ervaring te vereenvoudigen, waarbij gebruikers een client-app, een server-app moesten maken en de Azure AD-tenant verplichtom machtigingen voor directorylezen toe te kennen. In de nieuwe versie beheert de AKS-resourceprovider de client- en server-apps voor u.

## <a name="limitations"></a>Beperkingen

* U momenteel een bestaand AKS v1-cluster met Azure AD niet upgraden naar de v2-ervaring.

> [!IMPORTANT]
> AKS preview-functies zijn beschikbaar op basis van selfservice en opt-in. Previews worden 'as-is' en 'as available' aangeboden en zijn uitgesloten van de serviceniveauovereenkomsten en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klantenondersteuning op basis van de beste inspanning. Als zodanig zijn deze functies niet bedoeld voor productiegebruik. Zie de volgende ondersteuningsartikelen voor meer informatie:
>
> - [AKS-ondersteuningsbeleid](support-policies.md)
> - [Veelgestelde vragen over Azure Support](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende bronnen hebben geïnstalleerd:

- De Azure CLI, versie 2.2.0 of hoger
- De aks-preview 0.4.38 extensie
- Kubectl met een minimale versie van [1.18 beta](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#client-binaries)

Als u de aks-preview-extensie of hoger wilt installeren/bijwerken, gebruikt u de volgende Azure CLI-opdrachten:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Als u kubectl wilt installeren, gebruikt u de volgende
```azurecli
curl -LO "https://storage.googleapis.com/kubernetes-release/release/v1.18.0-beta.2/bin/linux/amd64/kubectl"
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin/kubectl
kubectl version --client
```

Gebruik [deze instructies](https://kubernetes.io/docs/tasks/tools/install-kubectl/) voor andere besturingssystemen.

> [!CAUTION]
> Nadat u een functie bij een abonnement hebt geregistreerd, u deze functie momenteel niet uitschrijven. Wanneer u een aantal voorbeeldfuncties inschakelt, kunnen standaardinstellingen worden gebruikt voor alle AKS-clusters die daarna in het abonnement zijn gemaakt. Schakel geen voorbeeldfuncties in op productieabonnementen. Gebruik in plaats daarvan een apart abonnement om preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status wordt weergegeven als **Geregistreerd.** U de registratiestatus controleren met de opdracht [az-functielijst:](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list)

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weergegeven als `Microsoft.ContainerService` geregistreerd, vernieuwt u de registratie van de resourceprovider met behulp van de opdracht [AZ-providerregister:](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register)

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Een AKS-cluster maken met Azure AD ingeschakeld

U nu een AKS-cluster maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-brongroep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Met de bovenstaande opdracht wordt een AKS-cluster met drie nodes gemaakt, maar de gebruiker die het cluster heeft gemaakt, is geen lid van een groep die toegang heeft tot dit cluster. Deze gebruiker moet een Azure AD-groep maken, zichzelf toevoegen als lid van de groep en vervolgens het cluster bijwerken zoals hieronder wordt weergegeven. Volg [hier](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) de instructies

Zodra u een groep hebt gemaakt en uzelf (en anderen) als lid hebt toegevoegd, u het cluster bijwerken met de Azure AD-groep met behulp van de volgende opdracht

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```
Als u eerst een groep maakt en leden toevoegt, u de Azure AD-groep op het moment van het maken inschakelen met de volgende opdracht.

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id1,id2>] [--aad-tenant-id <id>]
```

Een succesvolle creatie van een Azure AD v2-cluster heeft de volgende sectie in de antwoordgroep
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

Het cluster wordt binnen enkele minuten gemaakt.

## <a name="access-an-azure-ad-enabled-cluster"></a>Toegang tot een Azure AD-cluster
Ga als u de beheerdersreferenties op de getint krijgen om toegang te krijgen tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Gebruik nu de opdracht kubectl get nodes om knooppunten in het cluster weer te geven:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Ga als u de gebruikersreferenties opdeiert om toegang te krijgen tot het cluster:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Volg de instructies om in te loggen.

U ontvangt: **U moet ingelogd zijn op de server (zonder toestemming)**

De gebruiker hierboven krijgt een foutmelding omdat de gebruiker geen deel uitmaakt van een groep die toegang heeft tot het cluster.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Azure AD-rolebased access control][azure-ad-rbac].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md

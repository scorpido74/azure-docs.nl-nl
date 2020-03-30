---
title: De referenties voor een AKS-cluster (Azure Kubernetes Service) opnieuw instellen
description: Informatie over hoe de serviceprincipal- of AAD-toepassingsreferenties voor een AKS-cluster (Azure Kubernetes Service) bijwerken of opnieuw instellen
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: b7d652be3733cb130a3973909de59489047efe0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475541"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>De referenties voor Azure Kubernetes Service (AKS) bijwerken of roteren

Standaard worden AKS-clusters gemaakt met een serviceprincipal met een vervaldatum van één jaar. Als u in de buurt van de vervaldatum bent, u de referenties opnieuw instellen om de serviceprincipal voor een extra periode te verlengen. U de referenties ook bijwerken of roteren als onderdeel van een gedefinieerd beveiligingsbeleid. In dit artikel wordt beschreven hoe u deze referenties voor een AKS-cluster bijwerken.

Mogelijk hebt u [uw AKS-cluster][aad-integration]ook geïntegreerd met Azure Active Directory en gebruikt u het als verificatieprovider voor uw cluster. In dat geval hebt u nog 2 identiteiten gemaakt voor uw cluster, de AAD Server App en de AAD Client App, u deze referenties ook opnieuw instellen. 

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.65 of hoger installeren en configureren. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Een nieuwe serviceprincipal voor uw AKS-cluster bijwerken of maken

Wanneer u de referenties voor een AKS-cluster wilt bijwerken, u ervoor kiezen om:

* de referenties voor de bestaande serviceprincipal bijwerken die door het cluster wordt gebruikt, of
* maak een serviceprincipal en werk het cluster bij om deze nieuwe referenties te gebruiken.

### <a name="reset-existing-service-principal-credential"></a>Bestaande serviceprincipalreferentie opnieuw instellen

Als u de referenties voor de bestaande serviceprincipal wilt bijwerken, ontvangt u de servicehoofd-id van uw cluster met de opdracht [az aks show.][az-aks-show] In het volgende voorbeeld wordt de id voor het cluster met de naam *myAKSCluster* in de *brongroep myResourceGroup* opdekweg. De serviceprincipal-id is ingesteld als een variabele met de naam *SP_ID* voor gebruik in extra opdracht.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Met een variabele set die de service principal ID bevat, u nu de referenties opnieuw instellen met behulp van [az ad sp credential reset][az-ad-sp-credential-reset]. In het volgende voorbeeld kan het Azure-platform een nieuw veilig geheim genereren voor de serviceprincipal. Dit nieuwe veilige geheim wordt ook opgeslagen als een variabele.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Ga nu verder met het [bijwerken van het AKS-cluster met nieuwe servicehoofdreferenties.](#update-aks-cluster-with-new-service-principal-credentials) Deze stap is noodzakelijk om de wijzigingen van de Service Principal over het AKS-cluster te laten nadenken.

### <a name="create-a-new-service-principal"></a>Een nieuwe serviceprincipal maken

Als u ervoor kiest om de bestaande servicehoofdreferenties in de vorige sectie bij te werken, slaat u deze stap over. Doorgaan met [het bijwerken van het AKS-cluster met nieuwe servicehoofdreferenties](#update-aks-cluster-with-new-service-principal-credentials).

Als u een serviceprincipal wilt maken en vervolgens het AKS-cluster wilt bijwerken om deze nieuwe referenties te gebruiken, gebruikt u de opdracht [AZ Ad SP create-for-rbac.][az-ad-sp-create] In het volgende voorbeeld wordt met de parameter `--skip-assignment` voorkomen dat eventuele extra standaardtoewijzingen worden toegewezen:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

De uitvoer lijkt op die in het volgende voorbeeld. Noteer uw eigen `appId` en `password`. Deze waarden worden gebruikt in de volgende stap.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Definieer nu variabelen voor de serviceprincipal-id en het clientgeheim met behulp van de uitvoer van uw eigen [az-ad sp create-for-rbac-opdracht,][az-ad-sp-create] zoals in het volgende voorbeeld wordt weergegeven. De *SP_ID* is uw *appId,* en de *SP_SECRET* is uw *wachtwoord:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Ga nu verder met het [bijwerken van het AKS-cluster met nieuwe servicehoofdreferenties.](#update-aks-cluster-with-new-service-principal-credentials) Deze stap is noodzakelijk om de wijzigingen van de Service Principal over het AKS-cluster te laten nadenken.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>AKS-cluster bijwerken met nieuwe serviceprincipal-referenties

Ongeacht of u ervoor hebt gekozen om de referenties voor de bestaande serviceprincipal bij te werken of een serviceprincipal te maken, u werkt het AKS-cluster nu bij met uw nieuwe referenties met behulp van de opdracht [az aks-updatereferenties.][az-aks-update-credentials] De variabelen voor het *--service-principal* en *--client-secret* worden gebruikt:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Het duurt even voordat de servicehoofdreferenties in de AKS worden bijgewerkt.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>AKS-cluster bijwerken met nieuwe AAD-toepassingsreferenties

U nieuwe AAD Server- en clienttoepassingen maken door de [AAD-integratiestappen][create-aad-app]te volgen. Of stel uw bestaande AAD-toepassingen opnieuw in [volgens dezelfde methode als voor serviceprincipal reset.](#reset-existing-service-principal-credential) Daarna hoeft u alleen nog maar uw cluster AAD Application referenties bij te werken met behulp van dezelfde [az aks update-referenties][az-aks-update-credentials] commando, maar met behulp van de *--reset-aad* variabelen.

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Volgende stappen

In dit artikel zijn de serviceprincipal voor het AKS-cluster zelf en de AAD Integration Applications bijgewerkt. Zie Aanbevolen procedures voor verificatie en autorisatie in [AKS voor][best-practices-identity]meer informatie over het beheren van identiteit voor workloads binnen een cluster.

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset

---
title: Beheerde identiteiten gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van beheerde identiteiten in azure Kubernetes service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 00ecc077ba55ab9f91fc58f8a47fcdf7440deea6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82112963"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Beheerde identiteiten gebruiken in azure Kubernetes service

Op dit moment vereist een Azure Kubernetes service (AKS)-cluster (met name de Kubernetes-Cloud provider) een identiteit voor het maken van extra resources, zoals load balancers en beheerde schijven in azure, deze identiteit kan een *beheerde identiteit* of een *Service-Principal*zijn. Als u een [Service-Principal](kubernetes-service-principal.md)gebruikt, moet u één of AKS in uw naam maken. Als u een beheerde identiteit gebruikt, wordt deze automatisch voor u gemaakt door AKS. Clusters die service-principals gebruiken, bereiken uiteindelijk een status waarin de Service-Principal moet worden vernieuwd om het cluster te laten functioneren. Het beheren van service-principals voegt complexiteit toe. Daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Dezelfde machtigings vereisten gelden voor service-principals en beheerde identiteiten.

*Beheerde identiteiten* zijn in feite een wrapper rond service-principals en maken hun beheer eenvoudiger. Meer informatie over [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)vindt u hier.

AKS maakt twee beheerde identiteiten:

- Door het **systeem toegewezen beheerde identiteit**: de identiteit die de Kubernetes-Cloud provider gebruikt om Azure-resources namens de gebruiker te maken. De levens cyclus van de door het systeem toegewezen identiteit is gekoppeld aan die van het cluster. De identiteit wordt verwijderd wanneer het cluster wordt verwijderd.
- Door de **gebruiker toegewezen beheerde identiteit**: de identiteit die wordt gebruikt voor autorisatie in het cluster. De door de gebruiker toegewezen identiteit wordt bijvoorbeeld gebruikt om AKS te autoriseren voor het gebruik van Azure-container registers (ACRs), of om de kubelet te autoriseren om meta gegevens op te halen uit Azure.

Invoeg toepassingen verifiëren ook met behulp van een beheerde identiteit. Voor elke invoeg toepassing wordt een beheerde identiteit gemaakt door AKS en de laatste tijd voor de levens duur van de invoeg toepassing. 

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende bron hebben geïnstalleerd:

- De Azure CLI, versie 2.2.0 of hoger

## <a name="create-an-aks-cluster-with-managed-identities"></a>Een AKS-cluster maken met beheerde identiteiten

U kunt nu een AKS-cluster met beheerde identiteiten maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-resource groep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Een succes volle cluster maken met behulp van beheerde identiteiten bevat deze service-principal-profiel informatie:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Voor het maken en gebruiken van uw eigen VNet, een statisch IP-adres of een gekoppelde Azure-schijf waar de resources zich buiten de resource groep MC_ * bevinden, gebruikt u de PrincipalID van het cluster systeem toegewezen beheerde identiteit voor het uitvoeren van een roltoewijzing. Zie [toegang tot andere Azure-resources delegeren](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)voor meer informatie over het toewijzen van rollen.
>
> Machtigings verlening voor door de cluster beheerde identiteit die wordt gebruikt door de Azure-Cloud provider, kan 60 minuten duren om in te vullen.

Haal tot slot referenties op voor toegang tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Het cluster wordt in een paar minuten gemaakt. U kunt vervolgens de werk belasting van uw toepassing implementeren naar het nieuwe cluster en ermee werken, net zoals u dat hebt gedaan met AKS-clusters op basis van de Service-Principal.

> [!IMPORTANT]
>
> - AKS-clusters met beheerde identiteiten kunnen alleen worden ingeschakeld tijdens het maken van het cluster.
> - Bestaande AKS-clusters kunnen niet worden bijgewerkt of bijgewerkt om beheerde identiteiten in te scha kelen.

---
title: Beheerde identiteiten gebruiken in Azure Kubernetes Service
description: Meer informatie over het gebruik van beheerde identiteiten in Azure Kubernetes Service (AKS)
services: container-service
author: saudas
manager: saudas
ms.topic: article
ms.date: 04/02/2020
ms.author: saudas
ms.openlocfilehash: 7a71d3bd70d97df884f1bc962c0ef9897d7fd2cb
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024401"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Beheerde identiteiten gebruiken in Azure Kubernetes Service

Op dit moment vereist een AKS-cluster (Azure Kubernetes Service) (met name de Kubernetes-cloudprovider) een identiteit om extra bronnen te maken, zoals load balancers en beheerde schijven in Azure, deze identiteit kan een *beheerde identiteit* of een *serviceprincipal*zijn. Als u een [serviceprincipal](kubernetes-service-principal.md)gebruikt, moet u er een opgeven of maakt AKS er een namens u. Als u beheerde identiteit gebruikt, wordt dit automatisch voor u gemaakt door AKS. Clusters met serviceprincipals bereiken uiteindelijk een status waarin de serviceprincipal moet worden vernieuwd om het cluster te laten werken. Het beheren van serviceprincipals voegt complexiteit toe, daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Voor zowel serviceprincipals als beheerde identiteiten gelden dezelfde machtigingsvereisten.

*Beheerde identiteiten* zijn in wezen een wrapper rond service principals, en maken hun beheer eenvoudiger. Lees voor meer informatie over [beheerde identiteiten voor Azure-bronnen](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS creëert twee beheerde identiteiten:

- **Beheerde identiteit met systeemtoegewezen :** de identiteit die de Kubernetes-cloudprovider gebruikt om Azure-bronnen namens de gebruiker te maken. De levenscyclus van de door het systeem toegewezen identiteit is gekoppeld aan die van het cluster. De identiteit wordt verwijderd wanneer het cluster wordt verwijderd.
- **Door de gebruiker toegewezen beheerde identiteit:** de identiteit die wordt gebruikt voor autorisatie in het cluster. De door de gebruiker toegewezen identiteit wordt bijvoorbeeld gebruikt om AKS te autoriseren om Azure Container Registries (ADR's) te gebruiken of om de kubelet te autoriseren om metagegevens van Azure te krijgen.

Invoegtoepassingen verifiëren ook met een beheerde identiteit. Voor elke add-on wordt een beheerde identiteit gecreëerd door AKS en duurt het de levensduur van de add-on. 

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende bron hebben geïnstalleerd:

- De Azure CLI, versie 2.2.0 of hoger

## <a name="create-an-aks-cluster-with-managed-identities"></a>Een AKS-cluster maken met beheerde identiteiten

U nu een AKS-cluster met beheerde identiteiten maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-brongroep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Een succesvol clustermaken met beheerde identiteiten bevat de belangrijkste profielinformatie van de service:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Voor het maken en gebruiken van uw eigen VNet, statisch IP-adres of gekoppelde Azure-schijf waarbij de resources zich buiten de resourcegroep MC_* bevinden, gebruikt u de PrincipalID van de beheerde identiteit van het clustersysteem toegewezen om een roltoewijzing uit te voeren. Zie Toegang tot andere [Azure-bronnen delegeren voor](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)meer informatie over roltoewijzing.

Tot slot u referenties opdoen om toegang te krijgen tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Het cluster wordt binnen enkele minuten gemaakt. U vervolgens uw toepassingsworkloads implementeren in het nieuwe cluster en ermee communiceren, net zoals u hebt gedaan met AKS-clusters op basis van serviceprincipal.

> [!IMPORTANT]
>
> - AKS-clusters met beheerde identiteiten kunnen alleen worden ingeschakeld tijdens het maken van het cluster.
> - Bestaande AKS-clusters kunnen niet worden bijgewerkt of geüpgraded om beheerde identiteiten in te schakelen.

---
title: Beheerde identiteiten gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van beheerde identiteiten in azure Kubernetes service (AKS)
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121432"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Voor beeld: beheerde identiteiten gebruiken in azure Kubernetes service

Momenteel vereist een Azure Kubernetes service (AKS)-cluster (met name de Kubernetes-Cloud provider) een *Service-Principal* voor het maken van extra resources, zoals load balancers en beheerde schijven in Azure. Ofwel moet u een service-principal of AKS in uw naam maken. Service-principals hebben doorgaans een verval datum. Clusters bereiken uiteindelijk een status waarin de Service-Principal moet worden vernieuwd om het cluster te laten functioneren. Het beheren van service-principals voegt complexiteit toe.

*Beheerde identiteiten* zijn in feite een wrapper rond service-principals en maken hun beheer eenvoudiger. Meer informatie over [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)vindt u hier.

AKS maakt twee beheerde identiteiten:

- Door het **systeem toegewezen beheerde identiteit**: de identiteit die de Kubernetes-Cloud provider gebruikt om Azure-resources namens de gebruiker te maken. De levens cyclus van de door het systeem toegewezen identiteit is gekoppeld aan die van het cluster. De identiteit wordt verwijderd wanneer het cluster wordt verwijderd.
- Door de **gebruiker toegewezen beheerde identiteit**: de identiteit die wordt gebruikt voor autorisatie in het cluster. De door de gebruiker toegewezen identiteit wordt bijvoorbeeld gebruikt om AKS te autoriseren voor het gebruik van Access Control records (ACRs), of om de kubelet te autoriseren om meta gegevens op te halen uit Azure.

In deze preview-periode is een Service-Principal nog vereist. Het wordt gebruikt voor de autorisatie van invoeg toepassingen, zoals bewaking, virtuele knoop punten, Azure Policy en HTTP-toepassings routering. Er wordt een probleem ondervindt bij het verwijderen van de afhankelijkheid van invoeg toepassingen op de Service Principal Name (SPN). Uiteindelijk wordt de vereiste van een SPN in AKS volledig verwijderd.

> [!IMPORTANT]
> AKS preview-functies zijn beschikbaar op self-service. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door de klant ondersteuning. Daarom zijn deze functies niet bedoeld voor productie gebruik. Zie de volgende ondersteunings artikelen voor meer informatie:
>
> - [AKS-ondersteunings beleid](support-policies.md)
> - [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende resources hebben geïnstalleerd:

- De Azure CLI, versie 2.0.70 of hoger
- De 0.4.14-uitbrei ding AKS-preview

Gebruik de volgende Azure CLI-opdrachten om de 0.4.14-uitbrei ding AKS-preview of hoger te installeren:

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> Nadat u een functie voor een abonnement hebt geregistreerd, kunt u de registratie van die functie op dit moment niet ongedaan maken. Wanneer u een aantal preview-functies inschakelt, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die later in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik in plaats daarvan een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status als **geregistreerd**wordt weer gegeven. U kunt de registratie status controleren met behulp van de opdracht [AZ Feature List](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Wanneer de status wordt weer gegeven als geregistreerd, vernieuwt u de registratie van de `Microsoft.ContainerService` resource provider met behulp van de opdracht [AZ provider REGI ster](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

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

Haal tot slot referenties op voor toegang tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Het cluster wordt in een paar minuten gemaakt. U kunt vervolgens de werk belasting van uw toepassing implementeren naar het nieuwe cluster en ermee werken, net zoals u dat hebt gedaan met AKS-clusters op basis van de Service-Principal.

> [!IMPORTANT]
>
> - AKS-clusters met beheerde identiteiten kunnen alleen worden ingeschakeld tijdens het maken van het cluster.
> - Bestaande AKS-clusters kunnen niet worden bijgewerkt of bijgewerkt om beheerde identiteiten in te scha kelen.

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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827548"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Voor beeld: beheerde identiteiten gebruiken in azure Kubernetes service

Op dit moment moeten gebruikers een Service-Principal opgeven, of AKS deze namens u maken voor het AKS-cluster (met name de Kubernetes-Cloud provider) om extra resources, zoals load balancers en beheerde schijven in azure, te creëren. Service-principals worden doorgaans gemaakt met een verval datum. Clusters zullen uiteindelijk een toestand bereiken waarin de Service-Principal anders moet worden vernieuwd. het cluster werkt dan niet. Het beheren van service-principals voegt complexiteit toe. Beheerde identiteiten zijn in feite een wrapper rond service-principals en maken hun beheer eenvoudiger. Meer informatie over [Managed Identities](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) vindt u in het artikel.

AKS maakt twee beheerde identiteiten één door het systeem toegewezen beheerde identiteit en de andere door de gebruiker toegewezen identiteit. Een door het systeem toegewezen beheerde identiteit wordt gebruikt door de Cloud provider kubernetes om Azure-resources namens de gebruiker te maken. De levens cyclus van deze door het systeem toegewezen beheerde identiteit is gekoppeld aan dat van het cluster en wordt verwijderd wanneer het cluster wordt verwijderd. AKS maakt ook een door de gebruiker toegewezen beheerde identiteit die in het cluster wordt gebruikt voor het autoriseren van AKS om toegang te krijgen tot ACRs, kubelet om meta gegevens op te halen van Azure, enzovoort.

In deze preview-periode is een Service-Principal nog vereist. Het wordt gebruikt voor de autorisatie van invoeg toepassingen, zoals bewaking, virtueel knoop punt, Azure-beleid en http-toepassings routering. Er wordt voortdurend gewerkt aan het verwijderen van de afhankelijkheid van de invoeg toepassingen op de SPN en uiteindelijk de SPN-vereiste in AKS wordt volledig verwijderd.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid](support-policies.md)
> * [Veelgestelde vragen over ondersteuning voor Azure](faq.md)

## <a name="before-you-begin"></a>Voordat u begint

U moet het volgende hebben:

* U hebt ook de Azure CLI-versie 2.0.70 of hoger nodig en de AKS-Preview 0.4.14-extensie

## <a name="install-latest-aks-cli-preview-extension"></a>De meest recente AKS CLI-preview-extensie installeren

U hebt de **AKS-Preview 0.4.14-** uitbrei ding of hoger nodig.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Wanneer u een functie op een abonnement registreert, kunt u de registratie van die functie op dit moment niet ongedaan maken. Nadat u enkele preview-functies hebt ingeschakeld, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Het kan enkele minuten duren voordat de status *geregistreerd*wordt weer gegeven. U kunt de registratie status controleren met behulp van de opdracht [AZ Feature List] [AZ-Feature-List]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Wanneer de status is geregistreerd, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster] [AZ-provider-REGI ster]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Een AKS-cluster maken met beheerde identiteit

U kunt nu een AKS-cluster met beheerde identiteiten maken met behulp van de volgende CLI-opdracht
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Een AKS-cluster maken
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Referenties voor toegang tot het cluster ophalen
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Zodra het cluster binnen een paar minuten is gemaakt, kunt u de werk belasting van uw toepassing implementeren en ermee werken, net zoals bij AKS-clusters op basis van de Service-Principal. 

> [!IMPORTANT]
> * AKS-clusters met beheerde identiteiten kunnen alleen worden ingeschakeld tijdens het maken van het cluster
> * Bestaande AKS-clusters kunnen niet worden bijgewerkt/bijgewerkt om beheerde identiteiten in te scha kelen
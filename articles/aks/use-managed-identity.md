---
title: Beheerde identiteiten gebruiken in azure Kubernetes service
description: Meer informatie over het gebruik van beheerde identiteiten in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 07/10/2020
ms.author: mlearned
ms.openlocfilehash: 27ae1d1a2c6309bdac2410dca4b48abf27d8ea0b
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86231978"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Beheerde identiteiten gebruiken in azure Kubernetes service

Op dit moment vereist een Azure Kubernetes service (AKS)-cluster (met name de Kubernetes-Cloud provider) een identiteit voor het maken van extra resources, zoals load balancers en beheerde schijven in Azure. Deze identiteit kan een *beheerde identiteit* of een service- *Principal*zijn. Als u een [Service-Principal](kubernetes-service-principal.md)gebruikt, moet u één of AKS in uw naam maken. Als u een beheerde identiteit gebruikt, wordt deze automatisch voor u gemaakt door AKS. Clusters die service-principals gebruiken, bereiken uiteindelijk een status waarin de Service-Principal moet worden vernieuwd om het cluster te laten functioneren. Het beheren van service-principals voegt complexiteit toe. Daarom is het eenvoudiger om beheerde identiteiten te gebruiken. Dezelfde machtigings vereisten gelden voor service-principals en beheerde identiteiten.

*Beheerde identiteiten* zijn in feite een wrapper rond service-principals en maken hun beheer eenvoudiger. De rotatie van de referenties voor MI gebeurt elke 46 dagen automatisch volgens Azure Active Directory standaard. AKS maakt gebruik van door het systeem toegewezen en door de gebruiker toegewezen beheerde identiteits typen. Deze identiteiten zijn momenteel onveranderbaar. Meer informatie over [beheerde identiteiten voor Azure-resources](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)vindt u hier.

## <a name="before-you-begin"></a>Voordat u begint

U moet de volgende bron hebben geïnstalleerd:

- De Azure CLI, versie 2.8.0 of hoger

## <a name="limitations"></a>Beperkingen

* Het is momenteel niet mogelijk om uw eigen beheerde identiteiten te maken.
* AKS-clusters met beheerde identiteiten kunnen alleen worden ingeschakeld tijdens het maken van het cluster.
* Bestaande AKS-clusters kunnen niet worden bijgewerkt of bijgewerkt om beheerde identiteiten in te scha kelen.
* Tijdens de **upgrade** bewerkingen van het cluster is de beheerde identiteit tijdelijk niet beschikbaar.

## <a name="summary-of-managed-identities"></a>Samen vatting van beheerde identiteiten

AKS maakt gebruik van verschillende beheerde identiteiten voor ingebouwde services en invoeg toepassingen.

| Identiteit                       | Naam    | Gebruiksvoorbeeld | Standaard machtigingen | Uw eigen identiteit meenemen
|----------------------------|-----------|----------|
| Besturingsvlak | niet zichtbaar | Wordt door AKS gebruikt voor het beheren van netwerk bronnen, zoals het maken van een load balancer voor ingangen, open bare IP, enzovoort.| Rol Inzender voor knooppunt resource groep | Momenteel niet ondersteund
| Kubelet | AKS-cluster naam-agent pool | Verificatie met Azure Container Registry (ACR) | Rol van lezer voor knooppunt resource groep | Momenteel niet ondersteund
| Invoeg toepassing | AzureNPM | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | AzureCNI netwerk bewaking | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | azurepolicy (gate keeper) | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | azurepolicy | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | Calico | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | Dashboard | Geen identiteit vereist | NA | Nee
| Invoeg toepassing | HTTPApplicationRouting | Hiermee worden de vereiste netwerk bronnen beheerd | Rol van lezer voor knooppunt resource groep, rol Inzender voor DNS-zone | Nee
| Invoeg toepassing | Ingangs toepassings gateway | Hiermee worden de vereiste netwerk bronnen beheerd| Rol Inzender voor knooppunt resource groep | Nee
| Invoeg toepassing | omsagent | Wordt gebruikt om AKS-metrische gegevens naar Azure Monitor te verzenden | Rol van uitgever voor metrische gegevens controleren | Nee
| Invoeg toepassing | Virtueel knoop punt (ACIConnector) | Beheert vereiste netwerk bronnen voor Azure Container Instances (ACI) | Rol Inzender voor knooppunt resource groep | Nee


## <a name="create-an-aks-cluster-with-managed-identities"></a>Een AKS-cluster maken met beheerde identiteiten

U kunt nu een AKS-cluster met beheerde identiteiten maken met behulp van de volgende CLI-opdrachten.

Maak eerst een Azure-resource groep:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Maak vervolgens een AKS-cluster:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Een succes volle cluster maken met behulp van beheerde identiteiten bevat deze service-principal-profiel informatie:

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Gebruik de volgende opdracht om een query op te vragen van het object objectid van de beheerde identiteit van het beheer vlak:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Het resultaat moet er als volgt uitzien:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Voor het maken en gebruiken van uw eigen VNet, een statisch IP-adres of een gekoppelde Azure-schijf waar de resources zich buiten de resource groep van het worker-knoop punt bevinden, gebruikt u de PrincipalID van het cluster systeem toegewezen beheerde identiteit voor het uitvoeren van een roltoewijzing. Zie [toegang tot andere Azure-resources delegeren](kubernetes-service-principal.md#delegate-access-to-other-azure-resources)voor meer informatie over het toewijzen van rollen.
>
> Machtigings verlening voor door de cluster beheerde identiteit die wordt gebruikt door de Azure-Cloud provider, kan 60 minuten duren om in te vullen.

Haal tot slot referenties op voor toegang tot het cluster:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Het cluster wordt in een paar minuten gemaakt. U kunt vervolgens de werk belasting van uw toepassing implementeren naar het nieuwe cluster en ermee werken, net zoals u dat hebt gedaan met AKS-clusters op basis van de Service-Principal.

## <a name="next-steps"></a>Volgende stappen
* Gebruik [Azure Resource Manager (arm)-Sjablonen][aks-arm-template] voor het maken van beheerde identiteits clusters.

<!-- LINKS - external -->
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

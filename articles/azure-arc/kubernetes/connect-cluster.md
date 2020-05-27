---
title: Een Azure Arc-Kubernetes-cluster verbinden (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Een Azure Arc-Kubernetes-cluster verbinden met Azure Arc
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: 962b6a17743ea2beed1e16503739c55c83babbce
ms.sourcegitcommit: 95269d1eae0f95d42d9de410f86e8e7b4fbbb049
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/26/2020
ms.locfileid: "83860542"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Een Azure Arc-Kubernetes-cluster verbinden (preview-versie)

Een Kubernetes-cluster verbinden met Azure Arc. 

## <a name="before-you-begin"></a>Voordat u begint

Controleer of u de volgende vereisten hebt voor bereid:

* Een Kubernetes-cluster dat actief is
* U hebt toegang nodig met kubeconfig en toegang tot cluster beheerders. 
* De gebruiker of service-principal die wordt gebruikt met `az login` en `az connectedk8s connect` opdrachten moeten de machtigingen lezen en schrijven hebben voor het resource type micro soft. Kubernetes/connectedclusters. De rol ' Azure Arc for Kubernetes-onboarding ' met deze machtigingen kan worden gebruikt voor roltoewijzingen op de gebruiker of service-principal die wordt gebruikt met Azure CLI voor onboarding.
* Nieuwste versie van de *connectedk8s* -en *k8sconfiguration* -extensies

## <a name="supported-regions"></a>Ondersteunde regio’s

* VS - oost
* Europa -west

## <a name="network-requirements"></a>Netwerkvereisten

Voor Azure Arc-agenten moeten de volgende protocollen/poorten/uitgaande Url's worden gebruikt.

* TCP op poort 443-->`https://:443`
* TCP op poort 9418-->`git://:9418`

| Eind punt (DNS)                                                                                               | Beschrijving                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Vereist voor de agent om verbinding te maken met Azure en het cluster te registreren                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Gegevens vlak eindpunt voor de agent om de status te pushen en configuratie gegevens op te halen                                      |
| `https://docker.io`                                                                                            | Vereist voor het ophalen van container installatie kopieën                                                                                         |
| `https://github.com`, git://github.com                                                                         | Voor beeld van GitOps opslag plaatsen worden gehost op GitHub. Configuratie agent vereist connectiviteit met het opgegeven Git-eind punt dat u opgeeft. |
| `https://login.microsoftonline.com`                                                                            | Vereist om Azure Resource Manager-tokens op te halen en bij te werken                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Vereist voor het ophalen van container installatie kopieën voor Azure Arc-agents                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Registreer de twee providers voor Azure Arc enabled Kubernetes:

```console
az provider register --namespace Microsoft.Kubernetes
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.Kubernetes'

az provider register --namespace Microsoft.KubernetesConfiguration
Registering is still on-going. You can monitor using 'az provider show -n Microsoft.KubernetesConfiguration'
```

Registratie is een asynchroon proces. De registratie kan ongeveer 10 minuten duren. U kunt het registratie proces bewaken met de volgende opdrachten:

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="install-azure-cli-extensions"></a>Azure CLI-extensies installeren

Installeer de `connectedk8s` extensie, waarmee u Kubernetes-clusters kunt verbinden met Azure:

```console
az extension add --name connectedk8s
```

Installeer de `k8sconfiguration` extensie:

```console
az extension add --name k8sconfiguration
```

Voer de volgende opdrachten uit om de extensies bij te werken naar de meest recente versies.

```console
az extension update --name connectedk8s
az extension update --name k8sconfiguration
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken

Gebruik een resource groep om meta gegevens voor uw cluster op te slaan.

Maak eerst een resource groep om de verbonden cluster bron te bewaren.

```console
az group create --name AzureArcTest -l EastUS -o table
```

**Uitvoer**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>Een cluster verbinden

We gaan ons Kubernetes-cluster nu koppelen aan Azure. De werk stroom voor `az connectedk8s connect` is als volgt:

1. Controleer de verbinding met uw Kubernetes-cluster: via `KUBECONFIG` , `~/.kube/config` , of`--kube-config`
1. Azure Arc-agents voor Kubernetes implementeren met behulp van helm 3 in de `azure-arc` naam ruimte

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**Uitvoer**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>Verbonden cluster verifiëren

Uw verbonden clusters weer geven:

```console
az connectedk8s list -g AzureArcTest -o table
```

**Uitvoer**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

Azure Arc enabled Kubernetes implementeert enkele opera tors in de `azure-arc` naam ruimte. U kunt deze implementaties en peulen hier bekijken:

```console
kubectl -n azure-arc get deployments,pods
```

**Uitvoer**

```console
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

## <a name="azure-arc-agents-for-kubernetes"></a>Azure Arc-agents voor Kubernetes

Azure Arc enabled Kubernetes bestaat uit een aantal agents (opera tors) die in uw cluster worden uitgevoerd en die zijn geïmplementeerd in de `azure-arc` naam ruimte.

* `deployment.apps/config-agent`: Hiermee wordt het verbonden cluster gewatcheerd voor configuratie bronnen voor broncode beheer die zijn toegepast op het cluster en de nalevings status van updates
* `deployment.apps/controller-manager`: is een operator van Opera tors en organiseert de interacties tussen onderdelen van Azure Arc
* `deployment.apps/metrics-agent`: verzamelt metrische gegevens van andere Arc-agents om ervoor te zorgen dat deze agents de optimale prestaties vertonen
* `deployment.apps/cluster-metadata-operator`: Hiermee worden de meta gegevens van het cluster verzameld, de Cluster versie, het aantal knoop punten en de Arc-agent versie
* `deployment.apps/resource-sync-agent`: synchroniseert de hierboven vermelde meta gegevens van het cluster naar Azure
* `deployment.apps/clusteridentityoperator`: onderhoudt het MSI-certificaat (Managed Service Identity) dat door andere agents wordt gebruikt voor communicatie met Azure
* `deployment.apps/flux-logs-agent`: Hiermee worden logboeken van de stroom-Opera tors verzameld die zijn geïmplementeerd als onderdeel van de configuratie van broncode beheer

## <a name="delete-a-connected-cluster"></a>Een verbonden cluster verwijderen

U kunt een `Microsoft.Kubernetes/connectedcluster` resource verwijderen met behulp van Azure CLI of Azure Portal.

Met de Azure CLI-opdracht `az connectedk8s delete` wordt de `Microsoft.Kubernetes/connectedCluster` resource in azure verwijderd. De Azure CLI verwijdert alle gekoppelde `sourcecontrolconfiguration` resources in Azure. De Azure CLI maakt gebruik van helm uninstall om de agents in het cluster te verwijderen.

De Azure Portal verwijdert de `Microsoft.Kubernetes/connectedcluster` resource in Azure en verwijdert alle gekoppelde `sourcecontrolconfiguration` resources in Azure.

Als u de agents in het cluster wilt verwijderen, moet u of worden uitgevoerd `az connectedk8s delete` `helm uninstall azurearcfork8s` .

## <a name="next-steps"></a>Volgende stappen

* [GitOps gebruiken in een verbonden cluster](./use-gitops-connected-cluster.md)
* [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)

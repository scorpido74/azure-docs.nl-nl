---
title: GitOps gebruiken met helm voor een Azure Arc-cluster configuratie (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps gebruiken met helm voor een Azure Arc-cluster configuratie (preview-versie)
keywords: GitOps, Kubernetes, K8s, azure, helm, Arc, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 677c5f2b27794ebea9d38e470b5e1a5ba12bff7e
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85857217"
---
# <a name="use-gitops-with-helm-for-an-azure-arc-enabled-cluster-configuration-preview"></a>GitOps gebruiken met helm voor een Azure Arc-cluster configuratie (preview-versie)

Helm is een open-source-verpakkings programma waarmee u de levens cyclus van Kubernetes-toepassingen kunt installeren en beheren. Net als Linux-pakket beheerders, zoals APT en yum, wordt helm gebruikt voor het beheren van Kubernetes-grafieken, die pakketten van vooraf geconfigureerde Kubernetes-resources zijn.

In dit artikel wordt beschreven hoe u helm configureert en gebruikt met Azure Arc enabled Kubernetes.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand Kubernetes-verbonden Azure-Arc-cluster. Als u een verbonden cluster nodig hebt, raadpleegt u de [Snelstartgids een cluster verbinden](./connect-cluster.md).

Stel dat u de omgevings variabelen wilt instellen voor gebruik in deze zelf studie. U hebt de naam van de resource groep en de cluster naam voor het verbonden cluster nodig.

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>Controleren of uw cluster is ingeschakeld met Arc

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

Uitvoer:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-helm-with-azure-arc-enabled-kubernetes"></a>Overzicht van het gebruik van helm met Azure Arc enabled Kubernetes

 De helm-operator biedt een uitbrei ding van stroom waarmee helm-grafiek releases worden geautomatiseerd. Een grafiek release wordt beschreven via een aangepaste Kubernetes-resource met de naam HelmRelease. Stroom synchroniseert deze resources van Git naar het cluster en de operator helm zorgt ervoor dat helm-grafieken worden vrijgegeven zoals opgegeven in de resources.

 Hieronder ziet u een voor beeld van een Git-opslag plaats-structuur die in deze zelf studie wordt gebruikt:

```bash
├── charts
│   └── azure-vote
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── prod
        └── azure-vote-app.yaml
```

In de Git-opslag plaats hebben we twee directory's, een met een helm-grafiek en één die de configuratie van de releases bevat. In de `releases/prod` map `azure-vote-app.yaml` bevat de de HelmRelease-configuratie die hieronder wordt weer gegeven:

```bash
 apiVersion: helm.fluxcd.io/v1
 kind: HelmRelease
 metadata:
   name: azure-vote-app
   namespace: prod
   annotations:
 spec:
   releaseName: azure-vote-app
   chart:
     git: https://github.com/Azure/arc-helm-demo
     path: charts/azure-vote
     ref: master
   values:
     image:
       repository: dstrebel/azurevote
       tag: v1
     replicaCount: 3
```

De helm-release configuratie bevat de volgende velden:

- `metadata.name`is verplicht en moet voldoen aan de Kubernetes-naamgevings conventies
- `metadata.namespace`is optioneel en bepaalt waar de release wordt gemaakt
- `spec.releaseName`is optioneel en als niet wordt vermeld, wordt de release naam $namespace-$name
- `spec.chart.path`is de map met de grafiek, opgegeven ten opzichte van de hoofdmap van de opslag plaats
- `spec.values`zijn gebruikers aanpassingen van standaard parameter waarden in de grafiek zelf

De opties die zijn opgegeven in de HelmRelease spec.-waarden, overschrijven de opties die zijn opgegeven in values. yaml uit de grafiek bron.

Meer informatie over de HelmRelease vindt u in de officiële [helm-operator documentatie](https://docs.fluxcd.io/projects/helm-operator/en/1.0.0-rc9/references/helmrelease-custom-resource.html)

## <a name="create-a-configuration"></a>Een configuratie maken

Met de Azure CLI-extensie voor kunt `k8sconfiguration` u ons verbonden cluster koppelen aan de voor beeld van de Git-opslag plaats. We geven deze configuratie een naam `azure-voting-app` en implementeren de stroom operator in de `prod` naam ruimte.

```bash
az k8sconfiguration create --name azure-voting-app --resource-group  $RESOURCE_GROUP --cluster-name $CLUSTER_NAME --operator-instance-name azure-voting-app --operator-namespace prod --enable-helm-operator --helm-operator-version='0.6.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --operator-params='--git-readonly --git-path=releases/prod' --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Configuratie parameters

[Meer informatie over aanvullende para meters die u kunt gebruiken](./use-gitops-connected-cluster.md#additional-parameters)om het maken van de configuratie aan te passen.


## <a name="validate-the-configuration"></a>De configuratie valideren

Controleer met behulp van de Azure CLI of de `sourceControlConfiguration` is gemaakt.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-voting-app --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

Houd er rekening mee dat de `sourceControlConfiguration` resource wordt bijgewerkt met de nalevings status, berichten en informatie over fout opsporing.

**Uitvoer**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "azure-vote-app",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "prod",
  "operatorParams": "--git-readonly --git-path=releases/prod",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Toepassing valideren

We gaan nu controleren om te controleren of de toepassing actief is door de service-IP op te halen.

```bash
kubectl get svc/azure-vote-front -n prod
```

**Uitvoer**

```bash
NAME               TYPE           CLUSTER-IP    EXTERNAL-IP      PORT(S)        AGE
azure-vote-front   LoadBalancer   10.0.14.161   52.186.160.216   80:30372/TCP   4d22h
```

Zoek het externe IP-adres uit de uitvoer hierboven en open het in een browser.

## <a name="next-steps"></a>Volgende stappen

- [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)

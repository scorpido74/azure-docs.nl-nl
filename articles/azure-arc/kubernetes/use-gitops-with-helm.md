---
title: Helm-grafieken implementeren met behulp van GitOps op Kubernetes-cluster waarvoor Arc is ingeschakeld (preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: GitOps gebruiken met helm voor een Azure Arc-cluster configuratie (preview-versie)
keywords: GitOps, Kubernetes, K8s, azure, helm, Arc, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: cca48910b679ff8f72ee06f4ed990bd480fb2200
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723636"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Helm-grafieken implementeren met behulp van GitOps op Kubernetes-cluster waarvoor Arc is ingeschakeld (preview)

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

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Overzicht van het gebruik van GitOps en helm met Azure Arc enabled Kubernetes

 De helm-operator biedt een uitbrei ding van stroom waarmee helm-grafiek releases worden geautomatiseerd. Een grafiek release wordt beschreven via een aangepaste Kubernetes-resource met de naam HelmRelease. Stroom synchroniseert deze resources van Git naar het cluster en de operator helm zorgt ervoor dat helm-grafieken worden vrijgegeven zoals opgegeven in de resources.

 Hieronder ziet u een voor beeld van een Git-opslag plaats-structuur die in deze zelf studie wordt gebruikt:

```bash
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

In de Git-opslag plaats hebben we twee directory's, een met een helm-grafiek en één die de configuratie van de releases bevat. In de `releases` map bevat de `app.yaml` HelmRelease-configuratie die hieronder wordt weer gegeven:

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

De helm-release configuratie bevat de volgende velden:

- `metadata.name` is verplicht en moet voldoen aan de Kubernetes-naamgevings conventies
- `metadata.namespace` is optioneel en bepaalt waar de release wordt gemaakt
- `spec.releaseName` is optioneel en als niet wordt vermeld, wordt de release naam $namespace-$name
- `spec.chart.path` is de map met de grafiek, opgegeven ten opzichte van de hoofdmap van de opslag plaats
- `spec.values` zijn gebruikers aanpassingen van standaard parameter waarden in de grafiek zelf

De opties die zijn opgegeven in de HelmRelease spec.-waarden, overschrijven de opties die zijn opgegeven in values. yaml uit de grafiek bron.

Meer informatie over de HelmRelease vindt u in de officiële [helm-operator documentatie](https://docs.fluxcd.io/projects/helm-operator/en/stable/)

## <a name="create-a-configuration"></a>Een configuratie maken

Met de Azure CLI-extensie voor kunt `k8sconfiguration` u ons verbonden cluster koppelen aan de voor beeld van de Git-opslag plaats. We geven deze configuratie een naam `azure-arc-sample` en implementeren de stroom operator in de `arc-k8s-demo` naam ruimte.

```bash
az k8sconfiguration create --name azure-arc-sample \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>Configuratie parameters

[Meer informatie over aanvullende para meters die u kunt gebruiken](./use-gitops-connected-cluster.md#additional-parameters)om het maken van de configuratie aan te passen.

## <a name="validate-the-configuration"></a>De configuratie valideren

Controleer met behulp van de Azure CLI of de `sourceControlConfiguration` is gemaakt.

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-arc-sample --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

De `sourceControlConfiguration` resource wordt bijgewerkt met de nalevings status, berichten en informatie over fout opsporing.

**Uitvoer**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "0.6.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>Toepassing valideren

Voer de volgende opdracht uit en navigeer naar `localhost:8080` in uw browser om te controleren of de toepassing wordt uitgevoerd.

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>Volgende stappen

- [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)

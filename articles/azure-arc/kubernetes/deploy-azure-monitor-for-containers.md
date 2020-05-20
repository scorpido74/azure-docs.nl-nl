---
title: Onboarding van Azure Arc met Azure Monitor voor containers (preview-versie)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Onboarding van Azure Arc met Azure Monitor voor containers
keywords: Kubernetes, Arc, azure, K8s, containers
ms.openlocfilehash: 3e1ea96a9241211b25a4e5b356723290fa647412
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/20/2020
ms.locfileid: "83680750"
---
# <a name="onboard-azure-monitor-for-containers-with-arc-preview"></a>Onboard Azure Monitor voor containers met Arc (preview-versie)

Onboard [Azure monitor ingeschakelde containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview) naar Azure Arc enabled Kubernetes-cluster (s).

## <a name="before-you-begin"></a>Voordat u begint

* [Kubernetes-versies](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions)
* Linux distributies voor de knoop punten van het cluster (Master & worker) – Ubuntu (18,04 LTS en 16,04 LTS)
* Minimale machtiging voor de RBAC-rol van Inzender voor het Azure-abonnement van het Azure Arc enabled Kubernetes-cluster
* Volledig gekwalificeerde Azure-Resource-ID van het Azure Arc enabled Kubernetes-cluster
* Kubeconfig-context van het Kubernetes-cluster
* De bewakings agent vereist dat cAdvisor op de Kubelet wordt uitgevoerd op een beveiligde poort: 10250 of onbeveiligde poort: 10255 op alle knoop punten om de metrische gegevens over prestaties op te halen   
* U kunt het beste de Kubelet cAdvisor-poort configureren voor beveiligde poort: 10250.
* Voor de bewakings agent zijn de volgende uitgaande poorten en domeinen vereist om de bewakings gegevens te verzenden naar de Azure Monitor back-end (indien geblokkeerd door de proxy/firewall)
    -  *. ods.opinsights.azure.com 443
    -  *. oms.opinsights.azure.com 443
    -  *. blob.core.windows.net 443
    -  dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

### <a name="using-helm-chart"></a>HELM-grafiek gebruiken

### <a name="option-1-using-powershell--script"></a>Optie 1: Power shell-script gebruiken

1. Het onboarding-script downloaden

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.ps1
     ```

2. Installeer [Power shell core](https://docs.microsoft.com/PowerShell/scripting/install/installing-PowerShell?view=PowerShell-6) op uw ontwikkel computer om het Power shell-onboarding-script uit te voeren.

3. Aanmelden bij Azure

    ```console
    az login --use-device-code
    ```

4. Onder script uitvoeren met uw cluster Azure Arc K8s-cluster ResourceId en-context van het kubernetes-cluster

    ```console
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId <resourcedIdOfAzureArcCluster> -kubeContext <kube-context>

    For Example ..
    .\onboarding_azuremonitor_for_containers.ps1 -azureArcClusterResourceId /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 -kubeContext MyK8sTestCluster
     ```

### <a name="option-2-using-bash-script"></a>Optie 2: bash-script gebruiken

> **Tip:** Het script maakt gebruik van bash 4-functies, dus zorg ervoor dat uw bash up-to-date is. U kunt uw huidige versie controleren met `bash --version` .

1. Het onboarding-script downloaden

    ```console
    curl -LO https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/haiku/onboarding_azuremonitor_for_containers.sh
     ```

2. Onder script uitvoeren met uw cluster Azure Arc K8s-cluster ResourceId en-context van het kubernetes-cluster

    ```console
    bash onboarding_azuremonitor_for_containers.sh <resourcedIdOfAzureArcCluster>  <kube-context>

    For Example:
    bash onboarding_azuremonitor_for_containers.sh /subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1 MyK8sTestCluster

     ```

## <a name="configure-agent-data-collection"></a>Gegevens verzameling van agent configureren

Standaard verzamelt de agent geen stdout-en stderr-logboeken van containers in uitvoeren-naam ruimte.
Raadpleeg de https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-agent-config agent configureren met gewenste instellingen voor het verzamelen van gegevens.

## <a name="configure-scraping-of-prometheus-metrics"></a>Het opvallen van Prometheus-metrische gegevens configureren

Azure Monitor voor containers worden de metrische gegevens voor Prometheus en opgenomen in de Azure Monitor back-end.
Raadpleeg https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-prometheus-integration voor instructies over het configureren van Prometheus-uitval.

## <a name="user-interface"></a>Gebruikersinterface

Navigeer naar https://aka.ms/azmon-containers-azurearc om het Onboarded cluster weer te geven.

## <a name="disable-monitoring"></a>Bewaking uitschakelen

Als u de bewaking om een of andere reden wilt uitschakelen, hoeft u alleen maar de Azure Monitor voor containers HELM te verwijderen om te stoppen met het verzamelen en opnemen van bewakings gegevens voor de Azure Monitor voor back-end van containers.

    ```console
    helm del azmon-containers-release-1
    ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)


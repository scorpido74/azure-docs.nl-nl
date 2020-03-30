---
title: Azure Monitor-integratie voor Azure Red Hat OpenShift 4.3
description: Meer informatie over het inschakelen van Azure Monitor op uw Microsoft Azure Red Hat OpenShift-cluster.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082843"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor-integratie voor Azure Red Hat OpenShift 4.3

> [!IMPORTANT] 
> Houd er rekening mee dat Azure Red Hat OpenShift 4.3 momenteel alleen beschikbaar is in een privépreview in Oost-VS. Private preview-acceptatie is alleen op uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie probeert in te schakelen: [Azure Red Hat OpenShift Private Preview-registratie](https://aka.ms/aro-preview-register)

> [!NOTE]
> Preview-functies zijn selfservice en worden geleverd zoals het is en zoals beschikbaar en zijn uitgesloten van de service-level agreement (SLA) en beperkte garantie. Daarom zijn de functies niet bedoeld voor productiegebruik.

In dit artikel wordt beschreven hoe u de privépreview van Azure Monitor inschakelt voor containers voor OpenShift 4.3-clusters die on-prem of in een cloudomgeving worden gehost. Dezelfde instructies zijn ook van toepassing om bewaking in te schakelen voor Azure Red Hat OpenShift (ARO) 4.3-clusters.  

## <a name="prerequisites"></a>Vereisten

- [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Toegang tot kubeconfig van het kubernetes-cluster
- Toegang tot een Azure-abonnement
- Toegang tot het OpenShift 4.3-cluster om het azure-monitor voor het helmdiagram voor containers te installeren
- Minimale RBAC-functiemachtiging voor inzender voor het Azure-abonnement  
- Monitoring agent vereist de volgende uitgaande poorten - en domeinen om de monitoringgegevens naar de Azure Monitor backend te verzenden (Indien geblokkeerd door proxy/firewall):
  - *.ods.opinsights.azure.com 443
  - *.oms.opinsights.azure.com 443
  - *.blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Het script maakt gebruik van bash 4 functies, dus zorg ervoor dat je bash up-to-date is. U uw huidige `bash --version`versie controleren met.

### <a name="download-the-onboarding-script"></a>Het onboarding-script downloaden

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Voer het volgende script uit met azureSubscriptionId, werkruimteregio, clusterNaam en context van het Kubernetes-cluster.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Bijvoorbeeld:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Agentgegevensverzameling configureren

Standaard verzamelt de monitoringagent de {stdout; stderr} containerlogboeken van alle containers die in alle naamruimten worden uitgevoerd, behalve het Kube-systeem.  Als u de containerlogboekverzameling wilt configureren die specifiek is voor bepaalde naamruimte of naamruimten, u verwijzen naar [de configuratie van de containerinsights-agent](../azure-monitor/insights/container-insights-agent-config.md). Hier u monitoringagent configureren met de gewenste instellingen voor gegevensverzameling met behulp van config-kaart.

## <a name="configure-scraping-of-prometheus-metrics"></a>Schraapen van Prometheus-statistieken configureren

Azure Monitor voor containers schraapt de Prometheus-statistieken en neemt de backend van de Azure Monitor toe. Raadpleeg [de configuratie van Container Insights Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md) voor de instructies voor het configureren van Prometheus-schrapen.

Na een succesvolle onboarding navigeer je naar [Hybride bewaking](https://aka.ms/azmon-containers-hybrid) en selecteer je Omgeving als **'Alles'** om je nieuw ingebouwde OpenShift v4-cluster te bekijken.

## <a name="disable-monitoring"></a>Bewaking uitschakelen

Als u de controle wilt uitschakelen, u de azure-monitor voor containers helm grafiek met behulp van de volgende opdracht om te stoppen met het verzamelen en innemen van monitoring gegevens naar Azure Monitor voor containers backend.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Controle bijwerken

Voer het onboardingscript opnieuw uit zoals beschreven in de sectie [Onboarding](#onboarding) met dezelfde parameter om bij te werken naar de laatste Helm-grafiek.

## <a name="after-successful-onboarding"></a>Na succesvolle onboarding

Navigeer naar [Hybride bewaking](https://aka.ms/azmon-containers-hybrid)en u het nieuw ingeschakelde OpenShift/ARO v4-cluster met status bekijken op het tabblad **Bewaakte clusters.** Daar u dieper inzicht krijgen in statistieken, voorraad en logboeken door op de **clusterkolom** te klikken.

## <a name="supported-features"></a>Ondersteunde functies

Zie [Container Insights-overzicht](../azure-monitor/insights/container-insights-overview.md)voor meer informatie over de ondersteunde functies en functionaliteit.

Neem contact askcoin@microsoft.com met ons op via voor feedback en vragen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over monitoring:
- [Overzicht containerinsights](../azure-monitor/insights/container-insights-overview.md)

- [Overzicht van logboekquery's](../azure-monitor/log-query/log-query-overview.md)

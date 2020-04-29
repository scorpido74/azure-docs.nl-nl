---
title: Azure Monitor integratie voor Azure Red Hat open Shift 4,3
description: Meer informatie over het inschakelen van Azure Monitor op uw Microsoft Azure Red Hat open Shift-cluster.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 03/06/2020
ms.openlocfilehash: a784fc070400995c56d16a3bc264d589bcb1f64e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79082843"
---
# <a name="azure-monitor-integration-for-azure-red-hat-openshift-43"></a>Azure Monitor integratie voor Azure Red Hat open Shift 4,3

> [!IMPORTANT] 
> Azure Red Hat open Shift 4,3 is momenteel alleen beschikbaar als persoonlijke preview in VS-Oost. Privé preview-acceptatie is alleen via de uitnodiging. Zorg ervoor dat u uw abonnement registreert voordat u deze functie inschakelt: [Azure Red Hat open Shift-inschrijving voor preview](https://aka.ms/aro-preview-register)

> [!NOTE]
> Preview-functies zijn self-service en worden aangeboden als en beschikbaar en zijn uitgesloten van de Service Level Agreement (SLA) en beperkte garantie. De functies zijn daarom niet bedoeld voor productie gebruik.

In dit artikel wordt beschreven hoe u de persoonlijke preview van Azure Monitor kunt inschakelen voor containers voor open Shift 4,3-clusters die on-premises of in elke cloud omgeving worden gehost. Dezelfde instructies zijn ook van toepassing op het inschakelen van bewaking voor Azure Red Hat open Shift (ARO) 4,3-clusters.  

## <a name="prerequisites"></a>Vereisten

- [Azure-CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- [Helm 3](https://helm.sh/docs/intro/install/)
- Toegang tot kubeconfig van het kubernetes-cluster
- Toegang tot een Azure-abonnement
- Toegang tot het open Shift 4,3-cluster voor het installeren van de Azure Monitor voor containers helm-grafiek
- Minimale machtiging voor de RBAC-rol van Inzender voor het Azure-abonnement  
- Bewakings agent vereist de volgende uitgaande poorten: en domeinen om de bewakings gegevens te verzenden naar de Azure Monitor back-end (indien geblokkeerd door de proxy/firewall):
  - *. ods.opinsights.azure.com 443
  - *. oms.opinsights.azure.com 443
  - *. blob.core.windows.net 443
  - dc.services.visualstudio.com 443

## <a name="onboarding"></a>Onboarding

> [!TIP]
> Het script maakt gebruik van bash 4-functies, dus zorg ervoor dat uw bash up-to-date is. U kunt uw huidige versie controleren met `bash --version`.

### <a name="download-the-onboarding-script"></a>Het onboarding-script downloaden

```bash
curl -LO  https://raw.githubusercontent.com/microsoft/OMS-docker/ci_feature/docs/openshiftV4/onboarding_azuremonitor_for_containers.sh
```

Voer het volgende script uit met azureSubscriptionId, werkruimte regio, cluster naam en context van het Kubernetes-cluster.

```bash
bash onboarding_azuremonitor_for_containers.sh <azureSubscriptionId> <azureRegionforLogAnalyticsWorkspace> <clusterName> <kubeconfigContextNameOftheCluster>
```

Bijvoorbeeld:

```bash
 bash onboarding_azuremonitor_for_containers.sh 27ac26cf-a9f0-4908-b300-9a4e9a0fb205 eastus myocp42 admin 
```

## <a name="configure-agent-data-collection"></a>Gegevens verzameling van agent configureren

Standaard verzamelt de bewakings agent de container logboeken {stdout; stderr} van alle containers die worden uitgevoerd in alle naam ruimten, met uitzonde ring van uitvoeren-System.  Als u de container logboek verzameling specifiek voor bepaalde naam ruimten of naam ruimten wilt configureren, kunt u verwijzen naar de configuratie van de [container Insights-agent](../azure-monitor/insights/container-insights-agent-config.md). Hier kunt u de bewakings agent configureren met de gewenste instellingen voor het verzamelen van gegevens met behulp van de configuratie toewijzing.

## <a name="configure-scraping-of-prometheus-metrics"></a>Het opvallen van Prometheus-metrische gegevens configureren

Azure Monitor voor containers worden de metrische gegevens voor Prometheus en opgenomen in de Azure Monitor back-end. Raadpleeg de [configuratie van de container Insights-Prometheus](../azure-monitor/insights/container-insights-prometheus-integration.md) voor de instructies voor het configureren van Prometheus-uitval.

Als u het onboarding hebt voltooid, gaat u naar [hybride bewaking](https://aka.ms/azmon-containers-hybrid) en selecteert u omgeving als **' alles '** om uw nieuw onboarded open Shift v4-cluster weer te geven.

## <a name="disable-monitoring"></a>Bewaking uitschakelen

Als u de bewaking wilt uitschakelen, kunt u het helm-diagram van de Azure Monitor voor containers verwijderen met de volgende opdracht om te stoppen met het verzamelen en opnemen van bewakings gegevens aan Azure Monitor voor de back-end van containers.

``` bash
helm del azmon-containers-release-1
```

## <a name="update-monitoring"></a>Bewaking bijwerken

Voer het voorbereidings script opnieuw uit zoals beschreven in de sectie voor [onboarding](#onboarding) met dezelfde para meter om bij te werken naar de meest recente helm-grafiek.

## <a name="after-successful-onboarding"></a>Na een geslaagde onboarding

Navigeer naar [hybride bewaking](https://aka.ms/azmon-containers-hybrid)en u kunt uw nieuw ingeschakelde openshift/Aro v4-cluster zien met de status in het tabblad **bewaakte clusters** . Daar kunt u meer inzicht krijgen, zoals metrische gegevens, inventarisatie en logboeken door te klikken op de kolom **cluster** .

## <a name="supported-features"></a>Ondersteunde functies

Zie [overzicht van container Insights](../azure-monitor/insights/container-insights-overview.md)voor meer informatie over de ondersteunde functies en functionaliteit.

Neem contact met askcoin@microsoft.com ons op via feedback en vragen.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over bewaking:
- [Overzicht van container Insights](../azure-monitor/insights/container-insights-overview.md)

- [Overzicht van logboek Query's](../azure-monitor/log-query/log-query-overview.md)

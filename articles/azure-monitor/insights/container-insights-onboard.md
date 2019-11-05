---
title: Azure Monitor voor containers inschakelen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers inschakelt en configureert, zodat u kunt begrijpen hoe uw container presteert en welke prestatie problemen zijn geïdentificeerd.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/15/2019
ms.openlocfilehash: dd58ec08c6ec372cf53a79b75162748cfe336b23
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73477123"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor inschakelen voor containers

In dit artikel vindt u een overzicht van de beschik bare opties voor het instellen van Azure Monitor voor containers voor het bewaken van de prestaties van werk belastingen die zijn geïmplementeerd in Kubernetes-omgevingen en die worden gehost op de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/), AKS-Engine op [Azure stack ](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)of Kubernetes on-premises geïmplementeerd.

Azure Monitor voor containers kunnen worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van AKS met behulp van de volgende ondersteunde methoden:

* Van de Azure Portal, Azure PowerShell of met Azure CLI
* [Terraform en AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt:

* **Een Log Analytics-werk ruimte.**

    Azure Monitor voor containers ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld in azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    U kunt een werk ruimte maken wanneer u de bewaking van uw nieuwe AKS-cluster inschakelt of de voorbereidings ervaring een standaard werkruimte maakt in de standaard resource groep van het AKS-cluster abonnement. Als u ervoor hebt gekozen om deze zelf te maken, kunt u deze maken via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [Power shell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure Portal](../learn/quick-create-workspace.md). Zie [regio toewijzing voor Azure monitor voor containers](container-insights-region-mapping.md)voor een lijst met de ondersteunde toewijzings paren die worden gebruikt voor de standaardwerk ruimte.

* U bent lid van de **rol log Analytics Inzender** om container bewaking in te scha kelen. Zie [werk ruimten beheren](../platform/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

* U bent lid van de rol **[eigenaar](../../role-based-access-control/built-in-roles.md#owner)** op de cluster bron AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Prometheus-metrische gegevens worden standaard niet verzameld. Voordat u [de agent configureert](container-insights-prometheus-integration.md) om deze te verzamelen, is het belang rijk dat u de Prometheus- [documentatie](https://prometheus.io/) bekijkt om te begrijpen wat u kunt definiëren.

## <a name="network-firewall-requirements"></a>Netwerk firewall vereisten

De informatie in de volgende tabel bevat de proxy-en firewall configuratie gegevens die zijn vereist voor de container agent om te communiceren met Azure Monitor voor containers. Al het netwerk verkeer van de agent is uitgaand naar Azure Monitor.

|Agentresource|Poorten |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *. monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

De informatie in de volgende tabel bevat de configuratie gegevens van de proxy en Firewall voor Azure China.

|Agentresource|Poorten |Beschrijving | 
|--------------|------|-------------|
| *. ods.opinsights.azure.cn | 443 | Gegevensopname |
| *. oms.opinsights.azure.cn | 443 | OMS-onboarding |
| *.blob.core.windows.net | 443 | Wordt gebruikt voor het bewaken van uitgaande verbindingen. |
| microsoft.com | 80 | Wordt gebruikt voor de netwerk verbinding. Dit is alleen vereist als de versie van de agent installatie kopie ciprod09262019 of eerder is. |
| dc.services.visualstudio.com | 443 | Voor agent-telemetrie met behulp van open bare Azure-Cloud Application Insights. |

De informatie in de volgende tabel bevat de proxy-en firewall configuratie gegevens voor de Amerikaanse overheid van Azure.

|Agentresource|Poorten |Beschrijving | 
|--------------|------|-------------|
| *. ods.opinsights.azure.us | 443 | Gegevensopname |
| *. oms.opinsights.azure.us | 443 | OMS-onboarding |
| *.blob.core.windows.net | 443 | Wordt gebruikt voor het bewaken van uitgaande verbindingen. |
| microsoft.com | 80 | Wordt gebruikt voor de netwerk verbinding. Dit is alleen vereist als de versie van de agent installatie kopie ciprod09262019 of eerder is. |
| dc.services.visualstudio.com | 443 | Voor agent-telemetrie met behulp van open bare Azure-Cloud Application Insights. |

## <a name="components"></a>Onderdelen

Uw vermogen om de prestaties te bewaken, is afhankelijk van een container Log Analytics-agent voor Linux speciaal ontwikkeld voor de Azure Monitor voor containers. Deze gespecialiseerde agent verzamelt prestatie-en gebeurtenis gegevens van alle knoop punten in het cluster en de agent wordt automatisch geïmplementeerd en geregistreerd bij de opgegeven Log Analytics-werk ruimte tijdens de implementatie. De agent versie is micro soft/OMS: ciprod04202018 of hoger, en wordt vertegenwoordigd door een datum in de volgende notatie: *mmddyyyy*.

>[!NOTE]
>Met de preview-versie van Windows Server-ondersteuning voor AKS is er voor een AKS-cluster met Windows Server-knoop punten geen agent geïnstalleerd om gegevens te verzamelen en door te sturen naar Azure Monitor. In plaats daarvan wordt een Linux-knoop punt dat automatisch in het cluster wordt geïmplementeerd als onderdeel van de standaard implementatie, worden de gegevens verzameld en doorgestuurd naar Azure Monitor voor alle Windows-knoop punten in het cluster.  
>

Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt deze automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS). Zie [Release aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent als u de gepubliceerde versies wilt volgen.

>[!NOTE]
>Als u al een AKS-cluster hebt geïmplementeerd, schakelt u de bewaking in met behulp van Azure CLI of een gegeven Azure Resource Manager sjabloon, zoals verderop in dit artikel wordt beschreven. U kunt `kubectl` niet gebruiken om de agent bij te werken, te verwijderen, opnieuw te implementeren of te implementeren.
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep als het cluster.

U schakelt Azure Monitor voor containers in met behulp van een van de volgende methoden die worden beschreven in de volgende tabel.

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Nieuw AKS-cluster | [Een cluster maken met Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| U kunt de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met Azure CLI. |
| | [Een cluster maken met behulp van terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| U kunt de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met behulp van de open source-terraform. |
| Bestaand AKS-cluster | [Inschakelen met behulp van Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met behulp van Azure CLI. |
| |[Inschakelen met behulp van terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met het open source-hulp programma terraform. |
| | [Inschakelen vanaf Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| U kunt de bewaking inschakelen van een of meer AKS-clusters die al zijn geïmplementeerd op de pagina met meerdere clusters van AKS in Azure Monitor. |
| | [Inschakelen vanuit AKS-cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| U kunt de bewaking rechtstreeks vanuit een AKS-cluster inschakelen in de Azure Portal. |
| | [Inschakelen met behulp van een Azure Resource Manager sjabloon](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| U kunt de bewaking van een AKS-cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager-sjabloon. |
| | [Inschakelen voor hybride Kubernetes-cluster](container-insights-hybrid-setup.md) | U kunt de bewaking van een AKS-engine inschakelen die wordt gehost in Azure Stack of voor Kubernetes die on-premises worden gehost. |

## <a name="next-steps"></a>Volgende stappen

* Als bewaking is ingeschakeld voor het vastleggen van metrische gegevens over de status van de AKS-cluster knooppunten en de peulen, zijn deze metrische gegevens over de status beschikbaar in de Azure Portal. Zie [Azure Kubernetes service Health weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure monitor voor containers.

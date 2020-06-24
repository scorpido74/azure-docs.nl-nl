---
title: Azure Monitor voor containers inschakelen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers inschakelt en configureert, zodat u kunt begrijpen hoe uw container presteert en welke prestatie problemen zijn geïdentificeerd.
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: a765c601682eb594d40ba98b8b4ef1853f35fb37
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84886016"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor inschakelen voor containers

In dit artikel vindt u een overzicht van de beschik bare opties voor het instellen van Azure Monitor voor containers om de prestaties te bewaken van werk belastingen die in Kubernetes-omgevingen zijn geïmplementeerd en die worden gehost op:

- [Azure Kubernetes service](https://docs.microsoft.com/azure/aks/) (AKS)

- Zelf beheerde Kubernetes-clusters die worden gehost op Azure met behulp van de [AKS-engine](https://github.com/Azure/aks-engine).

- Zelf beheerde Kubernetes-clusters die worden gehost op [Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) of on-premises met behulp van AKS-engine.

- [Azure Red Hat open Shift](../../openshift/intro-openshift.md) versie 3. x en 4. x

- [Red Hat open Shift](https://docs.openshift.com/container-platform/4.3/welcome/index.html) versie 4. x

- [Kubernetes-cluster met Arc-functionaliteit](../../azure-arc/kubernetes/overview.md)

Azure Monitor voor containers kunnen worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van Kubernetes met behulp van de volgende ondersteunde methoden:

- Van de Azure Portal, Azure PowerShell of met Azure CLI

- [Terraform en AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md) gebruiken

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, moet u ervoor zorgen dat u over het volgende beschikt:

- **Een Log Analytics-werkruimte.**

   Azure Monitor voor containers ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld in azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

   U kunt een werk ruimte maken wanneer u de bewaking van uw nieuwe AKS-cluster inschakelt of de voorbereidings ervaring een standaard werkruimte maakt in de standaard resource groep van het AKS-cluster abonnement. Als u ervoor hebt gekozen om deze zelf te maken, kunt u deze maken via [Azure Resource Manager](../platform/template-workspace-configuration.md), via [Power shell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure Portal](../learn/quick-create-workspace.md). Zie [regio toewijzing voor Azure monitor voor containers](container-insights-region-mapping.md)voor een lijst met de ondersteunde toewijzings paren die worden gebruikt voor de standaardwerk ruimte.

- U bent lid van de **rol log Analytics Inzender** om container bewaking in te scha kelen. Zie [werk ruimten beheren](../platform/manage-access.md)voor meer informatie over het controleren van de toegang tot een log Analytics-werk ruimte.

- U bent lid van de rol **[eigenaar](../../role-based-access-control/built-in-roles.md#owner)** op de cluster bron AKS.

   [!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

- Prometheus-metrische gegevens worden standaard niet verzameld. Voordat u [de agent configureert](container-insights-prometheus-integration.md) om deze te verzamelen, is het belang rijk dat u de Prometheus- [documentatie](https://prometheus.io/) raadpleegt om te begrijpen wat er kan worden geschrootd en welke methoden worden ondersteund.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Het volgende wordt officieel ondersteund met Azure Monitor voor containers.

- Omgevingen: Azure Red Hat open Shift, Kubernetes on-premises en AKS engine op Azure en Azure Stack. Zie de [AKS-engine op Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)voor meer informatie.
- De versies van Kubernetes en het ondersteunings beleid zijn hetzelfde als de versies van AKS die worden [ondersteund](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Netwerk firewall vereisten

De informatie in de volgende tabel bevat de proxy-en firewall configuratie gegevens die zijn vereist voor de container agent om te communiceren met Azure Monitor voor containers. Al het netwerk verkeer van de agent is uitgaand naar Azure Monitor.

|Agentresource|Poorten |
|--------------|------|
| `*.ods.opinsights.azure.com` | 443 |  
| `*.oms.opinsights.azure.com` | 443 |
| `dc.services.visualstudio.com` | 443 |
| `*.monitoring.azure.com` | 443 |
| `login.microsoftonline.com` | 443 |

De informatie in de volgende tabel bevat de proxy-en firewall configuratie-informatie voor Azure China 21Vianet.

|Agentresource|Poorten |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.cn` | 443 | Gegevensopname |
| `*.oms.opinsights.azure.cn` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | Voor agent-telemetrie met behulp van open bare Azure-Cloud Application Insights. |

De informatie in de volgende tabel bevat de proxy-en firewall configuratie gegevens voor de Amerikaanse overheid van Azure.

|Agentresource|Poorten |Description | 
|--------------|------|-------------|
| `*.ods.opinsights.azure.us` | 443 | Gegevensopname |
| `*.oms.opinsights.azure.us` | 443 | OMS-onboarding |
| `dc.services.visualstudio.com` | 443 | Voor agent-telemetrie met behulp van open bare Azure-Cloud Application Insights. |

## <a name="components"></a>Onderdelen

Uw vermogen om de prestaties te bewaken, is afhankelijk van een container Log Analytics-agent voor Linux speciaal ontwikkeld voor de Azure Monitor voor containers. Deze gespecialiseerde agent verzamelt prestatie-en gebeurtenis gegevens van alle knoop punten in het cluster en de agent wordt automatisch geïmplementeerd en geregistreerd bij de opgegeven Log Analytics-werk ruimte tijdens de implementatie. De agent versie is micro soft/OMS: ciprod04202018 of hoger, en wordt vertegenwoordigd door een datum in de volgende notatie: *mmddyyyy*.

>[!NOTE]
>Met de preview-versie van Windows Server-ondersteuning voor AKS is er voor een AKS-cluster met Windows Server-knoop punten geen agent geïnstalleerd om gegevens te verzamelen en door te sturen naar Azure Monitor. In plaats daarvan wordt een Linux-knoop punt dat automatisch in het cluster wordt geïmplementeerd als onderdeel van de standaard implementatie, worden de gegevens verzameld en doorgestuurd naar Azure Monitor voor alle Windows-knoop punten in het cluster.  
>

Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt deze automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS). Zie [Release aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod)van de agent als u de gepubliceerde versies wilt volgen.

>[!NOTE]
>Als u al een AKS-cluster hebt geïmplementeerd, schakelt u de bewaking in met behulp van Azure CLI of een gegeven Azure Resource Manager sjabloon, zoals verderop in dit artikel wordt beschreven. U kunt niet gebruiken `kubectl` om de agent bij te werken, te verwijderen, opnieuw te implementeren of te implementeren.
>De sjabloon moet worden geïmplementeerd in dezelfde resource groep als het cluster.

U schakelt Azure Monitor voor containers in met behulp van een van de volgende methoden die worden beschreven in de volgende tabel.

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Nieuw Kubernetes-cluster | [Een AKS-cluster maken met behulp van Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| U kunt de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met Azure CLI. |
| | [Een AKS-cluster maken met behulp van terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| U kunt de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met behulp van de open source-terraform. |
| | [Open Shift-cluster maken met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | U kunt de bewaking inschakelen van een nieuw open Shift-cluster dat u maakt met een vooraf geconfigureerd Azure Resource Manager sjabloon. |
| | [Open Shift-cluster maken met behulp van Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | U kunt bewaking inschakelen tijdens het implementeren van een nieuw open Shift-cluster met behulp van Azure CLI. |
| Bestaand Kubernetes-cluster | [Inschakelen voor AKS-cluster met behulp van Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met behulp van Azure CLI. |
| |[Inschakelen voor AKS-cluster met behulp van terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met het open source-hulp programma terraform. |
| | [Inschakelen voor AKS-cluster van Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| U kunt de bewaking inschakelen van een of meer AKS-clusters die al zijn geïmplementeerd op de pagina met meerdere clusters in Azure Monitor. |
| | [Inschakelen vanuit AKS-cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| U kunt de bewaking rechtstreeks vanuit een AKS-cluster inschakelen in de Azure Portal. |
| | [Inschakelen voor AKS-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| U kunt de bewaking van een AKS-cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager-sjabloon. |
| | [Inschakelen voor hybride Kubernetes-cluster](container-insights-hybrid-setup.md) | U kunt de bewaking van een AKS-engine inschakelen die wordt gehost in Azure Stack of voor Kubernetes die on-premises worden gehost. |
| | [Inschakelen voor Kubernetes-cluster met Arc-functionaliteit](container-insights-enable-arc-enabled-clusters.md). | U kunt de bewaking inschakelen van uw Kubernetes-clusters die buiten Azure worden gehost en die zijn ingeschakeld met Azure Arc. |
| | [Inschakelen voor open Shift-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | U kunt de bewaking van een bestaand openstaande cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Inschakelen voor open Shift-cluster van Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | U kunt de bewaking inschakelen van een of meer open Shift-clusters die al zijn geïmplementeerd op de pagina met meerdere clusters in Azure Monitor. |

## <a name="next-steps"></a>Volgende stappen

- Als bewaking is ingeschakeld, kunt u beginnen met het analyseren van de prestaties van uw Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS), Azure Stack of een andere omgeving. Zie [Kubernetes-cluster prestaties weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure monitor voor containers.

---
title: Azure Monitor voor containers inschakelen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers inschakelt en configureert, zodat u kunt begrijpen hoe uw container presteert en welke prestatie problemen zijn geïdentificeerd.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: fce2699c18f0fe426b85c165656100c097e69598
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75404325"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor inschakelen voor containers

In dit artikel vindt u een overzicht van de beschik bare opties voor het instellen van Azure Monitor voor containers om de prestaties te bewaken van werk belastingen die in Kubernetes-omgevingen zijn geïmplementeerd en die worden gehost op:

- [Azure Kubernetes service](https://docs.microsoft.com/azure/aks/) (AKS)

- AKS-engine op [Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) of Kubernetes on-premises geïmplementeerd.

- [Azure Red Hat open Shift](../../openshift/intro-openshift.md)

Azure Monitor voor containers kunnen worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van Kubernetes met behulp van de volgende ondersteunde methoden:

- Van de Azure Portal, Azure PowerShell of met Azure CLI

- Met behulp van [Terraform en AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Voordat u begint, zorg ervoor dat u het volgende hebt:

- **Een Log Analytics-werk ruimte.**

    Azure Monitor voor containers ondersteunt een Log Analytics-werk ruimte in de regio's die worden vermeld in azure- [producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    U kunt een werk ruimte maken wanneer u de bewaking van uw nieuwe AKS-cluster inschakelt of de voorbereidings ervaring een standaard werkruimte maakt in de standaard resource groep van het AKS-cluster abonnement. Als u wilt deze zelf maken, kunt u het maken via [Azure Resource Manager](../platform/template-workspace-configuration.md), tot en met [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json), of in de [Azure-portal](../learn/quick-create-workspace.md). Zie [regio toewijzing voor Azure monitor voor containers](container-insights-region-mapping.md)voor een lijst met de ondersteunde toewijzings paren die worden gebruikt voor de standaardwerk ruimte.

- U bent lid van de **rol log Analytics Inzender** om container bewaking in te scha kelen. Zie voor meer informatie over het beheren van toegang tot een Log Analytics-werkruimte [werkruimten beheren](../platform/manage-access.md).

- U bent lid van de rol **[eigenaar](../../role-based-access-control/built-in-roles.md#owner)** op de cluster bron AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Prometheus-metrische gegevens worden standaard niet verzameld. Voordat u [de agent configureert](container-insights-prometheus-integration.md) om deze te verzamelen, is het belang rijk dat u de Prometheus- [documentatie](https://prometheus.io/) raadpleegt om te begrijpen wat er kan worden geschrootd en welke methoden worden ondersteund.

## <a name="supported-configurations"></a>Ondersteunde configuraties

Het volgende wordt officieel ondersteund met Azure Monitor voor containers.

- Omgevingen: Azure Red Hat open Shift, Kubernetes on-premises en AKS engine op Azure en Azure Stack. Zie de [AKS-engine op Azure stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)voor meer informatie.
- De versies van Kubernetes en het ondersteunings beleid zijn hetzelfde als de versies van AKS die worden [ondersteund](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Netwerkvereisten voor de firewall

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
| Microsoft.com | 80 | Wordt gebruikt voor de netwerk verbinding. Dit is alleen vereist als de versie van de agent installatie kopie ciprod09262019 of eerder is. |
| dc.services.visualstudio.com | 443 | Voor agent-telemetrie met behulp van open bare Azure-Cloud Application Insights. |

De informatie in de volgende tabel bevat de proxy-en firewall configuratie gegevens voor de Amerikaanse overheid van Azure.

|Agentresource|Poorten |Beschrijving | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Gegevensopname |
| *.oms.opinsights.azure.us | 443 | OMS-onboarding |
| *.blob.core.windows.net | 443 | Wordt gebruikt voor het bewaken van uitgaande verbindingen. |
| Microsoft.com | 80 | Wordt gebruikt voor de netwerk verbinding. Dit is alleen vereist als de versie van de agent installatie kopie ciprod09262019 of eerder is. |
| dc.services.visualstudio.com | 443 | Voor agent-telemetrie met behulp van open bare Azure-Cloud Application Insights. |

## <a name="components"></a>Onderdelen

Uw vermogen om de prestaties te bewaken, is afhankelijk van een container Log Analytics-agent voor Linux speciaal ontwikkeld voor de Azure Monitor voor containers. Deze gespecialiseerde agent verzamelt prestaties en gebeurtenisgegevens van alle knooppunten in het cluster en de agent wordt automatisch geregistreerd en geïmplementeerd met de opgegeven Log Analytics-werkruimte tijdens de implementatie. De versie van de agent wordt door microsoft / oms:ciprod04202018 of hoger, en wordt vertegenwoordigd door een datum in de volgende indeling: *mmddyyyy*.

>[!NOTE]
>Met de preview-versie van Windows Server-ondersteuning voor AKS is er voor een AKS-cluster met Windows Server-knoop punten geen agent geïnstalleerd om gegevens te verzamelen en door te sturen naar Azure Monitor. In plaats daarvan wordt een Linux-knoop punt dat automatisch in het cluster wordt geïmplementeerd als onderdeel van de standaard implementatie, worden de gegevens verzameld en doorgestuurd naar Azure Monitor voor alle Windows-knoop punten in het cluster.  
>

Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt deze automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost in Azure Kubernetes Service (AKS). Als u wilt volgen de versies die zijn uitgebracht, Zie [agent aankondigingen](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Als u al een AKS-cluster hebt geïmplementeerd, kunt u inschakelen bewaking met behulp van Azure CLI of een opgegeven Azure Resource Manager-sjabloon, zoals verderop in dit artikel wordt gedemonstreerd. U kunt geen gebruiken `kubectl` als u wilt bijwerken, verwijderen, opnieuw implementeren of implementeren van de agent.
>De sjabloon opnieuw moet worden geïmplementeerd in dezelfde resourcegroep bevinden als het cluster.

U schakelt Azure Monitor voor containers in met behulp van een van de volgende methoden die worden beschreven in de volgende tabel.

| Implementatie status | Methode | Beschrijving |
|------------------|--------|-------------|
| Nieuw Kubernetes-cluster | [Een AKS-cluster maken met behulp van Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| U kunt de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met Azure CLI. |
| | [Een AKS-cluster maken met behulp van terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| U kunt de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met behulp van de open source-terraform. |
| | [Open Shift-cluster maken met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | U kunt de bewaking inschakelen van een nieuw open Shift-cluster dat u maakt met een vooraf geconfigureerd Azure Resource Manager sjabloon. |
| Bestaand Kubernetes-cluster | [Inschakelen voor AKS-cluster met behulp van Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met behulp van Azure CLI. |
| |[Inschakelen voor AKS-cluster met behulp van terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | U kunt de bewaking van een AKS-cluster inschakelen dat al is geïmplementeerd met het open source-hulp programma terraform. |
| | [Inschakelen voor AKS-cluster van Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| U kunt de bewaking inschakelen van een of meer AKS-clusters die al zijn geïmplementeerd op de pagina met meerdere clusters in Azure Monitor. |
| | [Inschakelen vanuit AKS-cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| U kunt de bewaking rechtstreeks vanuit een AKS-cluster inschakelen in de Azure Portal. |
| | [Inschakelen voor AKS-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| U kunt de bewaking van een AKS-cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager-sjabloon. |
| | [Inschakelen voor hybride Kubernetes-cluster](container-insights-hybrid-setup.md) | U kunt de bewaking van een AKS-engine inschakelen die wordt gehost in Azure Stack of voor Kubernetes die on-premises worden gehost. |
| | [Inschakelen voor open Shift-cluster met behulp van een Azure Resource Manager sjabloon](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | U kunt de bewaking van een bestaand openstaande cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager sjabloon. |
| | [Inschakelen voor open Shift-cluster van Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | U kunt de bewaking inschakelen van een of meer open Shift-clusters die al zijn geïmplementeerd op de pagina met meerdere clusters in Azure Monitor. |

## <a name="next-steps"></a>Volgende stappen

- Als bewaking is ingeschakeld, kunt u beginnen met het analyseren van de prestaties van uw Kubernetes-clusters die worden gehost op Azure Kubernetes service (AKS), Azure Stack of een andere omgeving. Zie [Kubernetes-cluster prestaties weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure monitor voor containers.

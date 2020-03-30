---
title: Azure-monitor inschakelen voor containers | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u Azure Monitor inschakelt en configureert voor containers, zodat u begrijpen hoe uw container presteert en welke prestatiegerelateerde problemen zijn geïdentificeerd.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275306"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure-monitor inschakelen voor containers

In dit artikel vindt u een overzicht van de opties die beschikbaar zijn voor het instellen van Azure Monitor voor containers om de prestaties van workloads te controleren die zijn geïmplementeerd in Kubernetes-omgevingen en worden gehost op:

- [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/) (AKS)

- Zelfbeheerde Kubernetes-clusters gehost op Azure met [AKS Engine](https://github.com/Azure/aks-engine).

- Zelfbeheerde Kubernetes-clusters gehost op [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) of on-premises met AKS Engine.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor voor containers kan worden ingeschakeld voor nieuwe of een of meer bestaande implementaties van Kubernetes met behulp van de volgende ondersteunde methoden:

- Vanuit de Azure-portal, Azure PowerShell of azure CLI

- Terraform [en AKS gebruiken](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Vereisten

Controleer voordat u begint het volgende:

- **Een Log Analytics-werkruimte.**

    Azure Monitor voor containers ondersteunt een Log Analytics-werkruimte in de regio's die worden vermeld in [Azure-producten per regio](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor).

    U een werkruimte maken wanneer u de bewaking van uw nieuwe AKS-cluster inschakelt of de onboarding-ervaring een standaardwerkruimte laat maken in de standaardbrongroep van het AKS-clusterabonnement. Als u ervoor kiest om het zelf te maken, u het maken via [Azure Resource Manager,](../platform/template-workspace-configuration.md)via [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json)of in de [Azure-portal](../learn/quick-create-workspace.md). Zie [Regiotoewijzing voor Azure Monitor voor containers voor](container-insights-region-mapping.md)een lijst met de ondersteunde toewijzingsparen die worden gebruikt voor de standaardwerkruimte.

- U bent lid van de **rol van de bijdrager log Analytics** om containerbewaking mogelijk te maken. Zie [Werkruimten beheren](../platform/manage-access.md)voor meer informatie over het beheren van toegang tot een Log Analytics-werkruimte.

- U bent lid van de rol **[Eigenaar](../../role-based-access-control/built-in-roles.md#owner)** op de AKS-clusterbron.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Prometheus-statistieken worden niet standaard verzameld. Voordat [u de agent configureert](container-insights-prometheus-integration.md) om ze te verzamelen, is het belangrijk dat u de Prometheus-documentatie bekijkt om te begrijpen wat kan worden geschraapt en methoden worden ondersteund. [documentation](https://prometheus.io/)

## <a name="supported-configurations"></a>Ondersteunde configuraties

Het volgende wordt officieel ondersteund met Azure Monitor voor containers.

- Omgevingen: Azure Red Hat OpenShift, Kubernetes on-premises en AKS Engine op Azure en Azure Stack. Zie [AKS Engine op Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)voor meer informatie.
- Versies van Kubernetes en ondersteuningsbeleid zijn hetzelfde als versies van [AKS ondersteund](../../aks/supported-kubernetes-versions.md). 

## <a name="network-firewall-requirements"></a>Vereisten voor netwerkfirewall

De informatie in de volgende tabel bevat de proxy- en firewallconfiguratiegegevens die nodig zijn voor de containeragent om te communiceren met Azure Monitor voor containers. Al het netwerkverkeer van de agent is uitgaand naar Azure Monitor.

|Agentresource|Poorten |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| *.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

De informatie in de volgende tabel bevat de proxy- en firewallconfiguratiegegevens voor Azure China.

|Agentresource|Poorten |Beschrijving | 
|--------------|------|-------------|
| *.ods.opinsights.azure.cn | 443 | Gegevensopname |
| *.oms.opinsights.azure.cn | 443 | OMS onboarding |
| *.blob.core.windows.net | 443 | Wordt gebruikt voor het bewaken van uitgaande connectiviteit. |
| microsoft.com | 80 | Wordt gebruikt voor netwerkconnectiviteit. Dit is alleen vereist als de versie van de agentafbeelding ciprod09262019 of eerder is. |
| dc.services.visualstudio.com | 443 | Voor agenttelemetrie met Azure Public Cloud Application Insights. |

De informatie in de volgende tabel bevat de proxy- en firewallconfiguratiegegevens voor Azure US Government.

|Agentresource|Poorten |Beschrijving | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Gegevensopname |
| *.oms.opinsights.azure.us | 443 | OMS onboarding |
| *.blob.core.windows.net | 443 | Wordt gebruikt voor het bewaken van uitgaande connectiviteit. |
| microsoft.com | 80 | Wordt gebruikt voor netwerkconnectiviteit. Dit is alleen vereist als de versie van de agentafbeelding ciprod09262019 of eerder is. |
| dc.services.visualstudio.com | 443 | Voor agenttelemetrie met Azure Public Cloud Application Insights. |

## <a name="components"></a>Onderdelen

Uw vermogen om de prestaties te monitoren is afhankelijk van een containerized Log Analytics-agent voor Linux die speciaal is ontwikkeld voor Azure Monitor voor containers. Deze gespecialiseerde agent verzamelt prestatie- en gebeurtenisgegevens van alle knooppunten in het cluster en de agent wordt tijdens de implementatie automatisch geïmplementeerd en geregistreerd bij de opgegeven Log Analytics-werkruimte. De agentversie is microsoft/oms:ciprod04202018 of hoger, en wordt vertegenwoordigd door een datum in het volgende formaat: *mmddyyyyy*.

>[!NOTE]
>Met de preview-release van Windows Server-ondersteuning voor AKS heeft een AKS-cluster met Windows Server-knooppunten geen agent geïnstalleerd om gegevens te verzamelen en door te sturen naar Azure Monitor. In plaats daarvan wordt een Linux-knooppunt automatisch geïmplementeerd in het cluster als onderdeel van de standaardimplementatie, die de gegevens verzamelt en doorstuurt naar Azure Monitor namens alle Windows-knooppunten in het cluster.  
>

Wanneer een nieuwe versie van de agent wordt uitgebracht, wordt deze automatisch bijgewerkt op uw beheerde Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS). Als u de uitgebrachte versies wilt volgen, ziet u [de aankondigingen van de agentrelease](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Als u al een AKS-cluster hebt geïmplementeerd, schakelt u bewaking in met Azure CLI of een meegeleverde Azure Resource Manager-sjabloon, zoals later in dit artikel wordt aangetoond. U kunt `kubectl` de agent niet upgraden, verwijderen, opnieuw implementeren of implementeren.
>De sjabloon moet worden geïmplementeerd in dezelfde resourcegroep als het cluster.

U schakelt Azure Monitor in voor containers met behulp van een van de volgende methoden die in de volgende tabel zijn beschreven.

| Implementatiestatus | Methode | Beschrijving |
|------------------|--------|-------------|
| Nieuw AKS Kubernetes-cluster | [AKS-cluster maken met Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| U de bewaking inschakelen van een nieuw AKS-cluster dat u maakt met Azure CLI. |
| | [AKS-cluster maken met Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| U het monitoren van een nieuw AKS-cluster inschakelen dat u maakt met behulp van de open-source tool Terraform. |
| | [OpenShift-cluster maken met een sjabloon Azure Resource Manager](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | U de bewaking inschakelen van een nieuw OpenShift-cluster dat u maakt met een vooraf geconfigureerde Azure Resource Manager-sjabloon. |
| | [OpenShift-cluster maken met Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | U bewaking inschakelen terwijl u een nieuw OpenShift-cluster implementeert met Azure CLI. |
| Bestaand AKS Kubernetes-cluster | [Inschakelen voor AKS-cluster met Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | U de bewaking inschakelen van een AKS-cluster dat al is geïmplementeerd met Azure CLI. |
| |[Inschakelen voor AKS-cluster met Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | U het monitoren van een AKS-cluster inschakelen dat al is geïmplementeerd met behulp van de open-sourcetool Terraform. |
| | [Inschakelen voor AKS-cluster vanuit Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| U bewaking inschakelen van een of meer AKS-clusters die al zijn geïmplementeerd vanaf de pagina met meerdere clusters in Azure Monitor. |
| | [Inschakelen vanuit AKS-cluster](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| U bewaking rechtstreeks vanuit een AKS-cluster inschakelen in de Azure-portal. |
| | [Inschakelen voor AKS-cluster met behulp van een Azure Resource Manager-sjabloon](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| U bewaking van een AKS-cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager-sjabloon. |
| | [Inschakelen voor hybride Kubernetes-cluster](container-insights-hybrid-setup.md) | U het bewaken van een AKS-engine die wordt gehost in Azure Stack of voor on-premises gehoste Kubernetes inschakelen. |
| | [Inschakelen voor OpenShift-cluster met behulp van een Azure Resource Manager-sjabloon](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | U bewaking van een bestaand OpenShift-cluster inschakelen met een vooraf geconfigureerde Azure Resource Manager-sjabloon. |
| | [Inschakelen voor OpenShift-cluster vanuit Azure-monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | U bewaking inschakelen van een of meer OpenShift-clusters die al zijn geïmplementeerd vanaf de multiclusterpagina in Azure Monitor. |

## <a name="next-steps"></a>Volgende stappen

- Als bewaking is ingeschakeld, u beginnen met het analyseren van de prestaties van uw Kubernetes-clusters die worden gehost op Azure Kubernetes Service (AKS), Azure Stack of andere omgeving. Zie [Kubernetes-clusterprestaties weergeven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure Monitor voor containers.

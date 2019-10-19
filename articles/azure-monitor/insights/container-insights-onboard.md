---
title: Azure Monitor voor containers inschakelen | Microsoft Docs
description: In dit artikel wordt beschreven hoe u Azure Monitor voor containers inschakelt en configureert, zodat u kunt begrijpen hoe uw container presteert en welke prestatie problemen zijn geïdentificeerd.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/12/2019
ms.openlocfilehash: 44cdc2d6b93ac9a62f96875ca6c679fbb97d85a9
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555398"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Azure Monitor inschakelen voor containers

In dit artikel vindt u een overzicht van de beschik bare opties voor het instellen van Azure Monitor voor containers om de prestaties te bewaken van werk belastingen die in Kubernetes-omgevingen zijn geïmplementeerd en die worden gehost op de [Azure Kubernetes-service](https://docs.microsoft.com/azure/aks/).

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

* Prometheus-metrische gegevens worden standaard niet verzameld. Voordat u [de agent configureert](container-insights-agent-config.md) om deze te verzamelen, is het belang rijk dat u de Prometheus- [documentatie](https://prometheus.io/) bekijkt om te begrijpen wat u kunt definiëren.

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

## <a name="next-steps"></a>Volgende stappen

* Als bewaking is ingeschakeld voor het vastleggen van metrische gegevens over de status van de AKS-cluster knooppunten en de peulen, zijn deze metrische gegevens over de status beschikbaar in de Azure Portal. Zie [Azure Kubernetes service Health weer geven](container-insights-analyze.md)voor meer informatie over het gebruik van Azure monitor voor containers.

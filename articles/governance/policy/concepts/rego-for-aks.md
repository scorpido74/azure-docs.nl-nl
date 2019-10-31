---
title: Meer informatie over Azure Policy voor de Azure Kubernetes-service
description: Meer informatie over hoe Azure Policy Rego gebruikt en beleids agent opent voor het beheren van clusters op de Azure Kubernetes-service.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: 6a3d1fb347819015887ffc4fd8089bbc1f3a70de
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176314"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Meer informatie over Azure Policy voor de Azure Kubernetes-service

Azure Policy integreert met de [Azure Kubernetes-service](../../../aks/intro-kubernetes.md) (AKS) om op schaal afdwingingen en beveiligingen op uw clusters op een gecentraliseerde, consistente manier toe te passen.
Door het gebruik van [gate keeper](https://github.com/open-policy-agent/gatekeeper), een _toegangs controller Webhook_ voor [Open Policy Agent](https://www.openpolicyagent.org/) (opa) uit te breiden, kunt Azure Policy de nalevings status van uw Azure-resources en AKS-clusters op één plek beheren en rapporteren.

> [!NOTE]
> Azure Policy voor AKS is een beperkte preview-versie en ondersteunt alleen ingebouwde beleids definities.

## <a name="overview"></a>Overzicht

Als u Azure Policy wilt inschakelen en gebruiken voor AKS met uw AKS-cluster, voert u de volgende acties uit:

- [Opt-in voor preview-functies](#opt-in-for-preview)
- [De Azure Policy-invoeg toepassing installeren](#installation-steps)
- [Een beleids definitie voor AKS toewijzen](#built-in-policies)
- [Wachten op validatie](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Opt-in voor preview

Voordat u de Azure Policy invoeg toepassing installeert of een van de service functies inschakelt, moet uw abonnement de resource provider **micro soft. container service** en de resource provider **micro soft. PolicyInsights** inschakelen en vervolgens worden goedgekeurd om Neem lid van de preview-versie. Als u wilt deel nemen aan de preview, volgt u deze stappen in de Azure Portal of met Azure CLI:

- Azure Portal:

  1. Registreer de resource providers **micro soft. container service** en **micro soft. PolicyInsights** . Zie [resource providers en-typen](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal)voor instructies.

  1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

     ![Beleid zoeken in alle services](../media/rego-for-aks/search-policy.png)

  1. Selecteer **deel nemen** aan de linkerkant van de pagina Azure Policy.

     ![Word lid van het beleid voor AKS preview](../media/rego-for-aks/join-aks-preview.png)

  1. Selecteer de rij van het abonnement dat u wilt toevoegen aan de preview.

  1. Selecteer de knop **opt-in** boven aan de lijst met abonnementen.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Invoeg toepassing Azure Policy

De _Azure Policy-invoeg toepassing_ voor Kubernetes verbindt de Azure Policy-service met de gate keeper Admission controller. De invoeg toepassing, die is geïnstalleerd in de naam ruimte van het _Azure-beleid_ , heeft de volgende functies:

- Controleert met Azure Policy voor toewijzingen aan het AKS-cluster
- Hiermee worden beleids gegevens gedownload en in de cache opgeslagen, met inbegrip van de _Rego_ -beleids definitie, zoals **configmaps**
- Hiermee wordt een volledige controle op de naleving van het AKS-cluster uitgevoerd
- Rapporteert controle-en nalevings details terug naar Azure Policy

### <a name="installing-the-add-on"></a>De invoeg toepassing installeren

#### <a name="prerequisites"></a>Vereisten

Voordat u de invoeg toepassing in uw AKS-cluster installeert, moet de uitbrei ding van de preview-versie zijn geïnstalleerd. Deze stap wordt uitgevoerd met Azure CLI:

1. U moet de Azure CLI-versie 2.0.62 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Het AKS-cluster moet versie _1,10_ of hoger zijn. Gebruik het volgende script om uw AKS-cluster versie te valideren:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installeer versie _0.4.0_ van de Azure cli preview-uitbrei ding voor AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Als u de uitbrei ding _voor AKS-preview_ eerder hebt geïnstalleerd, installeert u updates met behulp van de opdracht `az extension update --name aks-preview`.

#### <a name="installation-steps"></a>Installatie stappen

Zodra de vereisten zijn voltooid, installeert u de Azure Policy-invoeg toepassing in het AKS-cluster dat u wilt beheren.

- Azure Portal

  1. Start de AKS-service in de Azure Portal door op **alle services**te klikken en vervolgens op **Kubernetes Services**te zoeken en te selecteren.

  1. Selecteer een van uw AKS-clusters.

  1. Selecteer **beleid (preview-versie)** aan de linkerkant van de Kubernetes-service pagina.

     ![Beleids regels van het AKS-cluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Selecteer op de hoofd pagina de knop **invoeg toepassing inschakelen** .

     ![De Azure Policy voor de invoeg toepassing voor AKS inschakelen](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Als de knop **invoeg toepassing inschakelen** grijs wordt weer gegeven, is het abonnement nog niet toegevoegd aan de preview-versie. Zie [opt-in voor een voor beeld](#opt-in-for-preview) voor de vereiste stappen.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validatie-en rapportage frequentie

De invoeg toepassing controleert met Azure Policy op wijzigingen in beleids toewijzingen om de vijf minuten. Tijdens deze vernieuwings cyclus verwijdert de invoeg toepassing alle _configmaps_ in de naam ruimte van _Azure-beleid_ en wordt vervolgens de _Configmaps_ voor het gebruik van gate keeper opnieuw gemaakt.

> [!NOTE]
> Hoewel een _cluster beheerder_ machtiging kan hebben voor de naam ruimte van _Azure-beleid_ , wordt dit niet aanbevolen of wordt niet ondersteund voor het aanbrengen van wijzigingen in de naam ruimte. Hand matige wijzigingen die zijn aangebracht, gaan verloren tijdens de vernieuwings cyclus.

Elke 5 minuten wordt de invoeg toepassing aangeroepen voor een volledige scan van het cluster. Na het verzamelen van Details van de volledige scan en eventuele realtime-evaluaties door gate keeper van pogingen om wijzigingen aan te brengen in het cluster, worden de resultaten weer gegeven in Azure Policy om te worden opgenomen in [compatibiliteits Details](../how-to/get-compliance-data.md) zoals een Azure Policy toewijzing. Er worden alleen resultaten voor actieve beleids toewijzingen geretourneerd tijdens de controle cyclus.

## <a name="policy-language"></a>Beleids taal

De Azure Policy taal structuur voor het beheren van AKS volgt die van bestaande beleids regels. Het effect _EnforceRegoPolicy_ wordt gebruikt voor het beheren van uw AKS-clusters en bevat _gedetailleerde_ eigenschappen die specifiek zijn voor het werken met opa en gate keeper. Zie het effect [EnforceRegoPolicy](effects.md#enforceregopolicy) voor meer informatie en voor beelden.

Als onderdeel van de eigenschap _Details. beleid_ in de beleids definitie geeft Azure Policy de URI van een Rego-beleid door aan de invoeg toepassing. Rego is de taal die OPA en gate keeper ondersteunen voor het valideren of mutate van een aanvraag naar het Kubernetes-cluster. Dankzij de ondersteuning van een bestaande standaard voor Kubernetes-beheer, maakt Azure Policy het mogelijk om bestaande regels opnieuw te gebruiken en deze te koppelen aan Azure Policy voor een uniforme rapportage van de naleving van de Cloud. Zie [Wat is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)voor meer informatie.

## <a name="built-in-policies"></a>Ingebouwd beleid

Voer de volgende stappen uit om het ingebouwde beleid voor het beheren van AKS met behulp van de Azure Portal te vinden:

1. Start de Azure Policy-service in de Azure Portal. Selecteer **alle services** in het linkerdeel venster en zoek en selecteer vervolgens **beleid**.

1. Selecteer in het linkerdeel venster van de pagina Azure Policy **definities**.

1. Gebruik in de vervolg keuzelijst Categorie de **optie Alles selecteren** om het filter te wissen en selecteer vervolgens **Kubernetes-service**.

1. Selecteer de beleids definitie en selecteer vervolgens de knop **toewijzen** .

> [!NOTE]
> Bij het toewijzen van de Azure Policy voor AKS definitie moet het **bereik** de AKS-cluster resource bevatten.

U kunt ook de Snelstartgids [een beleid toewijzen-Portal](../assign-policy-portal.md) gebruiken om een AKS-beleid te zoeken en toe te wijzen. Zoek in plaats van het voor beeld ' vm's controleren ' naar een Kubernetes-beleids definitie.

## <a name="logging"></a>Logboekregistratie

### <a name="azure-policy-add-on-logs"></a>Azure Policy-invoeg logboeken

Als Kubernetes-controller/-container houdt de invoeg toepassing Azure Policy Logboeken in het AKS-cluster. De logboeken worden weer gegeven op de pagina **inzichten** van het AKS-cluster. Zie [inzicht in AKS-cluster prestaties met Azure monitor voor containers](../../../azure-monitor/insights/container-insights-analyze.md)voor meer informatie.

### <a name="gatekeeper-logs"></a>Gate keeper logboeken

Als u gate keeper logboeken voor nieuwe bron aanvragen wilt inschakelen, volgt u de stappen in [Logboeken van het Kubernetes-hoofd knooppunt inschakelen en controleren in AKS](../../../aks/view-master-logs.md).
Hier volgt een voorbeeld query voor het weer geven van geweigerde gebeurtenissen voor nieuwe resource aanvragen:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Als u logboeken van gate keeper-containers wilt weer geven, volgt u de stappen in [Enable and check Kubernetes master node logs in AKS](../../../aks/view-master-logs.md) en controleert u de optie _uitvoeren-apiserver_ in het deel venster **Diagnostische instellingen** .

## <a name="remove-the-add-on"></a>De invoeg toepassing verwijderen

Als u de invoeg toepassing Azure Policy wilt verwijderen uit uw AKS-cluster, gebruikt u de Azure Portal of Azure CLI:

- Azure Portal

  1. Start de AKS-service in de Azure Portal door op **alle services**te klikken en vervolgens op **Kubernetes Services**te zoeken en te selecteren.

  1. Selecteer uw AKS-cluster waarvoor u de invoeg toepassing Azure Policy wilt uitschakelen.

  1. Selecteer **beleid (preview-versie)** aan de linkerkant van de Kubernetes-service pagina.

     ![Beleids regels van het AKS-cluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Op de hoofd pagina selecteert u de knop **invoeg toepassing uitschakelen** .

     ![De Azure Policy voor de invoeg toepassing AKS uitschakelen](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Bekijk de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/getting-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).

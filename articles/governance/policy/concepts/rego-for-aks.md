---
title: Meer informatie over Azure Policy voor Azure Kubernetes-service
description: Lees hoe Azure Policy Rego en Open Policy Agent gebruikt om clusters te beheren op Azure Kubernetes Service.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372647"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Informatie over Azure Policy voor Azure Kubernetes Service

Azure Policy integreert met de [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) om op schaal handhavingen en beveiligingen op uw clusters toe te passen op een gecentraliseerde, consistente manier.
Door het gebruik van [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, een _webhook_ voor [toegangscontroller](https://www.openpolicyagent.org/) voor Open Policy Agent (OPA), azure policy uit te breiden, is het mogelijk om de nalevingsstatus van uw Azure-resources en AKS-clusters vanaf één plaats te beheren en te rapporteren.

> [!IMPORTANT]
> Azure Policy voor AKS bevindt zich in Preview en ondersteunt alleen ingebouwde beleidsdefinities. Ingebouwd beleid bevalt in de categorie **Kubernetes.** Het **effect EnforceRegoPolicy** en het bijbehorende **Kubernetes Service-categoriebeleid** worden _afgeschaft._ Gebruik in plaats daarvan het bijgewerkte effect [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)

> [!WARNING]
> Deze functie is nog niet in alle regio's beschikbaar. Zie [AKS-problemen voor](https://github.com/Azure/AKS/issues/1529)een status in de implementatie voor een status in de implementatie : Wijzigingen in de toepassing van het beleid .

## <a name="overview"></a>Overzicht

Ga als volgt te werk om Azure Policy for AKS in te schakelen en te gebruiken met uw AKS-cluster:

- [Aanmelden voor preview-functies](#opt-in-for-preview)
- [De azure-beleidsinvoegtoepassing installeren](#installation-steps)
- [Een beleidsdefinitie toewijzen voor AKS](#built-in-policies)
- [Wachten op validatie](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Aanmelden voor voorbeeld

Voordat u de Azure Policy Add-on installeert of een van de servicefuncties inschakelt, moet uw abonnement de **Microsoft.ContainerService-bronprovider** en de **Microsoft.PolicyInsights-bronprovider** inschakelen en vervolgens worden goedgekeurd om deel te nemen aan de preview. Voer de volgende stappen uit in de Azure-portal of met Azure CLI om deel te nemen aan de preview:

- Azure Portal:

  1. Registreer de **microsoft.containerService-** en **Microsoft.PolicyInsights-bronproviders.** Zie [Resourceproviders en -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal)voor stappen .

  1. Start de Azure Policy-service in Azure Portal door **Alle services** te selecteren en dan **Beleid** te zoeken en te selecteren.

     ![Zoeken naar beleid in alle services](../media/rego-for-aks/search-policy.png)

  1. Selecteer **Join Preview** aan de linkerkant van de Azure-beleidspagina.

     ![Deelnemen aan de preview-voor-wedstrijd Beleid voor AKS](../media/rego-for-aks/join-aks-preview.png)

  1. Selecteer de rij van het abonnement dat u aan het voorbeeld wilt toevoegen.

  1. Selecteer de knop **Opt-in** boven aan de lijst met abonnementen.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Azure-beleidsinvoegtoepassing

De _Azure Policy Add-on_ voor Kubernetes verbindt de Azure Policy-service met de gatekeeper-toegangscontroller. De add-on, die is geïnstalleerd in de naamruimte van het _kube-systeem,_ voert de volgende functies uit:

- Controleert met azure-beleidsservice op toewijzingen aan het cluster.
- Implementeert beleidsregels in het cluster als [constrainttemplate](https://github.com/open-policy-agent/gatekeeper#constraint-templates) en [aangepaste resources met beperkingen.](https://github.com/open-policy-agent/gatekeeper#constraints)
- Rapporteert controle- en nalevingsgegevens terug naar azure policy service.

### <a name="installing-the-add-on"></a>De invoegtoepassing installeren

#### <a name="prerequisites"></a>Vereisten

Voordat u de add-on in uw AKS-cluster installeert, moet de preview-extensie zijn geïnstalleerd. Deze stap wordt gedaan met Azure CLI:

1. Als gatekeeper v2-beleid is geïnstalleerd, verwijdert u de invoegtoepassing met de knop **Uitschakelen** op uw AKS-cluster onder de pagina **Beleid (voorbeeld).**

1. U moet de Azure CLI-versie 2.0.62 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

1. Het AKS-cluster moet versie _1.14_ of hoger zijn. Gebruik het volgende script om uw AKS-clusterversie te valideren:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Installeer versie _0.4.0_ van de Azure CLI `aks-preview`preview-extensie voor AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Als u de _aks-preview-extensie_ eerder hebt geïnstalleerd, installeert u eventuele updates met de `az extension update --name aks-preview` opdracht.

#### <a name="installation-steps"></a>Installatiestappen

Zodra de vereisten zijn voltooid, installeert u de Azure Policy Add-on in het AKS-cluster dat u wilt beheren.

- Azure Portal

  1. Start de AKS-service in de Azure-portal door op **Alle services**te klikken en vervolgens **Kubernetes-services**te zoeken en te selecteren.

  1. Selecteer een van uw AKS-clusters.

  1. Selecteer **Beleid (voorbeeld)** aan de linkerkant van de Kubernetes-servicepagina.

     ![Beleid uit het AKS-cluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Selecteer op de hoofdpagina de knop **Invoegtoepassing inschakelen.**

     ![Het Azure-beleid voor AKS-invoegtoepassing inschakelen](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Als de knop **Invoegtoepassing inschakelen** grijs is, is het abonnement nog niet toegevoegd aan de preview. Zie [Opt-in voor voorbeeld](#opt-in-for-preview) voor de vereiste stappen. Als er een **knop Uitschakelen** beschikbaar is, is Gatekeeper v2 nog steeds geïnstalleerd en moet deze worden verwijderd.

- Azure-CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Validatie- en rapportagefrequentie

De add-on controleert elke 15 minuten bij azure-beleidsservice op wijzigingen in beleidstoewijzingen.
Tijdens deze vernieuwingscyclus controleert de add-on op wijzigingen. Deze wijzigingen leiden tot het maken, bijwerken of verwijderen van de beperkingssjablonen en -beperkingen.

> [!NOTE]
> Hoewel een clusterbeheerder mogelijk toestemming heeft om beperkingssjablonen en beperkingenresources te maken en bij te werken, worden dit geen ondersteunde scenario's omdat handmatige updates worden overschreven.

Elke 15 minuten, de add-on oproepen voor een volledige scan van het cluster. Nadat de add-on details van de volledige scan en eventuele real-time evaluaties door Gatekeeper van pogingen tot wijzigingen in het cluster hebben verzameld, rapporteert de add-on de resultaten terug naar azure policy service voor opname in [nalevingsgegevens](../how-to/get-compliance-data.md#portal) zoals elke Azure Policy-toewijzing. Alleen resultaten voor actieve beleidsopdrachten worden geretourneerd tijdens de auditcyclus. Controleresultaten kunnen ook worden gezien als [schendingen](https://github.com/open-policy-agent/gatekeeper#audit) die worden vermeld in het statusveld van de mislukte beperking.

## <a name="policy-language"></a>Beleidstaal

De taalstructuur azure-beleid voor het beheer van Kubernetes volgt die van bestaand beleid. Het effect _EnforceOPAConstraint_ wordt gebruikt om uw Kubernetes-clusters te beheren en neemt details eigenschappen die specifiek zijn voor het werken met [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) en Gatekeeper v3. Zie het effect [Afdwingen opAConstraint](./effects.md#enforceopaconstraint) voor meer informatie en voorbeelden.
  
Als onderdeel van de _details.constraintTemplate_ en _details.constraint_ eigenschappen in de beleidsdefinitie, geeft Azure Policy de URI's van deze [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) door aan de add-on. Rego is de taal die OPA en Gatekeeper ondersteunen om een aanvraag voor het Kubernetes-cluster te valideren. Door een bestaande standaard voor Kubernetes-beheer te ondersteunen, maakt Azure Policy het mogelijk om bestaande regels opnieuw te gebruiken en deze te koppelen aan Azure Policy voor een uniforme rapportage-ervaring voor cloudcompliance. Zie [Wat is Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego)voor meer informatie.

## <a name="built-in-policies"></a>Ingebouwd beleid

Voer de volgende stappen uit om het ingebouwde beleid voor het beheren van uw cluster te vinden met behulp van de Azure-portal:

1. Start de Azure Policy-service in de Azure-portal. Selecteer Alle services in het linkerdeelvenster en zoek en selecteer **Beleid**.

1. Selecteer **Definities**in het linkerdeelvenster van de pagina Azure Policy .

1. Gebruik alles selecteren om het filter te wissen en selecteer Kubernetes in de vervolgkeuzelijst **Categorie.**

1. Selecteer de beleidsdefinitie en selecteer vervolgens de knop **Toewijzen.**

1. Stel het bereik in **op** de beheergroep, het abonnement of de resourcegroep van het Kubernetes-cluster waar de beleidstoewijzing van toepassing is.

   > [!NOTE]
   > Wanneer u de Azure-beleidsdefinitie voor AKS toeschrijft, moet het **bereik** de AKS-clusterbron bevatten.

1. Geef de beleidstoewijzing een **naam** en **beschrijving** die u gebruiken om deze eenvoudig te identificeren.

1. Stel de [handhaving van het beleid](./assignment-structure.md#enforcement-mode) in op een van de onderstaande waarden.

   - **Ingeschakeld** : Het beleid voor het cluster afdwingen. Kubernetes toelatingsverzoeken met schendingen worden geweigerd.
   
   - **Uitgeschakeld** - Dwing het beleid voor het cluster niet af. Kubernetes-toelatingsverzoeken met schendingen worden niet geweigerd. Nalevingsbeoordelingsresultaten zijn nog beschikbaar. Bij het uitrollen van nieuw beleid naar het uitvoeren van clusters is de optie _Disabled_ handig voor het testen van het beleid, omdat opnameverzoeken met schendingen niet worden geweigerd.

1. Selecteer **Volgende**.

1. **Parameterwaarden** instellen
   
   - Als u Kubernetes-naamruimten wilt uitsluiten van beleidsevaluatie, geeft u de lijst met naamruimten op in parameter **Namespace-uitsluitingen**. Het wordt aanbevolen om uit te sluiten: _kube-systeem_

1. Selecteer **Controleren + maken**.

Gebruik afwisselend het [beleid toewijzen : Snel](../assign-policy-portal.md) start Portal om een AKS-beleid te zoeken en toe te wijzen. Zoek naar een Kubernetes-beleidsdefinitie in plaats van de voorbeeld 'audit vms'.

> [!IMPORTANT]
> Ingebouwd beleid in categorie **Kubernetes** is alleen voor gebruik met AKS. Zie Kubernetes-voorbeelden voor een lijst met ingebouwde [beleidsregels.](../samples/built-in-policies.md#kubernetes)

## <a name="logging"></a>Logboekregistratie

### <a name="azure-policy-add-on-logs"></a>Azure-beleidsinvoegtoepassing

Als Kubernetes-controller/-container houden zowel Azure Policy Add-on als Gatekeeper logboeken bij in het AKS-cluster. De logboeken worden weergegeven op de **pagina Insights** van het AKS-cluster. Zie [AKS-clusterprestaties begrijpen met Azure Monitor voor containers voor](../../../azure-monitor/insights/container-insights-analyze.md)meer informatie.

## <a name="remove-the-add-on"></a>De invoegtoepassing verwijderen

Als u de Azure Policy Add-on wilt verwijderen uit uw AKS-cluster, gebruikt u de Azure-portal of Azure CLI:

- Azure Portal

  1. Start de AKS-service in de Azure-portal door op **Alle services**te klikken en vervolgens **Kubernetes-services**te zoeken en te selecteren.

  1. Selecteer uw AKS-cluster waar u de azure-beleidsinvoegtoepassing wilt uitschakelen.

  1. Selecteer **Beleid (voorbeeld)** aan de linkerkant van de Kubernetes-servicepagina.

     ![Beleid uit het AKS-cluster](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Selecteer op de hoofdpagina de knop **Invoegtoepassing uitschakelen.**

     ![Het Azure-beleid voor AKS-invoegtoepassing uitschakelen](../media/rego-for-aks/disable-policy-add-on.png)

- Azure-CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostische gegevens verzameld door Azure Policy Add-on

De Azure Policy Add-on voor Kubernetes verzamelt beperkte clusterdiagnostische gegevens. Deze diagnostische gegevens zijn essentiële technische gegevens met betrekking tot software en prestaties. Het wordt gebruikt op de volgende manieren:

- Azure Policy Add-up up-to-date houden
- Houd Azure Policy Add-on veilig, betrouwbaar en performant
- Azure Policy Add-on verbeteren - door de geaggregeerde analyse van het gebruik van de add-on

De informatie die door de add-on wordt verzameld, zijn geen persoonlijke gegevens. De volgende gegevens worden momenteel verzameld:

- Azure Policy Add-on agent-versie
- Clustertype
- Clustergebied
- Clusterbrongroep
- Clusterbron-id
- Clusterabonnement-id
- Cluster OS (Voorbeeld: Linux)
- Clusterstad (Voorbeeld: Seattle)
- Clusterstaat of provincie (Voorbeeld: Washington)
- Clusterland of -regio (Voorbeeld: Verenigde Staten)
- Uitzonderingen/fouten die zijn ondervonden bij azure-beleidsadd-on tijdens de installatie van agenten bij beleidsevaluatie
- Aantal Gatekeeper-beleidsregels dat niet is geïnstalleerd door azure-beleidsadd-on

## <a name="next-steps"></a>Volgende stappen

- Voorbeelden bekijken bij [Azure Policy-voorbeelden](../samples/index.md).
- Bekijk de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Begrijpen hoe [u programmatisch beleid maken.](../how-to/programmatically-create.md)
- Meer informatie over het [verzamelen van nalevingsgegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [herstellen van niet-conforme resources.](../how-to/remediate-resources.md)
- Bekijk wat een beheergroep is met [Uw resources organiseren met Azure-beheergroepen.](../../management-groups/overview.md)

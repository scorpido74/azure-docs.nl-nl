---
title: Meer informatie over Azure-beleid voor AKS-engine
description: Lees hoe Azure Policy CustomResourceDefinitions en Open Policy Agent van Gatekeeper v3 gebruikt om clusters met AKS Engine te beheren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436431"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Azure-beleid voor AKS-engine begrijpen

Azure Policy integreert met [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), een systeem dat handige tooling biedt om snel een zelfbeheerd Kubernetes-cluster op Azure op te start zetten. Deze integratie maakt op grote schaal handhavingen en beveiligingen op uw AKS Engine zelfbeheerde clusters op een gecentraliseerde, consistente manier mogelijk. Door het gebruik van [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (bèta), een _webhook_ voor Kubernetes, te uitbreiden, maakt Azure Policy het mogelijk om de nalevingsstatus van uw Azure-resources en AKS Engine zelfbeheerde clusters vanaf één plek te beheren en te rapporteren.

> [!NOTE]
> Azure Policy for AKS Engine staat in Public Preview en heeft geen SLA. Gatekeeper v3 is in Beta en wordt ondersteund door de open source gemeenschap. De service ondersteunt alleen ingebouwde beleidsdefinities en één AKS Engine-cluster voor elke resourcegroep die is geconfigureerd met een Service Principal.

> [!IMPORTANT]
> Als u ondersteuning wilt krijgen voor Azure Policy voor AKS Engine, AKS Engine of Gatekeeper v3, maakt u een [nieuw probleem](https://github.com/Azure/aks-engine/issues/new/choose) in de AKS Engine GitHub-opslagplaats.

## <a name="overview"></a>Overzicht

Ga als volgt te werk om Azure Policy for AKS Engine in te schakelen en te gebruiken met uw zelfbeheerde Kubernetes-cluster op Azure:

- [Vereisten](#prerequisites)
- [De azure-beleidsinvoegtoepassing installeren](#installing-the-add-on)
- [Een beleidsdefinitie toewijzen voor AKS Engine](#built-in-policies)
- [Wachten op validatie](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Vereisten

Voordat u de Azure Policy Add-on installeert of een van de servicefuncties inschakelt, moet uw abonnement de **microsoft.PolicyInsights-bronprovider** inschakelen en een roltoewijzing maken voor de hoofdsom van de clusterservice. 

1. Als u de resourceprovider wilt inschakelen, voert u de stappen in [resourceproviders en -typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) uit of voert u de opdracht Azure CLI of Azure PowerShell uit:

   - Azure-CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell
     
     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell
   
     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell
   
     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Een roltoewijzing maken voor de hoofdsom van de clusterservice

   - Als u de hoofd-app-id van de clusterserviceservice niet kent, zoekt u deze op met de volgende opdracht.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Toewijzing 'Beleidsinsights Data Writer (Preview)' toewijzen aan de hoofdapp-id van de clusterservice (waarde _aadClientID_ van vorige stap) met Azure CLI. Vervang `<subscriptionId>` door uw `<aks engine cluster resource group>` abonnements-ID en met de brongroep bevindt het zelfbeheerde Kubernetes-cluster van AKS Engine zich.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Azure-beleidsinvoegtoepassing

De _Azure Policy Add-on_ voor Kubernetes verbindt de Azure Policy-service met de gatekeeper-toegangscontroller. De add-on, die is geïnstalleerd in de naamruimte van het _kube-systeem,_ voert de volgende functies uit:

- Controleert met Azure Policy voor toewijzingen aan het AKS Engine-cluster
- Beleidsdetails, beperkingssjablonen en beperkingen downloaden en installeren
- Voert een volledige scancompliancecontrole uit op het AKS Engine-cluster
- Rapporten over controle- en nalevingsgegevens terug naar Azure-beleid

### <a name="installing-the-add-on"></a>De invoegtoepassing installeren

Zodra de vereisten zijn voltooid, kan de Azure Policy Add-on worden geïnstalleerd. De installatie kan worden tijdens het maken of bijwerken cyclus van een AKS Engine of als een onafhankelijke actie op een bestaand cluster.

- Installeren tijdens de maak- of updatecyclus

  Als u de Azure Policy Add-on wilt inschakelen tijdens het maken van een nieuw zelfbeheerd cluster of als een update voor een bestaand cluster, moet u de clusterdefinitie **van addons-eigenschap** voor AKS-engine opnemen.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Zie de clusterdefinitie van de externe handleiding [AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)voor meer informatie.

- Installeren in bestaand cluster met Helmdiagrammen

  Gebruik de volgende stappen om het cluster voor te bereiden en de invoegtoepassing te installeren:

  1. Installeer Gatekeeper in de naamruimte van het _poortwachterssysteem._

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Voeg _het label van het besturingsvlak_ toe aan het _kube-systeem_. Dit label sluit de controle van _kube-system_ pods en services door Gatekeeper en de Azure Policy Add-on uit.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchroniseer Kubernetes-gegevens (Namespace, Pod, Ingress, Service) met OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Zie [OPA - Gegevens repliceren](https://github.com/open-policy-agent/gatekeeper#replicating-data)voor meer informatie.

  1. Voeg de repo azure-beleid toe aan Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Zie [Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide)voor meer informatie.

  1. Installeer de add-on met een Helm-diagram. Vervang `<subscriptionId>` door uw `<aks engine cluster resource group>` abonnements-ID en met de brongroep bevindt het zelfbeheerde Kubernetes-cluster van AKS Engine zich.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Zie de definitie van [Azure Policy Add-on Helm Chart](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) op GitHub voor meer informatie over wat de add-on Helm Chart installeert.

     > [!NOTE]
     > Vanwege de relatie tussen Azure Policy Add-on en de resourcegroup id ondersteunt Azure Policy slechts één AKS Engine-cluster voor elke brongroep.

Als u wilt valideren of de installatie met invoegtoepassing is geslaagd en dat de _azure-beleidspod_ wordt uitgevoerd, voert u de volgende opdracht uit:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Validatie- en rapportagefrequentie

De add-on controleert elke 5 minuten bij Azure Policy op wijzigingen in beleidstoewijzingen. Tijdens deze vernieuwingscyclus controleert de add-on op wijzigingen. Deze wijzigingen leiden tot het maken, bijwerken of verwijderen van de beperkingssjablonen en -beperkingen.

> [!NOTE]
> Hoewel een _clusterbeheerder_ mogelijk toestemming heeft om wijzigingen aan te brengen in beperkingssjablonen en -beperkingen, wordt dit niet aanbevolen of ondersteund om wijzigingen aan te brengen in beperkingssjablonen of beperkingen die zijn gemaakt door Azure Policy. Handmatige wijzigingen gaan verloren tijdens de vernieuwingscyclus.

Elke 5 minuten, de add-on oproepen voor een volledige scan van het cluster. Nadat de add-on details van de volledige scan en eventuele real-time evaluaties door Gatekeeper van pogingen tot wijzigingen in het cluster hebben verzameld, rapporteert de add-on de resultaten terug naar Azure Policy voor opname in [nalevingsgegevens](../how-to/get-compliance-data.md) zoals elke Azure-beleidstoewijzing. Alleen resultaten voor actieve beleidsopdrachten worden geretourneerd tijdens de auditcyclus. Controleresultaten kunnen ook worden gezien als schendingen die worden vermeld in het statusveld van de mislukte beperking.

## <a name="policy-language"></a>Beleidstaal

De taalstructuur azure-beleid voor het beheren van AKS Engine volgt die van bestaand beleid. Het effect _EnforceOPAConstraint_ wordt gebruikt om uw AKS Engine-clusters te beheren en neemt _details eigenschappen_ die specifiek zijn voor het werken met [OPA Constraint Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) en Gatekeeper v3. Zie het effect [Afdwingen opAConstraint](effects.md#enforceopaconstraint) voor meer informatie en voorbeelden.

Als onderdeel van de _details.constraintTemplate_ en _details.constraint_ eigenschappen in de beleidsdefinitie, geeft Azure Policy de URI's van deze [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) door aan de add-on. Rego is de taal die OPA en Gatekeeper ondersteunen om een aanvraag voor het Kubernetes-cluster te valideren. Door een bestaande standaard voor Kubernetes-beheer te ondersteunen, maakt Azure Policy het mogelijk om bestaande regels opnieuw te gebruiken en deze te koppelen aan Azure Policy voor een uniforme rapportage-ervaring voor cloudcompliance. Zie [Wat is Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)voor meer informatie.

## <a name="built-in-policies"></a>Ingebouwd beleid

Voer de volgende stappen uit om het ingebouwde beleid voor het beheren van uw AKS Engine-cluster te vinden met behulp van de Azure-portal:

1. Start de Azure Policy-service in de Azure-portal. Selecteer **Alle services** in het linkerdeelvenster en zoek en selecteer **Beleid**.

1. Selecteer **Definities**in het linkerdeelvenster van de pagina Azure Policy .

1. Gebruik alles **selecteren** om het filter te wissen en selecteer Kubernetes in de vervolgkeuzelijst **Categorie.**

1. Selecteer de beleidsdefinitie en selecteer vervolgens de knop **Toewijzen.**

> [!NOTE]
> Wanneer u het Azure-beleid voor DE-engine-definitie toewijst, moet het **bereik** de brongroep van het AKS-engine-cluster zijn.

U afwisselend het [beleid toewijzen : Snel](../assign-policy-portal.md) start Portal om een AKS-enginebeleid te zoeken en toe te wijzen. Zoek naar een AKS Engine-beleidsdefinitie in plaats van de voorbeeld 'audit vms'.

> [!IMPORTANT]
> Ingebouwd beleid in categorie **Kubernetes** is alleen voor gebruik met AKS Engine.

## <a name="logging"></a>Logboekregistratie

### <a name="azure-policy-add-on-logs"></a>Azure-beleidsinvoegtoepassing

Als Kubernetes-controller/-container houdt de Azure Policy Add-on logboeken bij in het AKS Engine-cluster.

Als u de azure policy invoegtoepassinglogboeken wilt weergeven, gebruikt u: `kubectl`

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Poortwachterlogboeken

De _Gatekeeper-pod, gatekeeper-controller-manager-0,_ `gatekeeper-system` bevindt zich meestal in de of `kube-system` naamruimte, maar kan zich in een andere naamruimte bevindt, afhankelijk van hoe deze wordt geïmplementeerd.

Als u de logboeken van Gatekeeper wilt bekijken, gebruikt u: `kubectl`

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Zie [Gatekeeper debuggen in](https://github.com/open-policy-agent/gatekeeper#debugging) de OPA-documentatie voor meer informatie.

## <a name="remove-the-add-on"></a>De invoegtoepassing verwijderen

Als u de Azure Policy Add-on en Gatekeeper uit uw AKS Engine-cluster wilt verwijderen, gebruikt u de methode die aansluit bij de installatie van de invoegtoepassing:

- Indien geïnstalleerd door de eigenschap **addons** in te stellen in de clusterdefinitie voor AKS Engine:

  Implementeer de clusterdefinitie opnieuw in AKS Engine nadat de eigenschap **addons** voor _azure-beleid_ is gewijzigd in false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Indien geïnstalleerd met Helm grafieken:

  1. Oude beperkingen verwijderen

     Momenteel verwijdert het verwijderingsmechanisme alleen het Gatekeeper-systeem, verwijdert het geen _constrainttemplate-,_ _beperkings-_ of _Config-bronnen_ die door de gebruiker zijn gemaakt, noch verwijdert het hun bijbehorende _CRD's_.

     Wanneer Gatekeeper wordt uitgevoerd, is het mogelijk om ongewenste beperkingen te verwijderen door:

     - Alle exemplaren van de beperkingsbron verwijderen
     - De resource _ConstraintTemplate_ verwijderen, die de _CRD_ automatisch moet opschonen
     - Als u de _Config-bron_ verwijdert, worden finalisten van gesynchroniseerde resources verwijderd

  1. Azure-beleidsinvoegtoepassing verwijderen
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Gatekeeper verwijderen
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostische gegevens verzameld door Azure Policy Add-on

De Azure Policy Add-on voor Kubernetes verzamelt beperkte clusterdiagnostische gegevens. Deze diagnostische gegevens zijn essentiële technische gegevens met betrekking tot software en prestaties. Het wordt gebruikt op de volgende manieren:

- Azure Policy Add-on up-to-date houden
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
---
title: Azure Policy voor AKS-engine leren
description: Meer informatie over hoe Azure Policy CustomResourceDefinitions gebruikt en de beleids agent van Gate v3 kunt openen om clusters met AKS-engine te beheren.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f1d6f08f07e015b1e7fe2886746bf9e8b43fdce0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654962"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Azure Policy voor AKS-engine begrijpen

Azure Policy integreert met de [AKS-engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), een systeem dat handige hulp middelen biedt om snel een zelf beheerd Kubernetes-cluster in azure te Boots trappen. Dankzij deze integratie kunnen op een gecentraliseerde, consistente manier afdwingingen en beveiliging op uw eigen beheerde clusters van de AKS-engine worden uitgebreid. Door het gebruik van [Open Policy Agent](https://www.openpolicyagent.org/) (opa) [gate keeper](https://github.com/open-policy-agent/gatekeeper) v3 (bèta), een _toegangs controller webhook_ voor Kubernetes te verlengen, kunt Azure Policy de nalevings status van uw Azure-resources en AKS engine-eigen beheerde clusters van één locatie beheren en rapporteren.

> [!NOTE]
> Azure Policy voor de AKS-engine bevindt zich in de open bare preview en heeft geen SLA. Gate keeper v3 is een bèta versie en wordt ondersteund door de open source-community. De service ondersteunt alleen ingebouwde beleids definities, Linux-knooppunt groepen en één AKS-engine voor elke resource groep die is geconfigureerd met een service-principal.

> [!IMPORTANT]
> Als u ondersteuning voor Azure Policy voor de AKS-engine, AKS-Engine of gate keeper v3 wilt krijgen, maakt u een [nieuw probleem](https://github.com/Azure/aks-engine/issues/new/choose) in de AKS engine github-opslag plaats.

## <a name="overview"></a>Overzicht

Voer de volgende acties uit om Azure Policy voor de AKS-engine met uw zelf-beheerde Kubernetes-cluster in Azure in te scha kelen en te gebruiken:

- [Vereisten](#prerequisites)
- [De Azure Policy-invoeg toepassing installeren](#installing-the-add-on)
- [Een beleids definitie voor de AKS-engine toewijzen](#built-in-policies)
- [Wachten op validatie](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Vereisten

Voordat u de Azure Policy invoeg toepassing installeert of een van de service functies inschakelt, moet uw abonnement de resource provider **micro soft. PolicyInsights** inschakelen en een roltoewijzing maken voor de Cluster-service-principal. 

1. Als u de resource provider wilt inschakelen, volgt u de stappen in [resource providers en typen](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) of voert u de Azure CLI-of Azure PowerShell opdracht uit:

   - Azure CLI

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

1. Een roltoewijzing maken voor de service-principal van het cluster

   - Als u de App-ID van de Cluster-service-principal niet weet, kunt u deze bekijken met de volgende opdracht.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Wijs de roltoewijzing ' policy Insights Data Writer (preview) ' toe aan de App-ID van de Cluster-service-principal (waarde _aadClientID_ uit de vorige stap) met Azure cli. Vervang door `<subscriptionId>` uw abonnements-id en `<aks engine cluster resource group>` de resource groep waarin het zelf beheerde Kubernetes-cluster van de AKS-engine zich bevindt.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Invoeg toepassing Azure Policy

De _Azure Policy-invoeg toepassing_ voor Kubernetes verbindt de Azure Policy-service met de gate keeper Admission controller. De invoeg toepassing, die in de _uitvoeren-_ naam ruimte wordt geïnstalleerd, wordt voorzien van de volgende functies:

- Controleert met Azure Policy voor toewijzingen aan het AKS-engine-cluster
- Hiermee worden beleids Details, beperkings sjablonen en beperkingen gedownload en geïnstalleerd
- Hiermee wordt een volledige controle op de naleving van het AKS-engine-cluster uitgevoerd
- Rapporteert controle-en nalevings details terug naar Azure Policy

### <a name="installing-the-add-on"></a>De invoeg toepassing installeren

Zodra de vereisten zijn voltooid, kan de invoeg toepassing Azure Policy worden geïnstalleerd. De installatie kan worden uitgevoerd tijdens het maken of bijwerken van een AKS-Engine of als onafhankelijke actie op een bestaand cluster.

- Installeren tijdens maken of bijwerken van cyclus

  Als u de invoeg toepassing Azure Policy wilt inschakelen tijdens het maken van een nieuw zelf beheerd cluster of als een update van een bestaand cluster, neemt u de eigenschap **Addons** cluster definition voor de AKS-engine op.

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

  Zie voor meer informatie over de externe hand leiding [AKS engine-cluster definitie](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Installeren in een bestaand cluster met helm-grafieken

  Gebruik de volgende stappen om het cluster voor te bereiden en de invoeg toepassing te installeren:

  1. Gate keeper installeren op de _gate keeper-systeem_ naam ruimte.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Voeg een _besturings element-vlak_ label toe aan _uitvoeren-System_. Dit label sluit de controle uit van _uitvoeren-systeem en-_ Services door gate keeper en de Azure Policy-invoeg toepassing.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchroniseer Kubernetes-gegevens (naam ruimte, Pod, ingangs punt, service) met OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Zie [opa-gegevens repliceren](https://github.com/open-policy-agent/gatekeeper#replicating-data)voor meer informatie.

  1. Voeg de Azure Policy opslag plaats toe aan helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Zie [helm Chart-Quick Start Guide (Engelstalig](https://helm.sh/docs/using_helm/#quickstart-guide)) voor meer informatie.

  1. Installeer de invoeg toepassing met een helm-grafiek. Vervang door `<subscriptionId>` uw abonnements-id en `<aks engine cluster resource group>` de resource groep waarin het zelf beheerde Kubernetes-cluster van de AKS-engine zich bevindt.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Voor meer informatie over de installatie van de add-on helm-grafiek, zie de [grafiek definitie van de helm-invoeg toepassing Azure Policy](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) op github.

     > [!NOTE]
     > Als gevolg van de relatie tussen Azure Policy invoeg toepassing en de resource groep-ID Azure Policy ondersteunt slechts één AKS-engine cluster voor elke resource groep.

Voer de volgende opdracht uit om te controleren of de installatie van de invoeg toepassing is geslaagd en dat de pod van het _Azure-beleid_ wordt uitgevoerd:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Validatie-en rapportage frequentie

De invoeg toepassing controleert met Azure Policy op wijzigingen in beleids toewijzingen om de vijf minuten. Tijdens deze vernieuwings cyclus controleert de invoeg toepassing op wijzigingen. Met deze wijzigingen trigger worden de beperkings sjablonen en-beperkingen gemaakt, bijgewerkt of verwijderd.

> [!NOTE]
> Hoewel een _cluster beheerder_ gemachtigd is om wijzigingen aan te brengen in beperkings sjablonen en-beperkingen, wordt het niet aanbevolen of wordt deze niet ondersteund voor het aanbrengen van wijzigingen in beperkings sjablonen of beperkingen die zijn gemaakt door Azure Policy. Hand matige wijzigingen die zijn aangebracht, gaan verloren tijdens de vernieuwings cyclus.

Elke 5 minuten wordt de invoeg toepassing aangeroepen voor een volledige scan van het cluster. Na het verzamelen van Details van de volledige scan en eventuele realtime-evaluaties door gate keeper van pogingen om wijzigingen aan te brengen in het cluster, worden de resultaten weer gegeven in Azure Policy om te worden opgenomen in [compatibiliteits Details](../how-to/get-compliance-data.md) zoals een Azure Policy toewijzing. Er worden alleen resultaten voor actieve beleids toewijzingen geretourneerd tijdens de controle cyclus. Controle resultaten kunnen ook worden gezien als schendingen die worden vermeld in het veld status van de beperking mislukt.

## <a name="policy-language"></a>Beleids taal

De Azure Policy taal structuur voor het beheren van de AKS-engine volgt de bestaande beleids regels. Het effect _EnforceOPAConstraint_ wordt gebruikt om uw AKS-engine clusters te beheren en bevat _gedetailleerde_ eigenschappen die specifiek zijn voor het werken met [opa CONSTRAINT Framework](https://github.com/open-policy-agent/frameworks/tree/master/constraint) en gate keeper v3. Zie het effect [EnforceOPAConstraint](effects.md#enforceopaconstraint) voor meer informatie en voor beelden.

Als onderdeel van de eigenschappen _Details. constraintTemplate_ en _Details. CONSTRAINT_ in de beleids definitie Azure Policy door gegeven de uri's van deze [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) naar de invoeg toepassing. Rego is de taal die OPA en gate keeper ondersteunen voor het valideren van een aanvraag naar het Kubernetes-cluster. Dankzij de ondersteuning van een bestaande standaard voor Kubernetes-beheer, maakt Azure Policy het mogelijk om bestaande regels opnieuw te gebruiken en deze te koppelen aan Azure Policy voor een uniforme rapportage van de naleving van de Cloud. Zie [Wat is Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego)voor meer informatie.

## <a name="built-in-policies"></a>Ingebouwd beleid

Ga als volgt te werk om het ingebouwde beleid voor het beheren van uw AKS engine-cluster te vinden met behulp van de Azure Portal:

1. Start de Azure Policy-service in de Azure Portal. Selecteer **alle services** in het linkerdeel venster en zoek en selecteer vervolgens **beleid**.

1. Selecteer in het linkerdeel venster van de pagina Azure Policy **definities**.

1. Gebruik in de vervolg keuzelijst Categorie de **optie Alles selecteren** om het filter te wissen en selecteer vervolgens **Kubernetes**.

1. Selecteer de beleids definitie en selecteer vervolgens de knop **toewijzen** .

> [!NOTE]
> Bij het toewijzen van de Azure Policy voor de AKS-engine definitie moet het **bereik** de resource groep van het AKS-engine-cluster zijn.

U kunt ook de Snelstartgids [een beleid toewijzen-Portal](../assign-policy-portal.md) gebruiken om een AKS-engine beleid te zoeken en toe te wijzen. Zoek in plaats van het voor beeld ' vm's controleren ' naar een beleids definitie van een AKS-engine.

> [!IMPORTANT]
> Ingebouwde beleids regels in categorie **Kubernetes** zijn alleen bedoeld voor gebruik met de AKS-engine.

## <a name="logging"></a>Logboekregistratie

### <a name="azure-policy-add-on-logs"></a>Azure Policy-invoeg logboeken

Als Kubernetes-controller/-container houdt de invoeg toepassing Azure Policy Logboeken in het AKS-engine-cluster.

Als u de logboeken voor Azure Policy-invoeg toepassingen wilt weer geven, gebruikt u `kubectl` :

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Gate keeper logboeken

De gate keeper Pod, _Gate-Controller-Manager-0_, bevindt zich meestal in de `gatekeeper-system` of `kube-system` naam ruimte, maar kan zich in een andere naam ruimte bevinden, afhankelijk van hoe de app is geïmplementeerd.

Als u de gate keeper-logboeken wilt weer geven, gebruikt u `kubectl` :

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Zie [debug Gate](https://github.com/open-policy-agent/gatekeeper#debugging) in de opa-documentatie voor meer informatie.

## <a name="remove-the-add-on"></a>De invoeg toepassing verwijderen

Als u Azure Policy de invoeg toepassing en gate keeper van uw AKS-Engine wilt verwijderen, gebruikt u de methode die wordt afgestemd op de manier waarop de invoeg toepassing is geïnstalleerd:

- Indien geïnstalleerd door de eigenschap **Addons** in de cluster definitie voor de AKS-engine in te stellen:

  Implementeer de cluster definitie opnieuw in de AKS-Engine nadat u de eigenschap **Addons** voor _Azure-Policy_ hebt gewijzigd in False:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Indien geïnstalleerd met helm-grafieken:

  1. Oude beperkingen verwijderen

     Op het moment dat het installatie mechanisme alleen het gate keeper-systeem verwijdert, worden er geen _ConstraintTemplate_, _beperkingen_of _configuratie_ resources verwijderd die door de gebruiker zijn gemaakt, en worden de bijbehorende _CRDs_niet verwijderd.

     Wanneer gate keeper actief is, is het mogelijk om ongewenste beperkingen te verwijderen door:

     - Alle exemplaren van de beperkings resource verwijderen
     - De _ConstraintTemplate_ -resource verwijderen, die automatisch de _CRD_ moet opschonen
     - Als u de _configuratie_ bron verwijdert, worden volt ooien van de gesynchroniseerde resources verwijderd

  1. Azure Policy-invoeg toepassing verwijderen
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Gate keeper verwijderen
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostische gegevens die door Azure Policy invoeg toepassing zijn verzameld

De Azure Policy-invoeg toepassing voor Kubernetes verzamelt beperkte diagnostische gegevens van het cluster. Deze diagnostische gegevens zijn vitale technische gegevens met betrekking tot software en prestaties. Deze wordt op de volgende manieren gebruikt:

- Azure Policy invoeg toepassing up-to-date houden
- Bewaar Azure Policy extra beveiliging, betrouw bare, krachtige prestaties
- De statistische analyse van het gebruik van de invoeg toepassing verbeteren Azure Policy

De gegevens die door de invoeg toepassing worden verzameld, zijn geen persoonlijke gegevens. De volgende details worden momenteel verzameld:

- Azure Policy-Agent versie van invoeg toepassing
- Cluster type
- Cluster regio
- Cluster resource groep
- Cluster bron-ID
- Abonnements-ID van cluster
- Cluster besturingssysteem (voor beeld: Linux)
- Cluster plaats (voor beeld: Seattle)
- Status of provincie van cluster (voor beeld: Washington)
- Land of regio van het cluster (voor beeld: Verenigde Staten)
- Uitzonde ringen/fouten aangetroffen door Azure Policy-invoeg toepassing tijdens de installatie van de agent op beleids evaluatie
- Het aantal gate keeper-beleids regels dat niet is geïnstalleerd door Azure Policy invoeg toepassing

## <a name="next-steps"></a>Volgende stappen

- Bekijk voor beelden op [Azure Policy voor beelden](../samples/index.md).
- Bekijk de [structuur van Azure Policy-definities](definition-structure.md).
- Lees [Informatie over de effecten van het beleid](effects.md).
- Meer informatie over het [programmatisch maken van beleids regels](../how-to/programmatically-create.md).
- Meer informatie over het [ophalen van compatibiliteits gegevens](../how-to/get-compliance-data.md).
- Meer informatie over het [oplossen van niet-compatibele resources](../how-to/remediate-resources.md).
- Bekijk wat een beheer groep is met [het organiseren van uw resources met Azure-beheer groepen](../../management-groups/overview.md).
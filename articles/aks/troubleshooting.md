---
title: Veelvoorkomende problemen met de Azure Kubernetes-service oplossen
description: Meer informatie over het oplossen van veelvoorkomende problemen bij het gebruik van Azure Kubernetes service (AKS)
services: container-service
author: sauryadas
ms.service: container-service
ms.topic: troubleshooting
ms.date: 08/13/2018
ms.author: saudas
ms.openlocfilehash: d2561b1882ea612f29c0ff0eeb4bd6614403c9ff
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72025477"
---
# <a name="aks-troubleshooting"></a>AKS problemen oplossen

Wanneer u Azure Kubernetes service (AKS)-clusters maakt of beheert, kunnen er af en toe problemen optreden. In dit artikel worden enkele veelvoorkomende problemen beschreven en stappen voor probleem oplossing.

## <a name="in-general-where-do-i-find-information-about-debugging-kubernetes-problems"></a>Waar vind ik informatie over problemen met fout opsporing in Kubernetes in het algemeen?

Probeer de [officiële hand leiding voor het oplossen van problemen met Kubernetes-clusters](https://kubernetes.io/docs/tasks/debug-application-cluster/troubleshooting/).
Er is ook een [hand leiding](https://github.com/feiskyer/kubernetes-handbook/blob/master/en/troubleshooting/index.md)voor het oplossen van problemen, gepubliceerd door een micro soft-Engineer voor het oplossen van problemen met peulen, knoop punten, clusters en andere functies.

## <a name="im-getting-a-quota-exceeded-error-during-creation-or-upgrade-what-should-i-do"></a>Ik krijg de fout ' quotum overschreden ' tijdens het maken of upgraden. Wat moet ik doen? 

U moet [kernen aanvragen](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

## <a name="what-is-the-maximum-pods-per-node-setting-for-aks"></a>Wat is het maximum aantal instellingen per knoop punt voor AKS?

De maximale instelling per knoop punt is 30 standaard als u een AKS-cluster implementeert in de Azure Portal.
De maximale instelling per knoop punt is standaard 110 als u een AKS-cluster implementeert in de Azure CLI. (Zorg ervoor dat u de nieuwste versie van de Azure CLI gebruikt). Deze standaard instelling kan worden gewijzigd met behulp van de vlag `–-max-pods` in de `az aks create` opdracht.

## <a name="im-getting-an-insufficientsubnetsize-error-while-deploying-an-aks-cluster-with-advanced-networking-what-should-i-do"></a>Er wordt een insufficientSubnetSize-fout opgetreden tijdens het implementeren van een AKS-cluster met een geavanceerd netwerk. Wat moet ik doen?

Als Azure CNI (Advanced Networking) wordt gebruikt, wijst AKS vooraf toegewezen IP-adres toe op basis van het ' Max-peul ' per knoop punt dat is geconfigureerd. Het aantal knoop punten in een AKS-cluster kan een wille keurige plaats zijn tussen 1 en 110. Op basis van het geconfigureerde maximum van Peul per knoop punt moet de grootte van het subnet groter zijn dan het ' product van het aantal knoop punten en het maximale pod per knoop punt '. De volgende basis vergelijking bevat een overzicht:

De grootte van het subnet > aantal knoop punten in het cluster (waarbij rekening wordt gehouden met de toekomstige schaal vereisten) * Max.

Zie [IP-adres sering voor uw cluster plannen](configure-azure-cni.md#plan-ip-addressing-for-your-cluster)voor meer informatie.

## <a name="my-pod-is-stuck-in-crashloopbackoff-mode-what-should-i-do"></a>Mijn Pod is vastgelopen in de CrashLoopBackOff-modus. Wat moet ik doen?

Er kunnen verschillende redenen zijn waarom de pod in die modus vastloopt. U kunt het volgende bekijken:

* De pod zelf, met behulp van `kubectl describe pod <pod-name>`.
* De logboeken, door gebruik te maken van `kubectl log <pod-name>`.

Zie [fouten opsporen in toepassingen](https://kubernetes.io/docs/tasks/debug-application-cluster/debug-application/#debugging-pods)voor meer informatie over het oplossen van problemen met Pod.

## <a name="im-trying-to-enable-rbac-on-an-existing-cluster-how-can-i-do-that"></a>Ik probeer RBAC in te scha kelen op een bestaand cluster. Hoe kan ik dat doen?

Helaas wordt het inschakelen van op rollen gebaseerd toegangs beheer (RBAC) op bestaande clusters op dit moment niet ondersteund. U moet expliciet nieuwe clusters maken. Als u de CLI gebruikt, is RBAC standaard ingeschakeld. Als u de AKS-Portal gebruikt, is een wissel knop voor het inschakelen van RBAC beschikbaar in de werk stroom maken.

## <a name="i-created-a-cluster-with-rbac-enabled-by-using-either-the-azure-cli-with-defaults-or-the-azure-portal-and-now-i-see-many-warnings-on-the-kubernetes-dashboard-the-dashboard-used-to-work-without-any-warnings-what-should-i-do"></a>Ik heb een cluster gemaakt waarop RBAC is ingeschakeld met behulp van de Azure CLI met de standaard instellingen of de Azure Portal, en nu worden er veel waarschuwingen weer gegeven op het Kubernetes-dash board. Het dash board dat wordt gebruikt om zonder waarschuwingen te werken. Wat moet ik doen?

De reden voor de waarschuwingen op het dash board is dat het cluster nu is ingeschakeld met RBAC en dat de toegang tot deze server standaard is uitgeschakeld. Over het algemeen is deze aanpak goed, omdat de standaard belichting van het dash board aan alle gebruikers van het cluster kan leiden tot beveiligings Risico's. Als u het dash board nog steeds wilt inschakelen, volgt u de stappen in [dit blog bericht](https://pascalnaber.wordpress.com/2018/06/17/access-dashboard-on-aks-with-rbac-enabled/).

## <a name="i-cant-connect-to-the-dashboard-what-should-i-do"></a>Ik kan geen verbinding maken met het dash board. Wat moet ik doen?

De eenvoudigste manier om toegang te krijgen tot uw service buiten het cluster, is om `kubectl proxy` uit te voeren, welke proxy's aanvragen verzonden naar de lokale poort 8001 van de Kubernetes-API-server. Vanaf daar kan de API-server worden geproxyeerd voor uw service: `http://localhost:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard/proxy/#!/node?namespace=default`.

Als u het Kubernetes-dash board niet ziet, controleert u of de `kube-proxy` pod wordt uitgevoerd in de naam ruimte `kube-system`. Als de status niet wordt uitgevoerd, verwijdert u de Pod en wordt de computer opnieuw opgestart.

## <a name="i-cant-get-logs-by-using-kubectl-logs-or-i-cant-connect-to-the-api-server-im-getting-error-from-server-error-dialing-backend-dial-tcp-what-should-i-do"></a>Ik kan geen logboeken ophalen met behulp van kubectl-Logboeken of ik kan geen verbinding maken met de API-server. Ik krijg de fout melding van server: fout bij het kiezen van de back-end: Dial TCP.... Wat moet ik doen?

Zorg ervoor dat de standaard netwerk beveiligings groep niet is gewijzigd en dat poort 22 en 9000 zijn geopend voor verbinding met de API-server. Controleer of de `tunnelfront`-pod wordt uitgevoerd in de *uitvoeren-systeem* naam ruimte met behulp van de `kubectl get pods --namespace kube-system` opdracht. Als dat niet het geval is, dwingt u het verwijderen van de pod af en wordt de computer opnieuw opgestart.

## <a name="im-trying-to-upgrade-or-scale-and-am-getting-a-message-changing-property-imagereference-is-not-allowed-error-how-do-i-fix-this-problem"></a>Ik probeer een upgrade uit te voeren of te schalen en ontvang een bericht: Het wijzigen van de eigenschap imageReference is niet toegestaan. Dit probleem Hoe kan ik oplossen?

Mogelijk krijgt u deze fout omdat u de tags in de agent knooppunten in het AKS-cluster hebt gewijzigd. Het wijzigen en verwijderen van tags en andere eigenschappen van resources in de MC_ *-resource groep kan leiden tot onverwachte resultaten. Als u de resources onder de groep MC_ * in het AKS-cluster wijzigt, wordt de serviceniveau doelstelling (SLO) verbroken.

## <a name="im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed"></a>Er worden fouten weer ontvangen dat mijn cluster de status Mislukt heeft en het bijwerken of schalen werkt pas nadat het is opgelost

*Deze probleemoplossings hulp wordt omgeleid van https://aka.ms/aks-cluster-failed*

Deze fout treedt op wanneer clusters een mislukte status om meerdere redenen invoeren. Volg de onderstaande stappen om de status van het cluster fout op te lossen voordat u de eerder mislukte bewerking opnieuw probeert uit te voeren:

1. De `upgrade`-en `scale`-bewerkingen mislukken totdat het cluster de status `failed` heeft. Veelvoorkomende basis problemen en oplossingen zijn:
    * Schalen met **onvoldoende Compute-quota (CRP)** . U kunt dit oplossen door uw cluster eerst terug te schalen naar een stabiele doel status binnen het quotum. Voer vervolgens de volgende [stappen uit om een toename van het reken quotum aan te vragen](../azure-supportability/resource-manager-core-quotas-request.md) voordat u opnieuw opschaalt na de initiële quotum limieten.
    * Het schalen van een cluster met geavanceerde netwerken en **onvoldoende subnet-bronnen (netwerken)** . U kunt dit oplossen door uw cluster eerst terug te schalen naar een stabiele doel status binnen het quotum. Voer vervolgens [de volgende stappen uit om een verhoging van het resource quotum aan te vragen](../azure-resource-manager/resource-manager-quota-errors.md#solution) voordat u opnieuw opschaalt na de initiële quotum limieten.
2. Zodra de onderliggende oorzaak van de upgrade fout is opgelost, moet uw cluster de status voltooid hebben. Als de status is gecontroleerd, voert u de oorspronkelijke bewerking opnieuw uit.

## <a name="im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-currently-being-upgraded-or-has-failed-upgrade"></a>Er wordt een fout opgetreden bij het upgraden of schalen van de status die mijn cluster momenteel wordt bijgewerkt of waarvoor een upgrade is mislukt

*Deze probleemoplossings hulp wordt omgeleid van https://aka.ms/aks-pending-upgrade*

Het bijwerken en schalen van bewerkingen op een cluster met een groep met één knoop punt of een cluster met [meerdere knooppunt Pools](use-multiple-node-pools.md) sluiten elkaar wederzijds uit. U kunt geen cluster-of knooppunt groep tegelijkertijd bijwerken en schalen. In plaats daarvan moet elk bewerkings type worden voltooid voor de doel resource vóór de volgende aanvraag op dezelfde resource. Als gevolg hiervan zijn bewerkingen beperkt wanneer actieve upgrade of schaal bewerkingen worden uitgevoerd of geprobeerd en vervolgens worden mislukt. 

Om het probleem op te lossen `az aks show -g myResourceGroup -n myAKSCluster -o table` om de gedetailleerde status van het cluster op te halen. Op basis van het resultaat:

* Als het cluster actief wordt bijgewerkt, wacht u totdat de bewerking wordt beëindigd. Als deze is geslaagd, voert u de bewerking opnieuw uit.
* Als het upgraden van het cluster is mislukt, volgt u de stappen in de vorige sectie.

## <a name="can-i-move-my-cluster-to-a-different-subscription-or-my-subscription-with-my-cluster-to-a-new-tenant"></a>Kan ik mijn cluster verplaatsen naar een ander abonnement of mijn abonnement met mijn cluster naar een nieuwe Tenant?

Als u uw AKS-cluster hebt verplaatst naar een ander abonnement of het cluster dat eigenaar is van een abonnement op een nieuwe Tenant, verliest het cluster de functionaliteit als gevolg van het verlies van roltoewijzingen en machtigingen voor service-principals. **AKS biedt geen ondersteuning voor het verplaatsen van clusters in abonnementen of tenants** als gevolg van deze beperking.

## <a name="im-receiving-errors-trying-to-use-features-that-require-virtual-machine-scale-sets"></a>Ik ontvang fouten bij het gebruiken van functies waarvoor schaal sets voor virtuele machines zijn vereist

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-vmss-enablement*

Er kunnen fouten optreden die aangeven dat uw AKS-cluster zich niet op een schaalset voor virtuele machines bevindt, zoals in het volgende voor beeld:

**Agent pool ' agent pool ' heeft automatische schaling ingesteld op ingeschakeld, maar niet op Virtual Machine Scale Sets**

Als u functies zoals de cluster-automatische schaal of meerdere knooppunt groepen wilt gebruiken, moeten er AKS-clusters worden gemaakt die gebruikmaken van virtuele-machine schaal sets. Er worden fouten geretourneerd als u probeert functies te gebruiken die afhankelijk zijn van virtuele-machine schaal sets en u een normaal, niet-virtuele machine Scale set AKS-cluster hebt gericht.

Volg de *voordat u begint* met de stappen in het juiste document om een AKS-cluster op de juiste manier te maken:

* [De automatische schaal functie van het cluster gebruiken](cluster-autoscaler.md)
* [Meerdere knooppunt groepen maken en gebruiken](use-multiple-node-pools.md)
 
## <a name="what-naming-restrictions-are-enforced-for-aks-resources-and-parameters"></a>Welke naam beperkingen worden afgedwongen voor AKS-resources en-para meters?

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-naming-rules*

Naam beperkingen worden geïmplementeerd door zowel het Azure-platform als de AKS. Als een resource naam of-para meter een van deze beperkingen uitbreekt, wordt er een fout bericht weer gegeven waarin u wordt gevraagd een andere invoer op te geven. De volgende algemene richt lijnen zijn van toepassing:

* De naam van de resource groep AKS *MC_* combineert de naam van de resource groep en de resource naam. De automatisch gegenereerde syntaxis van `MC_resourceGroupName_resourceName_AzureRegion` mag niet langer zijn dan 80 tekens. Als dat nodig is, vermindert u de lengte van de naam van de resource groep of de AKS-cluster naam.
* De *dnsPrefix* moet beginnen en eindigen met alfanumerieke waarden. Geldige tekens zijn alfanumerieke waarden en afbreek streepjes (-). De *dnsPrefix* mag geen speciale tekens bevatten, zoals een punt (.).

## <a name="im-receiving-errors-when-trying-to-create-update-scale-delete-or-upgrade-cluster-that-operation-is-not-allowed-as-another-operation-is-in-progress"></a>Ik ontvang fouten bij het maken, bijwerken, schalen, verwijderen of upgraden van een cluster. deze bewerking is niet toegestaan omdat er een andere bewerking wordt uitgevoerd.

*Deze hulp bij het oplossen van problemen is gericht op aka.ms/aks-pending-operation*

Cluster bewerkingen zijn beperkt wanneer er nog een eerdere bewerking wordt uitgevoerd. Gebruik de `az aks show -g myResourceGroup -n myAKSCluster -o table` opdracht om een gedetailleerde status van uw cluster op te halen. Gebruik uw eigen resource groep en AKS cluster naam waar nodig.

Op basis van de uitvoer van de cluster status:

* Als het cluster zich in een andere inrichtings status bevindt dan *geslaagd* of *mislukt*, wacht u totdat de bewerking (*bijwerken/bijwerken/maken/schalen/verwijderen/migreren*) is beëindigd. Wanneer de vorige bewerking is voltooid, moet u de meest recente cluster bewerking opnieuw proberen.

* Als de upgrade van het cluster is mislukt, volgt u de stappen [die worden beschreven waarbij ik fouten ontvang dat mijn cluster de status Mislukt heeft en dat de upgrade of schaal aanpassing pas werkt als het probleem is opgelost](#im-receiving-errors-that-my-cluster-is-in-failed-state-and-upgrading-or-scaling-will-not-work-until-it-is-fixed).

## <a name="im-receiving-errors-that-my-service-principal-was-not-found-when-i-try-to-create-a-new-cluster-without-passing-in-an-existing-one"></a>Er wordt een fout melding ontvangen dat mijn Service-Principal niet is gevonden bij het maken van een nieuw cluster zonder dat dit wordt door gegeven.

Bij het maken van een AKS-cluster is een Service-Principal vereist om namens u resources te maken. AKS biedt de mogelijkheid om een nieuwe te maken op het moment dat het cluster wordt gemaakt, maar dit vereist dat Azure Active Directory de nieuwe Service-Principal in een redelijke tijd volledig moet door geven om het cluster te kunnen maken. Wanneer deze doorgifte te lang duurt, mislukt de validatie van het cluster om te worden gemaakt omdat er hiervoor geen beschik bare service-principal kan worden gevonden. 

Gebruik de volgende tijdelijke oplossingen:
1. Gebruik een bestaande service-principal die al is door gegeven in meerdere regio's en die bestaat voor AKS tijdens het maken van een cluster.
2. Als u automatiserings scripts gebruikt, voegt u vertragingen toe tussen het maken van service-principals en het maken van AKS-clusters.
3. Als u Azure Portal gebruikt, keert u terug naar de cluster instellingen tijdens het maken en voert u de validatie pagina na een paar minuten opnieuw uit.

## <a name="im-receiving-errors-after-restricting-my-egress-traffic"></a>Ik ontvang fouten na het beperken van mijn uitgaand verkeer

Bij het beperken van uitgaand verkeer van een AKS-cluster zijn er [vereiste en optionele aanbevolen](limit-egress-traffic.md) uitgaande poorten/netwerk regels en FQDN/toepassings regels voor AKS. Als uw instellingen conflicteren met een van deze regels, kunt u bepaalde `kubectl`-opdrachten mogelijk niet uitvoeren. Er worden ook fouten weer geven bij het maken van een AKS-cluster.

Controleer of de instellingen niet conflicteren met een van de vereiste of optionele aanbevolen uitgaande poorten/netwerk regels en FQDN/toepassings regels.

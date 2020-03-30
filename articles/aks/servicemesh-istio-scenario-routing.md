---
title: Intelligente routering en canary releases met Istio in Azure Kubernetes Service (AKS)
description: Meer informatie over het gebruik van Istio voor het bieden van intelligente routering en het implementeren van canary-releases in een AKS-cluster (Azure Kubernetes Service)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273010"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Intelligente routering en canary-releases gebruiken met Istio in Azure Kubernetes Service (AKS)

[Istio][istio-github] is een open-source service mesh die een belangrijke set functionaliteit biedt voor de microservices in een Kubernetes-cluster. Deze functies omvatten verkeersbeheer, service-identiteit en -beveiliging, beleidshandhaving en waarneembaarheid. Voor meer informatie over Istio, zie de officiële [Wat is Istio?][istio-docs-concepts] documentatie.

In dit artikel ziet u hoe u de verkeersbeheerfunctionaliteit van Istio gebruiken. Een voorbeeld AKS stem-app wordt gebruikt om intelligente routing en canary releases te verkennen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De toepassing implementeren
> * De toepassing bijwerken
> * Een canary release van de applicatie uitrollen
> * De implementatie afronden

## <a name="before-you-begin"></a>Voordat u begint

> [!NOTE]
> Dit scenario is getest tegen `1.3.2`Istio versie .

De stappen in dit artikel gaan ervan uit dat `1.13` u een AKS-cluster hebt gemaakt `kubectl` (Kubernetes en hoger, met RBAC ingeschakeld) en dat u een verbinding met het cluster hebt gemaakt. U hebt ook Istio nodig die in uw cluster is geïnstalleerd.

Als u hulp nodig hebt met een van deze items, zie dan de [AKS quickstart][aks-quickstart] en [Installeer Istio in AKS][istio-install] begeleiding.

## <a name="about-this-application-scenario"></a>Informatie over dit toepassingsscenario

De voorbeeld AKS stem-app biedt twee stemopties **(Katten** of **Honden)** aan gebruikers. Er is een opslagcomponent die het aantal stemmen voor elke optie aanhoudt. Daarnaast is er een analytics-component die details geeft rond de uitgebrachte stemmen voor elke optie.

In dit toepassingsscenario begint u `1.0` met het implementeren `1.0` van de versie van de stem-app en de versie van de analysecomponent. De analytics-component biedt eenvoudige tellingen voor het aantal stemmen. De stem-app en analytics-component werken samen met de versie `1.0` van de opslagcomponent, die wordt ondersteund door Redis.

U upgradet de `1.1`analytics-component naar versie , die telt biedt, en nu totalen en percentages.

Een subset van `2.0` gebruikers test versie van de app via een canary release. Deze nieuwe versie maakt gebruik van een opslagcomponent die wordt ondersteund door een MySQL-database.

Zodra u er zeker `2.0` van bent dat de versie werkt `2.0` zoals verwacht op uw subset van gebruikers, rolt u versie uit naar al uw gebruikers.

## <a name="deploy-the-application"></a>De toepassing implementeren

Laten we beginnen met het implementeren van de toepassing in uw Azure Kubernetes Service (AKS)-cluster. In het volgende diagram ziet u wat `1.0` er aan het einde van deze sectie wordt uitgevoerd - versie van alle componenten met binnenkomende aanvragen die via de Istio-ingress-gateway worden onderhouden:

![De onderdelen en routering van de AKS Voting-app.](media/servicemesh/istio/scenario-routing-components-01.png)

De artefacten die u samen met dit artikel moet volgen, zijn beschikbaar in de [GitHub repo van Azure-Samples/aks-voting-app.][github-azure-sample] U de artefacten downloaden of de repo als volgt klonen:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Wijzig de volgende map in de gedownloade / gekloonde repo en voer alle volgende stappen uit deze map uit:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Maak eerst een naamruimte in uw AKS-cluster voor `voting` de voorbeeld-AKS-stem-app die als volgt wordt genoemd:

```console
kubectl create namespace voting
```

Label de naamruimte met `istio-injection=enabled`. Dit label instrueert Istio om de istio-proxies automatisch als sidecars in al uw pods in deze naamruimte te injecteren.

```console
kubectl label namespace voting istio-injection=enabled
```

Laten we nu de componenten voor de AKS Voting-app maken. Maak deze componenten `voting` in de naamruimte die in een vorige stap is gemaakt.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

In de volgende voorbeelduitvoer worden de resources weergegeven die worden gemaakt:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio heeft een aantal specifieke eisen rond pods en diensten. Zie de [istio-vereisten voor pods en services-documentatie voor][istio-requirements-pods-and-services]meer informatie.

Als u de pods wilt zien die zijn gemaakt, gebruikt u de opdracht [kubectl get pods][kubectl-get] als volgt:

```console
kubectl get pods -n voting --show-labels
```

In de volgende voorbeelduitvoerwordt weergegeven `voting-app` dat er drie exemplaren `voting-analytics` `voting-storage` van de pod en één instantie van zowel de pod als de pods zijn. Elk van de pods heeft twee containers. Een van deze containers is het onderdeel, en de andere is de: `istio-proxy`

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Als u informatie over de pod wilt zien, gebruiken we de opdracht [kubectl describe pod][kubectl-describe] met labelselectors om de `voting-analytics` pod te selecteren. We filteren de uitvoer om de details van de twee containers in de pod weer te geven:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

U pas verbinding maken met de stem-app als u de Istio [Gateway][istio-reference-gateway] en [Virtual Service hebt gemaakt.][istio-reference-virtualservice] Deze Istio-bronnen leiden het verkeer van de standaard-Istio-ingress-gateway naar onze toepassing.

> [!NOTE]
> Een **gateway** is een onderdeel aan de rand van het servicenet dat binnenkomend of uitgaand HTTP- en TCP-verkeer ontvangt.
> 
> Een **virtuele service** definieert een set routeringsregels voor een of meer bestemmingsservices.

Gebruik `kubectl apply` de opdracht om de gateway- en virtuele serviceyaml te implementeren. Vergeet niet de naamruimte op te geven waarin deze resources zijn geïmplementeerd.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

In de volgende voorbeelduitvoerwordt de nieuwe gateway en virtuele service weergegeven die worden gemaakt:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Verkrijg het IP-adres van de Istio Ingress Gateway met de volgende opdracht:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

In de volgende voorbeelduitvoer wordt het IP-adres van de Ingress-gateway weergegeven:

```output
20.188.211.19
```

Open een browser en plak het IP-adres. De voorbeeld-AKS-stem-app wordt weergegeven.

![De AKS Voting app draait in ons Istio enabled AKS cluster.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

De informatie onder aan het scherm laat `1.0` zien `voting-app` dat `1.0` `voting-storage` de app versie van en versie van (Redis) gebruikt.

## <a name="update-the-application"></a>De toepassing bijwerken

Laten we een nieuwe versie van de analysecomponent implementeren. Deze nieuwe `1.1` versie geeft totalen en percentages weer naast het aantal voor elke categorie.

Het volgende diagram laat zien wat er aan het `1.1` einde `voting-analytics` van deze sectie `voting-app` wordt uitgevoerd - alleen de versie van ons onderdeel heeft verkeer dat van het onderdeel wordt gerouteerd. Hoewel de `1.0` versie `voting-analytics` van onze component blijft draaien `voting-analytics` en wordt verwezen door de service, de Istio proxy's verbieden verkeer van en naar het.

![De onderdelen en routering van de AKS Voting-app.](media/servicemesh/istio/scenario-routing-components-02.png)

Laten we de `1.1` versie `voting-analytics` van de component implementeren. Maak deze component `voting` in de naamruimte:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

In de volgende voorbeelduitvoer worden de resources weergegeven die worden gemaakt:

```output
deployment.apps/voting-analytics-1-1 created
```

Open de voorbeeld-AKS-stem-app opnieuw in een browser, met behulp van het IP-adres van de Istio Ingress Gateway die in de vorige stap is verkregen.

Uw browser wisselt tussen de twee weergaven hieronder. Aangezien u een [Kubernetes-service][kubernetes-service] gebruikt voor de `voting-analytics` component`app: voting-analytics`met slechts één labelkiezer ( ), gebruikt Kubernetes het standaardgedrag van round-robin tussen de pods die overeenkomen met die kiezer. In dit geval is `1.0` het `1.1` zowel `voting-analytics` versie als van uw pods.

![Versie 1.0 van de analytics-component die wordt uitgevoerd in onze AKS Voting-app.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Versie 1.1 van de analytics-component die wordt uitgevoerd in onze AKS Voting-app.](media/servicemesh/istio/scenario-routing-update-app-01.png)

U het schakelen tussen de `voting-analytics` twee versies van de component als volgt visualiseren. Vergeet niet om het IP-adres van uw eigen Istio Ingress Gateway te gebruiken.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

In de volgende voorbeelduitvoer wordt het relevante deel van de geretourneerde website weergegeven terwijl de site tussen versies schakelt:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Verkeer vergrendelen naar versie 1.1 van de toepassing

Laten we nu het verkeer `1.1` vergrendelen `voting-analytics` naar alleen `1.0` de `voting-storage` versie van de component en naar de versie van de component. Vervolgens definieert u routeringsregels voor alle andere onderdelen.

> * Een **virtuele service** definieert een set routeringsregels voor een of meer bestemmingsservices.
> * Een **doelregel** definieert verkeersbeleid en versiespecifiek beleid.
> * Een **beleid** definieert welke verificatiemethoden kunnen worden geaccepteerd op werkbelasting(en).

Gebruik `kubectl apply` de opdracht om de definitie `voting-app` van virtuele service op uw [doelregels][istio-reference-destinationrule] en [virtuele services][istio-reference-virtualservice] voor de andere componenten toe te voegen. U voegt [Policy][istio-reference-policy] een beleid `voting` toe aan de naamruimte om ervoor te zorgen dat alle communicatie tussen services wordt beveiligd met behulp van wederzijdse TLS- en clientcertificaten.

* Het beleid `peers.mtls.mode` is `STRICT` ingesteld om ervoor te zorgen dat `voting` wederzijdse TLS wordt afgedwongen tussen uw services binnen de naamruimte.
* We hebben `trafficPolicy.tls.mode` ook `ISTIO_MUTUAL` de to in al onze bestemmingsregels. Istio biedt diensten met sterke identiteiten en beveiligt communicatie tussen diensten met behulp van wederzijdse TLS- en clientcertificaten die Istio transparant beheert.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

In de volgende voorbeelduitvoerwordt de nieuwe beleidsregels, bestemmingsregels en virtuele services weergegeven die worden bijgewerkt/gemaakt:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Als u de AKS Voting-app opnieuw opent `1.1` in `voting-analytics` een browser, `voting-app` wordt alleen de nieuwe versie van de component door de component gebruikt.

![Versie 1.1 van de analytics-component die wordt uitgevoerd in onze AKS Voting-app.](media/servicemesh/istio/scenario-routing-update-app-01.png)

U visualiseren dat u nu `1.1` alleen `voting-analytics` als volgt naar de versie van uw component wordt doorgestuurd. Vergeet niet om het IP-adres van uw eigen Istio Ingress Gateway te gebruiken:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

In de volgende voorbeelduitvoer wordt het relevante deel van de geretourneerde website weergegeven:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

Laten we nu bevestigen dat Istio wederzijdse TLS gebruikt om de communicatie tussen elk van onze services te beveiligen. Hiervoor gebruiken we de [authn tls-check][istioctl-authn-tls-check] opdracht op de `istioctl` client binaire, die de volgende vorm neemt.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Deze set opdrachten geeft informatie over de toegang tot de opgegeven services, van alle pods die zich in een naamruimte bevinden en overeenkomen met een set labels:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

Uit de volgende voorbeelduitvoer blijkt dat wederzijdse TLS wordt afgedwongen voor elk van onze bovenstaande query's. De uitvoer toont ook de beleids- en bestemmingsregels die de wederzijdse TLS afdwingen:

```output
# mTLS configuration between istio ingress pods and the voting-app service
HOST:PORT                                    STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-app.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-app/voting

# mTLS configuration between each of the voting-app pods and the voting-analytics service
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting
HOST:PORT                                          STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-analytics.voting.svc.cluster.local:8080     OK         mTLS       mTLS       default/voting     voting-analytics/voting

# mTLS configuration between each of the voting-app pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
HOST:PORT                                        STATUS     SERVER     CLIENT     AUTHN POLICY       DESTINATION RULE
voting-storage.voting.svc.cluster.local:6379     OK         mTLS       mTLS       default/voting     voting-storage/voting
```

## <a name="roll-out-a-canary-release-of-the-application"></a>Een canary release van de applicatie uitrollen

Laten we nu een `2.0` nieuwe `voting-app`versie `voting-analytics`van `voting-storage` de , en componenten implementeren. De `voting-storage` nieuwe component gebruikt MySQL in `voting-app` plaats `voting-analytics` van Redis en de `voting-storage` onderdelen en componenten worden bijgewerkt zodat ze deze nieuwe component kunnen gebruiken.

Het `voting-app` onderdeel ondersteunt nu functievlagfunctionaliteit. Met deze functievlag u de canary release-mogelijkheid van Istio testen voor een subset van gebruikers.

In het volgende diagram ziet u wat er aan het einde van deze sectie wordt uitgevoerd.

* Versie `1.0` van `voting-app` de `1.1` component, `voting-analytics` versie `1.0` van `voting-storage` de component en versie van de component kunnen met elkaar communiceren.
* Versie `2.0` van `voting-app` de `2.0` component, `voting-analytics` versie `2.0` van `voting-storage` de component en versie van de component kunnen met elkaar communiceren.
* Versie `2.0` van `voting-app` de component is alleen toegankelijk voor gebruikers met een specifieke functievlagset. Deze wijziging wordt beheerd met behulp van een functievlag via een cookie.

![De onderdelen en routering van de AKS Voting-app.](media/servicemesh/istio/scenario-routing-components-03.png)

Werk eerst de Istio-bestemmingsregels en virtuele services bij om deze nieuwe componenten te kunnen gebruiken. Deze updates zorgen ervoor dat u verkeer niet verkeerd doorzet naar de nieuwe componenten en dat gebruikers geen onverwachte toegang krijgen:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

In de volgende voorbeelduitvoer ziet u dat de doelregels en virtuele services worden bijgewerkt:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Laten we vervolgens de Kubernetes-objecten toevoegen `2.0` voor de nieuwe versiecomponenten. U werkt `voting-storage` de service `3306` ook bij om de poort voor MySQL op te nemen:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

In de volgende voorbeelduitvoerziet u dat de Kubernetes-objecten zijn bijgewerkt of gemaakt:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Wacht tot alle `2.0` versiepods actief zijn. Gebruik de opdracht [kubectl][kubectl-get] `-w` get pods met de horlogeschakelaar `voting` om te kijken naar wijzigingen op alle pods in de naamruimte:

```console
kubectl get pods --namespace voting -w
```

U moet nu kunnen schakelen `1.0` tussen `2.0` de versie en versie (kanarie) van de stemtoepassing. De functievlag die onder aan het scherm wordt geschakeld, stelt een cookie in. Deze cookie wordt `voting-app` door de Virtuele Dienst gebruikt `2.0`om gebruikers naar de nieuwe versie te leiden.

![Versie 1.0 van de AKS Voting app - feature flag IS NOT set.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Versie 2.0 van de AKS Voting-app - functievlag IS ingesteld.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

De stemtellingen zijn verschillend tussen de versies van de app. Dit verschil benadrukt dat u twee verschillende opslagback-ends gebruikt.

## <a name="finalize-the-rollout"></a>De implementatie afronden

Zodra u de canary-release hebt `voting-app` getest, werkt u de `2.0` virtuele `voting-app` service bij om al het verkeer naar de versie van de component te leiden. Alle gebruikers zien `2.0` dan de versie van de toepassing, ongeacht of de functievlag is ingesteld of niet:

![De onderdelen en routering van de AKS Voting-app.](media/servicemesh/istio/scenario-routing-components-04.png)

Werk alle doelregels bij om de versies van de onderdelen te verwijderen die u niet langer actief wilt hebben. Werk vervolgens alle virtuele services bij om te stoppen met verwijzen naar deze versies.

Aangezien er geen verkeer meer is naar een van de oudere versies van de componenten, u nu alle implementaties voor die onderdelen veilig verwijderen.

![De onderdelen en routering van de AKS Voting-app.](media/servicemesh/istio/scenario-routing-components-05.png)

Je hebt nu met succes een nieuwe versie van de AKS Voting App uitgerold.

## <a name="clean-up"></a>Opruimen 

U de AKS-stem-app die we in dit scenario `voting` hebben gebruikt, uit uw AKS-cluster verwijderen door de naamruimte als volgt te verwijderen:

```console
kubectl delete namespace voting
```

Uit de volgende voorbeelduitvoer blijkt dat alle onderdelen van de AKS-stemapp uit uw AKS-cluster zijn verwijderd.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Volgende stappen

U aanvullende scenario's verkennen met behulp van het voorbeeld van de [Istio Bookinfo-toepassing][istio-bookinfo-example].

<!-- LINKS - external -->
[github-azure-sample]: https://github.com/Azure-Samples/aks-voting-app
[istio-github]: https://github.com/istio/istio

[istio]: https://istio.io
[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-requirements-pods-and-services]: https://istio.io/docs/setup/kubernetes/prepare/requirements/
[istio-reference-gateway]: https://istio.io/docs/reference/config/networking/v1alpha3/gateway/
[istio-reference-policy]: https://istio.io/docs/reference/config/istio.authentication.v1alpha1/#Policy
[istio-reference-virtualservice]: https://istio.io/docs/reference/config/networking/v1alpha3/virtual-service/
[istio-reference-destinationrule]: https://istio.io/docs/reference/config/networking/v1alpha3/destination-rule/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[istioctl-authn-tls-check]: https://istio.io/docs/reference/commands/istioctl/#istioctl-authn-tls-check

[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-install]: ./servicemesh-istio-install.md

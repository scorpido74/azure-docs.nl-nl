---
title: Intelligente route ring en Canarische releases met Istio in azure Kubernetes service (AKS)
description: Meer informatie over het gebruik van Istio voor intelligente route ring en het implementeren van Canarische releases in een Azure Kubernetes service-cluster (AKS)
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 4a695957c287e69ff6b40e5a01254a729eaae441
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2020
ms.locfileid: "78273010"
---
# <a name="use-intelligent-routing-and-canary-releases-with-istio-in-azure-kubernetes-service-aks"></a>Intelligente route ring en Canarische releases gebruiken met Istio in azure Kubernetes service (AKS)

[Istio][istio-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten verkeer beheer, service-identiteit en-beveiliging, afdwinging van beleid en de bekrachtiging. Zie voor meer informatie over Istio de officiële [What is Istio?][istio-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u de functionaliteit voor verkeers beheer van Istio gebruikt. Een voor beeld van een AKS stem-app wordt gebruikt om intelligente route ring en Canarische releases te verkennen.

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * De toepassing implementeren
> * De toepassing bijwerken
> * Een Canarische versie van de toepassing samen vouwen
> * De implementatie volt ooien

## <a name="before-you-begin"></a>Voordat u begint

> [!NOTE]
> Dit scenario is getest op Istio-versie `1.3.2`.

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.13` en hoger, waarbij RBAC is ingeschakeld) en een `kubectl`-verbinding met het cluster heeft gemaakt. U hebt ook Istio geïnstalleerd in uw cluster.

Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart] start en [installeert u Istio in AKS][istio-install] Guidance (Engelstalig).

## <a name="about-this-application-scenario"></a>Over dit toepassings scenario

De voor beeld-AKS stem-app biedt twee stem opties (**katten** of **honden**) aan gebruikers. Er is een opslag onderdeel waarmee het aantal stemmen voor elke optie wordt gehandhaafd. Daarnaast bevat een analyse component waarmee details over de stemmen voor elke optie worden verstrekt.

In dit toepassings scenario begint u met het implementeren van versie `1.0` van de stem-app en versie `1.0` van het onderdeel analyse. De analyse component biedt eenvoudige tellingen voor het aantal stemmen. De stem-app en het analyse onderdeel communiceren met versie `1.0` van het opslag onderdeel, die wordt ondersteund door redis.

U werkt de analyse component bij naar versie `1.1`, die tellingen en nu totalen en percentages bevat.

Een subset van gebruikers test versie `2.0` van de app via een Canarische versie. Deze nieuwe versie maakt gebruik van een opslag onderdeel dat wordt ondersteund door een MySQL-data base.

Wanneer u zeker weet dat versie `2.0` werkt zoals verwacht in uw subset van gebruikers, implementeert u versie `2.0` voor al uw gebruikers.

## <a name="deploy-the-application"></a>De toepassing implementeren

Laten we beginnen met het implementeren van de toepassing in uw Azure Kubernetes service (AKS)-cluster. In het volgende diagram ziet u wat er wordt uitgevoerd aan het einde van deze sectie-versie `1.0` van alle onderdelen met inkomende aanvragen die worden verwerkt via de Istio ingress-gateway:

![De onderdelen en route ring van de AKS stem-app.](media/servicemesh/istio/scenario-routing-components-01.png)

De artefacten die u moet volgen in dit artikel, zijn beschikbaar in de [Azure-samples/AKS-stem-app][github-azure-sample] github opslag plaats. U kunt de artefacten downloaden of de opslag plaats als volgt klonen:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Ga naar de volgende map in de gedownloade/gekloonde opslag plaats en voer alle volgende stappen uit in deze map:

```console
cd aks-voting-app/scenarios/intelligent-routing-with-istio
```

Maak eerst een naam ruimte in uw AKS-cluster voor de voor beeld-AKS stem-app met de naam `voting` als volgt:

```console
kubectl create namespace voting
```

Voorzie de naam ruimte van een label met `istio-injection=enabled`. Dit label geeft Istio om de Istio-proxy's automatisch als zijspan te injecteren in al uw peulen in deze naam ruimte.

```console
kubectl label namespace voting istio-injection=enabled
```

We gaan nu de onderdelen maken voor de AKS stem-app. Maak deze onderdelen in de `voting` naam ruimte die u in een vorige stap hebt gemaakt.

```console
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de resources die worden gemaakt:

```output
deployment.apps/voting-storage-1-0 created
service/voting-storage created
deployment.apps/voting-analytics-1-0 created
service/voting-analytics created
deployment.apps/voting-app-1-0 created
service/voting-app created
```

> [!NOTE]
> Istio heeft een aantal specifieke vereisten rond peulen en services. Zie de [Istio-vereisten voor peul-en service documentatie][istio-requirements-pods-and-services]voor meer informatie.

Als u wilt zien wat het meest is gemaakt, gebruikt u de opdracht [kubectl Get peul][kubectl-get] als volgt:

```console
kubectl get pods -n voting --show-labels
```

In de volgende voorbeeld uitvoer ziet u dat er drie exemplaren van de `voting-app` pod en één exemplaar van zowel de `voting-analytics` als `voting-storage` peul zijn. Elk van de peulen heeft twee containers. Een van deze containers is het onderdeel en de andere is de `istio-proxy`:

```output
NAME                                    READY     STATUS    RESTARTS   AGE   LABELS
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s   app=voting-analytics,pod-template-hash=57c7fccb44,version=1.0
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s   app=voting-app,pod-template-hash=956756fd,version=1.0
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s   app=voting-storage,pod-template-hash=5d8fcc89c4,version=1.0
```

Als u informatie wilt weer geven over de Pod, gebruikt u de [kubectl pod][kubectl-describe] -opdracht beschrijven met label selectie vakjes om de `voting-analytics` pod te selecteren. De uitvoer wordt gefilterd om de details weer te geven van de twee containers die aanwezig zijn in de Pod:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - show autoinjected proxy](includes/servicemesh/istio/scenario-routing-show-proxy-powershell.md)]

::: zone-end

U kunt pas verbinding maken met de stem-app als u de Istio- [Gateway][istio-reference-gateway] en de [virtuele service][istio-reference-virtualservice]hebt gemaakt. Deze Istio bronnen routeren verkeer van de standaard-Istio van de binnenkomende gateway naar onze toepassing.

> [!NOTE]
> Een **Gateway** is een onderdeel aan de rand van het service-net dat binnenkomend of uitgaand http-en TCP-verkeer ontvangt.
> 
> Een **virtuele service** definieert een set routerings regels voor een of meer doel Services.

Gebruik de `kubectl apply` opdracht om de gateway en de virtuele service yaml te implementeren. Vergeet niet om de naam ruimte op te geven waarin deze resources worden geïmplementeerd.

```console
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de nieuwe gateway en de virtuele service die wordt gemaakt:

```output
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Gebruik de volgende opdracht om het IP-adres van de Istio ingress-gateway te verkrijgen:

```output
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

In de volgende voorbeeld uitvoer wordt het IP-adres van de ingangs gateway weer gegeven:

```output
20.188.211.19
```

Open een browser en plak het IP-adres. De voor beeld-app voor AKS stem wordt weer gegeven.

![De AKS stem-app die wordt uitgevoerd in onze Istio ingeschakelde AKS-cluster.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

In de informatie onder aan het scherm ziet u dat de App versie `1.0` van `voting-app` en versie `1.0` van `voting-storage` (redis) gebruikt.

## <a name="update-the-application"></a>De toepassing bijwerken

We gaan een nieuwe versie van het onderdeel analyse implementeren. In deze nieuwe versie `1.1` worden totalen en percentages weer gegeven naast het aantal voor elke categorie.

In het volgende diagram ziet u wat er aan het einde van deze sectie alleen wordt uitgevoerd `1.1` van ons `voting-analytics` onderdeel verkeer heeft gerouteerd van het `voting-app` onderdeel. Ondanks dat versie `1.0` van onze `voting-analytics` onderdeel blijft lopen en waarnaar wordt verwezen door de `voting-analytics` service, worden de Istio-proxy's niet toegestaan.

![De onderdelen en route ring van de AKS stem-app.](media/servicemesh/istio/scenario-routing-components-02.png)

We gaan versie `1.1` van het onderdeel `voting-analytics` implementeren. Dit onderdeel maken in de naam ruimte `voting`:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de resources die worden gemaakt:

```output
deployment.apps/voting-analytics-1-1 created
```

Open opnieuw de voor beeld-AKS stem-app in een browser, met behulp van het IP-adres van de Istio ingress-gateway die u in de vorige stap hebt verkregen.

Uw browser kan worden vervangen door de twee weer gaven die hieronder worden weer gegeven. Omdat u een Kubernetes- [service][kubernetes-service] gebruikt voor het onderdeel `voting-analytics` met slechts één label kiezer (`app: voting-analytics`), gebruikt Kubernetes het standaard gedrag van Round-Robin tussen het Peul dat overeenkomt met die selector. In dit geval is de versie `1.0` en `1.1` van uw `voting-analytics` peul.

![Versie 1,0 van de analyse component die wordt uitgevoerd in onze AKS-stem-app.](media/servicemesh/istio/scenario-routing-deploy-app-01.png)

![Versie 1,1 van de analyse component die wordt uitgevoerd in onze AKS-stem-app.](media/servicemesh/istio/scenario-routing-update-app-01.png)

U kunt de scha kelen tussen de twee versies van het onderdeel `voting-analytics` als volgt visualiseren. Vergeet niet om het IP-adres van uw eigen Istio ingangs gateway te gebruiken.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

In de volgende voorbeeld uitvoer ziet u het relevante deel van de geretourneerde website als de site switches tussen versies:

```output
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Het verkeer naar versie 1,1 van de toepassing vergren delen

We gaan het verkeer nu alleen vergren delen naar versie `1.1` van het onderdeel `voting-analytics` en de versie `1.0` van het onderdeel `voting-storage`. Vervolgens definieert u de routerings regels voor alle andere onderdelen.

> * Een **virtuele service** definieert een set routerings regels voor een of meer doel Services.
> * Een **doel regel** definieert Traffic-beleids regels en versie-specifieke beleids regels.
> * Een **beleid** definieert welke verificatie methoden kunnen worden geaccepteerd op werk belasting (s).

Gebruik de `kubectl apply` opdracht om de definitie van de virtuele service op uw `voting-app` te vervangen en om [doel regels][istio-reference-destinationrule] en [virtuele Services][istio-reference-virtualservice] voor de andere onderdelen toe te voegen. U voegt een [beleid][istio-reference-policy] toe aan de naam ruimte van `voting` om ervoor te zorgen dat alle communicatie tussen services wordt beveiligd met wederzijdse TLS-en client certificaten.

* Voor het beleid is `peers.mtls.mode` ingesteld op `STRICT` om ervoor te zorgen dat wederzijdse TLS wordt afgedwongen tussen uw services binnen de `voting` naam ruimte.
* We stellen de `trafficPolicy.tls.mode` ook in op `ISTIO_MUTUAL` in alle doel regels. Istio biedt services met sterke identiteiten en beveiligt communicatie tussen services met behulp van wederzijdse TLS-en client certificaten die Istio transparant beheert.

```console
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u het nieuwe beleid, de doel regels en de virtuele services die worden bijgewerkt/gemaakt:

```output
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Als u de AKS-stem-app opnieuw opent in een browser, wordt alleen de nieuwe versie `1.1` van het onderdeel `voting-analytics` gebruikt door het `voting-app` onderdeel.

![Versie 1,1 van de analyse component die wordt uitgevoerd in onze AKS-stem-app.](media/servicemesh/istio/scenario-routing-update-app-01.png)

U kunt visualiseren dat u nu alleen naar versie `1.1` van uw `voting-analytics` onderdeel gaat als volgt. Vergeet niet om het IP-adres van uw eigen Istio ingangs gateway te gebruiken:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - loop through results](includes/servicemesh/istio/scenario-routing-loop-results-powershell.md)]

::: zone-end

In de volgende voorbeeld uitvoer ziet u het relevante deel van de geretourneerde website:

```output
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

We gaan nu controleren of Istio wederzijdse TLS gebruikt voor het beveiligen van de communicatie tussen de verschillende services. Hiervoor gebruiken we de [TLS-controle opdracht van authn][istioctl-authn-tls-check] op de binaire `istioctl`-client, die de volgende vorm heeft.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Deze reeks opdrachten bevat informatie over de toegang tot de opgegeven services, van alle peulen die zich in een naam ruimte bevinden en overeenkomen met een set labels:

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [Bash - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-bash.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [PowerShell - routing scenario - verify mtls](includes/servicemesh/istio/scenario-routing-verify-mtls-powershell.md)]

::: zone-end

In de volgende voorbeeld uitvoer ziet u dat wederzijdse TLS wordt afgedwongen voor elk van de bovenstaande query's. In de uitvoer ziet u ook de beleids-en doel regels die de wederzijdse TLS afdwingen:

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

## <a name="roll-out-a-canary-release-of-the-application"></a>Een Canarische versie van de toepassing samen vouwen

We gaan nu een nieuwe versie implementeren `2.0` van de onderdelen `voting-app`, `voting-analytics`en `voting-storage`. Het nieuwe `voting-storage`-onderdeel maakt gebruik van MySQL in plaats van redis en de `voting-app` en `voting-analytics` onderdelen worden bijgewerkt zodat ze dit nieuwe `voting-storage`-onderdeel kunnen gebruiken.

Het onderdeel `voting-app` ondersteunt nu functionaliteit voor functie vlaggen. Met deze functie vlag kunt u de Istio van de Canarische versie testen voor een subset van gebruikers.

In het volgende diagram ziet u wat u aan het einde van deze sectie kunt uitvoeren.

* Versie `1.0` van het onderdeel `voting-app`, versie `1.1` van de `voting-analytics` onderdeel en versie `1.0` van het `voting-storage` onderdeel kunnen communiceren met elkaar.
* Versie `2.0` van het onderdeel `voting-app`, versie `2.0` van de `voting-analytics` onderdeel en versie `2.0` van het `voting-storage` onderdeel kunnen communiceren met elkaar.
* Versie `2.0` van het onderdeel `voting-app` zijn alleen toegankelijk voor gebruikers waarvoor een specifieke functie vlag is ingesteld. Deze wijziging wordt beheerd met behulp van een functie vlag via een cookie.

![De onderdelen en route ring van de AKS stem-app.](media/servicemesh/istio/scenario-routing-components-03.png)

Werk eerst de Istio-doel regels en virtuele Services bij om deze nieuwe onderdelen in te stellen. Deze updates zorgen ervoor dat u geen verkeer stuurt naar de nieuwe onderdelen en gebruikers geen onverwachte toegang krijgen:

```console
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de doel regels en de virtuele services die worden bijgewerkt:

```output
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Vervolgens voegen we de Kubernetes-objecten voor de nieuwe versie `2.0` onderdelen toe. U werkt ook de `voting-storage`-service bij om de `3306` poort voor MySQL op te zetten:

```console
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u dat de Kubernetes-objecten zijn bijgewerkt of gemaakt:

```output
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Wacht totdat alle versies `2.0` peul worden uitgevoerd. Gebruik de opdracht [kubectl Get peul][kubectl-get] met de schakel optie `-w` Watch om te kijken of er wijzigingen zijn op alle peulen in de `voting` naam ruimte:

```console
kubectl get pods --namespace voting -w
```

U kunt nu scha kelen tussen de versie `1.0` en versie `2.0` (Canarische) van de stem toepassing. Met de functie vlag in-of uitschakelen onder aan het scherm wordt een cookie ingesteld. Deze cookie wordt gebruikt door de virtuele `voting-app`-service om gebruikers te routeren naar de nieuwe versie `2.0`.

![Versie 1,0 van de AKS stem-app-functie vlag IS niet ingesteld.](media/servicemesh/istio/scenario-routing-canary-release-01.png)

![Versie 2,0 van de AKS stem-app-functie vlag IS ingesteld.](media/servicemesh/istio/scenario-routing-canary-release-02.png)

De stem aantallen verschillen van de versies van de app. Dit verschil markeert dat u twee verschillende opslag back-ends gebruikt.

## <a name="finalize-the-rollout"></a>De implementatie volt ooien

Zodra u de Canarische versie hebt getest, werkt u de virtuele `voting-app`-service bij om al het verkeer naar versie `2.0` van het `voting-app` onderdeel te routeren. Alle gebruikers zien vervolgens de versie `2.0` van de toepassing, ongeacht of de functie vlag is ingesteld of niet:

![De onderdelen en route ring van de AKS stem-app.](media/servicemesh/istio/scenario-routing-components-04.png)

Werk alle doel regels bij om de versies te verwijderen van de onderdelen die u niet meer wilt laten actief. Werk vervolgens alle virtuele Services bij om te stoppen met het verwijzen naar die versies.

Omdat er geen verkeer meer is naar een van de oudere versies van de onderdelen, kunt u nu veilig alle implementaties voor deze onderdelen verwijderen.

![De onderdelen en route ring van de AKS stem-app.](media/servicemesh/istio/scenario-routing-components-05.png)

U hebt nu een nieuwe versie van de AKS-stem-app geïmplementeerd.

## <a name="clean-up"></a>Opruimen 

U kunt de AKS-stem-app die we in dit scenario hebben gebruikt, verwijderen uit uw AKS-cluster door de `voting` naam ruimte als volgt te verwijderen:

```console
kubectl delete namespace voting
```

In de volgende voorbeeld uitvoer ziet u dat alle onderdelen van de app AKS stemmen zijn verwijderd uit uw AKS-cluster.

```output
namespace "voting" deleted
```

## <a name="next-steps"></a>Volgende stappen

U kunt aanvullende scenario's verkennen met behulp van het [voor beeld van de Istio Bookinfo-toepassing][istio-bookinfo-example].

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

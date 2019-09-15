---
title: Intelligente route ring en Canarische releases met Istio in azure Kubernetes service (AKS)
description: Meer informatie over het gebruik van Istio voor intelligente route ring en het implementeren van Canarische releases in een Azure Kubernetes service-cluster (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 7baa2adbd615a449c73e70e1b96524fc1e18b25d
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000179"
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
> Dit scenario is getest op Istio-versie `1.1.3`.

Voor de stappen in dit artikel wordt ervan uitgegaan dat u een AKS- `1.11` cluster hebt gemaakt (Kubernetes en hoger, met RBAC ingeschakeld `kubectl` ) en een verbinding met het cluster tot stand hebt gebracht. U hebt ook Istio geïnstalleerd in uw cluster.

Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart] start en [installeert u Istio in AKS][istio-install] Guidance (Engelstalig).

## <a name="about-this-application-scenario"></a>Over dit toepassings scenario

De voor beeld-AKS stem-app biedt twee stem opties (**katten** of **honden**) aan gebruikers. Er is een opslag onderdeel waarmee het aantal stemmen voor elke optie wordt gehandhaafd. Daarnaast bevat een analyse component waarmee details over de stemmen voor elke optie worden verstrekt.

In dit toepassings scenario begint u met het implementeren van `1.0` de versie van de stem- `1.0` app en versie van het onderdeel analyse. De analyse component biedt eenvoudige tellingen voor het aantal stemmen. De stem-app en het analyse onderdeel communiceren `1.0` met de versie van het opslag onderdeel, dat wordt ondersteund door redis.

U werkt de analyse component bij naar `1.1`versie, die tellingen en nu totalen en percentages bevat.

Een subset van gebruikers test versie `2.0` van de app via een Canarische versie. Deze nieuwe versie maakt gebruik van een opslag onderdeel dat wordt ondersteund door een MySQL-data base.

Wanneer u zeker weet dat de `2.0` versie werkt zoals verwacht op uw subset van gebruikers, kunt u de `2.0` versie van alle gebruikers inrollen.

## <a name="deploy-the-application"></a>De toepassing implementeren

Laten we beginnen met het implementeren van de toepassing in uw Azure Kubernetes service (AKS)-cluster. In het volgende diagram ziet u wat er wordt uitgevoerd aan het einde van `1.0` deze sectie-versie van alle onderdelen met inkomende aanvragen die worden verwerkt via de Istio ingress-gateway:

![De onderdelen en route ring van de AKS stem-app.](media/istio/components-and-routing-01.png)

De artefacten die u moet volgen in dit artikel, zijn beschikbaar in de [Azure-samples/AKS-stem-app][github-azure-sample] github opslag plaats. U kunt de artefacten downloaden of de opslag plaats als volgt klonen:

```console
git clone https://github.com/Azure-Samples/aks-voting-app.git
```

Ga naar de volgende map in de gedownloade/gekloonde opslag plaats en voer alle volgende stappen uit in deze map:

```console
cd scenarios/intelligent-routing-with-istio
```

Maak eerst een naam ruimte in uw AKS-cluster voor de voor beeld-AKS `voting` stem-app met de naam:

```azurecli
kubectl create namespace voting
```

Label de naam ruimte `istio-injection=enabled`met. Dit label geeft Istio om de Istio-proxy's automatisch als zijspan te injecteren in al uw peulen in deze naam ruimte.

```azurecli
kubectl label namespace voting istio-injection=enabled
```

We gaan nu de onderdelen maken voor de AKS stem-app. Maak deze onderdelen in de `voting` naam ruimte die u in een vorige stap hebt gemaakt.

```azurecli
kubectl apply -f kubernetes/step-1-create-voting-app.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de resources die worden gemaakt:

```console
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

```azurecli
kubectl get pods -n voting
```

In de volgende voorbeeld uitvoer ziet u dat er drie exemplaren `voting-app` van de Pod en één exemplaar van zowel `voting-analytics` de `voting-storage` als de peul zijn. Elk van de peulen heeft twee containers. Een van deze containers is het onderdeel en de andere `istio-proxy`:

```console
NAME                                    READY     STATUS    RESTARTS   AGE
voting-analytics-1-0-57c7fccb44-ng7dl   2/2       Running   0          39s
voting-app-1-0-956756fd-d5w7z           2/2       Running   0          39s
voting-app-1-0-956756fd-f6h69           2/2       Running   0          39s
voting-app-1-0-956756fd-wsxvt           2/2       Running   0          39s
voting-storage-1-0-5d8fcc89c4-2jhms     2/2       Running   0          39s
```

Als u informatie over de pod wilt weer geven, gebruikt u de [kubectl beschrijven pod][kubectl-describe]. Vervang de naam van de pod door de naam van een pod in uw eigen AKS-cluster uit de vorige uitvoer:

```azurecli
kubectl describe pod voting-app-1-0-956756fd-d5w7z --namespace voting
```

De `istio-proxy` container is automatisch geïnjecteerd door Istio om het netwerk verkeer van en naar uw onderdelen te beheren, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```
[...]
Containers:
  voting-app:
    Image:         mcr.microsoft.com/aks/samples/voting/app:1.0
    ...
  istio-proxy:
    Image:         docker.io/istio/proxyv2:1.1.3
[...]
```

U kunt pas verbinding maken met de stem-app als u de Istio- [Gateway][istio-reference-gateway] en de [virtuele service][istio-reference-virtualservice]hebt gemaakt. Deze Istio bronnen routeren verkeer van de standaard-Istio van de binnenkomende gateway naar onze toepassing.

> [!NOTE]
> Een **Gateway** is een onderdeel aan de rand van het service-net dat binnenkomend of uitgaand http-en TCP-verkeer ontvangt.
> 
> Een **virtuele service** definieert een set routerings regels voor een of meer doel Services.

Gebruik de `kubectl apply` opdracht om de gateway en de virtuele service yaml te implementeren. Vergeet niet om de naam ruimte op te geven waarin deze resources worden geïmplementeerd.

```azurecli
kubectl apply -f istio/step-1-create-voting-app-gateway.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de nieuwe gateway en de virtuele service die wordt gemaakt:

```console
virtualservice.networking.istio.io/voting-app created
gateway.networking.istio.io/voting-app-gateway created
```

Gebruik de volgende opdracht om het IP-adres van de Istio ingress-gateway te verkrijgen:

```azurecli
kubectl get service istio-ingressgateway --namespace istio-system -o jsonpath='{.status.loadBalancer.ingress[0].ip}'
```

In de volgende voorbeeld uitvoer wordt het IP-adres van de ingangs gateway weer gegeven:

```
20.188.211.19
```

Open een browser en plak het IP-adres. De voor beeld-app voor AKS stem wordt weer gegeven.

![De AKS stem-app die wordt uitgevoerd in onze Istio ingeschakelde AKS-cluster.](media/istio/deploy-app-01.png)

In de informatie onder aan het scherm ziet u dat de app `1.0` versie van `voting-app` en versie `1.0` van `voting-storage` (redis) gebruikt.

## <a name="update-the-application"></a>De toepassing bijwerken

We gaan een nieuwe versie van het onderdeel analyse implementeren. In deze nieuwe `1.1` versie worden de totalen en percentages weer gegeven naast het aantal voor elke categorie.

In het volgende diagram ziet u wat er aan het einde van deze sectie wordt uitgevoerd- `1.1` alleen de `voting-analytics` versie van het onderdeel verkeer wordt gerouteerd `voting-app` van het onderdeel. Hoewel de versie `1.0` `voting-analytics` van het onderdeel blijft werken en er door de `voting-analytics` service naar wordt verwezen, worden de Istio-proxy's niet toegestaan.

![De onderdelen en route ring van de AKS stem-app.](media/istio/components-and-routing-02.png)

We gaan de versie `1.1` van het `voting-analytics` onderdeel implementeren. Dit onderdeel maken in de `voting` naam ruimte:

```console
kubectl apply -f kubernetes/step-2-update-voting-analytics-to-1.1.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de resources die worden gemaakt:

```console
deployment.apps/voting-analytics-1-1 created
```

Open opnieuw de voor beeld-AKS stem-app in een browser, met behulp van het IP-adres van de Istio ingress-gateway die u in de vorige stap hebt verkregen.

Uw browser kan worden vervangen door de twee weer gaven die hieronder worden weer gegeven. Omdat u een Kubernetes- [service][kubernetes-service] gebruikt voor het `voting-analytics` onderdeel met slechts één label kiezer (`app: voting-analytics`), gebruikt Kubernetes het standaard gedrag van Round-Robin tussen het Peul dat overeenkomt met die selector. In dit geval is het de versie `1.0` en `1.1` van uw `voting-analytics` peul.

![Versie 1,0 van de analyse component die wordt uitgevoerd in onze AKS-stem-app.](media/istio/deploy-app-01.png)

![Versie 1,1 van de analyse component die wordt uitgevoerd in onze AKS-stem-app.](media/istio/update-app-01.png)

U kunt de scha kelen tussen de twee versies van het `voting-analytics` onderdeel als volgt visualiseren. Vergeet niet om het IP-adres van uw eigen Istio ingangs gateway te gebruiken.

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

In de volgende voorbeeld uitvoer ziet u het relevante deel van de geretourneerde website als de site switches tussen versies:

```
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2 | Dogs: 4 </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

### <a name="lock-down-traffic-to-version-11-of-the-application"></a>Het verkeer naar versie 1,1 van de toepassing vergren delen

We gaan nu het verkeer vergren delen met `1.1` alleen de `voting-analytics` versie van het onderdeel `1.0` en de `voting-storage` versie van het onderdeel. Vervolgens definieert u de routerings regels voor alle andere onderdelen.

> * Een **virtuele service** definieert een set routerings regels voor een of meer doel Services.
> * Een **doel regel** definieert Traffic-beleids regels en versie-specifieke beleids regels.
> * Een **beleid** definieert welke verificatie methoden kunnen worden geaccepteerd op werk belasting (s).

Gebruik de `kubectl apply` opdracht om de definitie van de virtuele service te `voting-app` vervangen op uw en voeg [doel regels][istio-reference-destinationrule] en [virtuele Services][istio-reference-virtualservice] voor de andere onderdelen toe. U voegt een [beleid][istio-reference-policy] toe aan de `voting` naam ruimte om ervoor te zorgen dat alle communicatie tussen services wordt beveiligd met wederzijdse TLS-en client certificaten.

* Het beleid is `peers.mtls.mode` `STRICT` ingesteld op om ervoor te zorgen dat wederzijdse TLS tussen uw services binnen de `voting` naam ruimte wordt afgedwongen.
* We hebben ook de `trafficPolicy.tls.mode` in `ISTIO_MUTUAL` -en ingesteld op alle doel regels. Istio biedt services met sterke identiteiten en beveiligt communicatie tussen services met behulp van wederzijdse TLS-en client certificaten die Istio transparant beheert.

```azurecli
kubectl apply -f istio/step-2-update-and-add-routing-for-all-components.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u het nieuwe beleid, de doel regels en de virtuele services die worden bijgewerkt/gemaakt:

```console
virtualservice.networking.istio.io/voting-app configured
policy.authentication.istio.io/default created
destinationrule.networking.istio.io/voting-app created
destinationrule.networking.istio.io/voting-analytics created
virtualservice.networking.istio.io/voting-analytics created
destinationrule.networking.istio.io/voting-storage created
virtualservice.networking.istio.io/voting-storage created
```

Als u de AKS-stem-app opnieuw opent in een browser, wordt alleen `1.1` de nieuwe `voting-analytics` versie van het onderdeel gebruikt `voting-app` door het onderdeel.

![Versie 1,1 van de analyse component die wordt uitgevoerd in onze AKS-stem-app.](media/istio/update-app-01.png)

U kunt visualiseren dat u nu als volgt wordt doorgestuurd naar de `1.1` versie van `voting-analytics` uw onderdeel. Vergeet niet om het IP-adres van uw eigen Istio ingangs gateway te gebruiken:

Bash 

```bash
INGRESS_IP=20.188.211.19
for i in {1..5}; do curl -si $INGRESS_IP | grep results; done
```

PowerShell

```powershell
$INGRESS_IP="20.188.211.19"
(1..5) |% { (Invoke-WebRequest -Uri $INGRESS_IP).Content.Split("`n") | Select-String -Pattern "results" }
```

In de volgende voorbeeld uitvoer ziet u het relevante deel van de geretourneerde website:

```
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
  <div id="results"> Cats: 2/6 (33%) | Dogs: 4/6 (67%) </div>
```

We gaan nu controleren of Istio wederzijdse TLS gebruikt voor het beveiligen van de communicatie tussen de verschillende services. Hiervoor gebruiken we de [TLS-controle opdracht van authn][istioctl-authn-tls-check] op de `istioctl` binaire client, die de volgende vorm heeft.

```console
istioctl authn tls-check <pod-name[.namespace]> [<service>]
```

Deze reeks opdrachten bevat informatie over de toegang tot de opgegeven services, van alle peulen die zich in een naam ruimte bevinden en overeenkomen met een set labels:

Bash

```bash
# mTLS configuration between each of the istio ingress pods and the voting-app service
kubectl get pod -n istio-system -l app=istio-ingressgateway | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.istio-system voting-app.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-analytics service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-analytics.voting.svc.cluster.local

# mTLS configuration between each of the voting-app pods and the voting-storage service
kubectl get pod -n voting -l app=voting-app | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
kubectl get pod -n voting -l app=voting-analytics,version=1.1 | grep Running | cut -d ' ' -f1 | xargs -n1 -I{} istioctl authn tls-check {}.voting voting-storage.voting.svc.cluster.local
```

PowerShell

```powershell
# mTLS configuration between each of the istio ingress pods and the voting-app service
(kubectl get pod -n istio-system -l app=istio-ingressgateway | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".istio-system") voting-app.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-analytics service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-analytics.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-app pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-app | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }

# mTLS configuration between each of the voting-analytics version 1.1 pods and the voting-storage service
(kubectl get pod -n voting -l app=voting-analytics,version=1.1 | Select-String -Pattern "Running").Line |% { $_.Split()[0] |% { istioctl authn tls-check $($_ + ".voting") voting-storage.voting.svc.cluster.local } }
```

In de volgende voorbeeld uitvoer ziet u dat wederzijdse TLS wordt afgedwongen voor elk van de bovenstaande query's. In de uitvoer ziet u ook de beleids-en doel regels die de wederzijdse TLS afdwingen:

```console
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

We gaan nu `2.0` een nieuwe versie van de `voting-app`-, `voting-analytics`-en `voting-storage` -onderdelen implementeren. Het nieuwe `voting-storage` onderdeel gebruikt mysql in plaats van redis en de `voting-app` componenten `voting-analytics` en zijn bijgewerkt zodat ze dit nieuwe `voting-storage` onderdeel kunnen gebruiken.

Het `voting-app` onderdeel ondersteunt nu functionaliteit voor functie vlaggen. Met deze functie vlag kunt u de Istio van de Canarische versie testen voor een subset van gebruikers.

In het volgende diagram ziet u wat u aan het einde van deze sectie kunt uitvoeren.

* De `1.0` versie van `voting-app` het onderdeel, `1.1` de versie `voting-analytics` van het onderdeel `1.0` en de `voting-storage` versie van het onderdeel kan met elkaar communiceren.
* De `2.0` versie van `voting-app` het onderdeel, `2.0` de versie `voting-analytics` van het onderdeel `2.0` en de `voting-storage` versie van het onderdeel kan met elkaar communiceren.
* De `2.0` versie van `voting-app` het onderdeel is alleen toegankelijk voor gebruikers waarvoor een specifieke functie vlag is ingesteld. Deze wijziging wordt beheerd met behulp van een functie vlag via een cookie.

![De onderdelen en route ring van de AKS stem-app.](media/istio/components-and-routing-03.png)

Werk eerst de Istio-doel regels en virtuele Services bij om deze nieuwe onderdelen in te stellen. Deze updates zorgen ervoor dat u geen verkeer stuurt naar de nieuwe onderdelen en gebruikers geen onverwachte toegang krijgen:

```azurecli
kubectl apply -f istio/step-3-add-routing-for-2.0-components.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u de doel regels en de virtuele services die worden bijgewerkt:

```console
destinationrule.networking.istio.io/voting-app configured
virtualservice.networking.istio.io/voting-app configured
destinationrule.networking.istio.io/voting-analytics configured
virtualservice.networking.istio.io/voting-analytics configured
destinationrule.networking.istio.io/voting-storage configured
virtualservice.networking.istio.io/voting-storage configured
```

Vervolgens voegen we de Kubernetes-objecten voor de nieuwe versie `2.0` onderdelen toe. U moet ook de `voting-storage` service bijwerken zodat deze `3306` de poort voor mysql bevat:

```azurecli
kubectl apply -f kubernetes/step-3-update-voting-app-with-new-storage.yaml --namespace voting
```

In de volgende voorbeeld uitvoer ziet u dat de Kubernetes-objecten zijn bijgewerkt of gemaakt:

```console
service/voting-storage configured
secret/voting-storage-secret created
deployment.apps/voting-storage-2-0 created
persistentvolumeclaim/mysql-pv-claim created
deployment.apps/voting-analytics-2-0 created
deployment.apps/voting-app-2-0 created
```

Wacht totdat alle versies van `2.0` de versie worden uitgevoerd. Gebruik de opdracht [kubectl Get peul][kubectl-get] om alle peulen in de `voting` naam ruimte weer te geven:

```azurecli
kubectl get pods --namespace voting
```

Nu moet u kunnen scha kelen tussen de versie `1.0` en de versie `2.0` (Canarische) van de stem toepassing. Met de functie vlag in-of uitschakelen onder aan het scherm wordt een cookie ingesteld. Deze cookie wordt gebruikt door de `voting-app` virtuele service om gebruikers te routeren naar de `2.0`nieuwe versie.

![Versie 1,0 van de AKS stem-app-functie vlag IS niet ingesteld.](media/istio/canary-release-01.png)

![Versie 2,0 van de AKS stem-app-functie vlag IS ingesteld.](media/istio/canary-release-02.png)

De stem aantallen verschillen van de versies van de app. Dit verschil markeert dat u twee verschillende opslag back-ends gebruikt.

## <a name="finalize-the-rollout"></a>De implementatie volt ooien

Wanneer u de Canarische versie hebt getest, werkt u de `voting-app` virtuele service bij om alle verkeer naar de `2.0` versie van `voting-app` het onderdeel te routeren. Alle gebruikers zien vervolgens de `2.0` versie van de toepassing, ongeacht of de functie vlag is ingesteld of niet:

![De onderdelen en route ring van de AKS stem-app.](media/istio/components-and-routing-04.png)

Werk alle doel regels bij om de versies te verwijderen van de onderdelen die u niet meer wilt laten actief. Werk vervolgens alle virtuele Services bij om te stoppen met het verwijzen naar die versies.

Omdat er geen verkeer meer is naar een van de oudere versies van de onderdelen, kunt u nu veilig alle implementaties voor deze onderdelen verwijderen.

![De onderdelen en route ring van de AKS stem-app.](media/istio/components-and-routing-05.png)

U hebt nu een nieuwe versie van de AKS-stem-app geïmplementeerd.

## <a name="clean-up"></a>Opruimen 

U kunt de AKS-stem-app die we in dit scenario hebben gebruikt, verwijderen uit uw `voting` AKS-cluster door de naam ruimte als volgt te verwijderen:

```azurecli
kubectl delete namespace voting
```

In de volgende voorbeeld uitvoer ziet u dat alle onderdelen van de app AKS stemmen zijn verwijderd uit uw AKS-cluster.

```console
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
[istio-install]: ./istio-install.md

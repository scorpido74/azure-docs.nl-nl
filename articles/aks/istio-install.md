---
title: Istio installeren in azure Kubernetes service (AKS)
description: Meer informatie over het installeren en gebruiken van Istio voor het maken van een service-net in een Azure Kubernetes service-cluster (AKS)
services: container-service
author: paulbouwer
ms.service: container-service
ms.topic: article
ms.date: 04/19/2019
ms.author: pabouwer
ms.openlocfilehash: 9344d2832c37c34d5690dc8f3aae7394ca644276
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827329"
---
# <a name="install-and-use-istio-in-azure-kubernetes-service-aks"></a>Istio installeren en gebruiken in azure Kubernetes service (AKS)

[Istio][istio-github] is een open-source service-net dat een belang rijke set functionaliteit biedt voor de micro Services in een Kubernetes-cluster. Deze functies omvatten verkeer beheer, service-identiteit en-beveiliging, afdwinging van beleid en de bekrachtiging. Zie voor meer informatie over Istio de officiële [What is Istio?][istio-docs-concepts] -documentatie.

In dit artikel wordt beschreven hoe u Istio installeert. De Istio `istioctl`-client-binary wordt geïnstalleerd op de client computer en de Istio-onderdelen worden geïnstalleerd in een Kubernetes-cluster op AKS.

> [!NOTE]
> Deze instructies verwijzen naar Istio-versie `1.1.3`.
>
> De releases van Istio `1.1.x` zijn getest door het Istio-team tegen Kubernetes-versies `1.11`, `1.12`, `1.13`. U kunt aanvullende Istio-versies vinden op [github-Istio releases][istio-github-releases] en informatie over elk van de releases van [Istio News][istio-release-notes].

In dit artikel leert u het volgende:

> [!div class="checklist"]
> * Istio downloaden
> * De Istio istioctl-client binaire bestanden installeren
> * Installeer de Istio-CRDs op AKS
> * De Istio-onderdelen installeren op AKS
> * De Istio-installatie valideren
> * De invoeg toepassingen openen
> * Istio verwijderen uit AKS

## <a name="before-you-begin"></a>Voordat u begint

Voor de stappen die in dit artikel worden beschreven, wordt ervan uitgegaan dat u een AKS-cluster hebt gemaakt (Kubernetes `1.11` en hoger, waarbij RBAC is ingeschakeld) en een `kubectl`-verbinding met het cluster heeft gemaakt. Als u hulp nodig hebt bij een van deze items, raadpleegt u de [AKS Quick][aks-quickstart]start.

U hebt [helm][helm] nodig om deze instructies te volgen en Istio te installeren. Het is raadzaam dat versie `2.12.2` of hoger op de juiste wijze is geïnstalleerd en geconfigureerd in uw cluster. Als u hulp nodig hebt bij het installeren van helm, raadpleegt u de [AKS helm Installation guidance][helm-install](Engelstalig). Alle Istio-peulen moeten ook worden gepland om te worden uitgevoerd op Linux-knoop punten.

Zorg ervoor dat u de Istio-documentatie over [prestaties en schaal baarheid](https://istio.io/docs/concepts/performance-and-scalability/) hebt gelezen om inzicht te krijgen in de aanvullende bron vereisten voor het uitvoeren van Istio in uw AKS-cluster. De kern-en geheugen vereisten variëren op basis van uw specifieke werk belasting. Kies het juiste aantal knoop punten en de VM-grootte voor uw installatie.

In dit artikel worden de Istio-installatie richtlijnen gescheiden in verschillende afzonderlijke stappen. Het eind resultaat is hetzelfde als de officiële Istio-installatie [richtlijnen][istio-install-helm].

## <a name="download-istio"></a>Istio downloaden

Down load en pak eerst de nieuwste versie van Istio. De stappen zijn iets anders voor een bash-shell op MacOS, Linux of Windows-subsysteem voor Linux en voor een Power shell-shell. Kies een van de volgende installatie stappen die overeenkomen met uw voorkeurs omgeving:

* [Bash in MacOS, Linux of Windows-subsysteem voor Linux](#bash)
* [PowerShell](#powershell)

### <a name="bash"></a>Bash

Gebruik op MacOS `curl` om de meest recente Istio-versie te downloaden en vervolgens als volgt op te halen met `tar`:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

# MacOS
curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

Gebruik `curl` voor Linux-of Windows-subsysteem voor Linux en down load de nieuwste Istio-versie en Extraheer vervolgens met `tar` als volgt:

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.1.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-linux.tar.gz" | tar xz
```

Ga nu naar de sectie om [de Istio istioctl-client binaire bestanden te installeren](#install-the-istio-istioctl-client-binary).

### <a name="powershell"></a>PowerShell

In Power shell gebruikt u `Invoke-WebRequest` om de meest recente Istio-versie te downloaden en vervolgens als volgt op te halen met `Expand-Archive`:

```powershell
# Specify the Istio version that will be leveraged throughout these instructions
$ISTIO_VERSION="1.1.3"

# Windows
# Use TLS 1.2
[Net.ServicePointManager]::SecurityProtocol = "tls12"
$ProgressPreference = 'SilentlyContinue'; Invoke-WebRequest -URI "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-win.zip" -OutFile "istio-$ISTIO_VERSION.zip"
Expand-Archive -Path "istio-$ISTIO_VERSION.zip" -DestinationPath .
```

Ga nu naar de sectie om [de Istio istioctl-client binaire bestanden te installeren](#install-the-istio-istioctl-client-binary).

## <a name="install-the-istio-istioctl-client-binary"></a>De Istio istioctl-client binaire bestanden installeren

> [!IMPORTANT]
> Zorg ervoor dat u de stappen in deze sectie uitvoert vanuit de map op het hoogste niveau van de Istio-release die u hebt gedownload en geëxtraheerd.

De `istioctl`-client-binary wordt uitgevoerd op uw client computer en maakt het mogelijk om te communiceren met het Istio-service-net. De installatie stappen zijn enigszins anders tussen client besturingssystemen. Kies een van de volgende installatie stappen die overeenkomen met uw voorkeurs omgeving:

* [MacOS](#macos)
* [Linux of Windows-subsysteem voor Linux](#linux-or-windows-subsystem-for-linux)
* [Windows](#windows)

### <a name="macos"></a>MacOS

Gebruik de volgende opdrachten om de Istio `istioctl`-client-binary te installeren in een shell op basis van bash op MacOS. Met deze opdrachten wordt de `istioctl`-client-binary gekopieerd naar de standaard locatie van het gebruikers programma in uw `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Als u de voltooiing van de opdracht regel voor de Istio `istioctl`-client binair wilt maken, moet u deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Ga nu verder met de volgende sectie om [de Istio CRDs op AKS te installeren](#install-the-istio-crds-on-aks).

### <a name="linux-or-windows-subsystem-for-linux"></a>Linux of Windows-subsysteem voor Linux

Gebruik de volgende opdrachten om de Istio `istioctl`-client-binary te installeren in een op bash gebaseerde shell op een Linux-of [Windows-subsysteem voor Linux][install-wsl]. Met deze opdrachten wordt de `istioctl`-client-binary gekopieerd naar de standaard locatie van het gebruikers programma in uw `PATH`.

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

Als u de voltooiing van de opdracht regel voor de Istio `istioctl`-client binair wilt maken, moet u deze als volgt instellen:

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

Ga nu verder met de volgende sectie om [de Istio CRDs op AKS te installeren](#install-the-istio-crds-on-aks).

### <a name="windows"></a>Windows

Gebruik de volgende opdrachten om de Istio `istioctl`-client-binary te installeren in een **Power shell**-shell op Windows. Met deze opdrachten kopieert u de `istioctl`-client binaire bestanden naar een Istio-map en maakt u deze zowel onmiddellijk beschikbaar (in de huidige shell) als permanent (tussen de shell opnieuw opstarten) via uw `PATH`. U hebt geen verhoogde beheerders bevoegdheden nodig om deze opdrachten uit te voeren en u hoeft de shell niet opnieuw op te starten.

```powershell
# Copy istioctl.exe to C:\Istio
cd istio-$ISTIO_VERSION
New-Item -ItemType Directory -Force -Path "C:\Istio"
Copy-Item -Path .\bin\istioctl.exe -Destination "C:\Istio\"

# Add C:\Istio to PATH. 
# Make the new PATH permanently available for the current User, and also immediately available in the current shell.
$PATH = [environment]::GetEnvironmentVariable("PATH", "User") + "; C:\Istio\"
[environment]::SetEnvironmentVariable("PATH", $PATH, "User") 
[environment]::SetEnvironmentVariable("PATH", $PATH)
```

Ga nu verder met de volgende sectie om [de Istio CRDs op AKS te installeren](#install-the-istio-crds-on-aks).

## <a name="install-the-istio-crds-on-aks"></a>Installeer de Istio-CRDs op AKS

> [!IMPORTANT]
> Zorg ervoor dat u de stappen in deze sectie uitvoert vanuit de map op het hoogste niveau van de Istio-release die u hebt gedownload en geëxtraheerd.

Istio maakt gebruik van [aangepaste resource definities (CRDs)][kubernetes-crd] voor het beheren van de runtime configuratie. De Istio CRDs moet eerst worden geïnstalleerd, omdat de Istio-onderdelen hiervan afhankelijk zijn. Gebruik helm en de grafiek `istio-init` om de Istio-CRDs te installeren in de naam ruimte `istio-system` in uw AKS-cluster:

```azurecli
helm install install/kubernetes/helm/istio-init --name istio-init --namespace istio-system
```

[Taken][kubernetes-jobs] worden geïmplementeerd als onderdeel van het helm-diagram van `istio-init` om de CRDs te installeren. Deze taken moeten tussen 1 en 2 minuten worden uitgevoerd, afhankelijk van uw cluster omgeving. U kunt als volgt controleren of de taken zijn voltooid:

```azurecli
kubectl get jobs -n istio-system
```

In de volgende voorbeeld uitvoer ziet u de voltooide taken.

```console
NAME                COMPLETIONS   DURATION   AGE
istio-init-crd-10   1/1           16s        18s
istio-init-crd-11   1/1           15s        18s
```

Nu we hebben bevestigd dat de taken zijn voltooid, gaan we controleren of het juiste aantal Istio CRDs is geïnstalleerd. U kunt controleren of alle 53 Istio CRDs zijn geïnstalleerd door de juiste opdracht uit te voeren voor uw omgeving. De opdracht moet het getal `53` retour neren.

Bash

```bash
kubectl get crds | grep 'istio.io' | wc -l
```

PowerShell

```powershell
(kubectl get crds | Select-String -Pattern 'istio.io').Count
```

Als u op dit punt hebt geklikt, betekent dit dat de Istio CRDs is geïnstalleerd. Ga nu verder met de volgende sectie om [de Istio-onderdelen op AKS te installeren](#install-the-istio-components-on-aks).

## <a name="install-the-istio-components-on-aks"></a>De Istio-onderdelen installeren op AKS

> [!IMPORTANT]
> Zorg ervoor dat u de stappen in deze sectie uitvoert vanuit de map op het hoogste niveau van de Istio-release die u hebt gedownload en geëxtraheerd.

[Grafana][grafana] en [Kiali][kiali] worden geïnstalleerd als onderdeel van onze Istio-installatie. Grafana biedt analyse-en bewakings dashboards en Kiali biedt een service-net-waarneembaar dash board. In de installatie zijn voor elk van deze onderdelen referenties vereist die als [geheim][kubernetes-secrets]moeten worden verschaft.

Voordat we de Istio-onderdelen kunnen installeren, moeten we de geheimen voor Grafana en Kiali maken. Maak deze geheimen door de juiste opdrachten uit te voeren voor uw omgeving.

### <a name="add-grafana-secret"></a>Grafana-geheim toevoegen

Vervang het `REPLACE_WITH_YOUR_SECURE_PASSWORD`-token door uw wacht woord en voer de volgende opdrachten uit:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
GRAFANA_USERNAME=$(echo -n "grafana" | base64)
GRAFANA_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$GRAFANA_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("grafana"))
$GRAFANA_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: grafana
  namespace: istio-system
  labels:
    app: grafana
type: Opaque
data:
  username: $GRAFANA_USERNAME
  passphrase: $GRAFANA_PASSPHRASE" | kubectl apply -f -
```

### <a name="add-kiali-secret"></a>Kiali-geheim toevoegen

Vervang het `REPLACE_WITH_YOUR_SECURE_PASSWORD`-token door uw wacht woord en voer de volgende opdrachten uit:

#### <a name="macos-linux"></a>MacOS, Linux

```bash
KIALI_USERNAME=$(echo -n "kiali" | base64)
KIALI_PASSPHRASE=$(echo -n "REPLACE_WITH_YOUR_SECURE_PASSWORD" | base64)

cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE
EOF
```

#### <a name="windows"></a>Windows

```powershell
$KIALI_USERNAME=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("kiali"))
$KIALI_PASSPHRASE=[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes("REPLACE_WITH_YOUR_SECURE_PASSWORD"))

"apiVersion: v1
kind: Secret
metadata:
  name: kiali
  namespace: istio-system
  labels:
    app: kiali
type: Opaque
data:
  username: $KIALI_USERNAME
  passphrase: $KIALI_PASSPHRASE" | kubectl apply -f -
```

### <a name="install-istio-components"></a>Istio-onderdelen installeren

Nu we de Grafana-en Kiali-geheimen in het AKS-cluster hebben gemaakt, is het tijd om de Istio-onderdelen te installeren. Gebruik helm en de grafiek `istio` om de Istio-onderdelen te installeren in de naam ruimte `istio-system` in uw AKS-cluster. Gebruik de juiste opdrachten voor uw omgeving.

> [!NOTE]
> We gebruiken de volgende opties als onderdeel van onze installatie:
> - `global.controlPlaneSecurityEnabled=true`-wederzijdse TLS ingeschakeld voor het besturings vlak
> - `mixer.adapters.useAdapterCRDs=false`: Hiermee worden de controles op de mixer adapter CRDs verwijderd, omdat deze worden afgeschaft en de prestaties worden verbeterd
> - `grafana.enabled=true`-Grafana-implementatie inschakelen voor analyse-en bewakings dashboards
> - `grafana.security.enabled=true`-verificatie inschakelen voor Grafana
> - `tracing.enabled=true`-de Jaeger-implementatie voor tracering inschakelen
> - `kiali.enabled=true`: de Kiali-implementatie inschakelen voor een dash board met Service-Mesh

Bash

```bash
helm install install/kubernetes/helm/istio --name istio --namespace istio-system \
  --set global.controlPlaneSecurityEnabled=true \
  --set mixer.adapters.useAdapterCRDs=false \
  --set grafana.enabled=true --set grafana.security.enabled=true \
  --set tracing.enabled=true \
  --set kiali.enabled=true
```

PowerShell

```powershell
helm install install/kubernetes/helm/istio --name istio --namespace istio-system `
  --set global.controlPlaneSecurityEnabled=true `
  --set mixer.adapters.useAdapterCRDs=false `
  --set grafana.enabled=true --set grafana.security.enabled=true `
  --set tracing.enabled=true `
  --set kiali.enabled=true
```

Het helm-diagram van @no__t 0 implementeert een groot aantal objecten. U kunt de lijst zien in de uitvoer van uw `helm install`-opdracht hierboven. De implementatie van de Istio-onderdelen kan vier tot vijf minuten duren, afhankelijk van uw cluster omgeving.

> [!NOTE]
> Alle Istio-peulen moeten worden gepland om te worden uitgevoerd op Linux-knoop punten. Als u naast Linux-knooppunt Pools in uw cluster ook Windows Server-knooppunt groepen hebt, moet u controleren of alle Istio van het hele micro maal zijn gepland voor uitvoering op Linux-knoop punten.

Op dit moment hebt u Istio geïmplementeerd op uw AKS-cluster. Om ervoor te zorgen dat we een geslaagde implementatie van Istio hebben, gaan we verder met de volgende sectie om [de Istio-installatie te valideren](#validate-the-istio-installation).

## <a name="validate-the-istio-installation"></a>De Istio-installatie valideren

Controleer eerst of de verwachte services zijn gemaakt. Gebruik de opdracht [kubectl Get SVC][kubectl-get] om de actieve services weer te geven. Voer een query uit op de naam ruimte `istio-system`, waarbij de Istio-en invoeg toepassings onderdelen zijn geïnstalleerd door de `istio` helm-grafiek:

```console
kubectl get svc --namespace istio-system --output wide
```

In de volgende voorbeeld uitvoer ziet u de services die nu moeten worden uitgevoerd:

- `istio-*`-services
- `jaeger-*`, `tracing` en `zipkin`-tracerings Services
- service voor metrische gegevens van de `prometheus`-invoeg toepassing
- `grafana` invoeg toepassings analyse en bewakings dashboard service
- dash board service voor de `kiali`-invoeg toepassing-service

Als de `istio-ingressgateway` een extern IP-adres van `<pending>` toont, wacht u enkele minuten totdat er een IP-adressen zijn toegewezen door Azure-netwerken.

```console
NAME                     TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                                                                                                                                      AGE       SELECTOR
grafana                  ClusterIP      10.0.81.182    <none>          3000/TCP                                                                                                                                     119s      app=grafana
istio-citadel            ClusterIP      10.0.96.33     <none>          8060/TCP,15014/TCP                                                                                                                           119s      istio=citadel
istio-galley             ClusterIP      10.0.237.158   <none>          443/TCP,15014/TCP,9901/TCP                                                                                                                   119s      istio=galley
istio-ingressgateway     LoadBalancer   10.0.154.12    20.188.211.19   15020:30603/TCP,80:31380/TCP,443:31390/TCP,31400:31400/TCP,15029:31198/TCP,15030:30610/TCP,15031:30937/TCP,15032:31344/TCP,15443:31499/TCP   119s      app=istio-ingressgateway,istio=ingressgateway,release=istio
istio-pilot              ClusterIP      10.0.178.56    <none>          15010/TCP,15011/TCP,8080/TCP,15014/TCP                                                                                                       119s      istio=pilot
istio-policy             ClusterIP      10.0.116.118   <none>          9091/TCP,15004/TCP,15014/TCP                                                                                                                 119s      istio-mixer-type=policy,istio=mixer
istio-sidecar-injector   ClusterIP      10.0.31.160    <none>          443/TCP                                                                                                                                      119s      istio=sidecar-injector
istio-telemetry          ClusterIP      10.0.187.246   <none>          9091/TCP,15004/TCP,15014/TCP,42422/TCP                                                                                                       119s      istio-mixer-type=telemetry,istio=mixer
jaeger-agent             ClusterIP      None           <none>          5775/UDP,6831/UDP,6832/UDP                                                                                                                   119s      app=jaeger
jaeger-collector         ClusterIP      10.0.116.63    <none>          14267/TCP,14268/TCP                                                                                                                          119s      app=jaeger
jaeger-query             ClusterIP      10.0.22.108    <none>          16686/TCP                                                                                                                                    119s      app=jaeger
kiali                    ClusterIP      10.0.142.50    <none>          20001/TCP                                                                                                                                    119s      app=kiali
prometheus               ClusterIP      10.0.138.134   <none>          9090/TCP                                                                                                                                     119s      app=prometheus
tracing                  ClusterIP      10.0.165.210   <none>          80/TCP                                                                                                                                       118s      app=jaeger
zipkin                   ClusterIP      10.0.126.211   <none>          9411/TCP                                                                                                                                     118s      app=jaeger
```

Controleer vervolgens of het vereiste peul is gemaakt. Gebruik de opdracht [kubectl Get peul][kubectl-get] en voer een query uit op de `istio-system`-naam ruimte:

```console
kubectl get pods --namespace istio-system
```

In de volgende voorbeeld uitvoer ziet u de peulen die worden uitgevoerd:

- het `istio-*`-peul
- de metrische gegevens voor de `prometheus-*`-invoeg toepassing pod
- de `grafana-*`-invoeg toepassing analyse en bewakings dashboard pod
- het Pod-dash board voor de `kiali`-invoeg toepassing

```console
NAME                                     READY     STATUS      RESTARTS   AGE
grafana-88779954d-nzpm7                  1/1       Running     0          6m26s
istio-citadel-7f699dc8c8-n7q8g           1/1       Running     0          6m26s
istio-galley-649bc8cd97-wfjzm            1/1       Running     0          6m26s
istio-ingressgateway-65dfbd566-42wkn     1/1       Running     0          6m26s
istio-init-crd-10-tmtw5                  0/1       Completed   0          20m38s
istio-init-crd-11-ql25l                  0/1       Completed   0          20m38s
istio-pilot-958dd8cc4-4ckf9              2/2       Running     0          6m26s
istio-policy-86b4b7cf9-zf7v7             2/2       Running     4          6m26s
istio-sidecar-injector-d48786c5c-pmrj9   1/1       Running     0          6m26s
istio-telemetry-7f6996fdcc-84w94         2/2       Running     3          6m26s
istio-tracing-79db5954f-h7hmz            1/1       Running     0          6m26s
kiali-5c4cdbb869-s28dv                   1/1       Running     0          6m26s
prometheus-67599bf55b-pgxd8              1/1       Running     0          6m26s
```

Er moeten twee `istio-init-crd-*` peul zijn met een `Completed`-status. Deze peulen zijn verantwoordelijk voor het uitvoeren van de taken die de CRDs in een eerdere stap hebben gemaakt. Alle andere peulen moeten de status `Running` weer geven. Als uw peul niet over deze status beschikt, moet u een paar minuten wachten totdat dit het geval is. Als een van de peulen een probleem rapporteert, gebruikt u de [kubectl pod][kubectl-describe] opdracht om de uitvoer en status te controleren.

## <a name="accessing-the-add-ons"></a>De invoeg toepassingen openen

Er zijn een aantal invoeg toepassingen geïnstalleerd Istio in onze installatie hierboven die aanvullende functionaliteit bieden. De gebruikers interfaces voor de invoeg toepassingen worden niet openbaar weer gegeven via een extern IP-adres. Als u toegang wilt krijgen tot de gebruikers interfaces van de invoeg toepassing, gebruikt u de opdracht [kubectl-poort-door sturen][kubectl-port-forward] . Met deze opdracht maakt u een beveiligde verbinding tussen uw client computer en de relevante pod in uw AKS-cluster.

We hebben een extra beveiligingslaag voor Grafana en Kiali toegevoegd door in dit artikel eerder referenties op te geven.

### <a name="grafana"></a>Grafana

De analyse-en bewakings dashboards voor Istio worden gegeven door [Grafana][grafana]. Stuur de lokale poort `3000` op de client computer door naar poort `3000` op de Pod met Grafana in uw AKS-cluster:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=grafana -o jsonpath='{.items[0].metadata.name}') 3000:3000
```

In de volgende voorbeeld uitvoer ziet u dat de poort voorwaarts is geconfigureerd voor Grafana:

```console
Forwarding from 127.0.0.1:3000 -> 3000
Forwarding from [::1]:3000 -> 3000
```

U kunt Grafana nu bereiken op de volgende URL op de client computer- [http://localhost:3000](http://localhost:3000). Vergeet niet om de referenties te gebruiken die u eerder hebt gemaakt via het Grafana-geheim wanneer u hierom wordt gevraagd.

### <a name="prometheus"></a>Prometheus

Metrische gegevens voor Istio worden gegeven door [Prometheus][prometheus]. Stuur de lokale poort `9090` op de client computer door naar poort `9090` op de Pod met Prometheus in uw AKS-cluster:

```console
kubectl -n istio-system port-forward $(kubectl -n istio-system get pod -l app=prometheus -o jsonpath='{.items[0].metadata.name}') 9090:9090
```

In de volgende voorbeeld uitvoer ziet u dat de poort voorwaarts is geconfigureerd voor Prometheus:

```console
Forwarding from 127.0.0.1:9090 -> 9090
Forwarding from [::1]:9090 -> 9090
```

U kunt de Prometheus-expressie browser nu bereiken via de volgende URL op de client computer- [http://localhost:9090](http://localhost:9090).

### <a name="jaeger"></a>Jaeger

Tracering binnen Istio wordt verschaft door [Jaeger][jaeger]. Stuur de lokale poort `16686` op de client computer door naar poort `16686` op de Pod met Jaeger in uw AKS-cluster:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=jaeger -o jsonpath='{.items[0].metadata.name}') 16686:16686
```

In de volgende voorbeeld uitvoer ziet u dat de poort voorwaarts is geconfigureerd voor Jaeger:

```console
Forwarding from 127.0.0.1:16686 -> 16686
Forwarding from [::1]:16686 -> 16686
```

U kunt nu de Jaeger tracering-gebruikers interface bereiken op de volgende URL op de client computer- [http://localhost:16686](http://localhost:16686).

### <a name="kiali"></a>Kiali

Een dash board met Service-net-behulp van [Kiali][kiali]. Stuur de lokale poort `20001` op de client computer door naar poort `20001` op de Pod met Kiali in uw AKS-cluster:

```console
kubectl port-forward -n istio-system $(kubectl get pod -n istio-system -l app=kiali -o jsonpath='{.items[0].metadata.name}') 20001:20001
```

In de volgende voorbeeld uitvoer ziet u dat de poort voorwaarts is geconfigureerd voor Kiali:

```console
Forwarding from 127.0.0.1:20001 -> 20001
Forwarding from [::1]:20001 -> 20001
```

U kunt nu het dash board voor Kiali Service-Mesh bereiken op de volgende URL op uw client computer- [http://localhost:20001/kiali/console/](http://localhost:20001/kiali/console/). Vergeet niet om de referenties te gebruiken die u eerder hebt gemaakt via het Kiali-geheim wanneer u hierom wordt gevraagd.

## <a name="uninstall-istio-from-aks"></a>Istio verwijderen uit AKS

> [!WARNING]
> Het verwijderen van Istio van een actief systeem kan leiden tot problemen met verkeer tussen uw services. Zorg ervoor dat u voor het systeem nog steeds goed werkt zonder Istio voordat u doorgaat.

### <a name="remove-istio-components-and-namespace"></a>Istio-onderdelen en naam ruimte verwijderen

Gebruik de volgende opdrachten om Istio te verwijderen uit uw AKS-cluster. Met de `helm delete`-opdrachten worden de `istio`-en `istio-init`-grafieken verwijderd en wordt de `istio-system`-naam ruimte verwijderd met de opdracht `kubectl delete ns`.

```azurecli
helm delete --purge istio
helm delete --purge istio-init
kubectl delete ns istio-system
```

### <a name="remove-istio-crds"></a>Istio CRDs verwijderen

Met de bovenstaande opdrachten worden alle Istio-onderdelen en-naam ruimten verwijderd, maar er zijn nog steeds de Istio-CRDs. Als u de CRDs wilt verwijderen, kunt u een van de volgende benaderingen gebruiken.

Aanpak #1: bij deze opdracht wordt ervan uitgegaan dat u deze stap uitvoert vanuit de map op het hoogste niveau van de gedownloade en uitgepakte versie van Istio die u hebt gebruikt voor het installeren van Istio met.

```azure-cli
kubectl delete -f install/kubernetes/helm/istio-init/files
```

Aanpak #2: gebruik een van deze opdrachten als u niet langer toegang hebt tot de gedownloade en uitgepakte versie van Istio die u hebt gebruikt voor het installeren van Istio met. Het duurt wat langer om deze opdracht te volt ooien. Dit kan een paar minuten duren.

Bash
```bash
kubectl get crds -o name | grep 'istio.io' | xargs -n1 kubectl delete
```

PowerShell
```powershell
kubectl get crds -o name | Select-String -Pattern 'istio.io' |% { kubectl delete $_ }
```

## <a name="next-steps"></a>Volgende stappen

In de volgende documentatie wordt beschreven hoe u Istio kunt gebruiken om intelligente route ring te bieden voor het implementeren van een distributie release:

> [!div class="nextstepaction"]
> [AKS Istio intelligent routerings scenario][istio-scenario-routing]

Als u meer wilt weten over de installatie-en configuratie opties voor Istio, raadpleegt u de volgende officiële Istio-artikelen:

- [Installatie handleiding voor Istio-helm][istio-install-helm]
- [Installatie opties voor Istio-helm][istio-install-helm-options]

U kunt ook aanvullende scenario's volgen met behulp van het [voor beeld van de Istio Bookinfo-toepassing][istio-bookinfo-example].

Raadpleeg de volgende Azure Monitor documentatie voor meer informatie over het bewaken van uw AKS-toepassing met behulp van Application Insights en Istio:
- [Controle van toepassings bewaking op nul voor Kubernetes-gehoste toepassingen][app-insights]

<!-- LINKS - external -->
[istio]: https://istio.io
[helm]: https://helm.sh

[istio-docs-concepts]: https://istio.io/docs/concepts/what-is-istio/
[istio-github]: https://github.com/istio/istio
[istio-github-releases]: https://github.com/istio/istio/releases
[istio-release-notes]: https://istio.io/news/
[istio-install-download]: https://istio.io/docs/setup/kubernetes/download-release/
[istio-install-helm]: https://istio.io/docs/setup/kubernetes/install/helm/
[istio-install-helm-options]: https://istio.io/docs/reference/config/installation-options/
[istio-bookinfo-example]: https://istio.io/docs/examples/bookinfo/
[install-wsl]: https://docs.microsoft.com/windows/wsl/install-win10

[kubernetes-crd]: https://kubernetes.io/docs/concepts/extend-kubernetes/api-extension/custom-resources/#customresourcedefinitions
[kubernetes-jobs]: https://kubernetes.io/docs/concepts/workloads/controllers/jobs-run-to-completion/
[kubernetes-secrets]: https://kubernetes.io/docs/concepts/configuration/secret/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-port-forward]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#port-forward

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/
[jaeger]: https://www.jaegertracing.io/
[kiali]: https://www.kiali.io/

[app-insights]: https://docs.microsoft.com/azure/azure-monitor/app/kubernetes

<!-- LINKS - internal -->
[aks-quickstart]: ./kubernetes-walkthrough.md
[istio-scenario-routing]: ./istio-scenario-routing.md
[helm-install]: ./kubernetes-helm.md

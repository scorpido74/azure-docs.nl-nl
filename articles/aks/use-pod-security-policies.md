---
title: Pod-beveiligings beleid gebruiken in azure Kubernetes service (AKS)
description: Meer informatie over het beheren van pod-toelatingen met behulp van PodSecurityPolicy in azure Kubernetes service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/17/2019
ms.author: mlearned
ms.openlocfilehash: 131a71e27bba1c37b6d50b718b8eac788109a59f
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/25/2019
ms.locfileid: "72933771"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Voor beeld: uw cluster beveiligen met behulp van pod-beveiligings beleid in azure Kubernetes service (AKS)

Als u de beveiliging van uw AKS-cluster wilt verbeteren, kunt u het aantal peulen dat kan worden gepland, beperken. De meeste resources die u niet toestaat, kunnen niet worden uitgevoerd in het AKS-cluster. U definieert deze toegang met behulp van pod-beveiligings beleid. Dit artikel laat u zien hoe u pod-beveiligings beleid kunt gebruiken om de implementatie van een van de peulen in AKS te beperken.

> [!IMPORTANT]
> De preview-functies van AKS zijn self-service opt-in. Previews worden ' as-is ' en ' as available ' gegeven en zijn uitgesloten van de service level agreements en beperkte garantie. AKS-previews worden gedeeltelijk gedekt door klant ondersteuning, op basis van de beste inspanningen. Daarom zijn deze functies niet bedoeld voor productie gebruik. Raadpleeg de volgende artikelen met technische ondersteuning voor meer informatie.
>
> * [AKS-ondersteunings beleid][aks-support-policies]
> * [Veelgestelde vragen over ondersteuning voor Azure][aks-faq]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u beschikt over een bestaand AKS-cluster. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS Quick Start [met behulp van de Azure cli][aks-quickstart-cli] of [met behulp van de Azure Portal][aks-quickstart-portal].

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer  `az --version` uit om de versie te bekijken. Als u wilt installeren of upgraden, raadpleegt u [Azure cli installeren][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>AKS-preview CLI-extensie installeren

Als u pod-beveiligings beleid wilt gebruiken, hebt u de *AKS-preview cli-* extensie versie 0.4.1 of hoger nodig. Installeer de Azure CLI *-extensie AKS-preview* met behulp van de opdracht [AZ extension add][az-extension-add] en controleer vervolgens of er beschik bare updates zijn met behulp van de opdracht [AZ extension update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Pod-functie provider voor beveiligings beleid registreren

Als u een AKS-cluster wilt maken of bijwerken om pod-beveiligings beleid te gebruiken, moet u eerst een functie vlag voor uw abonnement inschakelen. Als u de functie vlag *PodSecurityPolicyPreview* wilt registreren, gebruikt u de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

> [!CAUTION]
> Wanneer u een functie op een abonnement registreert, kunt u de registratie van die functie op dit moment niet ongedaan maken. Nadat u enkele preview-functies hebt ingeschakeld, kunnen standaard waarden worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen preview-functies in voor productie abonnementen. Gebruik een afzonderlijk abonnement om Preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Het duurt enkele minuten voordat de status is *geregistreerd*. U kunt de registratie status controleren met de opdracht [AZ Feature List][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Overzicht van pod-beveiligings beleid

In een Kubernetes-cluster wordt een toegangs controller gebruikt om aanvragen voor de API-server te onderscheppen wanneer een bron moet worden gemaakt. De toegangs controller kan vervolgens de resource aanvraag *valideren* op basis van een set regels of de resource *mutate* om de implementatie parameters te wijzigen.

*PodSecurityPolicy* is een toegangs controller die een pod-specificatie valideert die voldoet aan uw gedefinieerde vereisten. Deze vereisten kunnen het gebruik van geprivilegieerde containers, toegang tot bepaalde typen opslag beperken of de gebruiker of groep die de container kan uitvoeren als. Wanneer u een resource probeert te implementeren waarbij de pod-specificaties niet voldoen aan de vereisten die worden beschreven in het beveiligings beleid van Pod, wordt de aanvraag geweigerd. Deze mogelijkheid om te bepalen wat de peulen kunnen worden gepland in het AKS-cluster, voor komt dat er mogelijke beveiligings problemen of escalaties van bevoegdheden.

Wanneer u pod-beveiligings beleid inschakelt in een AKS-cluster, worden er een aantal standaard beleids regels toegepast. Deze standaard beleidsregels bieden een out-of-the-box-ervaring om te definiëren wat het peul kan worden gepland. Cluster gebruikers kunnen echter problemen ondervinden bij het implementeren van het gehele beleid totdat u uw eigen beleids regels definieert. De aanbevolen aanpak is:

* Een AKS-cluster maken
* Uw eigen pod-beveiligings beleid definiëren
* De functie pod-beveiligings beleid inschakelen

Als u wilt weer geven hoe de standaard beleids regels pod-implementaties beperken, wordt in dit artikel eerst de functie pod-beveiligings beleid ingeschakeld. vervolgens maakt u een aangepast beleid.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Pod-beveiligings beleid inschakelen op een AKS-cluster

U kunt pod-beveiligings beleid in-of uitschakelen met behulp van de opdracht [AZ AKS update][az-aks-update] . In het volgende voor beeld wordt pod-beveiligings beleid ingeschakeld op de cluster naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*.

> [!NOTE]
> Schakel voor gebruik in de praktijk het beveiligings beleid pod pas in als u uw eigen aangepaste beleids regels hebt gedefinieerd. In dit artikel schakelt u pod-beveiligings beleid als eerste stap in om te zien hoe de standaard beleidsregels de pod-implementaties beperken.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standaard AKS-beleid

Wanneer u pod-beveiligings beleid inschakelt, maakt AKS één standaard beleid met de naam *privileged*. Bewerk of verwijder het standaard beleid niet. Maak in plaats daarvan uw eigen beleid voor het definiëren van de instellingen die u wilt beheren. Laten we eerst kijken naar wat deze standaard beleidsregels zijn, hoe ze pod-implementaties beïnvloeden.

Als u de beschik bare beleids regels wilt weer geven, gebruikt u de opdracht [kubectl Get PSP][kubectl-get] , zoals wordt weer gegeven in het volgende voor beeld

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Het beveiligings beleid *privileged* pod wordt toegepast op elke geverifieerde gebruiker in het AKS-cluster. Deze toewijzing wordt beheerd door ClusterRoles en ClusterRoleBindings. Gebruik de opdracht [kubectl Get clusterrolebindings][kubectl-get] en zoek naar de *standaard: Privileged:* binding:

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

Zoals wordt weer gegeven in de volgende verkorte uitvoer, wordt de *PSP: beperkte* ClusterRole toegewezen aan elk *systeem: geverifieerde* gebruikers. Deze mogelijkheid biedt een basis niveau van beperkingen zonder dat uw eigen beleid wordt gedefinieerd.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:authenticated
```

Het is belang rijk om te begrijpen hoe deze standaard beleidsregels communiceren met gebruikers aanvragen voor het plannen van Peul voordat u begint met het maken van uw eigen pod-beveiligings beleid. In de volgende paar secties gaan we een planning maken om deze standaard beleidsregels in actie te zien.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Een test gebruiker maken in een AKS-cluster

Wanneer u de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] gebruikt, worden de *beheerders* referenties voor het AKS-cluster standaard toegevoegd aan uw `kubectl` configuratie. De gebruiker met beheerders rechten negeert de afdwinging van pod-beveiligings beleid. Als u Azure Active Directory-integratie gebruikt voor uw AKS-clusters, kunt u zich aanmelden met de referenties van een gebruiker die geen beheerder is om het afdwingen van beleid in actie te zien. In dit artikel maken we een test gebruikers account in het AKS-cluster dat u kunt gebruiken.

Maak een voor beeld van een naam ruimte met de naam *PSP-AKS* voor test resources met behulp van de [kubectl maken naam ruimte][kubectl-create] opdracht. Maak vervolgens een service account met de naam niet *-beheerder-gebruiker* met behulp van de kubectl-opdracht [serviceaccount maken][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Maak vervolgens een RoleBinding voor de niet *-beheerder* om basis acties uit te voeren in de naam ruimte met behulp van de [kubectl Create RoleBinding][kubectl-create] opdracht:

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Alias opdrachten maken voor beheerders en gebruikers die geen beheerder zijn

Als u het verschil tussen de normale gebruiker met beheerders rechten wilt markeren wanneer u `kubectl` gebruikt en de niet-beheerders gebruiker die in de vorige stappen is gemaakt, maakt u twee opdracht regel aliassen:

* De alias voor de **kubectl-beheerder** is de gewone beheerder en ligt binnen het bereik van de naam ruimte *PSP-AKS* .
* De **kubectl-nonadminuser** -alias is voor de niet *-beheerder-gebruiker* die in de vorige stap is gemaakt en is afgestemd op de *PSP-AKS-* naam ruimte.

Maak deze twee aliassen, zoals wordt weer gegeven in de volgende opdrachten:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Het maken van een privileged pod testen

We gaan eerst testen wat er gebeurt wanneer u een pod plant met de beveiligings context van `privileged: true`. Met deze beveiligings context worden de bevoegdheden van de pod geëscaleerd. In de vorige sectie waarin het standaard beleid voor AKS-pod wordt weer gegeven, moet het *beperkte* beleid deze aanvraag weigeren.

Maak een bestand met de naam `nginx-privileged.yaml` en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

De pod kan niet worden gepland, zoals wordt weer gegeven in de volgende voorbeeld uitvoer:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

Het Pod is niet bereikbaar voor de plannings fase, dus er zijn geen resources om te verwijderen voordat u verdergaat.

## <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een niet-gemachtigde pod testen

In het vorige voor beeld heeft de pod-specificatie geautoriseerde escalatie aangevraagd. Deze aanvraag is geweigerd door het standaard beleid voor *beperkte* pod-beveiliging, waardoor de pod niet kan worden gepland. We gaan nu proberen om dezelfde NGINX-pod uit te voeren zonder de escalatie aanvraag van de bevoegdheid.

Maak een bestand met de naam `nginx-unprivileged.yaml` en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De Kubernetes-planner accepteert de pod-aanvraag. Als u echter de status van de Pod met behulp van `kubectl get pods`bekijkt, is er een fout:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Gebruik de [kubectl pod opdracht beschrijven][kubectl-describe] om de gebeurtenissen voor de Pod te bekijken. In het volgende verkorte voor beeld ziet u dat de container en de installatie kopie hoofd machtigingen vereisen, zelfs als we deze niet hebben aangevraagd:

```console
$ kubectl-nonadminuser describe pod nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                     From                               Message
  ----     ------     ----                    ----                               -------
  Normal   Scheduled  7m14s                   default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged to aks-agentpool-34777077-0
  Warning  Failed     5m2s (x12 over 7m13s)   kubelet, aks-agentpool-34777077-0  Error: container has runAsNonRoot and image will run as root
  Normal   Pulled     2m10s (x25 over 7m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
```

Hoewel we geen privileged-toegang hebben aangevraagd, moet de container installatie kopie voor NGINX een binding maken voor poort *80*. De *hoofd* gebruiker is vereist om de poorten *1024* en hieronder te koppelen. Wanneer de Pod wordt gestart, wordt deze aanvraag door het *beperkte* pod-beveiligings beleid geweigerd.

In dit voor beeld ziet u dat het standaard beleid voor pod-beveiliging dat door AKS wordt gemaakt, actief is en de acties beperkt die een gebruiker kan uitvoeren. Het is belang rijk om inzicht te krijgen in het gedrag van deze standaard beleidsregels, omdat u mogelijk niet verwacht dat een eenvoudige NGINX-pod wordt geweigerd.

Voordat u verdergaat met de volgende stap, verwijdert u deze test pod met de opdracht [kubectl delete pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Het maken van een pod met een specifieke gebruikers context testen

In het vorige voor beeld heeft de container installatie kopie automatisch geprobeerd de root te gebruiken om NGINX te binden aan poort 80. Deze aanvraag is geweigerd door het standaard beleid voor *beperkte* pod-beveiliging, waardoor de pod niet kan worden gestart. We gaan nu proberen om dezelfde NGINX-pod uit te voeren met een specifieke gebruikers context, zoals `runAsUser: 2000`.

Maak een bestand met de naam `nginx-unprivileged-nonroot.yaml` en plak het volgende YAML-manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Maak de Pod met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

De Kubernetes-planner accepteert de pod-aanvraag. Als u echter de status van de Pod met `kubectl get pods`bekijkt, is er een andere fout dan in het vorige voor beeld:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Gebruik de [kubectl pod opdracht beschrijven][kubectl-describe] om de gebeurtenissen voor de Pod te bekijken. In het volgende verkorte voor beeld ziet u de pod-gebeurtenissen:

```console
$ kubectl-nonadminuser describe pods nginx-unprivileged

Name:               nginx-unprivileged
Namespace:          psp-aks
Priority:           0
PriorityClassName:  <none>
Node:               aks-agentpool-34777077-0/10.240.0.4
Start Time:         Thu, 28 Mar 2019 22:05:04 +0000
[...]
Events:
  Type     Reason     Age                   From                               Message
  ----     ------     ----                  ----                               -------
  Normal   Scheduled  2m14s                 default-scheduler                  Successfully assigned psp-aks/nginx-unprivileged-nonroot to aks-agentpool-34777077-0
  Normal   Pulled     118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Container image "nginx:1.14.2" already present on machine
  Normal   Created    118s (x3 over 2m13s)  kubelet, aks-agentpool-34777077-0  Created container
  Normal   Started    118s (x3 over 2m12s)  kubelet, aks-agentpool-34777077-0  Started container
  Warning  BackOff    105s (x5 over 2m11s)  kubelet, aks-agentpool-34777077-0  Back-off restarting failed container
```

De gebeurtenissen geven aan dat de container is gemaakt en gestart. Er is niets direct duidelijk wat de oorzaak is van de pod in een mislukte status. Laten we eens kijken naar de pod-logboeken met behulp van de [kubectl-logboeken][kubectl-logs] opdracht:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

De volgende voorbeeld logboek uitvoer geeft een indicatie dat binnen de NGINX-configuratie zelf een machtigings fout optreedt wanneer de service probeert te starten. Deze fout is opnieuw het gevolg van een binding met poort 80. Hoewel in de pod-specificatie een reguliere gebruikers account is gedefinieerd, is dit gebruikers account niet voldoende in het besturingssysteem niveau voor de NGINX-service om te starten en te binden aan de beperkte poort.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Het is ook belang rijk om het gedrag van het standaard beveiligings beleid voor pod te begrijpen. Deze fout is iets moeilijker op te volgen en u wordt mogelijk niet verwacht dat een basis NGINX pod wordt geweigerd.

Voordat u verdergaat met de volgende stap, verwijdert u deze test pod met de opdracht [kubectl delete pod][kubectl-delete] :

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Een aangepast pod-beveiligings beleid maken

Nu u het gedrag van het standaard beveiligings beleid voor pod hebt gezien, bieden we een manier om de niet *-beheerder* in staat te stellen om heel goed te plannen.

We gaan een beleid maken voor het afwijzen van het Peul waarvoor privileged Access moet worden aangevraagd. Andere opties, zoals *runAsUser* of toegestane *volumes*, worden niet expliciet beperkt. Dit type beleid weigert een aanvraag voor bevoegde toegang, maar anders kan het cluster de aangevraagde peul uitvoeren.

Maak een bestand met de naam `psp-deny-privileged.yaml` en plak het volgende YAML-manifest:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Maak het beleid met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Als u de beschik bare beleids regels wilt weer geven, gebruikt u de opdracht [kubectl Get PSP][kubectl-get] , zoals in het volgende voor beeld wordt weer gegeven. Vergelijk het beleid voor *PSP-deny-privileges* met het standaard *beperkte* beleid dat in de voor gaande voor beelden is afgedwongen om een pod te maken. Alleen het gebruik van *PRIV* -escalatie wordt door uw beleid geweigerd. Er zijn geen beperkingen voor de gebruiker of groep voor het beleid voor *PSP-deny-privileged* .

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Gebruikers account toestaan het aangepaste pod-beveiligings beleid te gebruiken

In de vorige stap hebt u een pod-beveiligings beleid gemaakt waarmee u de peuling kunt afwijzen waarvoor privileged Access moet worden aangevraagd. Als u wilt toestaan dat het beleid wordt gebruikt, maakt u een *rol* of een *ClusterRole*. Vervolgens koppelt u een van deze rollen met behulp van een *RoleBinding* of *ClusterRoleBinding*.

Voor dit voor beeld maakt u een ClusterRole waarmee u het beleid *PSP-deny-privileged* kunt *gebruiken* dat u in de vorige stap hebt gemaakt. Maak een bestand met de naam `psp-deny-privileged-clusterrole.yaml` en plak het volgende YAML-manifest:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Maak de ClusterRole met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Maak nu een ClusterRoleBinding om de ClusterRole te gebruiken die u in de vorige stap hebt gemaakt. Maak een bestand met de naam `psp-deny-privileged-clusterrolebinding.yaml` en plak het volgende YAML-manifest:

```yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Maak een ClusterRoleBinding met de opdracht [kubectl apply][kubectl-apply] en geef de naam van uw yaml-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> In de eerste stap van dit artikel is de functie pod-beveiligings beleid ingeschakeld op het AKS-cluster. De aanbevolen procedure is om alleen de functie pod-beveiligings beleid in te scha kelen nadat u uw eigen beleid hebt gedefinieerd. Dit is de fase waarin u de functie pod-beveiligings beleid inschakelt. Er zijn een of meer aangepaste beleids regels gedefinieerd en er zijn gebruikers accounts aan die beleids regels gekoppeld. Nu kunt u de functie pod-beveiligings beleid veilig inschakelen en de problemen die worden veroorzaakt door het standaard beleid minimaliseren.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Het maken van een niet-gemachtigd pod opnieuw testen

Als uw aangepaste pod-beveiligings beleid is toegepast en een binding voor het gebruikers account voor het gebruik van het beleid, probeert u opnieuw een niet-gemachtigd pod te maken. Gebruik hetzelfde `nginx-privileged.yaml`-manifest om de Pod te maken met behulp van de opdracht [kubectl apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De Pod is gepland. Wanneer u de status van de pod controleert met behulp van de [kubectl Get peul][kubectl-get] opdracht, wordt de pod *uitgevoerd*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

In dit voor beeld ziet u hoe u een aangepast pod-beveiligings beleid kunt maken om de toegang tot het AKS-cluster te definiëren voor verschillende gebruikers of groepen. De standaard AKS-beleids regels bieden een nauw keurig beheer van wat er kan worden uitgevoerd. u kunt dus uw eigen aangepaste beleids regels maken om de benodigde beperkingen op te geven.

Verwijder de NGINX niet-gemachtigde pod met de opdracht [kubectl delete][kubectl-delete] en geef de naam van uw yaml-manifest op:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u pod-beveiligings beleid wilt uitschakelen, gebruikt u de opdracht [AZ AKS update][az-aks-update] opnieuw. In het volgende voor beeld wordt het Pod-beveiligings beleid uitgeschakeld voor de cluster naam *myAKSCluster* in de resource groep met de naam *myResourceGroup*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Verwijder vervolgens de ClusterRole en ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Verwijder het netwerk beleid met behulp van de opdracht [kubectl delete][kubectl-delete] en geef de naam van het yaml-manifest op:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Verwijder ten slotte de *PSP-AKS-* naam ruimte:

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel wordt uitgelegd hoe u een pod-beveiligings beleid kunt maken om te voor komen dat privileged Access wordt gebruikt. Er zijn veel functies die een beleid kan afdwingen, zoals het type volume of de RunAs-gebruiker. Zie voor meer informatie over de beschik bare opties de [Kubernetes pod Security Policy Reference docs][kubernetes-policy-reference].

Zie voor meer informatie over het beperken van pod-netwerk verkeer [beveiligde verkeer tussen peulen met netwerk beleid in AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

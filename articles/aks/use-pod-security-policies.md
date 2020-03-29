---
title: Pod-beveiligingsbeleid gebruiken in Azure Kubernetes Service (AKS)
description: Meer informatie over het beheren van pod-opnames met PodSecurityPolicy in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 74177136a7a61186ab1d273b57dbfce550a18ecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77914531"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Preview - Uw cluster beveiligen met behulp van podbeveiligingsbeleid in Azure Kubernetes Service (AKS)

Als u de beveiliging van uw AKS-cluster wilt verbeteren, u beperken welke pods kunnen worden gepland. Pods die resources aanvragen die u niet toestaat, kunnen niet worden uitgevoerd in het AKS-cluster. U definieert deze toegang met behulp van podbeveiligingsbeleid. In dit artikel ziet u hoe u podbeveiligingsbeleid gebruikt om de implementatie van pods in AKS te beperken.

> [!IMPORTANT]
> AKS preview-functies zijn self-service opt-in. Previews worden geleverd "as-is" en "as available" en zijn uitgesloten van de service level agreements en beperkte garantie. AKS Previews worden gedeeltelijk gedekt door de klantenservice op basis van de beste inspanning. Als zodanig zijn deze functies niet bedoeld voor productiegebruik. Zie voor meer informatie de volgende ondersteuningsartikelen:
>
> * [AKS-ondersteuningsbeleid][aks-support-policies]
> * [Veelgestelde vragen over Azure Support][aks-faq]

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt. Als u een AKS-cluster nodig hebt, raadpleegt u de AKS snelstart [met de Azure CLI][aks-quickstart-cli] of met behulp van de [Azure-portal][aks-quickstart-portal].

U moet de Azure CLI-versie 2.0.61 of hoger installeren en configureren. Voer  `az --version` uit om de versie te bekijken. Als u de Azure CLI wilt installeren of upgraden, raadpleegt u  [Azure CLI installeren][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Aks-preview CLI-extensie installeren

Als u het beveiligingsbeleid voor de pod wilt gebruiken, hebt u de CLI-extensieversie 0.4.1 of hoger *van aks-preview* nodig. Installeer de *aks-preview Azure CLI-extensie* met de opdracht [Toevoegen van AZ-extensie][az-extension-add] en controleer vervolgens op beschikbare updates met de opdracht [update van AZ-extensie:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Functieprovider voor pod-beveiligingsbeleid registreren

Als u een AKS-cluster wilt maken of bijwerken om het beveiligingsbeleid van de pod te gebruiken, schakelt u eerst een functievlag in op uw abonnement. Als u de *functievlag van PodSecurityPolicyPreview* wilt registreren, gebruikt u de opdracht [az-functieregister][az-feature-register] zoals weergegeven in het volgende voorbeeld:

> [!CAUTION]
> Wanneer u een functie registreert voor een abonnement, u deze functie momenteel niet ongedaan maken. Nadat u een aantal voorbeeldfuncties hebt ingeschakeld, kunnen standaardinstellingen worden gebruikt voor alle AKS-clusters die vervolgens in het abonnement zijn gemaakt. Schakel geen voorbeeldfuncties in op productieabonnementen. Gebruik een apart abonnement om preview-functies te testen en feedback te verzamelen.

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Het duurt een paar minuten voordat de status *geregistreerd wordt weergegeven.* U de registratiestatus controleren met de opdracht [AZ Feature List:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de *microsoft.ContainerService-resourceprovider* met de opdracht [AZ-providerregister:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Overzicht van podbeveiligingsbeleid

In een Kubernetes-cluster wordt een toegangscontroller gebruikt om aanvragen naar de API-server te onderscheppen wanneer een bron moet worden gemaakt. De toegangsverantwoordelijke kan vervolgens de resourceaanvraag *valideren* aan de hand van een set regels of de resource *muteren* om implementatieparameters te wijzigen.

*PodSecurityPolicy* is een toegangscontroller die een podspecificatie valideert die voldoet aan uw gedefinieerde vereisten. Deze vereisten kunnen het gebruik van bevoorrechte containers, de toegang tot bepaalde soorten opslag beperken of de gebruiker of groep die de container kan uitvoeren als. Wanneer u een resource probeert te implementeren waarbij de podspecificaties niet voldoen aan de vereisten die zijn beschreven in het beveiligingsbeleid van de pod, wordt de aanvraag geweigerd. Deze mogelijkheid om te bepalen welke pods kunnen worden gepland in het AKS-cluster voorkomt een aantal mogelijke beveiligingsproblemen of privilege-escalaties.

Wanneer u het podbeveiligingsbeleid inschakelt in een AKS-cluster, worden enkele standaardbeleidsregels toegepast. Deze standaardbeleidsregels bieden een kant-en-klare ervaring om te definiëren welke pods kunnen worden gepland. Clustergebruikers kunnen echter problemen ondervinden bij het implementeren van pods totdat u uw eigen beleid definieert. De aanbevolen aanpak is:

* Een AKS-cluster maken
* Uw eigen podbeveiligingsbeleid definiëren
* De functie beveiligingsbeleid van de pod inschakelen

Als u wilt laten zien hoe de standaardbeleidsimplementaties van de pod worden beperkt, schakelen we in dit artikel eerst de functie podbeveiligingsbeleid in en maken we vervolgens een aangepast beleid.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Podbeveiligingsbeleid inschakelen voor een AKS-cluster

U het podbeveiligingsbeleid in- of uitschakelen met de opdracht [AZ Aks-update.][az-aks-update] In het volgende voorbeeld wordt podbeveiligingsbeleid ingeschakeld voor de clusternaam *myAKSCluster* in de brongroep *myResourceGroup*.

> [!NOTE]
> Schakel het beveiligingsbeleid van de pod pas in als u uw eigen aangepaste beleid hebt gedefinieerd. In dit artikel schakelt u het podbeveiligingsbeleid in als eerste stap om te zien hoe het standaardbeleid pod-implementaties beperkt.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Standaard AKS-beleid

Wanneer u het podbeveiligingsbeleid inschakelt, maakt AKS één standaardbeleid met de naam *bevoorrecht*. Het standaardbeleid niet bewerken of verwijderen. Maak in plaats daarvan uw eigen beleid dat de instellingen definieert die u wilt beheren. Laten we eerst eens kijken naar wat deze standaardbeleidsregels zijn hoe deze van invloed zijn op pod-implementaties.

Als u het beschikbare beleid wilt bekijken, gebruikt u de [kubectl get psp-opdracht,][kubectl-get] zoals in het volgende voorbeeld wordt weergegeven

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Het *beveiligingsbeleid voor de geprivilegieerde* pod wordt toegepast op elke geverifieerde gebruiker in het AKS-cluster. Deze toewijzing wordt beheerd door clusterrollen en clusterrolebindingen. Gebruik de opdracht [clusterrolebindingen voor kubectl-groepen][kubectl-get] en zoek naar de *standaard:geprivilegieerd:* binding:

```console
kubectl get clusterrolebindings default:privileged -o yaml
```

Zoals in de volgende gecondenseerde uitvoer wordt weergegeven, wordt de *psp:restricted* ClusterRole toegewezen aan elk *systeem:geverifieerde* gebruikers. Deze mogelijkheid biedt een basisniveau van beperkingen zonder dat uw eigen beleid wordt gedefinieerd.

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

Het is belangrijk om te begrijpen hoe deze standaardbeleidsregels werken met gebruikersverzoeken om pods te plannen voordat u begint met het maken van uw eigen podbeveiligingsbeleid. Laten we in de volgende secties enkele pods plannen om deze standaardbeleidsregels in actie te zien.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Een testgebruiker maken in een AKS-cluster

Wanneer u de opdracht [az aks-get-credentials][az-aks-get-credentials] gebruikt, worden standaard de *beheerdersreferenties* voor het AKS-cluster aan uw `kubectl` config toegevoegd. De beheerder gebruiker omzeilt de handhaving van pod beveiligingsbeleid. Als u Azure Active Directory-integratie gebruikt voor uw AKS-clusters, u zich aanmelden met de referenties van een niet-beheerdersgebruiker om de handhaving van het beleid in actie te zien. Laten we in dit artikel een testgebruikersaccount maken in het AKS-cluster dat u gebruiken.

Maak een voorbeeldnaamruimte met de *naampsp-aks* voor testbronnen met de opdracht [kubectl create namespace.][kubectl-create] Maak vervolgens een serviceaccount met de naam *nonadmin-user* met de opdracht [kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Maak vervolgens een rolebinding voor de *niet-beheerdersgebruiker* om basisacties in de naamruimte uit te voeren met de opdracht [kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Aliasopdrachten maken voor beheerders- en niet-beheerdersgebruikers

Als u het verschil wilt markeren `kubectl` tussen de gewone beheerdergebruiker bij gebruik en de niet-beheerdersgebruiker die in de vorige stappen is gemaakt, maakt u twee opdrachtregelaliassen:

* De **kubectl-admin** alias is voor de reguliere admin gebruiker, en is scoped naar de *psp-aks* naamruimte.
* De **alias kubectl-nonadminuser** is bedoeld voor de *niet-admin-gebruiker* die in de vorige stap is gemaakt en is geschikt voor de *naamruimte van psp-aks.*

Maak deze twee aliassen zoals weergegeven in de volgende opdrachten:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>De creatie van een geprivilegieerde pod testen

Laten we eerst testen wat er gebeurt als u `privileged: true`een pod plant met de beveiligingscontext van . Deze beveiligingscontext escaleert de bevoegdheden van de pod. In het vorige gedeelte waarin het standaard-AKS-podbeveiligingsbeleid werd weergegeven, moet het *beperkte* beleid dit verzoek weigeren.

Maak een `nginx-privileged.yaml` bestand met de naam en plak het volgende YAML-manifest:

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

Maak de pod met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

De pod wordt niet gepland, zoals in de volgende voorbeelduitvoer wordt weergegeven:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: [spec.containers[0].securityContext.privileged: Invalid value: true: Privileged containers are not allowed]
```

De pod bereikt de planningsfase niet, dus er zijn geen resources die u verwijderen voordat u verder gaat.

## <a name="test-creation-of-an-unprivileged-pod"></a>Het maken van een onbevoegde pod testen

In het vorige voorbeeld werd in de podspecificatie om geprivilegieerde escalatie gevraagd. Deze aanvraag wordt geweigerd door het *standaardbeveiligingsbeleid voor beperkte* pod, zodat de pod niet wordt gepland. Laten we nu proberen dat dezelfde NGINX-pod uit te voeren zonder het verzoek om escalatie van bevoegdheden.

Maak een `nginx-unprivileged.yaml` bestand met de naam en plak het volgende YAML-manifest:

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

Maak de pod met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De Kubernetes scheduler accepteert de podaanvraag. Als u echter de status van `kubectl get pods`de pod bekijkt met behulp van, is er een fout:

```console
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS                       RESTARTS   AGE
nginx-unprivileged   0/1     CreateContainerConfigError   0          26s
```

Gebruik de opdracht [kubectl describe pod][kubectl-describe] om de gebeurtenissen voor de pod te bekijken. In het volgende gecondenseerde voorbeeld ziet u dat de container en de afbeelding rootmachtigingen vereisen, ook al hebben we deze niet aangevraagd:

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

Hoewel we geen bevoorrechte toegang hebben aangevraagd, moet de containerafbeelding voor NGINX een binding maken voor poort *80.* Om poorten *1024* en lager te binden, is de *hoofdgebruiker* vereist. Wanneer de pod probeert te starten, wordt dit verzoek in het *beperkte* podbeveiligingsbeleid ontkend.

In dit voorbeeld wordt weergegeven dat het standaardbeveiligingsbeleid van de pod dat door AKS is gemaakt, van kracht is en de acties beperkt die een gebruiker kan uitvoeren. Het is belangrijk om het gedrag van deze standaardbeleidsregels te begrijpen, omdat u mogelijk niet verwacht dat een eenvoudige NGINX-pod wordt geweigerd.

Verwijder deze testpod met de opdracht [kubectl delete pod][kubectl-delete] voordat u verder gaat met de volgende stap:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Het maken van een pod testen met een specifieke gebruikerscontext

In het vorige voorbeeld probeerde de containerafbeelding automatisch root te gebruiken om NGINX aan poort 80 te binden. Deze aanvraag is geweigerd door het *standaardbeveiligingsbeleid voor beperkte* pod, zodat de pod niet wordt gestart. Laten we nu proberen dat dezelfde NGINX-pod uit `runAsUser: 2000`te voeren met een specifieke gebruikerscontext, zoals .

Maak een `nginx-unprivileged-nonroot.yaml` bestand met de naam en plak het volgende YAML-manifest:

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

Maak de pod met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

De Kubernetes scheduler accepteert de podaanvraag. Als u echter de status van `kubectl get pods`de pod bekijkt met behulp van , is er een andere fout dan het vorige voorbeeld:

```console
$ kubectl-nonadminuser get pods

NAME                         READY   STATUS              RESTARTS   AGE
nginx-unprivileged-nonroot   0/1     CrashLoopBackOff    1          3s
```

Gebruik de opdracht [kubectl describe pod][kubectl-describe] om de gebeurtenissen voor de pod te bekijken. In het volgende gecondenseerde voorbeeld worden de podgebeurtenissen weergegeven:

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

De gebeurtenissen geven aan dat de container is gemaakt en gestart. Er is niets meteen duidelijk over waarom de pod is in een mislukte staat. Laten we eens kijken naar de pod logs met behulp van de [kubectl logs][kubectl-logs] opdracht:

```console
kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous
```

De volgende voorbeeldlogboekuitvoer geeft een indicatie dat er binnen de NGINX-configuratie zelf een fout met machtigingen optreedt wanneer de service probeert te starten. Deze fout wordt opnieuw veroorzaakt door dat u moet binden aan poort 80. Hoewel de podspecificatie een normaal gebruikersaccount heeft gedefinieerd, is dit gebruikersaccount niet voldoende in het OS-niveau om de NGINX-service te starten en te binden aan de beperkte poort.

```console
$ kubectl-nonadminuser logs nginx-unprivileged-nonroot --previous

2019/03/28 22:38:29 [warn] 1#1: the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
nginx: [warn] the "user" directive makes sense only if the master process runs with super-user privileges, ignored in /etc/nginx/nginx.conf:2
2019/03/28 22:38:29 [emerg] 1#1: mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
nginx: [emerg] mkdir() "/var/cache/nginx/client_temp" failed (13: Permission denied)
```

Nogmaals, het is belangrijk om het gedrag van het standaardbeveiligingsbeleid voor de pod te begrijpen. Deze fout was een beetje moeilijker op te sporen, en nogmaals, je mag niet verwachten dat een fundamentele NGINX pod te worden ontkend.

Verwijder deze testpod met de opdracht [kubectl delete pod][kubectl-delete] voordat u verder gaat met de volgende stap:

```console
kubectl-nonadminuser delete -f nginx-unprivileged-nonroot.yaml
```

## <a name="create-a-custom-pod-security-policy"></a>Een aangepast podbeveiligingsbeleid maken

Nu u het gedrag van het standaardbeveiligingsbeleid van de pod hebt gezien, bieden we een manier voor de *niet-beheerdersgebruiker* om pods met succes te plannen.

Laten we een beleid maken om pods te weigeren die bevoorrechte toegang aanvragen. Andere opties, zoals *runAsUser* of toegestane *volumes,* zijn niet expliciet beperkt. Dit type beleid weigert een verzoek om bevoorrechte toegang, maar laat het cluster anders de gevraagde pods uitvoeren.

Maak een `psp-deny-privileged.yaml` bestand met de naam en plak het volgende YAML-manifest:

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

Maak het beleid met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Als u het beschikbare beleid wilt weergeven, gebruikt u de [opdracht kubectl get psp,][kubectl-get] zoals in het volgende voorbeeld wordt weergegeven. Vergelijk het *beleid met psp-deny-privileged* met het *standaardbeleid dat* in de vorige voorbeelden is afgedwongen om een pod te maken. Alleen het gebruik van *PRIV* escalatie wordt ontkend door uw beleid. Er zijn geen beperkingen voor de gebruiker of groep voor het *psp-deny-privileged* beleid.

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Gebruikersaccount toestaan het aangepaste podbeveiligingsbeleid te gebruiken

In de vorige stap hebt u een podbeveiligingsbeleid gemaakt om pods met een aanvraag voor bevoorrechte toegang af te wijzen. Als u wilt toestaan dat het beleid wordt gebruikt, maakt u een *rol* of een *clusterrol*. Vervolgens associeert u een van deze rollen met behulp van een *Rolbinding* of *ClusterRoleBinding*.

Maak in dit voorbeeld een clusterrol waarmee u het in de vorige stap gemaakte *psp-deny-privileged* policy *gebruiken.* Maak een `psp-deny-privileged-clusterrole.yaml` bestand met de naam en plak het volgende YAML-manifest:

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

Maak de clusterrol met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Maak nu een clusterrolbinding om de clusterrol te gebruiken die in de vorige stap is gemaakt. Maak een `psp-deny-privileged-clusterrolebinding.yaml` bestand met de naam en plak het volgende YAML-manifest:

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

Maak een ClusterRoleBinding met de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> In de eerste stap van dit artikel is de functie beveiligingsbeleid van de pod ingeschakeld in het AKS-cluster. De aanbevolen praktijk was om de functie beveiligingsbeleid van de pod alleen in te schakelen nadat u uw eigen beleid hebt gedefinieerd. Dit is de fase waarin u de functie beveiligingsbeleid voor de pod zou inschakelen. Er zijn een of meer aangepaste beleidsregels gedefinieerd en gebruikersaccounts zijn aan dit beleid gekoppeld. Nu u de functie beveiligingsbeleid van de pod veilig inschakelen en problemen die worden veroorzaakt door het standaardbeleid minimaliseren.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Test het maken van een onbevoegde pod opnieuw

Nu uw aangepaste podbeveiligingsbeleid is toegepast en het gebruikersaccount een binding heeft om het beleid te gebruiken, proberen we opnieuw een pod zonder bevoegdheden te maken. Gebruik hetzelfde `nginx-privileged.yaml` manifest om de pod te maken met de opdracht [kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

De pod is gepland. Wanneer u de status van de pod controleert met de opdracht [kubectl get pods,][kubectl-get] wordt de pod *actief:*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

In dit voorbeeld ziet u hoe u aangepaste podbeveiligingsbeleidsregels maken om toegang tot het AKS-cluster voor verschillende gebruikers of groepen te definiëren. Het standaard AKS-beleid biedt strakke besturingselementen voor welke pods kunnen worden uitgevoerd, dus maak uw eigen aangepaste beleidsregels om vervolgens de beperkingen die u nodig hebt correct te definiëren.

Verwijder de NGINX-pod zonder bevoegdheden met de [opdracht kubectl delete][kubectl-delete] en geef de naam van uw YAML-manifest op:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Resources opschonen

Als u het beveiligingsbeleid van de pod wilt uitschakelen, gebruikt u de opdracht [az aks-update][az-aks-update] opnieuw. In het volgende voorbeeld wordt podbeveiligingsbeleid uitgeschakeld voor de clusternaam *myAKSCluster* in de brongroep *myResourceGroup:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Verwijder vervolgens de clusterrol en clusterrolbinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Verwijder het beveiligingsbeleid met de opdracht [kubectl delete][kubectl-delete] en geef de naam van uw YAML-manifest op:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Verwijder ten slotte de *naamruimte van psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel ziet u hoe u een podbeveiligingsbeleid maken om het gebruik van bevoorrechte toegang te voorkomen. Er zijn veel functies die een beleid kan afdwingen, zoals het type volume of de RunAs-gebruiker. Zie de [referentiedocumenten voor het beveiligingsbeleid van de Kubernetes-pod voor][kubernetes-policy-reference]meer informatie over de beschikbare opties .

Zie Verkeer tussen pods beveiligen [tussen pods met netwerkbeleid in AKS][network-policies]voor meer informatie over het beperken van het netwerkverkeer van pod's.

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

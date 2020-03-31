---
title: Clusterbronnen beheren met RBAC en Azure AD in Azure Kubernetes Service
description: Meer informatie over het gebruik van Azure Active Directory-groepslidmaatschap om de toegang tot clusterbronnen te beperken met behulp van rbac (Azure Kubernetes Service) in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: 456b6dcdd590b48e06c830db85b726d4bebb69e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596518"
---
# <a name="control-access-to-cluster-resources-using-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Toegang tot clusterbronnen beheren met behulp van toegangsbeheer op basis van rollen en Azure Active Directory-identiteiten in Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan worden geconfigureerd om Azure Active Directory (AD) te gebruiken voor gebruikersverificatie. In deze configuratie meldt u zich aan bij een AKS-cluster met behulp van een Azure AD-verificatietoken. U ook Kubernetes role-based access control (RBAC) configureren om de toegang tot clusterbronnen op basis van de identiteit of het groepslidmaatschap van een gebruiker te beperken.

In dit artikel ziet u hoe u Azure AD-groepslidmaatschap gebruiken om de toegang tot naamruimten en clusterbronnen te beheren met Kubernetes RBAC in een AKS-cluster. Voorbeeldgroepen en gebruikers worden gemaakt in Azure AD, waarna Rollen en rolbindingen worden gemaakt in het AKS-cluster om de juiste machtigingen te verlenen om resources te maken en weer te geven.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand AKS-cluster hebt ingeschakeld met Azure AD-integratie. Zie [Azure Active Directory integreren met AKS][azure-ad-aks-cli]als u een AKS-cluster nodig hebt.

U moet de Azure CLI-versie 2.0.61 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Demogroepen maken in Azure AD

Laten we in dit artikel twee gebruikersrollen maken die kunnen worden gebruikt om te laten zien hoe Kubernetes RBAC en Azure AD de toegang tot clusterbronnen beheren. De volgende twee voorbeeldrollen worden gebruikt:

* **Toepassingsontwikkelaar**
    * Een gebruiker met de naam *aksdev* die deel uitmaakt van de *appdev-groep.*
* **Site betrouwbaarheid ingenieur**
    * Een gebruiker met de naam *akssre* die deel uitmaakt van de *opssre-groep.*

In productieomgevingen u bestaande gebruikers en groepen gebruiken binnen een Azure AD-tenant.

Download eerst de resource-ID van je AKS-cluster met de opdracht [AZ AKS Show.][az-aks-show] Wijs de resource-id toe aan een variabele met de naam *AKS_ID* zodat er in aanvullende opdrachten naar kan worden verwezen.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Maak de eerste voorbeeldgroep in Azure AD voor de toepassingsontwikkelaars met de opdracht [AZ-advertentiegroep maken.][az-ad-group-create] In het volgende voorbeeld wordt een groep met de naam *appdev gedefinieerd:*

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Maak nu een Azure-roltoewijzing voor de *appdev-groep* met de opdracht [Voor het maken van az-rollen.][az-role-assignment-create] Met deze toewijzing kan elk `kubectl` lid van de groep worden gebruikt om te communiceren met een AKS-cluster door hen de gebruikersrol van het *Azure Kubernetes Service Cluster-cluster*toe te kennen.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Als u een fout `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.`ontvangt, zoals : Wacht een paar seconden tot de Azure `az role assignment create` AD-groepsobject-id door de map wordt verspreid, probeer dan de opdracht opnieuw.

Maak een tweede voorbeeldgroep, deze voor SRE's met de naam *opssre:*

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Maak opnieuw een Azure-roltoewijzing om leden van de groep de gebruikersrol van azure *Kubernetes Service Cluster te verlenen:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Demogebruikers maken in Azure AD

Met twee voorbeeldgroepen die zijn gemaakt in Azure AD voor onze toepassingsontwikkelaars en SRE's, u nu twee voorbeeldgebruikers maken. Als u de RBAC-integratie aan het einde van het artikel wilt testen, meldt u zich met deze accounts aan bij het AKS-cluster.

Maak het eerste gebruikersaccount in Azure AD met de opdracht [az-advertentiegebruikers.][az-ad-user-create]

In het volgende voorbeeld wordt een gebruiker met de weergavenaam *AKS Dev* en de gebruikersnaam (UPN) van `aksdev@contoso.com`. Werk de UPN bij om een geverifieerd domein voor uw Azure AD-tenant op `--password` te nemen *(vervang contoso.com* door uw eigen domein) en geef uw eigen beveiligde referentie op:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name aksdev@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)
```

Voeg nu de gebruiker toe aan de *appdev-groep* die in de vorige sectie is gemaakt met de opdracht [AZ-advertentiegroeplid toevoegen:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Maak een tweede gebruikersaccount aan. In het volgende voorbeeld wordt een gebruiker met de weergavenaam *AKS SRE* en de gebruikersnaam (UPN) van `akssre@contoso.com`. Werk de UPN opnieuw bij om een geverifieerd domein voor uw Azure AD-tenant op `--password` te nemen *(vervang contoso.com* door uw eigen domein) en geef uw eigen beveiligde referentie op:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name akssre@contoso.com \
  --password P@ssw0rd1 \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>De AKS-clusterbronnen voor app-ontwikkelaars maken

De Azure AD-groepen en -gebruikers worden nu gemaakt. Azure-roltoewijzingen zijn gemaakt om de groepsleden als gewone gebruiker verbinding te laten maken met een AKS-cluster. Laten we nu het AKS-cluster configureren om deze verschillende groepen toegang te geven tot specifieke bronnen.

Eerst, krijgen de cluster admin referenties met behulp van de [az aks get-credentials][az-aks-get-credentials] commando. In een van de volgende secties krijgt u de reguliere *gebruikersclusterreferenties* om de Azure AD-verificatiestroom in actie te zien.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Maak een naamruimte in het AKS-cluster met de opdracht [kubectl create namespace.][kubectl-create] In het volgende voorbeeld wordt een naamruimtenaam *v.:*

```console
kubectl create namespace dev
```

In Kubernetes definiëren *rollen* de machtigingen voor het verlenen en *Worden deze* toegepast op gewenste gebruikers of groepen. Deze toewijzingen kunnen worden toegepast op een bepaalde naamruimte of in het hele cluster. Zie [RBAC-autorisatie gebruiken][rbac-authorization]voor meer informatie .

Maak eerst een rol voor *de dev-naamruimte.* Deze rol verleent volledige machtigingen voor de naamruimte. In productieomgevingen u gedetailleerdere machtigingen opgeven voor verschillende gebruikers of groepen.

Maak een `role-dev-namespace.yaml` bestand met de naam en plak het volgende YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Maak de rol met de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f role-dev-namespace.yaml
```

Download vervolgens de resource-id voor de *appdev-groep* met de opdracht [weergegeven az-advertentiegroep.][az-ad-group-show] Deze groep is ingesteld als het onderwerp van een rolbinding in de volgende stap.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Maak nu een rolbinding voor de *appdev-groep* om de eerder gemaakte rol te gebruiken voor naamruimtetoegang. Maak een `rolebinding-dev-namespace.yaml` bestand met de naam en plak het volgende YAML-manifest. Vervang op de laatste regel *groepObjectId* door de uitvoer van groepsobject-id van de vorige opdracht:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Maak de rolebinding met de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>De AKS-clusterbronnen voor SRE's maken

Herhaal nu de vorige stappen om een naamruimte, rol en rolbinding voor de SRE's te maken.

Maak eerst een naamruimte voor *sre* met de opdracht [kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Maak een `role-sre-namespace.yaml` bestand met de naam en plak het volgende YAML-manifest:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Maak de rol met de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f role-sre-namespace.yaml
```

Haal de resource-id voor de *opssre-groep* op met de opdracht [az-advertentiegroep toon:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Maak een rolebinding voor de *opssre-groep* om de eerder gemaakte rol te gebruiken voor naamruimtetoegang. Maak een `rolebinding-sre-namespace.yaml` bestand met de naam en plak het volgende YAML-manifest. Vervang op de laatste regel *groepObjectId* door de uitvoer van groepsobject-id van de vorige opdracht:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Maak de rolebinding met de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interactie met clusterbronnen met Azure AD-identiteiten

Laten we nu testen of de verwachte machtigingen werken wanneer u resources in een AKS-cluster maakt en beheert. In deze voorbeelden plant en weert u pods in de toegewezen naamruimte van de gebruiker. Vervolgens probeert u pods buiten de toegewezen naamruimte te plannen en weer te geven.

Eerst de *kubeconfig-context* resetten met behulp van de opdracht [az aks get-credentials.][az-aks-get-credentials] In een vorige sectie stelt u de context in met behulp van de referenties van de clusterbeheerder. De beheerdersgebruiker omzeilt azure AD-aanmeldingsprompts. Zonder `--admin` de parameter wordt de gebruikerscontext toegepast waarvoor alle aanvragen moeten worden geverifieerd met Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Plan een basisNGINX-pod met de opdracht [kubectl run][kubectl-run] in de *dev-naamruimte:*

```console
kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev
```

Als de aanmeldingsprompt voert u `appdev@contoso.com` de referenties in voor uw eigen account die aan het begin van het artikel zijn gemaakt. Zodra u bent aangemeld, wordt het accounttoken `kubectl` in de cache opgeslagen voor toekomstige opdrachten. De NGINX is met succes gepland, zoals weergegeven in het volgende voorbeeld uitvoer:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Gebruik nu de [opdracht kubectl get pods][kubectl-get] om pods in de *dev-naamruimte* weer te geven.

```console
kubectl get pods --namespace dev
```

Zoals in de volgende voorbeelduitvoer wordt weergegeven, wordt de NGINX-pod *uitgevoerd:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Clusterbronnen maken en weergeven buiten de toegewezen naamruimte

Probeer nu pods buiten de *dev-naamruimte* te bekijken. Gebruik de [kubectl get pods][kubectl-get] opdracht `--all-namespaces` opnieuw, dit keer om te zien als volgt:

```console
kubectl get pods --all-namespaces
```

Het groepslidmaatschap van de gebruiker heeft geen Kubernetes-rol die deze actie mogelijk maakt, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

Probeer op dezelfde manier een pod in verschillende naamruimte te plannen, zoals de naamruimte van de *sre.* Het groepslidmaatschap van de gebruiker komt niet overeen met een Kubernetes-rol en RoleBinding om deze machtigingen te verlenen, zoals wordt weergegeven in de volgende voorbeelduitvoer:

```console
$ kubectl run --generator=run-pod/v1 nginx-dev --image=nginx --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>De SRE-toegang tot de AKS-clusterbronnen testen

Als u wilt bevestigen dat ons Azure AD-groepslidmaatschap en Kubernetes RBAC correct werken tussen verschillende gebruikers en groepen, probeert u de vorige opdrachten wanneer deze zijn aangemeld als de *opssre-gebruiker.*

Reset de *kubeconfig-context* met de opdracht [az aks get-credentials][az-aks-get-credentials] waarmee het eerder gecachede verificatietoken voor de *aksdev-gebruiker* wordt gewist:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Probeer pods te plannen en *sre* weer te geven in de toegewezen sre-naamruimte. Meld u aan wanneer u `opssre@contoso.com` daarom wordt gevraagd met uw eigen referenties die aan het begin van het artikel zijn gemaakt:

```console
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre
kubectl get pods --namespace sre
```

Zoals in de volgende voorbeelduitvoer wordt weergegeven, u de pods maken en weergeven:

```console
$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Probeer nu pods buiten toegewezen SRE-naamruimte weer te geven of te plannen:

```console
kubectl get pods --all-namespaces
kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
```

Deze `kubectl` opdrachten mislukken, zoals wordt weergegeven in de volgende voorbeelduitvoer. Het groepslidmaatschap van de gebruiker en Kubernetes Role en RoleBindings verlenen geen machtigingen om resources in andere naamruimten te maken of te beheren:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run --generator=run-pod/v1 nginx-sre --image=nginx --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebt u resources gemaakt in het AKS-cluster en gebruikers en groepen in Azure AD. Voer de volgende opdrachten uit om al deze resources op te schonen:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Volgende stappen

Zie [Toegangs- en identiteitsopties voor AKS voor][rbac-authorization]meer informatie over het beveiligen van Kubernetes-clusters.

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS voor][operator-best-practices-identity]aanbevolen procedures voor identiteits- en bronbeheer.

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md

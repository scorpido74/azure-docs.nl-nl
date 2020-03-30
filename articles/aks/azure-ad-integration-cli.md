---
title: Azure Active Directory integreren met Azure Kubernetes Service
description: Meer informatie over het gebruik van het Azure CLI voor het maken en azure Active Directory-enabled Azure Kubernetes Service (AKS) cluster
services: container-service
ms.topic: article
ms.date: 04/16/2019
ms.openlocfilehash: d17ae12beecf9d83ef6d688af799787c5ccf322b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253050"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli"></a>Azure Active Directory integreren met Azure Kubernetes Service met azure cli

Azure Kubernetes Service (AKS) kan worden geconfigureerd om Azure Active Directory (AD) te gebruiken voor gebruikersverificatie. In deze configuratie u zich aanmelden bij een AKS-cluster met behulp van een Azure AD-verificatietoken. Clusteroperators kunnen ook Kubernetes role-based access control (RBAC) configureren op basis van de identiteit van een gebruiker of het lidmaatschap van een directorygroep.

In dit artikel ziet u hoe u de vereiste Azure AD-componenten maakt, vervolgens een Azure AD-cluster implementeert en een basisRBAC-rol in het AKS-cluster maakt. U deze stappen ook [uitvoeren met de Azure-portal.][azure-ad-portal]

Zie [Azure CLI-voorbeelden - AKS-integratie met Azure AD][complete-script]voor het volledige voorbeeldscript dat in dit artikel wordt gebruikt.

De volgende beperkingen zijn van toepassing:

- Azure AD kan alleen worden ingeschakeld wanneer u een nieuw cluster met RBAC maakt. U Azure AD niet inschakelen in een bestaand AKS-cluster.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.61 of hoger installeren en configureren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][install-azure-cli].

Ga [https://shell.azure.com](https://shell.azure.com) naar Cloud Shell openen in uw browser.

Maak een variabele voor de gewenste AKS-clusternaam voor consistentie en om de opdrachten in dit artikel uit te voeren. In het volgende voorbeeld wordt de naam *myakscluster gebruikt:*

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Overzicht van Azure AD-verificatie

Azure AD-verificatie wordt geleverd aan AKS-clusters met OpenID Connect. OpenID Connect is een identiteitslaag die is gebouwd bovenop het OAuth 2.0-protocol. Zie de documentatie voor Open [ID connect voor][open-id-connect]meer informatie over OpenID Connect.

Vanuit het Kubernetes-cluster wordt Webhook Token Authentication gebruikt om verificatietokens te verifiëren. Webhook-tokenverificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster. Zie de [webhook-verificatiedocumentatie][kubernetes-webhook]voor meer informatie over webhook-tokenverificatie.

> [!NOTE]
> Bij het configureren van Azure AD voor AKS-verificatie worden twee Azure AD-toepassingen geconfigureerd. Deze bewerking moet worden voltooid door een Azure-tenantbeheerder.

## <a name="create-azure-ad-server-component"></a>Azure AD-servercomponent maken

Als u wilt integreren met AKS, maakt en gebruikt u een Azure AD-toepassing die fungeert als eindpunt voor de identiteitsaanvragen. De eerste Azure AD-toepassing die u nodig hebt, krijgt Azure AD-groepslidmaatschap voor een gebruiker.

Maak de servertoepassingscomponent met de opdracht [Az-advertentie-app maken][az-ad-app-create] en werk vervolgens de groepslidmaatschapsclaims bij met de [updateopdracht voor az-advertentie-apps.][az-ad-app-update] In het volgende voorbeeld wordt de *aksnamevariabele* gebruikt die is gedefinieerd in de sectie [Voordat u begint,](#before-you-begin) en wordt een variabele gemaakt

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group memebership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Maak nu een serviceprincipal voor de server-app met de opdracht [AZ Ad SP create.][az-ad-sp-create] Deze serviceprincipal wordt gebruikt om zichzelf te authenticeren binnen het Azure-platform. Download vervolgens het hoofdgeheim van de service met de opdracht [az-ad sp-referenties opnieuw instellen][az-ad-sp-credential-reset] en wijs de variabele *serverApplicationSecret* toe voor gebruik in een van de volgende stappen:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Het Azure AD heeft machtigingen nodig om de volgende acties uit te voeren:

* Mapgegevens lezen
* Aanmelden en gebruikersprofiel lezen

Wijs deze machtigingen toe met de opdracht Toestemming toevoegen van de [az-advertentie-app:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Geef ten slotte de machtigingen toe die in de vorige stap voor de servertoepassing zijn toegewezen met behulp van de [toestemmingssubsidieopdracht voor az-advertentie-apps.][az-ad-app-permission-grant] Deze stap mislukt als de lopende rekening geen tenantbeheerder is. U moet ook machtigingen voor Azure AD-toepassing toevoegen om informatie op te vragen waarvoor mogelijk administratieve toestemming vereist is met toestemming voor toestemming van de [AZ-advertentie-app:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Azure AD-clientcomponent maken

De tweede Azure AD-toepassing wordt gebruikt wanneer een gebruiker zich`kubectl`bij het AKS-cluster logt met de Kubernetes CLI ( ). Deze clienttoepassing neemt de verificatieaanvraag van de gebruiker over en verifieert hun referenties en machtigingen. Maak de Azure AD-app voor de clientcomponent met de opdracht [AZ-advertentie-app maken:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Maak een serviceprincipal voor de clienttoepassing met de opdracht [AZ Ad SP Create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Download de oAuth2-id voor de server-app om de verificatiestroom tussen de twee app-onderdelen mogelijk te maken met de opdracht show van de [AZ-advertentie-app.][az-ad-app-show] Deze oAuth2 ID wordt gebruikt in de volgende stap.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Voeg de machtigingen toe voor de onderdelen van de clienttoepassing en servertoepassing om de oAuth2-communicatiestroom te gebruiken met behulp van de [opdracht voor het toevoegen van de toestemming seinvoor toestemming van de AZ-advertentie-app.][az-ad-app-permission-add] Geef vervolgens machtigingen voor de clienttoepassing toe aan communicatie met de servertoepassing met de [toestemmingsopdracht voor toestemming van az-advertentie-app:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Het cluster implementeren

Met de twee Azure AD-toepassingen die zijn gemaakt, maakt u nu het AKS-cluster zelf. Maak eerst een resourcegroep met de opdracht [Az-groep maken.][az-group-create] In het volgende voorbeeld wordt de resourcegroep in de *EastUS-regio* gezoend:

Maak een resourcegroep voor het cluster:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Haal de tenant-id van uw Azure-abonnement op met de opdracht [az-accountshow.][az-account-show] Maak vervolgens het AKS-cluster met de opdracht [az aks create.][az-aks-create] De opdracht voor het maken van het AKS-cluster biedt de server- en clienttoepassings-id's, het hoofdgeheim van de servertoepassingsservice en uw tenant-id:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Tot slot, krijgen de cluster admin referenties met behulp van de [az aks get-credentials][az-aks-get-credentials] commando. In een van de volgende stappen krijgt u de reguliere *gebruikersclusterreferenties* om de Azure AD-verificatiestroom in actie te zien.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>RBAC-binding maken

Voordat een Azure Active Directory-account kan worden gebruikt met het AKS-cluster, moet een rolbinding of clusterrolbinding worden gemaakt. *Rollen* definiëren de machtigingen voor het verlenen en *bindingen* passen deze toe op gewenste gebruikers. Deze toewijzingen kunnen worden toegepast op een bepaalde naamruimte of in het hele cluster. Zie [RBAC-autorisatie gebruiken][rbac-authorization]voor meer informatie .

Download de gebruikersnaam (UPN) voor de gebruiker die momenteel is ingelogd met de opdracht [AZ-advertentie signed-in-user.][az-ad-signed-in-user-show] Dit gebruikersaccount is ingeschakeld voor Azure AD-integratie in de volgende stap.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Als de gebruiker waarvoor u de RBAC-binding verleent zich in dezelfde Azure AD-tenant bevindt, wijst u machtigingen toe op basis van de *userPrincipalName.* Als de gebruiker zich in een andere Azure AD-tenant bevindt, moet u de eigenschap *objectId* zoeken en gebruiken.

Maak een YAML-manifest met de naam `basic-azure-ad-binding.yaml` en plak de volgende inhoud. Vervang op de laatste regel *userPrincipalName_or_objectId* door de UPN- of object-ID-uitvoer van de vorige opdracht:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Maak de clusterrolebinding met de [opdracht kubectl apply][kubectl-apply] en geef de bestandsnaam van uw YAML-manifest op:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Access-cluster met Azure AD

Laten we nu de integratie van Azure AD-verificatie voor het AKS-cluster testen. Stel `kubectl` de config-context in om gewone gebruikersreferenties te gebruiken. Deze context geeft alle verificatieaanvragen door via Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Gebruik nu de opdracht [kubectl get pods][kubectl-get] om pods in alle naamruimten weer te geven:

```console
kubectl get pods --all-namespaces
```

U ontvangt een aanmeldingsprompt om te verifiëren met Behulp van Azure AD-referenties met behulp van een webbrowser. Nadat u hebt geverifieerd, `kubectl` worden de pods in het AKS-cluster weergegeven in de opdracht, zoals weergegeven in de volgende voorbeelduitvoer:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Het verificatietoken `kubectl` waarvoor het is ontvangen, wordt in de cache opgeslagen. U wordt alleen opnieuw gevraagd om u aan te melden wanneer het token is verlopen of het Kubernetes config-bestand opnieuw is gemaakt.

Als u een foutbericht voor autorisatie ziet nadat u zich met een webbrowser hebt aangemeld als in de volgende voorbeelduitvoer, controleert u de volgende mogelijke problemen:

```output
error: You must be logged in to the server (Unauthorized)
```

* U hebt de juiste object-id of UPN gedefinieerd, afhankelijk van of het gebruikersaccount zich in dezelfde Azure AD-tenant bevindt of niet.
* De gebruiker is geen lid van meer dan 200 groepen.
* Geheim gedefinieerd in de toepassingsregistratie voor server komt overeen met de waarde die is geconfigureerd met`--aad-server-app-secret`

## <a name="next-steps"></a>Volgende stappen

Zie het [Azure AD-integratiescript in de REPO van AKS-voorbeelden][complete-script]voor het volledige script met de opdrachten die in dit artikel worden weergegeven.

Als u Azure AD-gebruikers en -groepen wilt gebruiken om de toegang tot clusterbronnen te beheren, raadpleegt u [Toegang tot clusterbronnen beheren met behulp van op rollen gebaseerdtoegangsbeheer en Azure AD-identiteiten in AKS][azure-ad-rbac].

Zie [Toegangs- en identiteitsopties voor AKS voor][rbac-authorization]meer informatie over het beveiligen van Kubernetes-clusters.

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS voor][operator-best-practices-identity]aanbevolen procedures voor identiteits- en bronbeheer.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[azure-ad-portal]: azure-ad-integration.md
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md

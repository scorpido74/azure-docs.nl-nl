---
title: Azure Active Directory integreren met de Azure Kubernetes-service
description: AKS-clusters (Azure Kubernetes service) Azure Active Directory ingeschakeld maken
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 04/26/2019
ms.author: mlearned
ms.openlocfilehash: 26f1544cab5cf5be2edd52f97c758d46eb835514
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103794"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Active Directory integreren met de Azure Kubernetes-service

Azure Kubernetes service (AKS) kan worden geconfigureerd voor het gebruik van Azure Active Directory (Azure AD) voor gebruikers verificatie. In deze configuratie kunt u zich aanmelden bij een AKS-cluster met behulp van uw Azure AD-verificatie token.

Cluster beheerders kunnen op rollen gebaseerd toegangs beheer (RBAC) Kubernetes configureren op basis van de identiteit van een gebruiker of het lidmaatschap van de Directory groep.

In dit artikel wordt uitgelegd hoe u:

- Implementeer de vereisten voor AKS en Azure AD.
- Implementeer een Azure AD-cluster.
- Maak een eenvoudige RBAC-rol in het AKS-cluster met behulp van de Azure Portal.

U kunt deze stappen ook uitvoeren met behulp van de [Azure cli][azure-ad-cli].

> [!NOTE]
> Azure AD kan alleen worden ingeschakeld wanneer u een nieuw cluster met RBAC-functionaliteit maakt. U kunt Azure AD niet inschakelen op een bestaand AKS-cluster.

## <a name="authentication-details"></a>Verificatie gegevens

Azure AD-verificatie wordt geleverd aan AKS-clusters met OpenID connect-verbinding. OpenID Connect Connect is een id-laag die boven op het OAuth 2,0-protocol is gebouwd.

Zie [toegang tot webtoepassingen toestaan met OpenID Connect Connect en Azure AD][open-id-connect]voor meer informatie over OpenID Connect Connect.

In een Kubernetes-cluster wordt webhook-token verificatie gebruikt om tokens te authenticeren. Webhook-token verificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster.

Zie de sectie [webhook-token verificatie][kubernetes-webhook] in de Kubernetes-documentatie voor meer informatie over de verificatie van webhook-tokens.

Als u Azure AD-verificatie voor een AKS-cluster wilt bieden, worden er twee Azure AD-toepassingen gemaakt. De eerste toepassing is een server onderdeel dat gebruikers verificatie biedt. De tweede toepassing is een client onderdeel dat wordt gebruikt wanneer u wordt gevraagd om de CLI voor verificatie. Deze client toepassing maakt gebruik van de server toepassing voor de daad werkelijke verificatie van de referenties die door de client worden verschaft.

> [!NOTE]
> Wanneer u Azure AD configureert voor AKS-verificatie, worden er twee Azure AD-toepassingen geconfigureerd. De stappen voor het delegeren van machtigingen voor elke toepassing moeten door een Azure-Tenant beheerder worden uitgevoerd.

## <a name="create-the-server-application"></a>De server toepassing maken

De eerste Azure AD-toepassing wordt toegepast om het lidmaatschap van de Azure AD-groep van een gebruiker op te halen. Als u deze toepassing wilt maken in de Azure Portal:

1. Selecteer **Azure Active Directory** > app-registratiesnieuwe > **registratie**.

    a. Geef de toepassing een naam, bijvoorbeeld *AKSAzureADServer*.

    b. Selecteer voor **ondersteunde account typen** **alleen accounts in deze organisatie Directory**.
    
    c. Kies **Web** voor het omleidings-URI-type en voer vervolgens een waarde in *https://aksazureadserver* voor de URI-indeling, zoals.

    d. Selecteer **registreren** wanneer u klaar bent.

2. Selecteer **manifest**en bewerk vervolgens de waarde **groupMembershipClaims:** **.** Wanneer u klaar bent met de updates, selecteert u **Opslaan**.

    ![Groepslid maatschap bijwerken](media/aad-integration/edit-manifest.png)

3. Selecteer in het linkerdeel venster van de Azure AD-toepassing **certificaten & geheimen**.

    a. Selecteer **+ Nieuw client geheim**.

    b. Voeg een sleutel beschrijving toe, zoals *AKS Azure ad-server*. Kies een verloop tijd en selecteer vervolgens **toevoegen**.

    c. Let op de sleutel waarde, die alleen op dit moment wordt weer gegeven. Wanneer u een Azure AD-AKS-cluster implementeert, wordt deze waarde het server-toepassings geheim genoemd.

4. Selecteer in het linkerdeel venster van de Azure AD-toepassing **API-machtigingen**en selecteer vervolgens **+ een machtiging toevoegen**.

    a. Selecteer **Microsoft Graph**onder **micro soft-api's**.

    b. Selecteer **gedelegeerde machtigingen**en schakel vervolgens het selectie vakje naast **Directory > Directory. Read. all (Directory gegevens lezen)** .

    c. Als er een standaard gemachtigde machtiging voor **gebruiker > gebruiker. lezen (aanmelden en gebruikers profiel lezen)** niet bestaat, schakelt u het selectie vakje ernaast in.

    d. Selecteer **toepassings machtigingen**en schakel vervolgens het selectie vakje naast **Directory > Directory. Read. all (Directory gegevens lezen)** .

    ![Machtigingen voor grafieken instellen](media/aad-integration/graph-permissions.png)

    e. Selecteer **machtigingen toevoegen** om de updates op te slaan.

    f. Onder **toestemming geven**, selecteert u **toestemming geven aan beheerder**. Deze knop is niet beschikbaar het huidige account dat wordt gebruikt, wordt niet vermeld als een Tenant beheerder.

    Als de machtigingen zijn verleend, wordt de volgende melding weer gegeven in de portal:

   ![Melding van geslaagde machtigingen](media/aad-integration/permissions-granted.png)

5. Selecteer in het linkerdeel venster van de Azure AD-toepassing **een API beschikbaar**maken en selecteer vervolgens **+ een bereik toevoegen**.
    
    a. Voer een **Scope naam**in, een **weergave naam**voor de beheerder en vervolgens een beschrijving van de **beheerder** , zoals *AKSAzureADServer*.

    b. Zorg ervoor dat de **status** is ingesteld op **ingeschakeld**.

    ![De server-app beschikbaar maken als een API voor gebruik met andere services](media/aad-integration/expose-api.png)

    c. Selecteer **bereik toevoegen**.

6. Ga terug naar de **overzichts** pagina van de toepassing en noteer de **toepassings-id (client)** . Wanneer u een Azure AD-AKS-cluster implementeert, wordt deze waarde de server toepassings-ID genoemd.

    ![Toepassings-ID ophalen](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>De client toepassing maken

De tweede Azure AD-toepassing wordt gebruikt wanneer u zich aanmeldt met de Kubernetes CLI (kubectl).

1. Selecteer **Azure Active Directory** > app-registratiesnieuwe > **registratie**.

    a. Geef de toepassing een naam, bijvoorbeeld *AKSAzureADClient*.

    b. Selecteer voor **ondersteunde account typen** **alleen accounts in deze organisatie Directory**.

    c. Selecteer **Web** voor het omleidings-URI-type en voer vervolgens een waarde in *https://aksazureadclient* met de URI-indeling, zoals.

    d. Selecteer **registreren** wanneer u klaar bent.

2. Selecteer in het linkerdeel venster van de Azure AD-toepassing **API-machtigingen**en selecteer vervolgens **+ een machtiging toevoegen**.

    a. Selecteer **mijn api's**en kies vervolgens uw Azure ad-server toepassing die u in de vorige stap hebt gemaakt, zoals *AKSAzureADServer*.

    b. Selecteer **gedelegeerde machtigingen**en schakel vervolgens het selectie vakje naast uw Azure ad-server-app in.

    ![Toepassings machtigingen configureren](media/aad-integration/select-api.png)

    c. Selecteer **machtigingen toevoegen**.

    d. Onder **toestemming geven**, selecteert u **toestemming geven aan beheerder**. Deze knop is niet beschikbaar als het huidige account geen Tenant beheerder is. Wanneer machtigingen worden verleend, wordt de volgende melding weer gegeven in de portal:

    ![Melding van geslaagde machtigingen](media/aad-integration/permissions-granted.png)

3. Selecteer in het linkerdeel venster van de Azure AD-toepassing **verificatie**.

    - Onder **standaard client type**selecteert u **Ja** om **de client als een open bare client te behandelen**.

5. Noteer de toepassings-ID in het linkerdeel venster van de Azure AD-toepassing. Wanneer u een Azure AD-AKS-cluster implementeert, wordt deze waarde de client toepassings-ID genoemd.

   ![De toepassings-ID ophalen](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>De Tenant-ID ophalen

Vervolgens haalt u de ID van uw Azure-Tenant op. Deze waarde wordt gebruikt wanneer u het AKS-cluster maakt.

Selecteer in de Azure Portal **Azure Active Directory** > **Eigenschappen** en noteer de **map-id**. Wanneer u een Azure AD-AKS-cluster maakt, wordt deze waarde de Tenant-ID genoemd.

![De Azure-Tenant-ID ophalen](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Het AKS-cluster implementeren

Gebruik de opdracht [AZ Group Create][az-group-create] om een resource groep te maken voor het AKS-cluster.

```azurecli
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [AZ AKS Create][az-aks-create] om het AKS-cluster te implementeren. Vervang vervolgens de waarden in de volgende voorbeeld opdracht. Gebruik de waarden die zijn verzameld tijdens het maken van de Azure AD-toepassingen voor de server app-ID, het app-geheim, de client-App-ID en de Tenant-ID.

```azurecli
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --generate-ssh-keys \
  --aad-server-app-id b1536b67-29ab-4b63-b60f-9444d0c15df1 \
  --aad-server-app-secret wHYomLe2i1mHR2B3/d4sFrooHwADZccKwfoQwK2QHg= \
  --aad-client-app-id 8aaf8bd5-1bdd-4822-99ad-02bfaa63eea7 \
  --aad-tenant-id 72f988bf-0000-0000-0000-2d7cd011db47
```

Het duurt enkele minuten om een AKS-cluster te maken.

## <a name="create-an-rbac-binding"></a>Een RBAC-binding maken

> [!NOTE]
> De naam van de binding van de cluster functie is hoofdletter gevoelig.

Voordat u een Azure Active Directory-account gebruikt met een AKS-cluster, moet u een rol-binding of cluster functie-binding maken. Rollen definiëren de machtigingen die moeten worden verleend en bindingen worden toegepast op de gewenste gebruikers. Deze toewijzingen kunnen worden toegepast op een bepaalde naam ruimte of in het hele cluster. Zie [using RBAC Authorization][rbac-authorization](Engelstalig) voor meer informatie.

Gebruik eerst de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] met het `--admin` argument om u aan te melden bij het cluster met beheerders toegang.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Maak vervolgens ClusterRoleBinding voor een Azure AD-account dat u toegang wilt verlenen tot het AKS-cluster. Het volgende voor beeld geeft het account volledige toegang tot alle naam ruimten in het cluster:

- Als de gebruiker aan wie u de RBAC-binding hebt verleend, zich in dezelfde Azure AD-Tenant bevindt, wijst u machtigingen toe op basis van de user principal name (UPN). Ga naar de stap voor het maken van het YAML-manifest voor ClusterRoleBinding.

- Als de gebruiker zich in een andere Azure AD-Tenant bevindt, kunt u in plaats daarvan de eigenschap **objectId** opvragen en gebruiken. Als dat nodig is, haalt u de objectId van het vereiste gebruikers account op met behulp van de opdracht [AZ AD User show][az-ad-user-show] . Geef de user principal name (UPN) van het vereiste account op:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Maak een bestand, zoals *RBAC-Aad-User. yaml*, en plak de volgende inhoud. Vervang op de laatste regel **userPrincipalName_or_objectId** door de UPN-of object-id. De keuze is afhankelijk van of de gebruiker dezelfde Azure AD-Tenant is of niet.

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

Pas de binding toe met behulp van de opdracht [kubectl apply][kubectl-apply] , zoals weer gegeven in het volgende voor beeld:

```console
kubectl apply -f rbac-aad-user.yaml
```

Er kan ook een verbindingsrol binding worden gemaakt voor alle leden van een Azure AD-groep. Azure AD-groepen worden opgegeven met behulp van de groeps object-ID, zoals wordt weer gegeven in het volgende voor beeld.

Maak een bestand, zoals *RBAC-Aad-Group. yaml*, en plak de volgende inhoud. Werk de groeps object-ID bij met een van uw Azure AD-Tenant:

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
   kind: Group
   name: "894656e1-39f8-4bfe-b16a-510f61af6f41"
```

Pas de binding toe met behulp van de opdracht [kubectl apply][kubectl-apply] , zoals weer gegeven in het volgende voor beeld:

```console
kubectl apply -f rbac-aad-group.yaml
```

Zie [using RBAC Authorization][rbac-authorization](Engelstalig) voor meer informatie over het beveiligen van een Kubernetes-cluster met RBAC.

## <a name="access-the-cluster-with-azure-ad"></a>Toegang tot het cluster met Azure AD

Haal de context voor de niet-beheerders gebruiker op met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] .

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Nadat u de `kubectl` opdracht hebt uitgevoerd, wordt u gevraagd om u te verifiëren met behulp van Azure. Volg de instructies op het scherm om het proces te volt ooien, zoals wordt weer gegeven in het volgende voor beeld:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Wanneer het proces is voltooid, wordt het verificatie token in de cache opgeslagen. U wordt alleen gevraagd om u opnieuw aan te melden wanneer het token verloopt of het Kubernetes-configuratie bestand opnieuw wordt gemaakt.

Als er een autorisatie fout bericht wordt weer gegeven nadat u zich hebt aangemeld, controleert u de volgende criteria:

```console
error: You must be logged in to the server (Unauthorized)
```


- U hebt de juiste object-ID of UPN-naam gedefinieerd, afhankelijk van of het gebruikers account zich in dezelfde Azure AD-Tenant bevindt of niet.
- De gebruiker is geen lid van meer dan 200 groepen.
- Het geheim dat is gedefinieerd in de registratie van de toepassing voor de server, `--aad-server-app-secret`komt overeen met de waarde die is geconfigureerd met.

## <a name="next-steps"></a>Volgende stappen

Zie [toegang tot cluster bronnen beheren met op rollen gebaseerd toegangs beheer en Azure AD-identiteiten in AKS][azure-ad-rbac]voor meer informatie over het gebruik van Azure AD-gebruikers en-groepen om de toegang tot cluster bronnen te beheren.

Zie voor meer informatie over het beveiligen van Kubernetes-clusters [toegang en identiteits opties voor AKS][rbac-authorization].

Zie [Aanbevolen procedures voor verificatie en autorisatie in AKS][operator-best-practices-identity]voor meer informatie over identiteits-en resource beheer.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v1-protocols-openid-connect-code.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

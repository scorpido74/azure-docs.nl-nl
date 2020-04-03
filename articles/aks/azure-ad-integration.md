---
title: Azure Active Directory integreren met Azure Kubernetes Service
description: Aks-clusters (Azure Kubernetes Service) met Azure Kubernetes Service (Azure Kubernetes Service) maken
services: container-service
ms.topic: article
ms.date: 02/02/2019
ms.openlocfilehash: de57a46f92fab2486aa7722daf8745a01be1f4f6
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617582"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service"></a>Azure Active Directory integreren met Azure Kubernetes Service

Azure Kubernetes Service (AKS) kan worden geconfigureerd om Azure Active Directory (Azure AD) te gebruiken voor gebruikersverificatie. In deze configuratie u zich aanmelden bij een AKS-cluster met behulp van uw Azure AD-verificatietoken.

Clusterbeheerders kunnen RBAC (Kubernetes role-based access control) configureren op basis van het identiteits- of directorygroepslidmaatschap van een gebruiker.

In dit artikel wordt uitgelegd hoe u:

- Implementeer de vereisten voor AKS en Azure AD.
- Een Azure AD-cluster implementeren.
- Maak een basisRBAC-rol in het AKS-cluster met behulp van de Azure-portal.

U deze stappen ook uitvoeren met azure [cli][azure-ad-cli].

> [!NOTE]
> Azure AD kan alleen worden ingeschakeld wanneer u een nieuw cluster met RBAC maakt. U Azure AD niet inschakelen in een bestaand AKS-cluster.

## <a name="authentication-details"></a>Verificatiegegevens

Azure AD-verificatie wordt geleverd aan AKS-clusters met OpenID Connect. OpenID Connect is een identiteitslaag die is gebouwd bovenop het OAuth 2.0-protocol.

Zie [Toegang tot webtoepassingen autoriseren met OpenID Connect en Azure AD][open-id-connect]voor meer informatie over OpenID Connect.

In een Kubernetes-cluster wordt webhook-tokenverificatie gebruikt voor verificatietokens. Webhook-tokenverificatie wordt geconfigureerd en beheerd als onderdeel van het AKS-cluster.

Zie de sectie [Webhook Token Authentication][kubernetes-webhook] in Kubernetes Documentation voor meer informatie over webhook-tokenverificatie.

Als u Azure AD-verificatie wilt bieden voor een AKS-cluster, worden twee Azure AD-toepassingen gemaakt. De eerste toepassing is een servercomponent die gebruikersverificatie biedt. De tweede toepassing is een clientcomponent die wordt gebruikt wanneer u wordt gevraagd door de CLI voor verificatie. Deze clienttoepassing maakt gebruik van de servertoepassing voor de daadwerkelijke verificatie van de referenties die door de client worden verstrekt.

> [!NOTE]
> Wanneer u Azure AD configureert voor AKS-verificatie, zijn twee Azure AD-toepassingen geconfigureerd. De stappen voor het delegeren van machtigingen voor elke toepassing moeten worden voltooid door een Azure-tenantbeheerder.

## <a name="create-the-server-application"></a>De servertoepassing maken

De eerste Azure AD-toepassing wordt toegepast om het Azure AD-groepslidmaatschap van een gebruiker te krijgen. Ga als volgt te werk om deze toepassing te maken in de Azure-portal:

1. Selecteer **Azure Active Directory** > **App-registraties** > **Nieuwe registratie**.

    a. Geef de toepassing een naam, zoals *AKSAzureADServer*.

    b. Selecteer **Accounts in deze organisatiemap alleen**voor ondersteunde **accounttypen**.
    
    c. Kies **Web** voor het type URI omleiden en voer een *https://aksazureadserver*uri-opgemaakte waarde in, zoals .

    d. Selecteer **Registreren** wanneer u klaar bent.

2. Selecteer **Manifest**en bewerk vervolgens de **groupMembershipClaims:** value as **All**. Wanneer u klaar bent met de updates, selecteert u **Opslaan**.

    ![Groepslidmaatschap bijwerken voor iedereen](media/aad-integration/edit-manifest.png)

3. Selecteer **Certificaten & geheimen**in het linkerdeelvenster van de Azure AD-toepassing.

    a. Selecteer **+ Nieuw klantgeheim**.

    b. Voeg een sleutelbeschrijving toe, zoals *de AKS Azure AD-server*. Kies een vervaldatum en selecteer **Toevoegen**.

    c. Let op de sleutelwaarde, die alleen op dit moment wordt weergegeven. Wanneer u een AKS-cluster met Azure AD implementeert, wordt deze waarde het geheim van de servertoepassing genoemd.

4. Selecteer **api-machtigingen**in het linkerdeelvenster van de Azure AD-toepassing en selecteer **+ Een machtiging toevoegen**.

    a. Selecteer **Microsoft Graph**onder **Microsoft API's**.

    b. Schakel **Gedelegeerde machtigingen**in en schakel het selectievakje naast **Directory > Directory.Read.All (Mapgegevens lezen) in.**

    c. Als er geen standaard gedelegeerde machtiging voor **Gebruiker > Gebruiker.Lees (Aanmelden en gebruikersprofiel lezen)** bestaat niet, schakelt u het selectievakje naast het selectievakje in.

    d. Schakel **Toepassingsmachtigingen**in en schakel het selectievakje in naast **Directory > Directory.Read.All (Mapgegevens lezen).**

    ![Grafiekmachtigingen instellen](media/aad-integration/graph-permissions.png)

    e. Selecteer **Machtigingen toevoegen** om de updates op te slaan.

    f. Selecteer **onder Toestemming verlenen**de optie Toestemming voor beheerders **verlenen**. Deze knop is niet beschikbaar de huidige account die wordt gebruikt, wordt niet vermeld als tenantbeheerder.

    Wanneer machtigingen zijn verleend, wordt de volgende melding weergegeven in de portal:

   ![Kennisgeving van verleende machtigingen](media/aad-integration/permissions-granted.png)

5. Selecteer in het linkerdeelvenster van de Azure AD-toepassing De optie **Een API blootleggen**en selecteer **+ Een bereik toevoegen**.
    
    a. Voer een **scopenaam**in, een **weergavenaam voor beheerderstoestemming**en vervolgens een beschrijving van de **toestemming van beheerder,** zoals *AKSAzureADServer*.

    b. Controleer of **status** is ingesteld op **Ingeschakeld**.

    ![De server-app blootstellen als een API voor gebruik met andere services](media/aad-integration/expose-api.png)

    c. Selecteer **Bereik toevoegen**.

6. Ga terug naar de pagina **Overzicht** van toepassingen en noteer de **id van toepassing (client**). Wanneer u een AKS-cluster met Azure AD implementeert, wordt deze waarde de servertoepassings-id genoemd.

    ![Toepassings-id ophalen](media/aad-integration/application-id.png)

## <a name="create-the-client-application"></a>De clienttoepassing maken

De tweede Azure AD-toepassing wordt gebruikt wanneer u zich aanmeldt met de Kubernetes CLI (kubectl).

1. Selecteer **Azure Active Directory** > **App-registraties** > **Nieuwe registratie**.

    a. Geef de toepassing een naam, zoals *AKSAzureADClient*.

    b. Selecteer **Accounts in deze organisatiemap alleen**voor ondersteunde **accounttypen**.

    c. Selecteer **Web** voor het type URI omleiden en voer *https://aksazureadclient*een uri-opgemaakte waarde in, zoals .

    >[!NOTE]
    >Als u een nieuw rbac-cluster maakt om Azure Monitor voor containers te ondersteunen, voegt **Web** u de volgende twee extra omleidings-URL's toe aan deze lijst als webtoepassingstypen. De URL-waarde van `https://afd.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` de eerste basis moet zijn en de URL-waarde van de tweede basis moet`https://monitoring.hosting.portal.azure.net/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Als u deze functie in Azure China gebruikt, moet `https://afd.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html` de URL-waarde van de eerste basis zijn en moet de URL-waarde van het tweede basisbestand worden`https://monitoring.hosting.azureportal.chinaloudapi.cn/monitoring/Content/iframe/infrainsights.app/web/base-libs/auth/auth.html`
    >
    >Zie [De functie Live-gegevens (voorbeeld)](../azure-monitor/insights/container-insights-livedata-setup.md) voor Azure Monitor voor containers en de stappen voor het configureren van verificatie instellen onder de sectie [AD-geïntegreerde verificatie configureren](../azure-monitor/insights/container-insights-livedata-setup.md#configure-ad-integrated-authentication) voor meer informatie.

    d. Selecteer **Registreren** wanneer u klaar bent.

2. Selecteer **api-machtigingen**in het linkerdeelvenster van de Azure AD-toepassing en selecteer **+ Een machtiging toevoegen**.

    a. Selecteer **Mijn API's**en kies vervolgens uw Azure AD-servertoepassing die in de vorige stap is gemaakt, zoals *AKSAzureADServer*.

    b. Selecteer **Gedelegeerde machtigingen**en schakel het selectievakje in naast de Azure AD-server-app.

    ![Toepassingsmachtigingen configureren](media/aad-integration/select-api.png)

    c. Selecteer **Machtigingen toevoegen**.

    d. Selecteer **onder Toestemming verlenen**de optie Toestemming voor beheerders **verlenen**. Deze knop is niet beschikbaar als de huidige account geen tenantbeheerder is. Wanneer machtigingen worden verleend, wordt de volgende melding weergegeven in de portal:

    ![Kennisgeving van verleende machtigingen](media/aad-integration/permissions-granted.png)

3. Selecteer **Verificatie**in het linkerdeelvenster van de Azure AD-toepassing .

    - Selecteer **onder Standaardclienttype** **Ja** om de client als openbare client te **behandelen**.

5. Noteer in het linkerdeelvenster van de Azure AD-toepassing de toepassings-id. Wanneer u een AKS-cluster met Azure AD implementeert, wordt deze waarde de clienttoepassings-id genoemd.

   ![De toepassings-id ophalen](media/aad-integration/application-id-client.png)

## <a name="get-the-tenant-id"></a>De tenant-id opvragen

Ontvang vervolgens de id van uw Azure-tenant. Deze waarde wordt gebruikt wanneer u het AKS-cluster maakt.

Selecteer azure active **directory-eigenschappen** > in de Azure Active**Directory-eigenschappen** en noteer de **Map-id**. Wanneer u een AKS-cluster met Azure-AD maakt, wordt deze waarde de tenant-id genoemd.

![De Azure-tenant-id aanschaffen](media/aad-integration/tenant-id.png)

## <a name="deploy-the-aks-cluster"></a>Het AKS-cluster implementeren

Gebruik de opdracht [Az-groep maken][az-group-create] om een resourcegroep voor het AKS-cluster te maken.

```azurecli
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [az aks create][az-aks-create] om het AKS-cluster te implementeren. Vervang vervolgens de waarden in de volgende voorbeeldopdracht. Gebruik de waarden die zijn verzameld bij het maken van de Azure AD-toepassingen voor de server-app-id, app-geheim, client-app-id en tenant-id.

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

Het maken van een AKS-cluster duurt enkele minuten.

## <a name="create-an-rbac-binding"></a>Een RBAC-binding maken

> [!NOTE]
> De bindingsnaam van de clusterrol is hoofdlettergevoelig.

Voordat u een Azure Active Directory-account met een AKS-cluster gebruikt, moet u rolbinding of clusterrolbinding maken. Rollen definiëren de machtigingen voor het verlenen en bindingen passen deze toe op gewenste gebruikers. Deze toewijzingen kunnen worden toegepast op een bepaalde naamruimte of in het hele cluster. Zie [RBAC-autorisatie gebruiken][rbac-authorization]voor meer informatie .

Gebruik eerst de opdracht [az aks get-credentials][az-aks-get-credentials] met het `--admin` argument om in te loggen bij het cluster met beheerderstoegang.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Maak vervolgens ClusterRoleBinding voor een Azure AD-account dat u toegang wilt verlenen tot het AKS-cluster. In het volgende voorbeeld krijgt het account volledige toegang tot alle naamruimten in het cluster:

- Als de gebruiker waarvoor u de RBAC-binding verleent zich in dezelfde Azure AD-tenant bevindt, wijst u machtigingen toe op basis van de gebruikersnaam (UPN). Ga verder met de stap om het YAML-manifest voor ClusterRoleBinding te maken.

- Als de gebruiker zich in een andere Azure AD-tenant bevindt, moet u de eigenschap **objectId** zoeken en gebruiken. Indien nodig, krijgt de objectId van de vereiste gebruikersaccount met behulp van de [az ad user show][az-ad-user-show] commando. Geef de gebruikersnaam (UPN) van het vereiste account op:

    ```azurecli-interactive
    az ad user show --upn-or-object-id user@contoso.com --query objectId -o tsv
    ```

Maak een bestand, zoals *rbac-aad-user.yaml,* en plak vervolgens de volgende inhoud. Vervang op de laatste regel **userPrincipalName_or_objectId** door de UPN- of object-ID. De keuze is afhankelijk van of de gebruiker dezelfde Azure AD-tenant is of niet.

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

Pas de binding toe met de [opdracht kubectl apply][kubectl-apply] zoals in het volgende voorbeeld:

```console
kubectl apply -f rbac-aad-user.yaml
```

Er kan ook een rolbinding worden gemaakt voor alle leden van een Azure AD-groep. Azure AD-groepen worden opgegeven met behulp van de groepsobject-id, zoals in het volgende voorbeeld wordt weergegeven.

Maak een bestand, zoals *rbac-aad-group.yaml,* en plak vervolgens de volgende inhoud. Werk de groepsobject-id bij met een van uw Azure AD-tenant:

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

Pas de binding toe met de [opdracht kubectl apply][kubectl-apply] zoals in het volgende voorbeeld:

```console
kubectl apply -f rbac-aad-group.yaml
```

Zie [RBAC Authorization gebruiken][rbac-authorization]voor meer informatie over het beveiligen van een Kubernetes-cluster met RBAC.

## <a name="access-the-cluster-with-azure-ad"></a>Toegang tot het cluster met Azure AD

Trek de context voor de niet-admin gebruiker met behulp van de [az aks get-credentials][az-aks-get-credentials] commando.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Nadat u `kubectl` de opdracht hebt uitgevoerd, wordt u gevraagd te verifiëren met Azure. Volg de instructies op het scherm om het proces te voltooien, zoals in het volgende voorbeeld wordt weergegeven:

```console
$ kubectl get nodes

To sign in, use a web browser to open https://microsoft.com/devicelogin. Next, enter the code BUJHWDGNL to authenticate.

NAME                       STATUS    ROLES     AGE       VERSION
aks-nodepool1-79590246-0   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-1   Ready     agent     1h        v1.13.5
aks-nodepool1-79590246-2   Ready     agent     1h        v1.13.5
```

Wanneer het proces is voltooid, wordt het verificatietoken in de cache opgeslagen. U wordt alleen gevraagd om opnieuw in te loggen wanneer het token verloopt of het Kubernetes config-bestand opnieuw wordt gemaakt.

Als u een foutbericht voor autorisatie ziet nadat u zich hebt aangemeld, controleert u de volgende criteria:

```console
error: You must be logged in to the server (Unauthorized)
```


- U hebt de juiste object-id of UPN gedefinieerd, afhankelijk van of het gebruikersaccount zich in dezelfde Azure AD-tenant bevindt of niet.
- De gebruiker is geen lid van meer dan 200 groepen.
- Het geheim dat is gedefinieerd in de toepassingsregistratie `--aad-server-app-secret`voor de server komt overeen met de waarde die is geconfigureerd met behulp van .

## <a name="next-steps"></a>Volgende stappen

Als u Azure AD-gebruikers en -groepen wilt gebruiken om de toegang tot clusterbronnen te beheren, raadpleegt u [Toegang tot clusterbronnen beheren met behulp van op rollen gebaseerdtoegangsbeheer en Azure AD-identiteiten in AKS][azure-ad-rbac].

Zie [Toegangs- en identiteitsopties voor AKS voor][rbac-authorization]meer informatie over het beveiligen van Kubernetes-clusters.

Zie Aanbevolen procedures voor verificatie [en autorisatie in AKS voor][operator-best-practices-identity]meer informatie over identiteits- en bronbeheer.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md

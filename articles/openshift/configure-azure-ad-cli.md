---
title: Azure Red Hat open SHIFT met open Shift 4-Azure Active Directory-verificatie configureren via de opdracht regel
description: Meer informatie over het configureren van Azure Active Directory-verificatie voor een open Shift-cluster van Azure Red Hat met open Shift 4 met behulp van de opdracht regel
ms.service: container-service
ms.topic: article
ms.date: 03/12/2020
author: sabbour
ms.author: asabbour
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc
ms.openlocfilehash: 45da3034891e5a82fb8423adb6bcd5e867f9d4e2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204999"
---
# <a name="configure-azure-active-directory-authentication-for-an-azure-red-hat-openshift-4-cluster-cli"></a>Azure Active Directory authenticatie configureren voor een Azure Red Hat open Shift 4-cluster (CLI)

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel de Azure CLI-versie 2.0.75 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

Haal de cluster-specifieke Url's op die u wilt gebruiken om de Azure Active Directory-toepassing te configureren.

Maak de OAuth call back-URL van het cluster en sla deze op in een variabele **oauthCallbackURL**. Zorg ervoor dat u **Aro-RG** vervangt door de naam van de resource groep en het **Aro-cluster** met de naam van uw cluster.

> [!NOTE]
> De `AAD` sectie in de OAuth call back-URL moet overeenkomen met de naam van de OAuth ID-provider die u later gaat instellen.

```azurecli-interactive
domain=$(az aro show -g aro-rg -n aro-cluster --query clusterProfile.domain -o tsv)
location=$(az aro show -g aro-rg -n aro-cluster --query location -o tsv)
apiServer=$(az aro show -g aro-rg -n aro-cluster --query apiserverProfile.url -o tsv)
webConsole=$(az aro show -g aro-rg -n aro-cluster --query consoleProfile.url -o tsv)
oauthCallbackURL=https://oauth-openshift.apps.$domain.$location.aroapp.io/oauth2callback/AAD
```

## <a name="create-an-azure-active-directory-application-for-authentication"></a>Een Azure Active Directory-toepassing maken voor verificatie

Maak een Azure Active Directory-toepassing en haal de gemaakte toepassings-id op. Vervang door **\<ClientSecret>** een beveiligd wacht woord.

```azurecli-interactive
az ad app create \
  --query appId -o tsv \
  --display-name aro-auth \
  --reply-urls $oauthCallbackURL \
  --password '<ClientSecret>'
```

U zou er ongeveer als volgt terug moeten gaan. Noteer dit als de **AppId** die u nodig hebt in latere stappen.

```output
6a4cb4b2-f102-4125-b5f5-9ad6689f7224
```

Haal de Tenant-ID op van het abonnement dat eigenaar is van de toepassing.

```azure
az account show --query tenantId -o tsv
```

U zou er ongeveer als volgt terug moeten gaan. Noteer dit als de **TenantId** die u nodig hebt in latere stappen.

```output
72f999sx-8sk1-8snc-js82-2d7cj902db47
```

## <a name="create-a-manifest-file-to-define-the-optional-claims-to-include-in-the-id-token"></a>Een manifest bestand maken om de optionele claims te definiëren die moeten worden meegenomen in het ID-token

Ontwikkel aars van toepassingen kunnen [optionele claims](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims) in hun Azure AD-toepassingen gebruiken om op te geven welke claims ze willen in tokens die naar hun toepassing worden verzonden.

U kunt optionele claims gebruiken voor het volgende:

- Selecteer extra claims die moeten worden toegevoegd aan de tokens voor uw toepassing.
- Wijzig het gedrag van bepaalde claims die Azure AD retourneert in tokens.
- Aangepaste claims toevoegen en openen voor uw toepassing.

We configureren open Shift om de claim te gebruiken `email` en terug te vallen op `upn` om de voorkeurs gebruikersnaam in te stellen door het toevoegen `upn` van het id-token dat wordt geretourneerd door Azure Active Directory.

Maak een **manifest.jsvoor** het bestand om de Azure Active Directory toepassing te configureren.

```bash
cat > manifest.json<< EOF
[{
  "name": "upn",
  "source": null,
  "essential": false,
  "additionalProperties": []
},
{
"name": "email",
  "source": null,
  "essential": false,
  "additionalProperties": []
}]
EOF
```

## <a name="update-the-azure-active-directory-applications-optionalclaims-with-a-manifest"></a>De optionalClaims van de Azure Active Directory-toepassing bijwerken met een manifest

Vervang door **\<AppID>** de id die u eerder hebt ontvangen.

```azurecli-interactive
az ad app update \
  --set optionalClaims.idToken=@manifest.json \
  --id <AppId>
```

## <a name="update-the-azure-active-directory-application-scope-permissions"></a>De machtigingen voor het Azure Active Directory-toepassings bereik bijwerken

Om de gebruikers gegevens van Azure Active Directory te kunnen lezen, moeten we de juiste bereiken definiëren.

Vervang door **\<AppID>** de id die u eerder hebt ontvangen.

Voeg een machtiging toe voor het bereik van de **Azure Active Directory Graph. User. Read** om aanmelden en gebruikers profiel lezen in te scha kelen.

```azurecli-interactive
az ad app permission add \
 --api 00000002-0000-0000-c000-000000000000 \
 --api-permissions 311a71cc-e848-46a1-bdf8-97ff7156d8e6=Scope \
 --id <AppId>
```

> [!NOTE]
> Tenzij u bent geverifieerd als globale beheerder voor deze Azure Active Directory, kunt u het bericht negeren om de toestemming te verlenen, omdat u wordt gevraagd om dit te doen zodra u zich aanmeldt bij uw eigen account.

## <a name="assign-users-and-groups-to-the-cluster-optional"></a>Gebruikers en groepen toewijzen aan het cluster (optioneel)

Toepassingen die zijn geregistreerd in een Azure Active Directory-Tenant (Azure AD), zijn standaard beschikbaar voor alle gebruikers van de Tenant die zijn geverifieerd. Met Azure AD kunnen Tenant beheerders en ontwikkel aars een app beperken tot een specifieke set gebruikers of beveiligings groepen in de Tenant.

Volg de instructies in de Azure Active Directory-documentatie om [gebruikers en groepen toe te wijzen aan de app](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users#app-registration).

## <a name="configure-openshift-openid-authentication"></a>Open Shift OpenID Connect-verificatie configureren

De `kubeadmin` referenties ophalen. Voer de volgende opdracht uit om het wacht woord voor de gebruiker te zoeken `kubeadmin` .

```azurecli-interactive
az aro list-credentials \
  --name aro-cluster \
  --resource-group aro-rg
```

In de volgende voorbeeld uitvoer ziet u dat het wacht woord in wordt weer gegeven `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Meld u aan bij de API-server van het open Shift-cluster met behulp van de volgende opdracht. De `$apiServer` variabele is [eerder]()ingesteld. Vervang door **\<kubeadmin password>** het wacht woord dat u hebt opgehaald.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

Maak een open Shift-geheim om het Azure Active Directory toepassings geheim op te slaan, vervangen **\<ClientSecret>** door het geheim dat u eerder hebt opgehaald.

```azurecli-interactive
oc create secret generic openid-client-secret-azuread \
  --namespace openshift-config \
  --from-literal=clientSecret=<ClientSecret>
```    

Maak een **oidc. yaml** -bestand om Openshift OpenID Connect-verificatie te configureren tegen Azure Active Directory. Vervang **\<AppID>** en **\<TenantId>** door de waarden die u eerder hebt opgehaald.

```bash
cat > oidc.yaml<< EOF
apiVersion: config.openshift.io/v1
kind: OAuth
metadata:
  name: cluster
spec:
  identityProviders:
  - name: AAD
    mappingMethod: claim
    type: OpenID
    openID:
      clientID: <AppId>
      clientSecret: 
        name: openid-client-secret-azuread
      extraScopes: 
      - email
      - profile
      extraAuthorizeParameters: 
        include_granted_scopes: "true"
      claims:
        preferredUsername: 
        - email
        - upn
        name: 
        - name
        email: 
        - email
      issuer: https://login.microsoftonline.com/<TenantId>
EOF
```

Pas de configuratie toe op het cluster.

```azurecli-interactive
oc apply -f oidc.yaml
```

U krijgt een antwoord dat lijkt op het volgende.

```output
oauth.config.openshift.io/cluster configured
```

## <a name="verify-login-through-azure-active-directory"></a>Aanmelden via Azure Active Directory verifiëren

Als u de open Shift-webconsole nu afmeldt en zich opnieuw probeert aan te melden, wordt een nieuwe optie weer gegeven om u aan te melden met **Aad**. Mogelijk moet u enkele minuten wachten.

![Aanmeld scherm met Azure Active Directory optie](media/aro4-login-2.png)

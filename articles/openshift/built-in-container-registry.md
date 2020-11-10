---
title: Het ingebouwde container register voor Azure Red Hat open Shift 4 configureren
description: Het ingebouwde container register voor Azure Red Hat open Shift 4 configureren
author: jiangma
ms.author: jiangma
ms.service: container-service
ms.topic: conceptual
ms.date: 10/15/2020
ms.openlocfilehash: 917da58c7f5ac2294fc30cd385a4834fde3f5f0b
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414542"
---
# <a name="configure-built-in-container-registry-for-azure-red-hat-openshift-4"></a>Het ingebouwde container register voor Azure Red Hat open Shift 4 configureren

In dit artikel configureert u het ingebouwde REGI ster van de container installatie kopie voor een Azure Red Hat open Shift (ARO) 4-cluster. U leert het volgende:

> [!div class="checklist"]
> * Azure AD instellen
> * OpenID Connect Connect instellen
> * Toegang tot het ingebouwde container installatie kopie register

## <a name="prerequisites"></a>Vereisten

* Maak een cluster door de stappen te volgen in [een Azure Red Hat open Shift 4-cluster maken](/azure/openshift/tutorial-create-cluster). Zorg ervoor dat u het cluster met het `--pull-secret` argument maakt naar `az aro create` .  Dit is vereist als u Azure AD wilt instellen voor aanmelding, zoals is vereist in dit artikel.
* Maak verbinding met het cluster door de stappen te volgen in [verbinding maken met een Azure Red Hat open Shift 4-cluster](/azure/openshift/tutorial-connect-cluster).
   * Volg de stappen in ' de openhouds-CLI installeren ', omdat de `oc` opdracht later in dit artikel wordt gebruikt.
   * Noteer de URL van de cluster console, die er als volgt uitziet `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` . De waarden voor `<random>` en worden `<region>` verderop in dit artikel gebruikt.
   * Noteer de `kubeadmin` referenties. Deze worden verderop in dit artikel gebruikt.

## <a name="set-up-azure-active-directory"></a>Azure Active Directory instellen

Azure Active Directory (Azure AD) implementeert OpenID Connect Connect (OIDC). Met OIDC kunt u Azure AD gebruiken om u aan te melden bij het ARO-cluster. Volg de onderstaande stappen om Azure AD in te stellen.

1. Stel een Azure AD-Tenant in door de stappen te volgen in [Quick Start: een Tenant instellen](/azure/active-directory/develop/quickstart-create-new-tenant). Uw Azure-account heeft mogelijk al een Tenant. Als dat het geval is, kunt u het maken van een item overs Laan als u over voldoende bevoegdheden in de Tenant beschikt om de stappen te volgen. Noteer uw **Tenant-id**. Deze waarde wordt later gebruikt.
2. Maak ten minste één Azure AD-gebruiker door de stappen in [gebruikers toevoegen of verwijderen met Azure Active Directory](/azure/active-directory/fundamentals/add-users-azure-active-directory)te volgen. U kunt deze accounts of uw eigen account gebruiken om de toepassing te testen. Noteer de e-mail adressen en wacht woorden van deze accounts om u aan te melden.
3. Maak een nieuwe toepassings registratie in uw Azure AD-Tenant door de stappen in Quick Start te volgen [: een toepassing registreren bij het micro soft Identity-platform](/azure/active-directory/develop/quickstart-register-app). 
   1. Haal de opmerking op in de vereisten over de waarden voor `<random>` en `<region>` . Gebruik deze waarden om een URI te maken op basis van de volgende formule:

      `https://oauth-openshift.apps.<random>.<region>.aroapp.io/oauth2callback/openid`
   1. Wanneer u het veld stap voor **omleidings-URI** bereikt, voert u de URI uit de vorige stap in.
   1. Selecteer **Registreren**.
   1. Noteer op de pagina **overzicht** voor de app de waarde die wordt weer gegeven voor de **toepassings-id (client)** , zoals hier wordt weer gegeven.
      :::image type="content" source="media/built-in-container-registry/azure-ad-app-overview-client-id.png" alt-text="Overzichts pagina van de Azure AD-toepassing.":::

4. Maak een nieuw client geheim. 
   1. Selecteer in de zojuist gemaakte toepassing registratie de optie **certificaten & geheimen** in het navigatie deel venster links.
   1. Selecteer **Nieuw clientgeheim**.
   1. Geef **een beschrijving** op en selecteer **toevoegen**.
   1. Bespaar de gegenereerde **geheime** waarde van de client. Deze waarde wordt verderop in het artikel gebruikt.

### <a name="add-openid-connect-identity-provider"></a>OpenID Connect Connect ID-provider toevoegen

ARO biedt een ingebouwd container register.  Als u toegang wilt krijgen tot deze, configureert u een id-provider (IDP) met behulp van Azure AD OIDC door de volgende stappen uit te voeren.

1. Meld u vanuit uw browser aan bij de open Shift-webconsole `kubeadmin` .  Dit is dezelfde procedure die wordt gebruikt voor het uitvoeren van de stappen in de [zelf studie: verbinding maken met een Azure Red Hat open Shift 4-cluster](/azure/openshift/tutorial-connect-cluster).
1. Selecteer in het navigatie deel venster links de optie **beheer**  >  **cluster instellingen**.
1. Selecteer in het midden van de pagina **globale configuratie**.
1. Zoek **OAuth** in de kolom **configuratie resource** van de tabel en selecteer deze.
1. Selecteer onder **id** -providers **toevoegen** en kies **OpenID connect verbinding maken**.
1. Stel **name** in als **OpenID Connect**.  Deze waarde is mogelijk al ingevuld.
1. Stel de **client-id** en het **client geheim** in als de waarden uit de vorige stap.
1. Volg deze stap om de waarde voor de **uitgevers-URL** te vinden.
   1. Vervang door **\<tenant-id>** de sectie die is opgeslagen tijdens het **instellen van Azure Active Directory** in de URL `https://login.microsoftonline.com/<tenant-id>/v2.0/.well-known/openid-configuration` .
   1. Open de URL in de browser die u hebt gebruikt om te communiceren met de Azure Portal.
   1. Kopieer de waarde van de eigenschap **Issuer** in de GERETOURNEERDe JSON-hoofd tekst en plak deze in de tekst van de naam van de **Uitgever**.  De waarde van de **verlener** ziet er ongeveer als volgt uit `https://login.microsoftonline.com/44p4o433-2q55-474q-on88-4on94469o74n/v2.0` .
1. Ga naar de onderkant van de pagina en selecteer **toevoegen**.
   :::image type="content" source="media/built-in-container-registry/openid-connect-identity-provider.png" alt-text="OpenID connect verbinding maken in open SHIFT.":::
1. Meld u af bij de webconsole open Shift door de knop **uitvoeren: beheer** te selecteren in de rechter bovenhoek van het browser venster en vervolgens **Afmelden** te kiezen.

### <a name="access-the-built-in-container-image-registry"></a>Toegang tot het ingebouwde container installatie kopie register

Met de volgende instructies wordt toegang tot het ingebouwde REGI ster ingeschakeld.

#### <a name="define-the-azure-ad-user-to-be-an-administrator"></a>De Azure AD-gebruiker definiëren als beheerder

1. Meld u met de referenties van een Azure AD-gebruiker aan bij de open Shift-webconsole vanuit uw browser.

   1. Gebruik een InPrivate-, Incognito-of andere vergelijk bare browser venster functie om u aan te melden bij de-console.
   1. Het venster ziet er anders uit nadat OIDC is ingeschakeld.
   :::image type="content" source="media/built-in-container-registry/oidc-enabled-login-window.png" alt-text="Aanmeld venster voor OpenID connect-verbinding is ingeschakeld.":::
   1. **OpenID Connect** selecteren

   > [!NOTE]
   > Noteer de gebruikers naam en het wacht woord waarmee u zich aanmeldt. Deze gebruikers naam en dit wacht woord functioneren als beheerder voor andere acties in deze en andere artikelen.
1. Meld u aan met de open Shift CLI met behulp van de volgende stappen.  Voor discussies wordt dit proces aangeduid als `oc login` .
   1. Vouw in de rechter bovenhoek van de webconsole het context menu van de aangemelde gebruiker uit en selecteer vervolgens **aanmeldings opdracht kopiëren**.
   1. Meld u indien nodig aan bij een nieuw tabblad venster met dezelfde gebruiker.
   1. Selecteer **weergave token**.
   1. Kopieer de onderstaande waarde voor **aanmelding met dit token** naar het klem bord en voer deze uit in een shell, zoals hier wordt weer gegeven.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

1. Voer `oc whoami` uit in de-console en noteer de uitvoer als **\<aad-user>** .  We gebruiken deze waarde verderop in het artikel.
1. Afmelden bij de open Shift-webconsole. Selecteer de knop in de rechter bovenhoek van het browser venster met de naam **\<aad-user>** en kies **Afmelden**.


#### <a name="grant-the-azure-ad-user-the-necessary-roles-for-registry-interaction"></a>De Azure AD-gebruiker de benodigde rollen geven voor de interactie met het REGI ster

1. Meld u met de referenties aan bij de open Shift-webconsole vanuit uw browser `kubeadmin` .
1. Meld u aan bij de open Shift CLI met het token voor `kubeadmin` door de bovenstaande stappen te volgen `oc login` , maar probeer ze na het aanmelden bij de webconsole met `kubeadmin` .
1. Voer de volgende opdrachten uit om de toegang tot het ingebouwde REGI ster voor de **Aad-gebruiker** in te scha kelen.

   ```bash
   # Switch to project "openshift-image-registry"
   oc project openshift-image-registry
   ```

   De uitvoer moet er ongeveer als volgt uitzien.

   ```bash
   Now using project "openshift-image-registry" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   ```

   ```bash
   # Expose the registry using "DefaultRoute"
   oc patch configs.imageregistry.operator.openshift.io/cluster --patch '{"spec":{"defaultRoute":true}}' --type=merge
   ```

   De uitvoer moet er ongeveer als volgt uitzien.

   ```bash
   config.imageregistry.operator.openshift.io/cluster patched
   ```

   ```bash
   # Add roles to "aad-user" for pulling and pushing images
   # Note: replace "<aad-user>" with the one you wrote down before
   oc policy add-role-to-user registry-viewer <aad-user>
   ```

   De uitvoer moet er ongeveer als volgt uitzien.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-viewer added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

   ```bash
   oc policy add-role-to-user registry-editor <aad-user>
   ```

   De uitvoer moet er ongeveer als volgt uitzien.

   ```bash
   clusterrole.rbac.authorization.k8s.io/registry-editor added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

#### <a name="obtain-the-container-registry-url"></a>De URL van het container register ophalen

Gebruik de `oc get route` opdracht zoals hieronder wordt weer gegeven om de container Registry-URL op te halen.

```bash
# Note: the value of "Container Registry URL" in the output is the fully qualified registry name.
HOST=$(oc get route default-route --template='{{ .spec.host }}')
echo "Container Registry URL: $HOST"
```

   > [!NOTE]
   > Let op de console-uitvoer van **container Registry URL**. Deze wordt gebruikt als de volledig gekwalificeerde register naam voor deze hand leiding en volgende.

## <a name="next-steps"></a>Volgende stappen

Gebruik het ingebouwde REGI ster van de container installatie kopie door een toepassing te implementeren op open SHIFT.  Voor Java-toepassingen volgt u de hand leiding voor het [implementeren van een Java-toepassing met open vrijheid/WebSphere vrijheid op een Azure Red Hat openshift 4-cluster](howto-deploy-java-liberty-app.md).

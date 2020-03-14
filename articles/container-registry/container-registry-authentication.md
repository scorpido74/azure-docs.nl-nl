---
title: Opties voor register verificatie
description: Verificatie opties voor een persoonlijk Azure container Registry, met inbegrip van het aanmelden met een Azure Active Directory identiteit, het gebruik van service-principals en het gebruik van optionele beheerders referenties.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247044"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Verifiëren met een Azure container Registry

Er zijn verschillende manieren om te verifiëren met een Azure container Registry, die allemaal van toepassing zijn op een of meer scenario's voor het gebruik van het REGI ster.

De aanbevolen manieren zijn verificatie naar een REGI ster rechtstreeks via [afzonderlijke aanmelding](#individual-login-with-azure-ad), of uw toepassingen en container-Orchestrator kunnen zonder toezicht of ' headless ' verificatie uitvoeren met behulp van een [Service-Principal](#service-principal)voor Azure Active Directory (Azure AD).

## <a name="authentication-options"></a>Verificatieopties

De volgende tabel bevat een lijst met beschik bare verificatie methoden en aanbevolen scenario's. Zie gekoppelde inhoud voor meer informatie.

| Methode                               | Verificatie uitvoeren                                           | Scenario 's                                                            | RBAC                             | Beperkingen                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuele ad-identiteits](#individual-login-with-azure-ad)                | `az acr login` in azure CLI                             | Interactieve push/pull door ontwikkel aars, testers                                    | Ja                              | AD-token moet elke 3 uur worden vernieuwd     |
| [Ad-service-principal](#service-principal)                  | `docker login`<br/><br/>`az acr login` in azure CLI<br/><br/> Aanmeldings instellingen voor het REGI ster in Api's of hulpprogram ma's<br/><br/> [Kubernetes pull secret](container-registry-auth-kubernetes.md)                                           | Push installatie zonder toezicht van CI/CD-pijp lijn<br/><br/> Pull-bewerking zonder toezicht naar Azure of externe services  | Ja                              | Standaard verval van het SP-wacht woord is 1 jaar       |                                                           
| [Integreren met AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | REGI ster koppelen wanneer AKS-cluster is gemaakt of bijgewerkt  | Pull naar AKS-cluster zonder toezicht                                                  | Nee, alleen pull-toegang             | Alleen beschikbaar met AKS-cluster            |
| [Beheerde identiteit voor Azure-resources](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` in azure CLI                                       | Push installatie zonder toezicht van Azure CI/CD-pijp lijn<br/><br/> Pull-bewerking zonder toezicht naar Azure-Services<br/><br/>   | Ja                              | Alleen gebruiken van Azure-Services die [beheerde identiteiten voor Azure-resources ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Gebruikers  beheerder](#admin-account)                           | `docker login`                                          | Interactieve push/pull door afzonderlijke ontwikkelaar of tester                           | Nee, altijd pull-en push-toegang  | Eén account per REGI ster, niet aanbevolen voor meerdere gebruikers         |
| [Toegangs token  met opslag plaats](container-registry-repository-scoped-permissions.md)              | `docker login`<br/><br/>`az acr login` in azure CLI   | Interactieve push/pull naar opslag plaats door afzonderlijke ontwikkelaar of tester<br/><br/> Pushen zonder toezicht/pull-naar opslag plaats per afzonderlijk systeem of extern apparaat                  | Ja                              | Momenteel niet geïntegreerd met AD-identiteit  |

## <a name="individual-login-with-azure-ad"></a>Afzonderlijke aanmelding met Azure AD

Wanneer u rechtstreeks met uw REGI ster werkt, zoals het verzamelen van installatie kopieën naar en het pushen van installatie kopieën van een ontwikkel werkstation, moet u verifiëren met behulp van de opdracht [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) in de [Azure cli](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Wanneer u zich aanmeldt met `az acr login`, gebruikt de CLI het token dat is gemaakt tijdens het uitvoeren van [AZ login](/cli/azure/reference-index#az-login) om uw sessie naadloos te verifiëren met het REGI ster. Voor het volt ooien van de verificatie stroom moet docker zijn geïnstalleerd en worden uitgevoerd in uw omgeving. `az acr login` gebruikt de docker-client om een Azure Active Directory-token in het `docker.config`-bestand in te stellen. Zodra u op deze manier bent aangemeld, worden uw referenties in de cache opgeslagen en is voor volgende `docker`-opdrachten in uw sessie geen gebruikers naam of wacht woord vereist.

> [!TIP]
> Gebruik `az acr login` ook om een afzonderlijke identiteit te verifiëren wanneer u artefacten wilt pushen of verzamelen, behalve docker-installatie kopieën naar uw REGI ster, zoals [OCI-artefacten](container-registry-oci-artifacts.md).  


Voor toegang tot het REGI ster is het token dat wordt gebruikt door `az acr login` **3 uur**geldig. Daarom raden we u aan altijd aan te melden bij het REGI ster voordat u een `docker` opdracht uitvoert. Als uw token verloopt, kunt u het vernieuwen met behulp van de `az acr login` opdracht opnieuw te gebruiken om opnieuw te verifiëren. 

Het gebruik van `az acr login` met Azure-identiteiten biedt [op rollen gebaseerde toegang](../role-based-access-control/role-assignments-portal.md). Voor sommige scenario's wilt u zich mogelijk aanmelden bij een REGI ster met uw eigen identiteit in azure AD. Voor scenario's met meerdere services of voor het afhandelen van de behoeften van een werk groep of een ontwikkel werk stroom waarbij u geen individuele toegang wilt beheren, kunt u zich ook aanmelden met een [beheerde identiteit voor Azure-resources](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Service-Principal

Als u een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md) aan uw REGI ster toewijst, kan uw toepassing of service deze gebruiken voor headless-verificatie. Service-principals staan op [rollen gebaseerde toegang](../role-based-access-control/role-assignments-portal.md) tot een REGI ster toe en u kunt meerdere service-principals aan een REGI ster toewijzen. Met meerdere service-principals kunt u verschillende toegangs rechten definiëren voor verschillende toepassingen.

De beschik bare rollen voor een container register zijn onder andere:

* **AcrPull**: pull

* **AcrPush**: pull en push

* **Eigenaar**: rollen pullen, pushen en toewijzen aan andere gebruikers

Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met rollen.

Zie [Azure container Registry verificatie met Service-principals](container-registry-auth-service-principal.md)voor CLI-scripts voor het maken van een service-principal voor verificatie met een Azure container Registry en meer informatie.

## <a name="admin-account"></a>Beheerders account

Elk container register bevat een beheer gebruikers account dat standaard is uitgeschakeld. U kunt de gebruiker beheerder inschakelen en de referenties beheren in de Azure Portal, of door gebruik te maken van de Azure CLI-of andere Azure-hulpprogram ma's.

> [!IMPORTANT]
> Het beheerders account is ontworpen voor één gebruiker voor toegang tot het REGI ster, voornamelijk voor test doeleinden. Het is niet raadzaam om de referenties van het beheerders account te delen met meerdere gebruikers. Alle gebruikers die worden geverifieerd met het beheerders account, worden weer gegeven als één gebruiker met push-en pull-toegang tot het REGI ster. Als u dit account wijzigt of uitschakelt, wordt de toegang tot het REGI ster uitgeschakeld voor alle gebruikers die de referenties gebruiken. Individuele identiteiten worden aanbevolen voor gebruikers en service-principals voor headless scenario's.
>

Het beheerders account wordt weer gegeven met twee wacht woorden, die beide opnieuw kunnen worden gegenereerd. Met twee wacht woorden kunt u de verbinding met het REGI ster onderhouden door één wacht woord te gebruiken terwijl u het andere opnieuw genereert. Als het beheerders account is ingeschakeld, kunt u de gebruikers naam en het wacht woord door geven aan de opdracht `docker login` wanneer u wordt gevraagd om basis verificatie naar het REGI ster. Bijvoorbeeld:

```
docker login myregistry.azurecr.io 
```

Zie voor aanbevolen procedures voor het beheren van aanmeldings referenties de naslag informatie voor het [Aanmelden bij docker](https://docs.docker.com/engine/reference/commandline/login/) -opdrachten.

Als u de gebruiker met beheerders rechten wilt inschakelen voor een bestaand REGI ster, kunt u de para meter `--admin-enabled` van de opdracht [AZ ACR update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) gebruiken in de Azure cli:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

U kunt de gebruiker beheerder in de Azure Portal inschakelen door te navigeren door uw REGI ster, **toegangs sleutels** te selecteren onder **instellingen**en vervolgens in te **scha kelen** onder **gebruiker met beheerders**rechten.

![Gebruikers interface van de beheerder inschakelen in de Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste installatie kopie pushen met behulp van Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

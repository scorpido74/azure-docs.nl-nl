---
title: Opties voor registerverificatie
description: Verificatieopties voor een privé-Azure-containerregister, waaronder aanmelden bij een Azure Active Directory-identiteit, het gebruik van serviceprincipals en het gebruik van optionele beheerdersreferenties.
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 5459ac29c1264b18404cb2863b9d4209907ac029
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247044"
---
# <a name="authenticate-with-an-azure-container-registry"></a>Verifiëren met een Azure-containerregister

Er zijn verschillende manieren om te verifiëren met een Azure-containerregister, die elk van toepassing zijn op een of meer scenario's voor registergebruik.

Aanbevolen manieren omvatten het rechtstreeks authenticeren naar een register via [afzonderlijke aanmelding,](#individual-login-with-azure-ad)of uw toepassingen en containerorchestrators kunnen zonder toezicht of 'headless' worden uitgevoerd met behulp van een Azure AD-serviceprincipal (Azure [Directory).](#service-principal)

## <a name="authentication-options"></a>Verificatieopties

In de volgende tabel worden beschikbare verificatiemethoden en aanbevolen scenario's weergegeven. Zie gekoppelde inhoud voor meer informatie.

| Methode                               | Authenticeren                                           | Scenario's                                                            | RBAC                             | Beperkingen                                |
|---------------------------------------|-------------------------------------------------------|---------------------------------------------------------------------|----------------------------------|--------------------------------------------|
| [Individuele AD-identiteit](#individual-login-with-azure-ad)                | `az acr login` in Azure CLI                             | Interactieve push/pull door ontwikkelaars, testers                                    | Ja                              | AD-token moet elke 3 uur worden vernieuwd     |
| [AD-serviceprincipal](#service-principal)                  | `docker login`<br/><br/>`az acr login`in Azure CLI<br/><br/> Registratie-instellingen in API's of tooling<br/><br/> [Kubernetes trekt geheim](container-registry-auth-kubernetes.md)                                           | Onbeheerde push van CI/CD-pijplijn<br/><br/> Onbeheerde pull naar Azure of externe services  | Ja                              | SP wachtwoord standaard verlopen is 1 jaar       |                                                           
| [Integreren met AKS](../aks/cluster-container-registry-integration.md?toc=/azure/container-registry/toc.json&bc=/azure/container-registry/breadcrumb/toc.json)                    | Register bijvoegen wanneer AKS-cluster is gemaakt of bijgewerkt  | Onbeheerde pull naar AKS-cluster                                                  | Nee, pull toegang alleen             | Alleen beschikbaar met AKS-cluster            |
| [Beheerde identiteit voor Azure-bronnen](container-registry-authentication-managed-identity.md)  | `docker login`<br/><br/> `az acr login` in Azure CLI                                       | Onbeheerde push van Azure CI/CD-pijplijn<br/><br/> Onbeheerde pull naar Azure-services<br/><br/>   | Ja                              | Alleen gebruik maken van Azure-services die [beheerde identiteiten voor Azure-bronnen ondersteunen](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-managed-identities-for-azure-resources)              |
| [Beheerdersgebruiker](#admin-account)                            | `docker login`                                          | Interactieve push/pull door individuele ontwikkelaar of tester                           | Nee, altijd pull and push toegang  | Eén account per register, niet aanbevolen voor meerdere gebruikers         |
| [Toegangstoken met repository-scoped](container-registry-repository-scoped-permissions.md)               | `docker login`<br/><br/>`az acr login`in Azure CLI   | Interactieve push/pull naar repository door individuele ontwikkelaar of tester<br/><br/> Onbeheerde push/pull naar repository door individueel systeem of extern apparaat                  | Ja                              | Momenteel niet geïntegreerd met AD-identiteit  |

## <a name="individual-login-with-azure-ad"></a>Individuele aanmelding met Azure AD

Wanneer u rechtstreeks met uw register werkt, zoals het trekken van afbeeldingen naar en het pushen van afbeeldingen van een ontwikkelingswerkstation, verifieert u met behulp van de az [acr-aanmeldingsopdracht](/cli/azure/acr?view=azure-cli-latest#az-acr-login) in de [Azure CLI:](/cli/azure/install-azure-cli)

```azurecli
az acr login --name <acrName>
```

Wanneer u zich `az acr login`aanmeldt bij , gebruikt de CLI het token dat is gemaakt wanneer u [az-aanmelding](/cli/azure/reference-index#az-login) uitvoerde om uw sessie naadloos te verifiëren met uw register. Om de verificatiestroom te voltooien, moet Docker worden geïnstalleerd en in uw omgeving worden uitgevoerd. `az acr login`gebruikt de Docker-client om een Azure `docker.config` Active Directory-token in het bestand in te stellen. Zodra u zich op deze manier hebt aangemeld, worden `docker` uw referenties in de cache opgeslagen en hebben volgende opdrachten in uw sessie geen gebruikersnaam of wachtwoord nodig.

> [!TIP]
> Ook `az acr login` gebruiken om een individuele identiteit te verifiëren wanneer u wilt duwen of trekken artefacten andere dan Docker afbeeldingen naar uw register, zoals [OCI artefacten](container-registry-oci-artifacts.md).  


Voor registertoegang is het `az acr login` token dat wordt gebruikt 3 **uur**geldig, dus we `docker` raden u aan altijd in te loggen bij het register voordat u een opdracht uitvoert. Als uw token verloopt, u `az acr login` het vernieuwen door de opdracht opnieuw te gebruiken om opnieuw te verifiëren. 

Het `az acr login` gebruik met [Azure-identiteiten biedt op rollen gebaseerde toegang.](../role-based-access-control/role-assignments-portal.md) Voor sommige scenario's u zich aanmelden bij een register met uw eigen individuele identiteit in Azure AD. Voor cross-servicescenario's of om te voldoen aan de behoeften van een werkgroep of een ontwikkelingsworkflow waarbij u geen individuele toegang wilt beheren, u zich ook aanmelden met een [beheerde identiteit voor Azure-resources.](container-registry-authentication-managed-identity.md)

## <a name="service-principal"></a>Service-principal

Als u een [serviceprincipal](../active-directory/develop/app-objects-and-service-principals.md) aan uw register toewijst, kan uw toepassing of service deze gebruiken voor headless-verificatie. Serviceprincipals bieden [op rollen gebaseerde toegang tot](../role-based-access-control/role-assignments-portal.md) een register toe en u meerdere serviceprincipals toewijzen aan een register. Met meerdere serviceprincipals u verschillende toegangen voor verschillende toepassingen definiëren.

De beschikbare rollen voor een containerregister zijn:

* **AcrPull**: trek

* **AcrPush**: trekken en duwen

* **Eigenaar**: rollen trekken, pushen en toewijzen aan andere gebruikers

Zie [Azure Container Registry-rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met rollen.

Zie [Azure Container Registry-verificatie met serviceprincipals](container-registry-auth-service-principal.md)voor het maken van een serviceprincipal voor het verifiëren met een Azure-containerregister en meer richtlijnen.

## <a name="admin-account"></a>Beheerdersaccount

Elk containerregister bevat een beheerdersgebruikersaccount, dat standaard is uitgeschakeld. U de beheerdersgebruiker inschakelen en de referenties ervan beheren in de Azure-portal of met behulp van de Azure CLI- of andere Azure-hulpprogramma's.

> [!IMPORTANT]
> Het beheerdersaccount is ontworpen voor één gebruiker om toegang te krijgen tot het register, voornamelijk voor testdoeleinden. We raden u af om de beheerdersaccountgegevens onder meerdere gebruikers te delen. Alle gebruikers die authenticeren met het beheerdersaccount worden weergegeven als één gebruiker met push- en pull-toegang tot het register. Als u dit account wijzigt of uitschakelt, wordt de toegang tot het register uitgeschakeld voor alle gebruikers die de referenties gebruiken. Individuele identiteit wordt aanbevolen voor gebruikers en serviceprincipals voor headless scenario's.
>

Het beheerdersaccount is voorzien van twee wachtwoorden, die beide kunnen worden geregenereerd. Met twee wachtwoorden u de verbinding met het register onderhouden met behulp van het ene wachtwoord terwijl u het andere regenereert. Als het beheerdersaccount is ingeschakeld, u de `docker login` gebruikersnaam en het wachtwoord doorgeven aan de opdracht wanneer u wordt gevraagd om basisverificatie aan het register. Bijvoorbeeld:

```
docker login myregistry.azurecr.io 
```

Zie de referentie voor de [inlogopdracht voor docker.](https://docs.docker.com/engine/reference/commandline/login/)

Als u de beheerdergebruiker voor een bestaand `--admin-enabled` register wilt inschakelen, u de parameter van de opdracht [az acr-update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) gebruiken in de Azure CLI:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

U de beheerdergebruiker in de Azure-portal inschakelen door door door uw register te navigeren en **Toegangssleutels** te selecteren onder **INSTELLINGEN**en vervolgens **Inschakelen** onder **Beheerder-gebruiker**.

![Gebruikersinterface van beheerdersgebruikers inschakelen in de Azure-portal][auth-portal-01]

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste afbeelding pushen met de Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

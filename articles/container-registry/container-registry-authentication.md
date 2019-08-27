---
title: Verifiëren met een Azure container Registry
description: Verificatie opties voor een Azure container Registry, met inbegrip van het aanmelden met een Azure Active Directory identiteit, het gebruik van service-principals en het gebruik van optionele beheerders referenties.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 12/21/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a55cba27c676b283a4da490f05dd6fc672e10d49
ms.sourcegitcommit: 94ee81a728f1d55d71827ea356ed9847943f7397
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70032392"
---
# <a name="authenticate-with-a-private-docker-container-registry"></a>Verifiëren met een privé-docker-container register

Er zijn verschillende manieren om te verifiëren met een Azure container Registry, die allemaal van toepassing zijn op een of meer scenario's voor het gebruik van het REGI ster.

De aanbevolen manieren zijn verificatie naar een REGI ster rechtstreeks via [afzonderlijke aanmelding](#individual-login-with-azure-ad), of uw toepassingen en container-Orchestrator kunnen zonder toezicht of "headless" verificatie uitvoeren met behulp van een Azure Active Directory (Azure AD)- [service Principal](#service-principal).

## <a name="individual-login-with-azure-ad"></a>Afzonderlijke aanmelding met Azure AD

Wanneer u rechtstreeks met uw REGI ster werkt, zoals het verzamelen van installatie kopieën naar en het pushen van installatie kopieën van een ontwikkel werkstation, moet u verifiëren met behulp van de opdracht [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) in de [Azure cli](/cli/azure/install-azure-cli):

```azurecli
az acr login --name <acrName>
```

Wanneer u zich aanmeldt met `az acr login`, gebruikt de CLI het token dat is gemaakt tijdens het uitvoeren van [AZ login](/cli/azure/reference-index#az-login) om uw sessie naadloos te verifiëren met uw REGI ster. Zodra u op deze manier bent aangemeld, worden uw referenties in de cache opgeslagen en `docker` zijn voor volgende opdrachten in uw sessie geen gebruikers naam of wacht woord vereist. 

Voor toegang tot het REGI ster is het `az acr login` token dat wordt gebruikt door 1 uur geldig. u wordt daarom aangeraden altijd aan te melden bij het `docker` REGI ster voordat u een opdracht uitvoert. Als uw token verloopt, kunt u het vernieuwen met behulp van de `az acr login` opdracht opnieuw om opnieuw te verifiëren. 

Het `az acr login` gebruik van met Azure-identiteiten biedt [toegang op basis van rollen](../role-based-access-control/role-assignments-portal.md). Voor sommige scenario's wilt u zich mogelijk aanmelden bij een REGI ster met uw eigen identiteit in azure AD. Voor scenario's met meerdere services of voor het afhandelen van de behoeften van een werk groep waarbij u afzonderlijke toegang niet wilt beheren, kunt u zich ook aanmelden met een [beheerde identiteit voor Azure-resources](container-registry-authentication-managed-identity.md).

## <a name="service-principal"></a>Service-principal

Als u een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md) aan uw REGI ster toewijst, kan uw toepassing of service deze gebruiken voor headless-verificatie. Service-principals staan op [rollen gebaseerde toegang](../role-based-access-control/role-assignments-portal.md) tot een REGI ster toe en u kunt meerdere service-principals aan een REGI ster toewijzen. Met meerdere service-principals kunt u verschillende toegangs rechten definiëren voor verschillende toepassingen.

De beschik bare rollen voor een container register zijn onder andere:

* **AcrPull**: pull

* **AcrPush**: pull en push

* **Eigenaar**: rollen pullen, pushen en toewijzen aan andere gebruikers

Zie [Azure container Registry rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met rollen.

Zie [Azure container Registry verificatie met Service](container-registry-auth-service-principal.md)-principals voor CLI-scripts voor het maken van een service-principal voor verificatie met een Azure container Registry en richt lijnen voor het gebruik van een service-principal.

## <a name="admin-account"></a>Beheeraccount

Elk container register bevat een beheer gebruikers account dat standaard is uitgeschakeld. U kunt de gebruiker beheerder inschakelen en de referenties beheren in de Azure Portal, of door gebruik te maken van de Azure CLI-of andere Azure-hulpprogram ma's.

> [!IMPORTANT]
> Het beheerders account is ontworpen voor één gebruiker voor toegang tot het REGI ster, voornamelijk voor test doeleinden. Het is niet raadzaam om de referenties van het beheerders account te delen met meerdere gebruikers. Alle gebruikers die worden geverifieerd met het beheerders account, worden weer gegeven als één gebruiker met push-en pull-toegang tot het REGI ster. Als u dit account wijzigt of uitschakelt, wordt de toegang tot het REGI ster uitgeschakeld voor alle gebruikers die de referenties gebruiken. Individuele identiteiten worden aanbevolen voor gebruikers en service-principals voor headless scenario's.
>

Het beheerders account wordt weer gegeven met twee wacht woorden, die beide opnieuw kunnen worden gegenereerd. Met twee wacht woorden kunt u de verbinding met het REGI ster onderhouden door één wacht woord te gebruiken terwijl u het andere opnieuw genereert. Als het beheerders account is ingeschakeld, kunt u de gebruikers naam en het wacht woord door `docker login` geven aan de opdracht wanneer u wordt gevraagd om basis verificatie naar het REGI ster. Bijvoorbeeld:

```
docker login myregistry.azurecr.io 
```

Zie voor aanbevolen procedures voor het beheren van aanmeldings referenties de naslag informatie voor het [Aanmelden](https://docs.docker.com/engine/reference/commandline/login/) bij docker-opdrachten.

Als u de gebruiker met beheerders rechten wilt inschakelen voor een bestaand REGI ster `--admin-enabled` , kunt u de para meter van de opdracht [AZ ACR update](/cli/azure/acr?view=azure-cli-latest#az-acr-update) gebruiken in de Azure cli:

```azurecli
az acr update -n <acrName> --admin-enabled true
```

U kunt de gebruiker beheerder in de Azure Portal inschakelen door te navigeren door uw REGI ster, **toegangs sleutels** te selecteren onder **instellingen**en vervolgens in te **scha kelen** onder **gebruiker met beheerders**rechten.

![Gebruikers interface van de beheerder inschakelen in de Azure Portal][auth-portal-01]

## <a name="next-steps"></a>Volgende stappen

* [Uw eerste installatie kopie pushen met behulp van Azure CLI](container-registry-get-started-azure-cli.md)

<!-- IMAGES -->
[auth-portal-01]: ./media/container-registry-authentication/auth-portal-01.png

---
title: RBAC-rollen en-machtigingen
description: Gebruik Azure op rollen gebaseerd toegangs beheer (RBAC) en identiteits-en toegangs beheer (IAM) om nauw keurige machtigingen te bieden aan bronnen in een Azure container Registry.
ms.topic: article
ms.date: 03/20/2019
ms.openlocfilehash: 8ef4f26dfd59c7b3b177ef58fa23e08f7e66d328
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456232"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Rollen en machtigingen Azure Container Registry

De Azure Container Registry-service ondersteunt een set Azure-rollen die verschillende machtigings niveaus bieden voor een Azure container Registry. Gebruik Azure op [rollen gebaseerd toegangs beheer](../role-based-access-control/index.yml) (RBAC) om specifieke machtigingen toe te wijzen aan gebruikers of service-principals die moeten communiceren met een REGI ster.

| Rol/machtiging       | [Toegang tot Resource Manager](#access-resource-manager) | [REGI ster maken/verwijderen](#create-and-delete-registry) | [Push-installatie kopie](#push-image) | [Pull-afbeelding](#pull-image) | [Afbeeldings gegevens verwijderen](#delete-image-data) | [Beleid wijzigen](#change-policies) |   [Installatie kopieën ondertekenen](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Eigenaar | X | X | X | X | X | X |  |  
| Inzender | X | X | X |  X | X | X |  |  
| Lezer | X |  |  | X |  |  |  |
| AcrPush |  |  | X | X | |  |  |  
| AcrPull |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Gebruikers en services onderscheiden

Telkens wanneer machtigingen worden toegepast, moet een best practice de meeste beperkte machtigingen voor een persoon of service opgeven voor het uitvoeren van een taak. De volgende machtigingen sets vertegenwoordigen een reeks mogelijkheden die door mensen en headless Services kunnen worden gebruikt.

### <a name="cicd-solutions"></a>CI/CD-oplossingen

Wanneer u `docker build` opdrachten van CI/CD-oplossingen automatiseert, hebt u `docker push` mogelijkheden nodig. Voor deze headless service scenario's wordt u aangeraden de functie **AcrPush** toe te wijzen. Deze rol, in tegens telling tot de bredere rol **Inzender** , voor komt dat het account andere register bewerkingen uitvoert of Azure Resource Manager.

### <a name="container-host-nodes"></a>Container knooppunten

Knoop punten waarop uw containers worden uitgevoerd, hebben ook de rol **AcrPull** nodig, maar hoeven geen **lezers** mogelijkheden te vereisen.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio code docker-extensie

Voor hulpprogram ma's zoals de Visual Studio code [docker-extensie](https://code.visualstudio.com/docs/azure/docker)is aanvullende toegang tot de bron provider vereist om de beschik bare Azure-container registers weer te geven. Geef in dit geval uw gebruikers toegang tot de rol **lezer** of **Inzender** . Deze rollen staan `docker pull`, `docker push`, `az acr list`, `az acr build`en andere mogelijkheden toe. 

## <a name="access-resource-manager"></a>Toegang tot Resource Manager

Azure Resource Manager toegang is vereist voor de Azure Portal en het register beheer met de [Azure cli](/cli/azure/). Als u bijvoorbeeld een lijst met registers wilt ophalen met behulp van de `az acr list` opdracht, moet u deze machtigingenset hebben. 

## <a name="create-and-delete-registry"></a>REGI ster maken en verwijderen

De mogelijkheid om Azure-container registers te maken en verwijderen.

## <a name="push-image"></a>Push-installatie kopie

De mogelijkheid om een installatie kopie te `docker push` of een ander [ondersteund artefact](container-registry-image-formats.md) , zoals een helm-diagram, naar een REGI ster te pushen. [Verificatie](container-registry-authentication.md) met het REGI ster vereist met behulp van de geautoriseerde identiteit. 

## <a name="pull-image"></a>Pull-afbeelding

De mogelijkheid om een niet-in quarantaine geplaatste installatie kopie te `docker pull` of een ander [ondersteund artefact](container-registry-image-formats.md) , zoals een helm-diagram, op te halen uit een REGI ster. [Verificatie](container-registry-authentication.md) met het REGI ster vereist met behulp van de geautoriseerde identiteit.

## <a name="delete-image-data"></a>Afbeeldings gegevens verwijderen

De mogelijkheid om [container installatie kopieën te verwijderen](container-registry-delete.md)of andere [ondersteunde artefacten](container-registry-image-formats.md) , zoals helm-grafieken, te verwijderen uit een REGI ster.

## <a name="change-policies"></a>Beleid wijzigen

De mogelijkheid om beleid te configureren in een REGI ster. Beleids regels zijn onder andere het opschonen van afbeeldingen, het inschakelen van quarantaine en het ondertekenen van installatie kopieën.

## <a name="sign-images"></a>Installatie kopieën ondertekenen

De mogelijkheid om installatie kopieën te ondertekenen, die meestal worden toegewezen aan een geautomatiseerd proces, waarbij een service-principal wordt gebruikt. Deze machtiging wordt doorgaans gecombineerd met een [push installatie kopie](#push-image) , zodat een vertrouwde installatie kopie naar een REGI ster kan worden gepusht. Zie voor meer informatie [vertrouwen in inhoud in azure container Registry](container-registry-content-trust.md).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het toewijzen van RBAC-rollen aan een Azure-identiteit met behulp van de [Azure Portal](../role-based-access-control/role-assignments-portal.md), de [Azure cli](../role-based-access-control/role-assignments-cli.md)of andere Azure-hulpprogram ma's.

* Meer informatie over [verificatie opties](container-registry-authentication.md) voor Azure container Registry.

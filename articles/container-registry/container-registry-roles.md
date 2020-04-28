---
title: RBAC-rollen en-machtigingen
description: Gebruik Azure op rollen gebaseerd toegangs beheer (RBAC) en identiteits-en toegangs beheer (IAM) om nauw keurige machtigingen te bieden aan bronnen in een Azure container Registry.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74893481"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Rollen en machtigingen Azure Container Registry

De Azure Container Registry-service ondersteunt een aantal [ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md) die verschillende machtigings niveaus bieden voor een Azure container Registry. Gebruik Azure op [rollen gebaseerd toegangs beheer](../role-based-access-control/index.yml) (RBAC) om specifieke machtigingen toe te wijzen aan gebruikers, service-principals of andere identiteiten die moeten communiceren met een REGI ster. 

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

Wanneer u `docker build` opdrachten van CI/cd-oplossingen automatiseert, `docker push` hebt u mogelijkheden nodig. Voor deze headless service scenario's wordt u aangeraden de functie **AcrPush** toe te wijzen. Deze rol, in tegens telling tot de bredere rol **Inzender** , voor komt dat het account andere register bewerkingen uitvoert of Azure Resource Manager.

### <a name="container-host-nodes"></a>Container knooppunten

Knoop punten waarop uw containers worden uitgevoerd, hebben ook de rol **AcrPull** nodig, maar hoeven geen **lezers** mogelijkheden te vereisen.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio code docker-extensie

Voor hulpprogram ma's zoals de Visual Studio code [docker-extensie](https://code.visualstudio.com/docs/azure/docker)is aanvullende toegang tot de bron provider vereist om de beschik bare Azure-container registers weer te geven. Geef in dit geval uw gebruikers toegang tot de rol **lezer** of **Inzender** . Deze rollen bieden `docker pull`, `docker push`, `az acr list` `az acr build`, en andere mogelijkheden. 

## <a name="access-resource-manager"></a>Toegang tot Resource Manager

Azure Resource Manager toegang is vereist voor de Azure Portal en het register beheer met de [Azure cli](/cli/azure/). Als u bijvoorbeeld een lijst met registers wilt ophalen met behulp van de `az acr list` opdracht, moet u deze machtiging instellen. 

## <a name="create-and-delete-registry"></a>REGI ster maken en verwijderen

De mogelijkheid om Azure-container registers te maken en verwijderen.

## <a name="push-image"></a>Push-installatie kopie

De mogelijkheid om `docker push` een installatie kopie te plaatsen of een ander [ondersteund artefact](container-registry-image-formats.md) , zoals een helm-diagram, naar een REGI ster te pushen. [Verificatie](container-registry-authentication.md) met het REGI ster vereist met behulp van de geautoriseerde identiteit. 

## <a name="pull-image"></a>Pull-afbeelding

De mogelijkheid om `docker pull` een niet-quarantaine installatie kopie te maken of een ander [ondersteund artefact](container-registry-image-formats.md) , zoals een helm-diagram, op te halen uit een REGI ster. [Verificatie](container-registry-authentication.md) met het REGI ster vereist met behulp van de geautoriseerde identiteit.

## <a name="delete-image-data"></a>Afbeeldings gegevens verwijderen

De mogelijkheid om [container installatie kopieën te verwijderen](container-registry-delete.md)of andere [ondersteunde artefacten](container-registry-image-formats.md) , zoals helm-grafieken, te verwijderen uit een REGI ster.

## <a name="change-policies"></a>Beleid wijzigen

De mogelijkheid om beleid te configureren in een REGI ster. Beleids regels zijn onder andere het opschonen van afbeeldingen, het inschakelen van quarantaine en het ondertekenen van installatie kopieën.

## <a name="sign-images"></a>Installatie kopieën ondertekenen

De mogelijkheid om installatie kopieën te ondertekenen, die meestal worden toegewezen aan een geautomatiseerd proces, waarbij een service-principal wordt gebruikt. Deze machtiging wordt doorgaans gecombineerd met een [push installatie kopie](#push-image) , zodat een vertrouwde installatie kopie naar een REGI ster kan worden gepusht. Zie voor meer informatie [vertrouwen in inhoud in azure container Registry](container-registry-content-trust.md).

## <a name="custom-roles"></a>Aangepaste rollen

Net als bij andere Azure-resources kunt u uw eigen [aangepaste rollen](../role-based-access-control/custom-roles.md) maken met verfijnde machtigingen voor Azure container Registry. Wijs vervolgens de aangepaste rollen toe aan gebruikers, service-principals of andere identiteiten die moeten communiceren met een REGI ster. 

Als u wilt bepalen welke machtigingen u wilt Toep assen op een aangepaste rol, raadpleegt u de lijst met micro soft. ContainerRegistry- [acties](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry), controleert u de toegestane acties van de [ingebouwde ACR-rollen](../role-based-access-control/built-in-roles.md)of voert u de volgende opdracht uit:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Zie [stappen voor het maken](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)van een aangepaste rol voor het definiëren van een aangepaste rol.

> [!IMPORTANT]
> In een aangepaste rol ondersteunt Azure Container Registry momenteel geen joker tekens zoals `Microsoft.ContainerRegistry/*` of `Microsoft.ContainerRegistry/registries/*` waarmee toegang wordt verleend aan alle overeenkomende acties. Geef elke vereiste actie afzonderlijk op in de rol.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het toewijzen van RBAC-rollen aan een Azure-identiteit met behulp van de [Azure Portal](../role-based-access-control/role-assignments-portal.md), de [Azure cli](../role-based-access-control/role-assignments-cli.md)of andere Azure-hulpprogram ma's.

* Meer informatie over [verificatie opties](container-registry-authentication.md) voor Azure container Registry.

* Meer informatie over het inschakelen van [machtigingen voor opslagplaatsen](container-registry-repository-scoped-permissions.md) (preview) in een container register.
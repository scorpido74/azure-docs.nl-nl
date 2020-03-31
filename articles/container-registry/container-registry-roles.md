---
title: RBAC-rollen en -machtigingen
description: Gebruik RBAC (Azure role-based access control) en identity and access management (IAM) om fijnmazige machtigingen te bieden aan resources in een Azure-containerregister.
ms.topic: article
ms.date: 12/02/2019
ms.openlocfilehash: 3fb103ac4c4dac736b3c0fc99b2cf49f01e9e005
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74893481"
---
# <a name="azure-container-registry-roles-and-permissions"></a>Rollen en machtigingen voor Azure Container Registry

De Azure Container Registry-service ondersteunt een set [ingebouwde Azure-rollen](../role-based-access-control/built-in-roles.md) die verschillende niveaus van machtigingen bieden voor een Azure-containerregister. Gebruik [RBAC (Azure role-based access control)](../role-based-access-control/index.yml) om specifieke machtigingen toe te wijzen aan gebruikers, serviceprincipals of andere identiteiten die moeten communiceren met een register. 

| Rol/machtiging       | [Access Resource Manager](#access-resource-manager) | [Register maken/verwijderen](#create-and-delete-registry) | [Afbeelding pushen](#push-image) | [Afbeelding trekken](#pull-image) | [Afbeeldingsgegevens verwijderen](#delete-image-data) | [Beleid wijzigen](#change-policies) |   [Afbeeldingen ondertekenen](#sign-images)  |
| ---------| --------- | --------- | --------- | --------- | --------- | --------- | --------- |
| Eigenaar | X | X | X | X | X | X |  |  
| Inzender | X | X | X |  X | X | X |  |  
| Lezer | X |  |  | X |  |  |  |
| AcrPush AcrPush |  |  | X | X | |  |  |  
| AcrPull (AcrPull) |  |  |  | X |  |  |  |  
| AcrDelete |  |  |  |  | X |  |  |
| AcrImageSigner |  |  |  |  |  |  | X |

## <a name="differentiate-users-and-services"></a>Gebruikers en services differentiëren

Elke keer dat machtigingen worden toegepast, is een aanbevolen toepassing om de meest beperkte set machtigingen voor een persoon of service te bieden om een taak uit te voeren. De volgende machtigingssets vertegenwoordigen een reeks mogelijkheden die door mensen en services zonder hoofd kunnen worden gebruikt.

### <a name="cicd-solutions"></a>CI/CD-oplossingen

Bij het `docker build` automatiseren van opdrachten vanuit `docker push` CI/CD-oplossingen hebt u mogelijkheden nodig. Voor deze headless servicescenario's raden we u aan de **AcrPush-rol** toe te wijzen. Deze rol voorkomt, in tegenstelling tot de bredere **rol inzender,** dat het account andere registerbewerkingen uitvoert of toegang krijgt tot Azure Resource Manager.

### <a name="container-host-nodes"></a>Containerhostknooppunten

Op dezelfde manier hebben knooppunten met uw containers de **AcrPull-rol** nodig, maar hoeven ze geen **Reader-mogelijkheden** te vereisen.

### <a name="visual-studio-code-docker-extension"></a>Visual Studio Code Docker-extensie

Voor hulpprogramma's zoals de Visual Studio Code [Docker-extensie](https://code.visualstudio.com/docs/azure/docker)is extra toegang tot de resourceprovider vereist om de beschikbare Azure-containerregisters weer te geven. Geef uw gebruikers in dit geval toegang tot de rol **Reader** of **Contributor.** Deze rollen `docker pull` `docker push`maken `az acr list` `az acr build`, , , en andere mogelijkheden mogelijk. 

## <a name="access-resource-manager"></a>Access Resource Manager

Azure Resource Manager-toegang is vereist voor de Azure-portal en het registerbeheer met de [Azure CLI.](/cli/azure/) Als u bijvoorbeeld een lijst met registers `az acr list` wilt krijgen met behulp van de opdracht, hebt u deze machtigingsset nodig. 

## <a name="create-and-delete-registry"></a>Register maken en verwijderen

De mogelijkheid om Azure-containerregisters te maken en te verwijderen.

## <a name="push-image"></a>Afbeelding pushen

De mogelijkheid `docker push` om een afbeelding te maken of een ander [ondersteund artefact,](container-registry-image-formats.md) zoals een Helm-diagram, naar een register te duwen. Vereist [verificatie](container-registry-authentication.md) met het register met behulp van de geautoriseerde identiteit. 

## <a name="pull-image"></a>Afbeelding trekken

De mogelijkheid `docker pull` om een niet-in quarantaine geplaatste afbeelding, of trek een ander [ondersteund artefact,](container-registry-image-formats.md) zoals een Helm grafiek, uit een register. Vereist [verificatie](container-registry-authentication.md) met het register met behulp van de geautoriseerde identiteit.

## <a name="delete-image-data"></a>Afbeeldingsgegevens verwijderen

De mogelijkheid om [containerafbeeldingen](container-registry-delete.md)te verwijderen of andere [ondersteunde artefacten](container-registry-image-formats.md) zoals Helmdiagrammen uit een register te verwijderen.

## <a name="change-policies"></a>Beleid wijzigen

De mogelijkheid om beleidsregels op een register te configureren. Beleidsregels omvatten het zuiveren van afbeeldingen, het inschakelen van quarantaine en het ondertekenen van afbeeldingen.

## <a name="sign-images"></a>Afbeeldingen ondertekenen

De mogelijkheid om afbeeldingen te ondertekenen, meestal toegewezen aan een geautomatiseerd proces, dat een serviceprincipal zou gebruiken. Deze toestemming wordt meestal gecombineerd met [pushafbeelding](#push-image) om een vertrouwde afbeelding naar een register te duwen. Zie [Inhoudsvertrouwensrelatie in Azure Container Registry](container-registry-content-trust.md)voor meer informatie.

## <a name="custom-roles"></a>Aangepaste rollen

Net als bij andere Azure-bronnen u uw eigen [aangepaste rollen](../role-based-access-control/custom-roles.md) maken met fijnmazige machtigingen voor Azure Container Registry. Wijs de aangepaste rollen vervolgens toe aan gebruikers, serviceprincipals of andere identiteiten die moeten communiceren met een register. 

Zie de lijst met [acties](../role-based-access-control/resource-provider-operations.md#microsoftcontainerregistry)voor Microsoft.ContainerRegistry, de toegestane acties van de [ingebouwde ACR-rollen](../role-based-access-control/built-in-roles.md)of voer de volgende opdracht uit om te bepalen welke machtigingen u moet toepassen op een aangepaste rol:

```azurecli
az provider operation show --namespace Microsoft.ContainerRegistry
```

Zie [Stappen om een aangepaste rol te maken](../role-based-access-control/custom-roles.md#steps-to-create-a-custom-role)als u een aangepaste rol wilt definiëren.

> [!IMPORTANT]
> In een aangepaste rol ondersteunt Azure Container Registry momenteel `Microsoft.ContainerRegistry/*` `Microsoft.ContainerRegistry/registries/*` geen jokertekens zoals of die toegang verlenen tot alle overeenkomende acties. Geef de vereiste actie afzonderlijk op in de rol.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het toewijzen van RBAC-rollen aan een Azure-identiteit met behulp van de [Azure-portal,](../role-based-access-control/role-assignments-portal.md)de [Azure CLI](../role-based-access-control/role-assignments-cli.md)of andere Azure-hulpprogramma's.

* Meer informatie over [verificatieopties](container-registry-authentication.md) voor Azure Container Registry.

* Meer informatie over het inschakelen van machtigingen met [opslagplaatsen](container-registry-repository-scoped-permissions.md) (voorbeeld) in een containerregister.
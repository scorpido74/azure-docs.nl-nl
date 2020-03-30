---
title: Beheerde identiteit in ACR-taak
description: Schakel een beheerde identiteit in voor Azure Resources in een Azure Container Registry-taak om de taak toegang te geven tot andere Azure-bronnen, waaronder andere registers voor privécontainers.
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 01/14/2020
ms.author: danlep
ms.openlocfilehash: f3294698f6973437a23fab798e8daf5642cc9b49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77111761"
---
# <a name="use-an-azure-managed-identity-in-acr-tasks"></a>Een door Azure beheerde identiteit gebruiken in ACR-taken 

Schakel een [beheerde identiteit in voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md) in een [ACR-taak](container-registry-tasks-overview.md)in, zodat de taak toegang heeft tot andere Azure-resources, zonder dat u referenties hoeft op te geven of te beheren. Gebruik bijvoorbeeld een beheerde identiteit om een taakstap in te schakelen om containerafbeeldingen naar een ander register te trekken of te pushen.

In dit artikel leert u hoe u de Azure CLI gebruiken om een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit in te schakelen voor een ACR-taak. U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken. Als u het lokaal wilt gebruiken, is versie 2.0.68 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install].

Ter illustratie maken de voorbeeldopdrachten in dit artikel gebruik van [az acr-taak om][az-acr-task-create] een basistaak voor het bouwen van afbeeldingen te maken die een beheerde identiteit mogelijk maakt. Zie voor voorbeeldscenario's om toegang te krijgen tot beveiligde bronnen uit een ACR-taak met behulp van een beheerde identiteit:

* [Verificatie tussen meerdere registers](container-registry-tasks-cross-registry-authentication.md)
* [Externe bronnen openen met geheimen die zijn opgeslagen in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)

## <a name="why-use-a-managed-identity"></a>Waarom een beheerde identiteit gebruiken?

Een beheerde identiteit voor Azure-resources biedt geselecteerde Azure-services met een automatisch beheerde identiteit in Azure Active Directory. U een ACR-taak configureren met een beheerde identiteit, zodat de taak toegang heeft tot andere beveiligde Azure-resources, zonder referenties door te geven in de taakstappen.

Beheerde identiteiten zijn van twee soorten:

* *Door de gebruiker toegewezen identiteiten*, die u aan meerdere resources toewijzen en zo lang als u wilt blijven bestaan. Door de gebruiker toegewezen identiteiten bevinden zich momenteel in een voorbeeld.

* Een door het systeem toegewezen identiteit , die uniek is voor een specifieke resource, zoals een *ACR-taak*en die de levensduur van die resource duurt.

U een of beide typen identiteiten inschakelen in een ACR-taak. Geef de identiteit toegang tot een andere bron, net als elke beveiligingsprincipal. Wanneer de taak wordt uitgevoerd, wordt de identiteit gebruikt om toegang te krijgen tot de bron in alle taakstappen die toegang vereisen.

## <a name="steps-to-use-a-managed-identity"></a>Stappen om een beheerde identiteit te gebruiken

Volg deze stappen op hoog niveau om een beheerde identiteit te gebruiken met een ACR-taak.

### <a name="1-optional-create-a-user-assigned-identity"></a>1. (Optioneel) Een door de gebruiker toegewezen identiteit maken

Als u van plan bent een door de gebruiker toegewezen identiteit te gebruiken, een bestaande identiteit te gebruiken of de identiteit te maken met behulp van de Azure CLI- of andere Azure-hulpprogramma's. Gebruik bijvoorbeeld de opdracht [AZ-identiteit maken.][az-identity-create] 

Als u alleen een door het systeem toegewezen identiteit wilt gebruiken, slaat u deze stap over. U maakt een door het systeem toegewezen identiteit wanneer u de ACR-taak maakt.

### <a name="2-enable-identity-on-an-acr-task"></a>2. Identiteit inschakelen voor een ACR-taak

Wanneer u een ACR-taak maakt, schakelt u optioneel een door de gebruiker toegewezen identiteit, een door het systeem toegewezen identiteit of beide in. Geef bijvoorbeeld de `--assign-identity` parameter door wanneer u de opdracht [az acr-taakmaken][az-acr-task-create] uitvoert in de Azure CLI.

Als u een door het `--assign-identity` systeem toegewezen `assign-identity [system]`identiteit wilt inschakelen, geeft u door zonder waarde of . Met de volgende voorbeeldopdracht wordt een Linux-taak gemaakt `hello-world` vanuit een openbare GitHub-opslagplaats die de afbeelding bouwt en een door het systeem toegewezen beheerde identiteit mogelijk maakt:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false \
    --assign-identity
```

Als u een door de `--assign-identity` gebruiker toegewezen identiteit wilt inschakelen, geeft u een waarde van de *resource-id* van de identiteit door. Met de volgende voorbeeldopdracht wordt een Linux-taak gemaakt `hello-world` vanuit een openbare GitHub-opslagplaats die de afbeelding bouwt en een door de gebruiker toegewezen beheerde identiteit mogelijk maakt:

```azurecli
az acr task create \
    --image hello-world:{{.Run.ID}} \
    --name hello-world --registry MyRegistry \
    --context https://github.com/Azure-Samples/acr-build-helloworld-node.git \
    --file Dockerfile \
    --commit-trigger-enabled false
    --assign-identity <resourceID>
```

U de resource-ID van de identiteit krijgen door de opdracht [AZ-identiteitsshow uit te][az-identity-show] voeren. De bron-id voor de ID *myUserAssignedIdentity* in resourcegroep *myResourceGroup* is van het formulier: 

```
"/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity"
```

### <a name="3-grant-the-identity-permissions-to-access-other-azure-resources"></a>3. De identiteitsmachtigingen verlenen om toegang te krijgen tot andere Azure-bronnen

Afhankelijk van de vereisten van uw taak, verleent u de identiteitsmachtigingen om toegang te krijgen tot andere Azure-bronnen. Voorbeelden zijn:

* Wijs de beheerde identiteit een rol toe met pull, push en pull of andere machtigingen aan een doelcontainerregister in Azure. Zie [Azure Container Registry-rollen en machtigingen](container-registry-roles.md)voor een volledige lijst met registerrollen. 
* Wijs de beheerde identiteit een rol toe om geheimen in een Azure-sleutelkluis te lezen.

Gebruik de [Azure CLI-](../role-based-access-control/role-assignments-cli.md) of andere Azure-hulpprogramma's om op rollen gebaseerde toegang tot resources te beheren. Voer bijvoorbeeld de opdracht [voor het maken van az-rollen uit][az-role-assignment-create] om de identiteit een rol aan de resource toe te wijzen. 

In het volgende voorbeeld wordt een beheerde identiteit de machtigingen toegegeven die u uit een containerregister wilt halen. De opdracht geeft de *hoofd-id* van de taakidentiteit en de *resource-id* van het doelregister op.


```azurecli
az role assignment create \
  --assignee <principalID> \
  --scope <registryID> \
  --role acrpull
```

### <a name="4-optional-add-credentials-to-the-task"></a>4. (Optioneel) Referenties toevoegen aan de taak

Als uw taak referenties nodig heeft om afbeeldingen naar een ander aangepast register te trekken of te pushen, of om toegang te krijgen tot andere bronnen, voegt u referenties toe aan de taak. Voer de opdracht [az acr-taakreferenties toe][az-acr-task-credential-add] om `--use-identity` referenties toe te voegen en geef de parameter door om aan te geven dat de identiteit toegang heeft tot de referenties. 

Als u bijvoorbeeld referenties wilt toevoegen voor een door het systeem toegewezen `use-identity [system]`identiteit om te verifiëren met het *azure-containerregister,* passeert u:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity [system]
```

Als u referenties wilt toevoegen voor een door de gebruiker `use-identity` toegewezen identiteit om te verifiëren met het *registerdoelregister,* passeert u een waarde van de *client-id* van de identiteit. Bijvoorbeeld:

```azurecli
az acr task credential add \
    --name helloworld \
    --registry myregistry \
    --login-server targetregistry.azurecr.io \
    --use-identity <clientID>
```

U de client-ID van de identiteit krijgen door het commando [az-identiteitsshow uit te][az-identity-show] voeren. De client-ID is een `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx`GUID van het formulier .

### <a name="5-run-the-task"></a>5. De taak uitvoeren

Nadat u een taak met een beheerde identiteit hebt geconfigureerd, voert u de taak uit. Als u bijvoorbeeld een van de taken in dit artikel wilt testen, activeert u deze handmatig met behulp van de opdracht [AZ ACR-taakuitvoeren.][az-acr-task-run] Als u aanvullende, geautomatiseerde taaktriggers hebt geconfigureerd, wordt de taak automatisch geactiveerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit inschakelt of gebruikt voor een ACR-taak. Zie voor scenario's om toegang te krijgen tot beveiligde bronnen van een ACR-taak met behulp van een beheerde identiteit:

* [Verificatie tussen meerdere registers](container-registry-tasks-cross-registry-authentication.md)
* [Externe bronnen openen met geheimen die zijn opgeslagen in Azure Key Vault](container-registry-tasks-authentication-key-vault.md)


<!-- LINKS - Internal -->
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[azure-cli-install]: /cli/azure/install-azure-cli

---
title: Externe verificatie van ACR-taak
description: Configureer een Azure Container Registry Task (ACR Task) om Docker Hub-referenties te lezen die zijn opgeslagen in een Azure-sleutelkluis, met behulp van een beheerde identiteit voor Azure-bronnen.
ms.topic: article
ms.date: 01/14/2020
ms.openlocfilehash: 47d3d643ee1287ef4f444095a2c6cfe6dcab294b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76842517"
---
# <a name="external-authentication-in-an-acr-task-using-an-azure-managed-identity"></a>Externe verificatie in een ACR-taak met een door Azure beheerde identiteit 

In een [ACR-taak](container-registry-tasks-overview.md)u [een beheerde identiteit voor Azure-resources inschakelen.](container-registry-tasks-authentication-managed-identity.md) De taak kan de identiteit gebruiken om toegang te krijgen tot andere Azure-bronnen, zonder dat u referenties hoeft op te geven of te beheren. 

In dit artikel leert u hoe u een beheerde identiteit inschakelt in een taak die toegang krijgt tot geheimen die zijn opgeslagen in een Azure-sleutelkluis. 

Als u de Azure-resources wilt maken, moet u in dit artikel de Azure CLI-versie 2.0.68 of hoger uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli].

## <a name="scenario-overview"></a>Overzicht van scenario's

In de voorbeeldtaak worden Docker Hub-referenties gelezen die zijn opgeslagen in een Azure-sleutelkluis. De referenties zijn voor een Docker Hub-account met schrijfmachtigingen (push) naar een private Docker Hub-repository. Als u de referenties wilt lezen, configureert u de taak met een beheerde identiteit en wijst u de juiste machtigingen toe. De taak die aan de identiteit is gekoppeld, bouwt een afbeelding en tekent in Docker Hub om de afbeelding naar de privérepo te duwen. 

In dit voorbeeld worden stappen weergegeven met een door de gebruiker toegewezen of door het systeem toegewezen beheerde identiteit. Uw identiteitskeuze is afhankelijk van de behoeften van uw organisatie.

In een real-world scenario kan een bedrijf afbeeldingen publiceren naar een privérepo in Docker Hub als onderdeel van een buildproces. 

## <a name="prerequisites"></a>Vereisten

U hebt een Azure-containerregister nodig waarin u de taak uitvoert. In dit artikel wordt dit register *myregistry*genoemd. Vervang in latere stappen door uw eigen registernaam.

Zie [Quickstart: Een privécontainerregister maken met azure cli](container-registry-get-started-azure-cli.md)als u nog geen Azure-containerregister hebt. U hoeft nog geen afbeeldingen naar het register te pushen.

Je hebt ook een privé-opslagplaats in Docker Hub nodig en een Docker Hub-account met machtigingen om naar de repo te schrijven. In dit voorbeeld wordt deze repo *hubuser/hubrepo*genoemd. 

## <a name="create-a-key-vault-and-store-secrets"></a>Maak een sleutelkluis en bewaar geheimen

Maak eerst, als dat nodig is, een resourcegroep met de naam *myResourceGroup* op de *locatie Eastus* met de volgende opdracht [az-groep maken:][az-group-create]

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Gebruik de opdracht [AZ Keyvault create][az-keyvault-create] om een sleutelkluis te maken. Zorg ervoor dat u een unieke naam van de sleutelkluis opgeeft. 

```azurecli-interactive
az keyvault create --name mykeyvault --resource-group myResourceGroup --location eastus
```

Bewaar de vereiste Docker Hub-referenties in de sleutelkluis met de geheime [setopdracht az keyvault.][az-keyvault-secret-set] In deze opdrachten worden de waarden doorgegeven in omgevingsvariabelen:

```azurecli
# Store Docker Hub user name
az keyvault secret set \
  --name UserName \
  --value $USERNAME \
  --vault-name mykeyvault

# Store Docker Hub password
az keyvault secret set \
  --name Password \
  --value $PASSWORD \
  --vault-name mykeyvault
```

In een real-world scenario, geheimen zou waarschijnlijk worden ingesteld en onderhouden in een apart proces.

## <a name="define-task-steps-in-yaml-file"></a>Taakstappen definiëren in YAML-bestand

De stappen voor deze voorbeeldtaak worden gedefinieerd in een [YAML-bestand](container-registry-tasks-reference-yaml.md). Maak een `dockerhubtask.yaml` bestand met de naam in een lokale werkmap en plak de volgende inhoud. Zorg ervoor dat u de naam van de sleutelkluis in het bestand vervangt door de naam van uw sleutelkluis.

```yml
version: v1.1.0
# Replace mykeyvault with the name of your key vault
secrets:
  - id: username
    keyvault: https://mykeyvault.vault.azure.net/secrets/UserName
  - id: password
    keyvault: https://mykeyvault.vault.azure.net/secrets/Password
steps:
# Log in to Docker Hub
  - cmd: bash echo '{{.Secrets.password}}' | docker login --username '{{.Secrets.username}}' --password-stdin 
# Build image
  - build: -t {{.Values.PrivateRepo}}:$ID https://github.com/Azure-Samples/acr-tasks.git -f hello-world.dockerfile
# Push image to private repo in Docker Hub
  - push:
    - {{.Values.PrivateRepo}}:$ID
```

De taakstappen gaan als volgt te werk:

* Geheime referenties beheren om te verifiëren met Docker Hub.
* Verifieer met Docker Hub door `docker login` de geheimen door te geven aan de opdracht.
* Een afbeelding maken met een voorbeeld dockerfile in de repo [Azure-Samples/acr-tasks.](https://github.com/Azure-Samples/acr-tasks.git)
* Duw de afbeelding naar de privé Docker Hub-opslagplaats.


## <a name="option-1-create-task-with-user-assigned-identity"></a>Optie 1: Taak maken met door de gebruiker toegewezen identiteit

De stappen in deze sectie maken een taak en schakelen een door de gebruiker toegewezen identiteit in. Zie [Optie 2: Taak maken met een door het systeem toegewezen identiteit](#option-2-create-task-with-system-assigned-identity)als u in plaats daarvan een door het systeem toegewezen identiteit wilt inschakelen. 

[!INCLUDE [container-registry-tasks-user-assigned-id](../../includes/container-registry-tasks-user-assigned-id.md)]

### <a name="create-task"></a>Taak maken

Maak de *taakdockerhubtask* door de volgende [opdracht az acr-taak te maken][az-acr-task-create] uit te voeren. De taak wordt uitgevoerd zonder broncodecontext en `dockerhubtask.yaml` de opdracht verwijst naar het bestand in de werkmap. De `--assign-identity` parameter geeft de resource-id van de door de gebruiker toegewezen identiteit door. 

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity $resourceID
```

[!INCLUDE [container-registry-tasks-user-id-properties](../../includes/container-registry-tasks-user-id-properties.md)]

## <a name="option-2-create-task-with-system-assigned-identity"></a>Optie 2: Taak maken met door het systeem toegewezen identiteit

De stappen in deze sectie maken een taak en schakelen een door het systeem toegewezen identiteit in. Zie [Optie 1: Taak maken met door de gebruiker toegewezen identiteit](#option-1-create-task-with-user-assigned-identity)als u in plaats daarvan een door de gebruiker toegewezen identiteit wilt inschakelen. 

### <a name="create-task"></a>Taak maken

Maak de *taakdockerhubtask* door de volgende [opdracht az acr-taak te maken][az-acr-task-create] uit te voeren. De taak wordt uitgevoerd zonder broncodecontext en `dockerhubtask.yaml` de opdracht verwijst naar het bestand in de werkmap. De `--assign-identity` parameter zonder waarde maakt de door het systeem toegewezen identiteit op de taak mogelijk.  

```azurecli
az acr task create \
  --name dockerhubtask \
  --registry myregistry \
  --context /dev/null \
  --file dockerhubtask.yaml \
  --assign-identity 
```

[!INCLUDE [container-registry-tasks-system-id-properties](../../includes/container-registry-tasks-system-id-properties.md)]

## <a name="grant-identity-access-to-key-vault"></a>Identiteitstoegang verlenen tot sleutelkluis

Voer de volgende opdracht [voor het sleutelkluisbeleid van AZ][az-keyvault-set-policy] uit om een toegangsbeleid in te stellen voor de sleutelkluis. In het volgende voorbeeld kan de identiteit geheimen uit de sleutelkluis lezen. 

```azurecli
az keyvault set-policy --name mykeyvault \
  --resource-group myResourceGroup \
  --object-id $principalID \
  --secret-permissions get
```

## <a name="manually-run-the-task"></a>De taak handmatig uitvoeren

Als u wilt controleren of de taak waarin u een beheerde identiteit hebt ingeschakeld, succesvol wordt uitgevoerd, activeert u de taak handmatig met de opdracht [AZ ACR-taakuitvoeren.][az-acr-task-run] De `--set` parameter wordt gebruikt om de naam privé-repo door te geven aan de taak. In dit voorbeeld is de naam tijdelijke aanduiding repo *hubuser/hubrepo*.

```azurecli
az acr task run --name dockerhubtask --registry myregistry --set PrivateRepo=hubuser/hubrepo
```

Wanneer de taak succesvol wordt uitgevoerd, wordt met uitvoer succesvolle verificatie naar Docker Hub weergegeven en wordt de afbeelding met succes gebouwd en naar de privérepo geschoven:

```console
Queued a run with ID: cf24
Waiting for an agent...
2019/06/20 18:05:55 Using acb_vol_b1edae11-30de-4f2b-a9c7-7d743e811101 as the home volume
2019/06/20 18:05:58 Creating Docker network: acb_default_network, driver: 'bridge'
2019/06/20 18:05:58 Successfully set up Docker network: acb_default_network
2019/06/20 18:05:58 Setting up Docker configuration...
2019/06/20 18:05:59 Successfully set up Docker configuration
2019/06/20 18:05:59 Logging in to registry: myregistry.azurecr.io
2019/06/20 18:06:00 Successfully logged into myregistry.azurecr.io
2019/06/20 18:06:00 Executing step ID: acb_step_0. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:00 Launching container with name: acb_step_0
[...]
Login Succeeded
2019/06/20 18:06:02 Successfully executed container: acb_step_0
2019/06/20 18:06:02 Executing step ID: acb_step_1. Timeout(sec): 600, Working directory: '', Network: 'acb_default_network'
2019/06/20 18:06:02 Scanning for dependencies...
2019/06/20 18:06:04 Successfully scanned dependencies
2019/06/20 18:06:04 Launching container with name: acb_step_1
Sending build context to Docker daemon    129kB
[...]
2019/06/20 18:06:07 Successfully pushed image: hubuser/hubrepo:cf24
2019/06/20 18:06:07 Step ID: acb_step_0 marked as successful (elapsed time in seconds: 2.064353)
2019/06/20 18:06:07 Step ID: acb_step_1 marked as successful (elapsed time in seconds: 2.594061)
2019/06/20 18:06:07 Populating digests for step ID: acb_step_1...
2019/06/20 18:06:09 Successfully populated digests for step ID: acb_step_1
2019/06/20 18:06:09 Step ID: acb_step_2 marked as successful (elapsed time in seconds: 2.743923)
2019/06/20 18:06:09 The following dependencies were found:
2019/06/20 18:06:09
- image:
    registry: registry.hub.docker.com
    repository: hubuser/hubrepo
    tag: cf24
    digest: sha256:92c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a
  runtime-dependency:
    registry: registry.hub.docker.com
    repository: library/hello-world
    tag: latest
    digest: sha256:0e11c388b664df8a27a901dce21eb89f11d8292f7fca1b3e3c4321bf7897bffe
  git:
    git-head-revision: b0ffa6043dd893a4c75644c5fed384c82ebb5f9e

Run ID: cf24 was successful after 15s
```

Als u wilt bevestigen dat de`cf24` afbeelding wordt gepusht, controleert u op de tag (in dit voorbeeld) in de privé-Docker Hub-repo.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [het inschakelen van een beheerde identiteit in een ACR-taak](container-registry-tasks-authentication-managed-identity.md).
* Zie de [ACR-taken YAML-referentie](container-registry-tasks-reference-yaml.md)


<!-- LINKS - Internal -->
[az-login]: /cli/azure/reference-index#az-login
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-build]: /cli/azure/acr#az-acr-build
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az-acr-repository-show-tags
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show
[az-acr-task-run]: /cli/azure/acr/task#az-acr-task-run
[az-acr-task-list-runs]: /cli/azure/acr/task#az-acr-task-list-runs
[az-acr-task-credential-add]: /cli/azure/acr/task/credential#az-acr-task-credential-add
[az-group-create]: /cli/azure/group?#az-group-create
[az-keyvault-create]: /cli/azure/keyvault?#az-keyvault-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy

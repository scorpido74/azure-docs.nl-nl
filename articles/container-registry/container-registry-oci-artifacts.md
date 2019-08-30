---
title: OCI-artefacten pushen naar privé Azure container Registry
description: Open container Initiative-artefacten pushen en pullen met behulp van een persoonlijk container register in azure
services: container-registry
author: SteveLasker
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 08/30/2019
ms.author: stevelas
ms.custom: ''
ms.openlocfilehash: 69423f85aecdc3f8049a7e784888e1f71d0bc702
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70182708"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Een OCI-artefact pushen en ophalen met behulp van een Azure container Registry

U kunt een Azure container Registry gebruiken om [OCI-artefacten (open container Initiative)](container-registry-image-formats.md#oci-artifacts) op te slaan en te beheren, evenals docker en docker-compatibele container installatie kopieën.

Om deze functie te demonstreren, wordt in dit artikel uitgelegd hoe u het [Oras-hulp programma OCI gebruiken als opslag](https://github.com/deislabs/oras) voor het pushen van een voorbeeld artefact-een tekst bestand-naar een Azure container Registry. Haal vervolgens het artefact uit het REGI ster. U kunt verschillende OCI-artefacten beheren in een Azure container Registry met behulp van verschillende opdracht regel Programma's die geschikt zijn voor elk artefact.

## <a name="prerequisites"></a>Vereisten

* **Azure-containerregister**: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure Portal](container-registry-get-started-portal.md) of de [Azure cli](container-registry-get-started-azure-cli.md).
* **Oras-hulp programma** : down load en installeer een huidige Oras-versie voor uw besturings systeem van de [github opslag plaats](https://github.com/deislabs/oras/releases). Het hulp programma wordt uitgebracht als een gecomprimeerd tarball`.tar.gz` (bestand). Pak het bestand uit met behulp van standaard procedures voor uw besturings systeem en installeer het.
* **Azure Active Directory Service-Principal (optioneel)** : Maak een [Service-Principal](container-registry-auth-service-principal.md) om toegang te krijgen tot uw REGI ster als u direct met Oras wilt verifiëren. Zorg ervoor dat aan de service-principal een rol is toegewezen, zoals AcrPush, zodat deze machtigingen heeft voor push-en pull-artefacten.
* **Azure cli (optioneel)** : als u een afzonderlijke identiteit wilt gebruiken, moet u een lokale installatie van de Azure cli. Versie 2.0.71 of hoger wordt aanbevolen. Voer `az --version `uit om de versie te vinden. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.
* **Docker (optioneel)** : als u een afzonderlijke identiteit wilt gebruiken, moet u ook docker lokaal hebben geïnstalleerd om te verifiëren met het REGI ster. Docker biedt pakketten waarmee docker eenvoudig kan worden geconfigureerd op een [macOS][docker-mac]-, [Windows][docker-windows]-of [Linux][docker-linux] -systeem.


## <a name="sign-in-to-a-registry"></a>Aanmelden bij een REGI ster

In deze sectie worden twee voorgestelde werk stromen weer gegeven om u aan te melden bij het REGI ster, afhankelijk van de gebruikte identiteit. Kies de methode die geschikt is voor uw omgeving.

### <a name="sign-in-with-oras"></a>Aanmelden met ORAS

Met behulp van een [Service-Principal](container-registry-auth-service-principal.md) met push `oras login` rechten voert u de opdracht uit om u aan te melden bij het REGI ster met de toepassings-id en het wacht woord van de Service-Principal. Geef de volledig gekwalificeerde register naam (alle kleine letters) op, in dit geval *myregistry.azurecr.io*. De id van de Service-Principal-toepassing wordt door `$SP_APP_ID`gegeven in de omgevings variabele en `$SP_PASSWD`het wacht woord in de variabele.

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Als u het wacht woord van stdin wilt `--password-stdin`lezen, gebruikt u.

### <a name="sign-in-with-azure-cli"></a>Aanmelden met Azure CLI

[Meld](/cli/azure/authenticate-azure-cli) u aan bij de Azure CLI met uw identiteit om artefacten te pushen en te pullen vanuit het container register.

Gebruik vervolgens de Azure CLI-opdracht [AZ ACR login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) om toegang te krijgen tot het REGI ster. Als u bijvoorbeeld wilt verifiëren naar een REGI ster met de naam *myregistry*:

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`maakt gebruik van de docker-client om een Azure Active Directory- `docker.config` token in het bestand in te stellen. De docker-client moet zijn geïnstalleerd en worden uitgevoerd om de afzonderlijke verificatie stroom te volt ooien.

## <a name="push-an-artifact"></a>Een artefact pushen

Maak een tekst bestand in een lokale werkmap met een bepaalde voorbeeld tekst. Bijvoorbeeld in een bash-shell:

```bash
echo "Here is an artifact!" > artifact.txt
```

Gebruik de `oras push` opdracht om dit tekst bestand naar uw REGI ster te pushen. In het volgende voor beeld wordt het voorbeeld tekst bestand naar `samples/artifact` de opslag plaats gepusht. Het REGI ster wordt geïdentificeerd met de volledig gekwalificeerde register naam *myregistry.azurecr.io* (alle kleine letters). Het artefact is gelabeld `1.0`. Het artefact heeft een niet-gedefinieerd type, standaard aangeduid door de teken reeks voor het *media type* na de `artifact.txt`bestands naam. Zie [OCI](https://github.com/opencontainers/artifacts) -artefacten voor aanvullende typen. 

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

Uitvoer voor een succes volle push lijkt op het volgende:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Als u de artefacten in uw REGI ster wilt beheren, kunt u, als u de `az acr` Azure cli gebruikt, standaard opdrachten uitvoeren voor het beheren van installatie kopieën. U kunt bijvoorbeeld de kenmerken van het artefact ophalen met de opdracht [AZ ACR repository show][az-acr-repository-show] :

```azurecli
az acr repository show \
    --name myregistry \
    --image samples/artifact:1.0
```

De uitvoer lijkt op het volgende:

```json
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2019-08-28T20:43:31.0001687Z",
  "digest": "sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx",
  "lastUpdateTime": "2019-08-28T20:43:31.0001687Z",
  "name": "1.0",
  "signed": false
}
```

## <a name="pull-an-artifact"></a>Een artefact ophalen

Voer de `oras pull` opdracht uit om het artefact uit het REGI ster op te halen.

Verwijder eerst het tekst bestand uit uw lokale werkmap:

```bash
rm artifact.txt
```

Voer `oras pull` uit om het artefact op te halen en geef het media type op dat wordt gebruikt om het artefact te pushen:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Controleer of de pull is geslaagd:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Het artefact verwijderen (optioneel)

Als u het artefact uit uw Azure container Registry wilt verwijderen, gebruikt u de opdracht [AZ ACR repository delete][az-acr-repository-delete] . In het volgende voor beeld wordt het artefact verwijderd dat u hebt opgeslagen:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [de Oras-bibliotheek](https://github.com/deislabs/oras/tree/master/docs), inclusief het configureren van een manifest voor een artefact
* Ga naar de [OCI](https://github.com/opencontainers/artifacts) -artefacten opslag plaats voor naslag informatie over nieuwe artefact typen



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete

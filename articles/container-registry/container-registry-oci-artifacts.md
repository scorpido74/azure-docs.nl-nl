---
title: Duwen en trekken OCI artefact
description: OCI-artefacten (Open Container Initiative) pushen en trekken met behulp van een privécontainerregister in Azure
author: SteveLasker
manager: gwallace
ms.topic: article
ms.date: 03/11/2020
ms.author: stevelas
ms.openlocfilehash: 2c6b66b635a2513ccc19e0352414d18d8389fef1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371049"
---
# <a name="push-and-pull-an-oci-artifact-using-an-azure-container-registry"></a>Een OCI-artefact pushen en trekken met behulp van een Azure-containerregister

U een Azure-containerregister gebruiken om [OCI-artefacten (Open Container Initiative)](container-registry-image-formats.md#oci-artifacts) op te slaan en te beheren, evenals containerafbeeldingen die compatibel zijn met Docker en Docker.

Om deze mogelijkheid aan te tonen, laat dit artikel zien hoe u het [GEREEDSCHAP OCI Registry as Storage (ORAS)](https://github.com/deislabs/oras) gebruiken om een voorbeeldartefact - een tekstbestand - naar een Azure-containerregister te duwen. Haal vervolgens het artefact uit het register. U verschillende OCI-artefacten beheren in een Azure-containerregister met behulp van verschillende opdrachtregelhulpprogramma's die geschikt zijn voor elk artefact.

## <a name="prerequisites"></a>Vereisten

* **Azure-containerregister**: maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md).
* **ORAS-tool** - Download en installeer een huidige ORAS-release voor uw besturingssysteem vanaf de [GitHub repo.](https://github.com/deislabs/oras/releases) De tool wordt uitgebracht als een`.tar.gz` gecomprimeerde tarball (bestand). Pak en installeer het bestand met behulp van standaardprocedures voor uw besturingssysteem.
* **Azure Active Directory-serviceprincipal (optioneel)** - Maak een [serviceprincipal](container-registry-auth-service-principal.md) om toegang te krijgen tot uw register om rechtstreeks met ORAS te verifiëren. Zorg ervoor dat de serviceprincipal een rol zoals AcrPush krijgt toegewezen, zodat deze machtigingen heeft om artefacten te pushen en te trekken.
* **Azure CLI (optioneel)** - Als u een individuele identiteit wilt gebruiken, hebt u een lokale installatie van de Azure CLI nodig. Versie 2.0.71 of hoger wordt aanbevolen. Ren `az --version `om de versie te vinden. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren](/cli/azure/install-azure-cli).
* **Docker (optioneel)** - Als u een individuele identiteit wilt gebruiken, moet u Docker ook lokaal laten installeren om te verifiëren bij het register. Docker biedt pakketten die eenvoudig Docker configureren op elk [Mac][docker-mac]-, [Windows][docker-windows]- of [Linux][docker-linux]-systeem.


## <a name="sign-in-to-a-registry"></a>Aanmelden bij een register

In deze sectie worden twee voorgestelde werkstromen weergegeven die u bij het register wilt aanmelden, afhankelijk van de gebruikte identiteit. Kies de methode die geschikt is voor uw omgeving.

### <a name="sign-in-with-oras"></a>Log hier in

Voer [service principal](container-registry-auth-service-principal.md) de `oras login` opdracht uit om u aan te melden bij het register met behulp van de serviceprincipal-toepassings-id en het wachtwoord. Geef de volledig gekwalificeerde registernaam op (alle kleine letters), in dit geval *myregistry.azurecr.io*. De serviceprincipal application ID wordt `$SP_APP_ID`doorgegeven in de `$SP_PASSWD`omgevingsvariabele en het wachtwoord in de variabele .

```bash
oras login myregistry.azurecr.io --username $SP_APP_ID --password $SP_PASSWD
```

Gebruik . `--password-stdin`

### <a name="sign-in-with-azure-cli"></a>Aanmelden met Azure CLI

[Meld u aan bij](/cli/azure/authenticate-azure-cli) de Azure CLI met uw identiteit om artefacten uit het containerregister te duwen en te trekken.

Gebruik vervolgens de Azure CLI-opdracht [az acr-login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) om toegang te krijgen tot het register. Bijvoorbeeld, om te verifiëren aan een register met de naam *myregistry:*

```azurecli
az login
az acr login --name myregistry
```

> [!NOTE]
> `az acr login`gebruikt de Docker-client om een Azure `docker.config` Active Directory-token in het bestand in te stellen. De Docker-client moet worden geïnstalleerd en uitgevoerd om de afzonderlijke verificatiestroom te voltooien.

## <a name="push-an-artifact"></a>Een artefact duwen

Maak een tekstbestand in een lokale werkmap met een voorbeeldtekst. Bijvoorbeeld, in een bash shell:

```bash
echo "Here is an artifact!" > artifact.txt
```

Gebruik `oras push` de opdracht om dit tekstbestand naar uw register te duwen. In het volgende voorbeeld wordt het `samples/artifact` voorbeeldtekstbestand naar de repo gepusht. Het register is geïdentificeerd met de volledig gekwalificeerde registernaam *myregistry.azurecr.io* (alle kleine letters). Het artefact `1.0`is gelabeld. Het artefact heeft standaard een niet-gedefinieerd type dat wordt geïdentificeerd door `artifact.txt`de *tekenreeks mediatype* die de bestandsnaam volgt. Zie [OCI-artefacten](https://github.com/opencontainers/artifacts) voor extra typen. 

**Linux**

```bash
oras push myregistry.azurecr.io/samples/artifact:1.0 \
    --manifest-config /dev/null:application/vnd.unknown.config.v1+json \
    ./artifact.txt:application/vnd.unknown.layer.v1+txt
```

**Windows**

```cmd
.\oras.exe push myregistry.azurecr.io/samples/artifact:1.0 ^
    --manifest-config NUL:application/vnd.unknown.config.v1+json ^
    .\artifact.txt:application/vnd.unknown.layer.v1+txt
```

Uitvoer voor een succesvolle push is vergelijkbaar met het volgende:

```console
Uploading 33998889555f artifact.txt
Pushed myregistry.azurecr.io/samples/artifact:1.0
Digest: sha256:xxxxxxbc912ef63e69136f05f1078dbf8d00960a79ee73c210eb2a5f65xxxxxx
```

Als u artefacten in uw register wilt beheren, `az acr` voert u standaardopdrachten uit voor het beheren van afbeeldingen als u de Azure CLI gebruikt. Download bijvoorbeeld de kenmerken van het artefact met de opdracht [az acr repository:][az-acr-repository-show]

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

## <a name="pull-an-artifact"></a>Trek een artefact

Voer `oras pull` de opdracht uit om het artefact uit uw register te halen.

Verwijder eerst het tekstbestand uit uw lokale werkmap:

```bash
rm artifact.txt
```

Voer `oras pull` uit om het artefact te trekken en geef het mediatype op dat wordt gebruikt om het artefact te duwen:

```bash
oras pull myregistry.azurecr.io/samples/artifact:1.0 \
    --media-type application/vnd.unknown.layer.v1+txt
```

Controleer of de pull is gelukt:

```bash
$ cat artifact.txt
Here is an artifact!
```

## <a name="remove-the-artifact-optional"></a>Het artefact verwijderen (optioneel)

Als u het artefact uit uw Azure-containerregister wilt verwijderen, gebruikt u de opdracht voor het verwijderen van de [AZ-aCR-opslagplaats.][az-acr-repository-delete] In het volgende voorbeeld wordt het artefact verwijderd dat u daar hebt opgeslagen:

```azurecli
az acr repository delete \
    --name myregistry \
    --image samples/artifact:1.0
```

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [de ORAS-bibliotheek,](https://github.com/deislabs/oras/tree/master/docs)inclusief het configureren van een manifest voor een artefact
* Bezoek de [OCI Artifacts](https://github.com/opencontainers/artifacts) repo voor referentieinformatie over nieuwe artefacttypen



<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-acr-repository-show]: /cli/azure/acr/repository?#az-acr-repository-show
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete

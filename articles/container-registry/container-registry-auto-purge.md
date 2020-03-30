---
title: Tags en manifesten wissen
description: Gebruik een zuiveringsopdracht om meerdere tags en manifesten uit een Azure-containerregister te verwijderen op basis van leeftijd en een tagfilter en plan eventueel zuiveringsbewerkingen.
ms.topic: article
ms.date: 08/14/2019
ms.openlocfilehash: f9d86b628bdd0ce0db3067b02a47517d8aadcba3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087330"
---
# <a name="automatically-purge-images-from-an-azure-container-registry"></a>Afbeeldingen automatisch verwijderen uit een Azure-containerregister

Wanneer u een Azure-containerregister gebruikt als onderdeel van een ontwikkelingswerkstroom, kan het register snel worden gevuld met afbeeldingen of andere artefacten die na een korte periode niet nodig zijn. U alle tags verwijderen die ouder zijn dan een bepaalde duur of overeenkomen met een opgegeven naamfilter. Als u meerdere artefacten snel `acr purge` wilt verwijderen, wordt in dit artikel de opdracht geïntroduceerd die u uitvoeren als een on-demand of [geplande](container-registry-tasks-scheduled.md) ACR-taak. 

De `acr purge` opdracht wordt momenteel gedistribueerd in`mcr.microsoft.com/acr/acr-cli:0.1`een openbare containerafbeelding ( ), opgebouwd uit broncode in de [acr-cli](https://github.com/Azure/acr-cli) repo in GitHub.

U de Azure Cloud Shell of een lokale installatie van de Azure CLI gebruiken om de acr-taakvoorbeelden in dit artikel uit te voeren. Als u het lokaal wilt gebruiken, is versie 2.0.69 of hoger vereist. Voer `az --version` uit om de versie te bekijken. Als u Azure CLI 2.0 wilt installeren of upgraden, raadpleegt u [Azure CLI 2.0 installeren][azure-cli-install]. 

> [!IMPORTANT]
> Deze functie is momenteel beschikbaar als preview-product. Previews worden voor u beschikbaar gesteld op voorwaarde dat u akkoord gaat met de [aanvullende gebruiksvoorwaarden][terms-of-use]. Sommige aspecten van deze functionaliteit kunnen wijzigen voordat deze functionaliteit algemeen beschikbaar wordt.

> [!WARNING]
> Gebruik `acr purge` de opdracht met voorzichtigheid: verwijderde afbeeldingsgegevens zijn onherstelbaar. Als u systemen hebt die afbeeldingen op manifesten wissen (in tegenstelling tot de naam van de afbeelding), moet u niet verwijderen van niet-gelabelde afbeeldingen. Als u niet-gelabelde afbeeldingen verwijderde, wordt voorkomen dat deze systemen de afbeeldingen uit uw register halen. In plaats van te trekken door manifest, overwegen de vaststelling van een *unieke tagging* regeling, een [aanbevolen beste praktijk](container-registry-image-tag-version.md).

Zie [Containerafbeeldingen verwijderen in Azure Container Registry](container-registry-delete.md)als u tags of manifesten met één afbeelding wilt verwijderen met azure cli-opdrachten.

## <a name="use-the-purge-command"></a>De opdracht Wissen gebruiken

De `acr purge` containeropdracht verwijdert afbeeldingen per tag in een opslagplaats die overeenkomt met een naamfilter en die ouder zijn dan een bepaalde duur. Standaard worden alleen tagverwijzingen verwijderd, niet de onderliggende [manifesten](container-registry-concepts.md#manifest) en laaggegevens. De opdracht heeft een optie om ook manifesten te verwijderen. 

> [!NOTE]
> `acr purge`verwijdert geen afbeeldingstag of opslagplaats `write-enabled` waar het `false`kenmerk is ingesteld op . Zie Een [containerafbeelding vergrendelen in een Azure-containerregister](container-registry-image-lock.md)voor meer informatie.

`acr purge`is ontworpen om te worden uitgevoerd als een containeropdracht in een [ACR-taak,](container-registry-tasks-overview.md)zodat deze automatisch wordt geverifieerd met het register waar de taak wordt uitgevoerd en acties uitvoert. De taakvoorbeelden in dit `acr purge` artikel gebruiken de [opdrachtalias](container-registry-tasks-reference-yaml.md#aliases) in plaats van een volledig gekwalificeerde opdracht voor containerafbeeldingen.

Geef ten minste het volgende `acr purge`op wanneer u uitvoert:

* `--filter`- Een repository en een *reguliere expressie* om tags in de repository te filteren. `--filter "hello-world:.*"` Voorbeelden: komt overeen `hello-world` met alle `--filter "hello-world:^1.*"` tags in `1`de opslagplaats en komt overeen met tags die beginnen met . Geef `--filter` meerdere parameters door om meerdere opslagplaatsen te wissen.
* `--ago`- Een [go-stijl duurstring](https://golang.org/pkg/time/) om een duur aan te geven waarboven afbeeldingen worden verwijderd. De duur bestaat uit een reeks van een of meer decimale getallen, elk met een eenheidsachtervoegsel. Geldige tijdeenheden omvatten "d" voor dagen, "h" voor uren, en "m" voor minuten. `--ago 2d3h6m` Hiermee selecteert u bijvoorbeeld alle gefilterde afbeeldingen die nog langer dan 2 `--ago 1.5h` dagen, 3 uur geleden en 6 minuten geleden zijn gewijzigd en selecteert u afbeeldingen die voor het laatst zijn gewijzigd meer dan 1,5 uur geleden.

`acr purge`ondersteunt verschillende optionele parameters. De volgende twee worden gebruikt in voorbeelden in dit artikel:

* `--untagged`- Hiermee geeft u op dat manifesten die geen bijbehorende tags hebben *(niet-gecodeerde manifesten)* worden verwijderd.
* `--dry-run`- Hiermee geeft u op dat er geen gegevens worden verwijderd, maar dat de uitvoer hetzelfde is als wanneer de opdracht wordt uitgevoerd zonder deze vlag. Deze parameter is handig voor het testen van een zuiveringsopdracht om ervoor te zorgen dat gegevens die u wilt bewaren niet per ongeluk worden verwijderd.

Voer voor aanvullende `acr purge --help`parameters uit. 

`acr purge`ondersteunt andere functies van ACR-takenopdrachten, waaronder [uitvoeren van variabelen](container-registry-tasks-reference-yaml.md#run-variables) en [taakbeheerlogboeken](container-registry-tasks-logs.md) die worden gestreamd en ook worden opgeslagen voor later ophalen.

### <a name="run-in-an-on-demand-task"></a>Uitvoeren in een on-demand taak

In het volgende voorbeeld wordt de opdracht `acr purge` [az acr run][az-acr-run] gebruikt om de opdracht on-demand uit te voeren. In dit voorbeeld worden alle afbeeldingstags en manifesten in de `hello-world` opslagplaats in *myregistry* verwijderd die meer dan 1 dag geleden zijn gewijzigd. De opdracht container wordt doorgegeven met behulp van een omgevingsvariabele. De taak wordt uitgevoerd zonder broncontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --untagged --ago 1d"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

### <a name="run-in-a-scheduled-task"></a>Uitvoeren in een geplande taak

In het volgende voorbeeld wordt de opdracht [az acr-taak maken][az-acr-task-create] gebruikt om een dagelijkse [geplande ACR-taak](container-registry-tasks-scheduled.md)te maken. De taak zuiveringen tags gewijzigd meer `hello-world` dan 7 dagen geleden in de repository. De opdracht container wordt doorgegeven met behulp van een omgevingsvariabele. De taak wordt uitgevoerd zonder broncontext.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 7d"

az acr task create --name purgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 0 * * *" \
  --registry myregistry \
  --context /dev/null
```

Voer de opdracht [az acr-taakweerom][az-acr-task-show] te zien dat de timertrigger is geconfigureerd.

### <a name="purge-large-numbers-of-tags-and-manifests"></a>Grote aantallen tags en manifesten wissen

Het verwijderen van een groot aantal tags en manifesten kan enkele minuten of langer duren. Als u duizenden tags en manifesten wilt verwijderen, moet de opdracht mogelijk langer duren dan de standaardtime-outtijd van 600 seconden voor een on-demandtaak of 3600 seconden voor een geplande taak. Als de time-outtijd wordt overschreden, wordt alleen een subset van tags en manifesten verwijderd. Als u ervoor wilt zorgen dat een `--timeout` grootschalige zuivering voltooid is, geeft u de parameter door om de waarde te verhogen. 

Met de volgende on-demandtaak wordt bijvoorbeeld een time-outtijd van 3600 seconden (1 uur) ingesteld:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge --filter 'hello-world:.*' \
  --ago 1d --untagged"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  --timeout 3600 \
  /dev/null
```

## <a name="example-scheduled-purge-of-multiple-repositories-in-a-registry"></a>Voorbeeld: Geplande zuivering van meerdere opslagplaatsen in een register

Dit voorbeeld loopt `acr purge` door het gebruik om periodiek meerdere opslagplaatsen in een register op te schonen. U bijvoorbeeld een ontwikkelingspijplijn hebben die `samples/devimage1` afbeeldingen `samples/devimage2` naar de opslagplaatsen en repositories duwt. U importeert regelmatig ontwikkelingsafbeeldingen in een productieopslagplaats voor uw implementaties, zodat u de ontwikkelingsafbeeldingen niet meer nodig hebt. Wekelijks zuiver je de `samples/devimage1` repositories en `samples/devimage2` repositories, ter voorbereiding op het werk van de komende week.

### <a name="preview-the-purge"></a>Een voorbeeld van de zuivering bekijken

Voordat we gegevens verwijderen, raden we u aan `--dry-run` een on-demand zuiveringstaak uit te voeren met behulp van de parameter. Met deze optie u de tags en manifesten zien die de opdracht zal wissen, zonder gegevens te verwijderen. 

In het volgende voorbeeld selecteert het filter in elke opslagplaats alle tags. De `--ago 0d` parameter komt overeen met afbeeldingen van alle leeftijden in de repositories die overeenkomen met de filters. Wijzig indien nodig de selectiecriteria voor uw scenario. De `--untagged` parameter geeft aan dat er naast tags ook manifesten moeten worden verwijderd. De containeropdracht wordt doorgegeven aan de opdracht [az acr run][az-acr-run] met behulp van een omgevingsvariabele.

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged --dry-run"

az acr run \
  --cmd "$PURGE_CMD" \
  --registry myregistry \
  /dev/null
```

Controleer de opdrachtuitvoer om de tags en manifesten te zien die overeenkomen met de selectieparameters. Omdat de opdracht `--dry-run`wordt uitgevoerd met , worden er geen gegevens verwijderd.

Voorbeelduitvoer:

```console
[...]
Deleting tags for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1:232889b
myregistry.azurecr.io/samples/devimage1:a21776a
Deleting manifests for repository: samples/devimage1
myregistry.azurecr.io/samples/devimage1@sha256:81b6f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e788b
myregistry.azurecr.io/samples/devimage1@sha256:3ded859790e68bd02791a972ab0bae727231dc8746f233a7949e40f8ea90c8b3
Deleting tags for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2:5e788ba
myregistry.azurecr.io/samples/devimage2:f336b7c
Deleting manifests for repository: samples/devimage2
myregistry.azurecr.io/samples/devimage2@sha256:8d2527cde610e1715ad095cb12bc7ed169b60c495e5428eefdf336b7cb7c0371
myregistry.azurecr.io/samples/devimage2@sha256:ca86b078f89607bc03ded859790e68bd02791a972ab0bae727231dc8746f233a

Number of deleted tags: 4
Number of deleted manifests: 4
[...]
```

### <a name="schedule-the-purge"></a>De zuivering plannen

Nadat u de droogloop hebt geverifieerd, maakt u een geplande taak om de zuivering te automatiseren. In het volgende voorbeeld wordt een wekelijkse taak op zondag om 1:00 UTC gepland om de vorige zuiveringsopdracht uit te voeren:

```azurecli
# Environment variable for container command line
PURGE_CMD="acr purge \
  --filter 'samples/devimage1:.*' --filter 'samples/devimage2:.*' \
  --ago 0d --untagged"

az acr task create --name weeklyPurgeTask \
  --cmd "$PURGE_CMD" \
  --schedule "0 1 * * Sun" \
  --registry myregistry \
  --context /dev/null
```

Voer de opdracht [az acr-taakweerom][az-acr-task-show] te zien dat de timertrigger is geconfigureerd.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over andere opties om [afbeeldingsgegevens](container-registry-delete.md) te verwijderen in Azure Container Registry.

Zie [Containerimagestorage in Azure Container Registry](container-registry-storage.md)voor meer informatie over afbeeldingsopslag.

<!-- LINKS - External -->

[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - Internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[az-acr-run]: /cli/azure/acr#az-acr-run
[az-acr-task-create]: /cli/azure/acr/task#az-acr-task-create
[az-acr-task-show]: /cli/azure/acr/task#az-acr-task-show


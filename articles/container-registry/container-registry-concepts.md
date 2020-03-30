---
title: Informatie over repositories & afbeeldingen
description: Inleiding tot belangrijke concepten van Azure-containerregisters, opslagplaatsen en containerafbeeldingen.
ms.topic: article
ms.date: 09/10/2019
ms.openlocfilehash: ea6e2577d3eee91626dd613617a0b79e4ff3d6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79247057"
---
# <a name="about-registries-repositories-and-images"></a>Over registers, opslagplaatsen en afbeeldingen

Dit artikel introduceert de belangrijkste concepten van containerregisters, opslagplaatsen en containerafbeeldingen en gerelateerde artefacten. 

## <a name="registry"></a>Register

Een *containerregister* is een service die containerafbeeldingen opslaat en distribueert. Docker Hub is een openbaar containerregister dat de open source-community ondersteunt en dient als een algemene catalogus van afbeeldingen. Azure Container Registry biedt gebruikers directe controle over hun afbeeldingen, met geïntegreerde verificatie, [geo-replicatie](container-registry-geo-replication.md) ter ondersteuning van wereldwijde distributie en betrouwbaarheid voor netwerk-close implementaties, [virtuele netwerk- en firewallconfiguratie,](container-registry-vnet.md) [tagvergrendeling](container-registry-image-lock.md)en vele andere verbeterde functies. 

Naast Docker-containerafbeeldingen ondersteunt Azure Container Registry gerelateerde [inhoudsartefacten,](container-registry-image-formats.md) waaronder OCI-afbeeldingsindelingen (Open Container Initiative).

## <a name="content-addressable-elements-of-an-artifact"></a>Inhoudsadresseerbare elementen van een artefact

Het adres van een artefact in een Azure-containerregister bevat de volgende elementen. 

`[loginUrl]/[namespace]/[artifact:][tag]`

* **loginUrl** - De volledig gekwalificeerde naam van de registerhost. De registerhost in een Azure-containerregister bevindt zich in de indeling *myregistry*.azurecr.io (alle kleine letters). U moet de loginUrl opgeven wanneer u Docker of andere clienthulpprogramma's gebruikt om artefacten naar een Azure-containerregister te halen of te pushen. 
* **naamruimte** - Logische groepering van gerelateerde afbeeldingen of artefacten met slash-afgebakende strepen - bijvoorbeeld voor een werkgroep of app
* **artefact** - De naam van een opslagplaats voor een bepaalde afbeelding of artefact
* **tag** - Een specifieke versie van een afbeelding of artefact die is opgeslagen in een opslagplaats


De volledige naam van een afbeelding in een Azure-containerregister kan er bijvoorbeeld uitzien als:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Zie de volgende secties voor meer informatie over deze elementen.

## <a name="repository-name"></a>Naam van opslagplaats

Containerregisters beheren *opslagplaatsen, verzamelingen*van containerafbeeldingen of andere artefacten met dezelfde naam, maar verschillende tags. Bijvoorbeeld, de volgende drie beelden zijn in de "acr-helloworld" repository:


- *acr-helloworld: laatste*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Repository-namen kunnen ook [naamruimten](container-registry-best-practices.md#repository-namespaces)bevatten. Met naamruimten u afbeeldingen groeperen met doorsturende slash-delimited repository-namen, bijvoorbeeld:

- *marketing/campagne10-18/web:v2*
- *marketing/campagne10-18/api:v3*
- *marketing/campagne10-18/e-mailafzender:v2*
- *product-retouren/web-submission:20180604*
- *productretouren/legacy-integrator:20180715*

## <a name="image"></a>Installatiekopie

Een containerafbeelding of ander artefact in een register is gekoppeld aan een of meer tags, heeft een of meer lagen en wordt geïdentificeerd door een manifest. Als u begrijpt hoe deze componenten zich tot elkaar verhouden, u uw register effectief beheren.

### <a name="tag"></a>Label

De *tag* voor een afbeelding of ander artefact geeft de versie aan. Een enkel artefact in een opslagplaats kan worden toegewezen een of vele tags, en kan ook worden "untagged." Dat wil zeggen dat u alle tags uit een afbeelding verwijderen, terwijl de gegevens van de afbeelding (de lagen) in het register blijven.

De repository (of repository en namespace) plus een tag definieert de naam van een afbeelding. U een afbeelding duwen en trekken door de naam ervan op te geven in de push- of pull-bewerking.

Hoe u containerafbeeldingen tagt, wordt geleid door uw scenario's om ze te ontwikkelen of te implementeren. Stabiele tags worden bijvoorbeeld aanbevolen voor het onderhouden van uw basisafbeeldingen en unieke tags voor het implementeren van afbeeldingen. Zie [Aanbevelingen voor het taggen en versien van containerafbeeldingen](container-registry-image-tag-version.md)voor meer informatie .

### <a name="layer"></a>Laag

Containerafbeeldingen bestaan uit een of meer *lagen,* die elk overeenkomen met een regel in het Dockerbestand die de afbeelding definieert. Afbeeldingen in een register delen gemeenschappelijke lagen, waardoor de opslagefficiëntie toeneemt. Verschillende afbeeldingen in verschillende opslagplaatsen kunnen bijvoorbeeld dezelfde Alpine Linux-basislaag delen, maar slechts één kopie van die laag wordt in het register opgeslagen.

Het delen van lagen optimaliseert ook de laagverdeling naar knooppunten met meerdere afbeeldingen die algemene lagen delen. Als een afbeelding die al op een knooppunt staat bijvoorbeeld de Alpine Linux-laag als basis bevat, wordt de volgende aantrekkingskracht van een ander beeld dat naar dezelfde laag verwijst, niet overgebracht naar het knooppunt. In plaats daarvan verwijst het naar de laag die al op het knooppunt aanwezig is.

Om veilige isolatie en bescherming tegen mogelijke laagmanipulatie te bieden, worden lagen niet gedeeld in registers.

### <a name="manifest"></a>Manifest

Elke containerafbeelding of -artefact die naar een containerregister wordt gepusht, is gekoppeld aan een *manifest*. Het manifest, gegenereerd door het register wanneer de afbeelding wordt geduwd, identificeert op unieke wijze de afbeelding en specificeert de lagen ervan. U de manifesten voor een opslagplaats aanbieden met de azure CLI-opdracht [az acr repository-showmanifesten:][az-acr-repository-show-manifests]

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Vermeld bijvoorbeeld de manifesten voor de "acr-helloworld"-opslagplaats:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Manifestsamenvatting

Manifesten worden geïdentificeerd door een unieke SHA-256 hash, of *manifest digest*. Elke afbeelding of artefact - of gelabeld of niet - wordt geïdentificeerd door zijn samenvatting. De digestwaarde is uniek, zelfs als de laaggegevens van de afbeelding identiek zijn aan die van een andere afbeelding. Dit mechanisme is wat u toestaat om identiek gecodeerde beelden herhaaldelijk aan een register te duwen. U bijvoorbeeld herhaaldelijk `myimage:latest` foutloos naar uw register pushen omdat elke afbeelding wordt geïdentificeerd door zijn unieke samenvatting.

U een afbeelding uit een register halen door de samenvatting ervan op te geven in de pull-bewerking. Sommige systemen kunnen worden geconfigureerd om te trekken door digest, omdat het garandeert dat de afbeelding versie wordt getrokken, zelfs als een identiek gelabelde afbeelding vervolgens wordt geduwd naar het register.

Trek bijvoorbeeld een afbeelding uit de "acr-helloworld" repository door manifest digest:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> Als u herhaaldelijk gewijzigde afbeeldingen met identieke tags pusht, u zwevende afbeeldingen maken - afbeeldingen die niet zijn gelabeld, maar toch ruimte in uw register verbruiken. Niet-gecodeerde afbeeldingen worden niet weergegeven in de Azure CLI of in de Azure-portal wanneer u afbeeldingen op tag aanbiedt of bekijkt. Echter, hun lagen bestaan nog steeds en verbruiken ruimte in uw register. Als u een niet-gelabelde afbeelding verwijderen, wordt de registerruimte vrijgegeven wanneer het manifest de enige of de laatste is die naar een bepaalde laag wijst. Zie [Containerafbeeldingen verwijderen in Azure Container Registry voor](container-registry-delete.md)informatie over het vrijmaken van ruimte die wordt gebruikt door niet-gelabelde afbeeldingen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [beeldopslag](container-registry-storage.md) en [ondersteunde inhoudsindelingen](container-registry-image-formats.md) in Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests



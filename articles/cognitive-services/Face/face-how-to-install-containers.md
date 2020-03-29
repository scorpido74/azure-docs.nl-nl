---
title: Containers installeren en uitvoeren - Gezicht
titleSuffix: Azure Cognitive Services
description: In dit artikel ziet u hoe u containers voor Face downloaden, installeren en uitvoeren in deze walkthrough-zelfstudie.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: dapine
ms.openlocfilehash: e467b195ab1e2124286bfef74d7d1b71a4d99dd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "76165983"
---
# <a name="install-and-run-face-containers-preview"></a>Gezichtscontainers installeren en uitvoeren (Voorbeeld)

Azure Cognitive Services Face biedt een gestandaardiseerde Linux-container voor Docker die menselijke gezichten in afbeeldingen detecteert. Het identificeert ook attributen, waaronder gezicht oriëntatiepunten zoals neuzen en ogen, geslacht, leeftijd, en andere machine-voorspelde gelaatstrekken. Naast detectie kan Face controleren of twee gezichten in dezelfde afbeelding of verschillende afbeeldingen hetzelfde zijn met behulp van een betrouwbaarheidsscore. Face kan ook gezichten vergelijken met een database om te zien of er al een vergelijkbaar of identiek gezicht bestaat. Het kan ook vergelijkbare gezichten in groepen ordenen met behulp van gedeelde visuele eigenschappen.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet aan de volgende voorwaarden voldoen voordat u de Face-servicecontainers gebruikt.

|Vereist|Doel|
|--|--|
|Docker-engine| De Docker Engine moet op een [hostcomputer](#the-host-computer)zijn geïnstalleerd. Docker biedt pakketten waarmee de Docker-omgeving op [MacOS](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) en [Linux](https://docs.docker.com/engine/installation/#supported-platforms) kan worden geconfigureerd. Zie het [Docker-overzicht](https://docs.docker.com/engine/docker-overview/) voor een inleiding tot de basisprincipes van Docker en containers.<br><br> Docker moet zijn geconfigureerd om de containers in staat te stellen verbinding te maken met en factureringsgegevens naar Azure te verzenden. <br><br> Op Windows moet Docker ook zijn geconfigureerd om Linux-containers te ondersteunen.<br><br>|
|Vertrouwdheid met Docker | U hebt een basiskennis van Docker-concepten nodig, zoals registers, opslagplaatsen, containers en containerafbeeldingen. Je hebt ook `docker` kennis nodig van basiscommando's.| 
|Face resource |Als u de container wilt gebruiken, moet u het:<br><br>Een Azure **Face-bron** en de bijbehorende API-sleutel en het eindpunt URI. Beide waarden zijn beschikbaar op de **pagina's Overzicht** **en Sleutels** voor de resource. Ze moeten de container starten.<br><br>**{API_KEY}**: Een van de twee beschikbare resourcesleutels op de pagina **Sleutels**<br><br>**{ENDPOINT_URI}**: Het eindpunt zoals vermeld op de **pagina Overzicht**

[!INCLUDE [Gathering required container parameters](../containers/includes/container-gathering-required-parameters.md)]

## <a name="request-access-to-the-private-container-registry"></a>Toegang aanvragen tot het register van privécontainers

[!INCLUDE [Request access to private container registry](../../../includes/cognitive-services-containers-request-access.md)]

### <a name="the-host-computer"></a>De hostcomputer

[!INCLUDE [Host Computer requirements](../../../includes/cognitive-services-containers-host-computer.md)]

### <a name="container-requirements-and-recommendations"></a>Containervereisten en aanbevelingen

In de volgende tabel worden de minimale en aanbevolen CPU-cores en geheugen beschreven die voor elke Face-servicecontainer moeten worden toegewezen.

| Container | Minimum | Aanbevolen | Transacties per seconde<br>(Minimum, maximum)|
|-----------|---------|-------------|--|
|Face | 1 core, 2 GB geheugen | 1 core, 4 GB geheugen |10, 20|

* Elke kern moet minimaal 2,6 GHz of sneller zijn.
* Transacties per seconde (TPS).

Kern en geheugen `--cpus` komen `--memory` overeen met de en `docker run` instellingen, die worden gebruikt als onderdeel van de opdracht.

## <a name="get-the-container-image-with-docker-pull"></a>De containerafbeelding krijgen met dockerpull

Containerafbeeldingen voor de Face-service zijn beschikbaar. 

| Container | Opslagplaats |
|-----------|------------|
| Face | `containerpreview.azurecr.io/microsoft/cognitive-services-face:latest` |

[!INCLUDE [Tip for using docker list](../../../includes/cognitive-services-containers-docker-list-tip.md)]

### <a name="docker-pull-for-the-face-container"></a>Docker pull voor de Face container

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-face:latest
```

## <a name="use-the-container"></a>Gebruik de container

Nadat de container zich op de [hostcomputer bevindt,](#the-host-computer)gebruikt u het volgende proces om met de container te werken.

1. [Voer de container uit](#run-the-container-with-docker-run) met de vereiste factureringsinstellingen. Meer [voorbeelden](./face-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar. 
1. [Query the container prediction endpoint](#query-the-containers-prediction-endpoint). 

## <a name="run-the-container-with-docker-run"></a>De container uitvoeren met dockerrun

Gebruik de opdracht [Docker run](https://docs.docker.com/engine/reference/commandline/run/) om de container uit te voeren. Raadpleeg [het verzamelen van vereiste parameters](#gathering-required-parameters) `{ENDPOINT_URI}` voor `{API_KEY}` meer informatie over hoe u de en waarden krijgen.

[Voorbeelden](face-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar.

```bash
docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 \
containerpreview.azurecr.io/microsoft/cognitive-services-face \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY}
```

Met deze opdracht gebeurt het volgende:

* Loopt een gezichtscontainer uit de containerafbeelding.
* Hiermee wordt één CPU-kern en 4 GB geheugen toegewezen.
* Stelt TCP-poort 5000 bloot en wijst een pseudo-TTY toe voor de container.
* Verwijdert automatisch de container nadat deze is afgesloten. De containerafbeelding is nog steeds beschikbaar op de hostcomputer. 

Meer [voorbeelden](./face-resource-container-config.md#example-docker-run-commands) van `docker run` de opdracht zijn beschikbaar. 

> [!IMPORTANT]
> De `Eula` `Billing`opties `ApiKey` en opties moeten zijn opgegeven om de container uit te voeren of de container wordt niet gestart. Zie [Facturering voor](#billing)meer informatie.

[!INCLUDE [Running multiple containers on the same host](../../../includes/cognitive-services-containers-run-multiple-same-host.md)]


## <a name="query-the-containers-prediction-endpoint"></a>Het voorspellingseindpunt van de container opvragen

De container biedt OP RUST gebaseerde queryvoorspellingseindpunt-API's. 

Gebruik de `http://localhost:5000`host, voor container API's.


<!--  ## Validate container is running -->

[!INCLUDE [Container's API documentation](../../../includes/cognitive-services-containers-api-documentation.md)]

## <a name="stop-the-container"></a>De container stoppen

[!INCLUDE [How to stop the container](../../../includes/cognitive-services-containers-stop.md)]

## <a name="troubleshooting"></a>Problemen oplossen

Als u de container uitvoert met een [uitvoerbevestiging](./face-resource-container-config.md#mount-settings) en logboekregistratie is ingeschakeld, genereert de container logboekbestanden die handig zijn om problemen op te lossen die zich voordoen terwijl u de container start of uitvoert.

[!INCLUDE [Cognitive Services FAQ note](../containers/includes/cognitive-services-faq-note.md)]

## <a name="billing"></a>Billing

De Containers van de Face-service verzenden factureringsgegevens naar Azure met behulp van een Face-bron op uw Azure-account. 

[!INCLUDE [Container's Billing Settings](../../../includes/cognitive-services-containers-how-to-billing-info.md)]

Zie [Containers configureren](./face-resource-container-config.md)voor meer informatie over deze opties.

<!--blogs/samples/video coures -->

[!INCLUDE [Discoverability of more container information](../../../includes/cognitive-services-containers-discoverability.md)]

## <a name="summary"></a>Samenvatting

In dit artikel hebt u concepten en werkstroom geleerd voor het downloaden, installeren en uitvoeren van Face-servicecontainers. Samenvatting:

* Containerafbeeldingen worden gedownload uit het Azure Container Registry.
* Containerafbeeldingen worden uitgevoerd in Docker.
* U de REST API of de SDK gebruiken om bewerkingen aan te roepen in Face-servicecontainers door de hostURI van de container op te geven.
* U moet factureringsgegevens opgeven wanneer u een container instantiate.

> [!IMPORTANT]
> Cognitive Services-containers hebben geen licentie om uit te voeren zonder dat deze is verbonden met Azure voor meting. Klanten moeten de containers te allen tijde in staat stellen factuurgegevens met de meetservice te communiceren. Cognitive Services-containers verzenden geen klantgegevens, zoals de afbeelding of tekst die wordt geanalyseerd, naar Microsoft.

## <a name="next-steps"></a>Volgende stappen

* Zie [Containers configureren voor](face-resource-container-config.md)configuratie-instellingen.
* Zie [Gezichtsoverzicht](Overview.md)voor meer informatie over het detecteren en identificeren van gezichten.
* Zie de [Face API](//westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)voor informatie over de methoden die door de container worden ondersteund.
* Zie Containers voor Cognitive Services voor het gebruik van meer afvalcontainers voor Cognitive [Services.](../cognitive-services-container-support.md)

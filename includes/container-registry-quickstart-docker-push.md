---
title: bestand opnemen
description: bestand opnemen
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 01/23/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 09eaf9465ec3912dea6e1f3ee1693f6bfed50abc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "67176591"
---
## <a name="push-image-to-registry"></a>Installatiekopie pushen naar register

Als u een installatiekopie naar een Azure Container Registry wilt pushen, moet u eerst over een installatiekopie beschikken. Als u nog geen lokale containerinstallatiekopieën hebt, voert u de volgende [docker pull][docker-pull]-opdracht uit om een bestaande installatiekopie op te halen uit Docker Hub. Haal voor dit voorbeeld de installatiekopie `hello-world` op.

```
docker pull hello-world
```

Voordat u een installatiekopie naar het register kunt pushen, moet u deze taggen met de volledige gekwalificeerde naam van de ACR-aanmeldingsserver. De naam van de inlogserver staat in de * \<opmaaknaam\>.azurecr.io* (alle kleine letters), bijvoorbeeld *mycontainerregistry007.azurecr.io*.

Label de installatiekopie met de opdracht [docker tag][docker-tag]. Vervang `<acrLoginServer>` door de aanmeldingsnaam van het ACR-exemplaar.

```
docker tag hello-world <acrLoginServer>/hello-world:v1
```

Gebruik ten slotte [docker push][docker-push] om de installatiekopie naar de ACR-instantie te pushen. Vervang `<acrLoginServer>` door de aanmeldingsnaam van het ACR-exemplaar. In dit voorbeeld wordt de **hello-world**-opslagplaats met de `hello-world:v1`-installatiekopie gemaakt.

```
docker push <acrLoginServer>/hello-world:v1
```

Nadat u de installatiekopie naar uw containerregister hebt gepusht, verwijdert u de `hello-world:v1`-installatiekopie uit uw lokale Docker-omgeving. (Merk op dat deze [docker rmi][docker-rmi]-opdracht de installatiekopie niet verwijdert uit de **hello-world**-opslagplaats in uw Azure-containerregister.)

```
docker rmi <acrLoginServer>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->


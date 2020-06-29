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
ms.openlocfilehash: b10bf18fde850223bda80a597f448747558113f1
ms.sourcegitcommit: 4ac596f284a239a9b3d8ed42f89ed546290f4128
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84752153"
---
## <a name="push-image-to-registry"></a>Installatiekopie pushen naar register

Als u een installatiekopie naar een Azure Container Registry wilt pushen, moet u eerst over een installatiekopie beschikken. Als u nog geen lokale containerinstallatiekopieën hebt, voert u de volgende opdracht [docker pull][docker-pull] uit om een bestaande installatiekopie op te halen uit Docker Hub. Haal voor dit voorbeeld de installatiekopie `hello-world` op.

```
docker pull hello-world
```

Voordat u een installatiekopie naar het register kunt pushen, moet u deze taggen met de volledige gekwalificeerde naam van de registeraanmeldingsserver. De naam van de aanmeldingsserver heeft de notatie *\<registry-name\>.azurecr.io* (in kleine letters), bijvoorbeeld *mijncontainerregister007.azurecr.io*.

Label de installatiekopie met de opdracht [docker tag][docker-tag]. Vervang `<login-server>` door de aanmeldingsnaam van het ACR-exemplaar.

```
docker tag hello-world <login-server>/hello-world:v1
```

Gebruik ten slotte [docker push][docker-push] om de installatiekopie naar het registerexemplaar te pushen. Vervang `<login-server>` door de aanmeldingsnaam van het registerexemplaar. In dit voorbeeld wordt de **hello-world**-opslagplaats met de `hello-world:v1`-installatiekopie gemaakt.

```
docker push <login-server>/hello-world:v1
```

Nadat u de installatiekopie naar uw containerregister hebt gepusht, verwijdert u de `hello-world:v1`-installatiekopie uit uw lokale Docker-omgeving. (U ziet dat met deze opdracht [docker rmi][docker-rmi] de installatiekopie niet wordt verwijderd uit de **hello-world**-opslagplaats in uw Azure-containerregister.)

```
docker rmi <login-server>/hello-world:v1
```

<!-- LINKS - External -->
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/

<!-- LINKS - Internal -->


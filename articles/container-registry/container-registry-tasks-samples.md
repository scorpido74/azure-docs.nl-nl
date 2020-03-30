---
title: ACR-taakvoorbeelden
description: Voorbeeld van Azure Container Registry Tasks (ACR-taken) voor het maken, uitvoeren en patchen van containerafbeeldingen
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456083"
---
# <a name="acr-tasks-samples"></a>Voorbeelden van ACR-taken

In dit artikel `task.yaml` wordt koppelingen gemaakt naar voorbeeldbestanden en gekoppelde Dockerfiles voor verschillende [ACR-taken (Azure Container Registry Tasks).](container-registry-tasks-overview.md) 

Zie de [repo azure-voorbeelden][task-examples] voor meer voorbeelden.

## <a name="scenarios"></a>Scenario's

* **Afbeelding**[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile bouwen](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)  - 

* **Run container** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml)

* **Afbeelding** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) bouwen en pushen

* **Afbeelding** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) bouwen en uitvoeren

* **Meerdere afbeeldingen** -  [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile bouwen](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile) en pushen

* **Afbeeldingen** -  bouwen en testen in parallel[YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Afbeeldingen bouwen en pushen naar meerdere registers** - [YAML](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over ACR-taken:

* [Taken in meerdere stappen](container-registry-tasks-multi-step.md) - Op ACR-taken gebaseerde workflows voor het maken, testen en patchen van containerafbeeldingen in de cloud.
* [Taakverwijzing](container-registry-tasks-reference-yaml.md) - Taakstaptypen, hun eigenschappen en gebruik.
* [Cmd repo](https://github.com/AzureCR/cmd) - Een verzameling containers als opdrachten voor ACR-taken.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

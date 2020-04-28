---
title: ACR-taak voorbeelden
description: Voor beelden van Azure Container Registry taken (ACR taken) om container installatie kopieën te bouwen, uit te voeren en te patchen
ms.topic: article
ms.date: 11/14/2019
ms.openlocfilehash: 49df3bf565052a729ac3c587bd2ba11a299d05f1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74456083"
---
# <a name="acr-tasks-samples"></a>Voor beelden van ACR-taken

Dit artikel is gekoppeld aan `task.yaml` voorbeeld bestanden en gekoppelde Dockerfiles voor verschillende scenario's voor [Azure container Registry taken](container-registry-tasks-overview.md) (ACR-taken). 

Zie de [Azure][task-examples] -voor beelden opslag plaats voor meer voor beelden.

## <a name="scenarios"></a>Scenario's

* **Image** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml)maken, [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Container** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml) uitvoeren

* **Image** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml)maken en pushen, [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Installatie kopie** - maken en uitvoeren[yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Meerdere installatie kopieën** -  bouwen en pushen[yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Afbeeldingen bouwen en testen in parallelle** -  [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bouw en push installatie kopieën naar meerdere registers** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over ACR-taken:

* [Taken met meerdere stappen](container-registry-tasks-multi-step.md) : ACR werk stromen voor het maken, testen en repareren van container installatie kopieën in de Cloud.
* [Taak verwijzing](container-registry-tasks-reference-yaml.md) -taak stap typen, eigenschappen en gebruik.
* [Cmd opslag plaats](https://github.com/AzureCR/cmd) : een verzameling containers als opdrachten voor ACR-taken.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

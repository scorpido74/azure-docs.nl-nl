---
title: Azure Container Registry taken-voor beelden
description: Voor beelden van Azure Container Registry taken (ACR taken) om container installatie kopieën te bouwen, uit te voeren en te patchen
services: container-registry
author: dlepow
manager: gwallace
ms.service: container-registry
ms.topic: article
ms.date: 11/14/2019
ms.author: danlep
ms.openlocfilehash: 488e13ed0d1961fbafad545057accb61957a7005
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2019
ms.locfileid: "74152851"
---
# <a name="acr-tasks-samples"></a>Voor beelden van ACR-taken

Dit artikel is een koppeling naar voor beeld `task.yaml`-bestanden en gekoppelde Dockerfiles voor verschillende scenario's voor [Azure container Registry taken](container-registry-tasks-overview.md) (ACR-taken). 

Zie de [Azure][task-examples] -voor beelden opslag plaats voor meer voor beelden.

## <a name="scenarios"></a>Scenario's

* **Installatie kopie maken** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Container** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/bash-echo.yaml) uitvoeren

* **Installatie kopie maken en pushen** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* Een **installatie kopie bouwen en uitvoeren** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-run-hello-world.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bouw en push meerdere installatie kopieën** -  [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/build-push-hello-world-multi.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bouw en test installatie kopieën in parallel** -  [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/when-parallel.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/hello-world.dockerfile)

* **Bouw en push installatie kopieën naar meerdere registers** - [yaml](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/testtask.yaml), [Dockerfile](https://github.com/Azure-Samples/acr-tasks/blob/master/multipleRegistries/hello-world.dockerfile)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over ACR-taken:

* [Taken met meerdere stappen](container-registry-tasks-multi-step.md) : ACR werk stromen voor het maken, testen en repareren van container installatie kopieën in de Cloud.
* [Taak verwijzing](container-registry-tasks-reference-yaml.md) -taak stap typen, eigenschappen en gebruik.
* [Cmd opslag plaats](https://github.com/AzureCR/cmd) : een verzameling containers als opdrachten voor ACR-taken.


<!-- LINKS - External -->
[task-examples]: https://github.com/Azure-Samples/acr-tasks

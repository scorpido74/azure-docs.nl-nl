---
title: Repositories weergeven in portal
description: Gebruik de Azure-portal om Azure Container Registry-repositories weer te geven, die Docker-containerafbeeldingen en andere ondersteunde artefacten hosten.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74456292"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Containerregisterrepositories weergeven in de Azure-portal

Met Azure Container Registry u Docker-containerafbeeldingen opslaan in opslagplaatsen. Door afbeeldingen op te slaan in opslagplaatsen, u groepen afbeeldingen (of versies van afbeeldingen) opslaan in geïsoleerde omgevingen. U deze opslagplaatsen opgeven wanneer u afbeeldingen naar uw register pusht en de inhoud ervan weergeeft in de Azure-portal.

## <a name="prerequisites"></a>Vereisten

* **Containerregister:** Maak een containerregister in uw Azure-abonnement. Gebruik bijvoorbeeld de [Azure-portal](container-registry-get-started-portal.md) of de [Azure CLI](container-registry-get-started-azure-cli.md).
* **Docker CLI:** Installeer [Docker][docker-install] op uw lokale machine, die u voorziet van de Docker command-line interface.
* **Containerafbeelding**: Een afbeelding naar uw containerregister duwen. Zie [Duwen en een afbeelding trekken](container-registry-get-started-docker-cli.md)voor richtlijnen voor het duwen en trekken van afbeeldingen.

## <a name="view-repositories-in-azure-portal"></a>Repositories weergeven in Azure-portal

U een lijst zien van de opslagplaatsen die uw afbeeldingen hosten, evenals de afbeeldingstags, in de Azure-portal.

Als u de stappen in [Push and pull een afbeelding hebt](container-registry-get-started-docker-cli.md) gevolgd (en de afbeelding vervolgens niet hebt verwijderd), moet u een Nginx-afbeelding in uw containerregister hebben. In de instructies in dat artikel is aangegeven dat u de `/samples/nginx`afbeelding tagt met een naamruimte, de 'samples' in . Als opfriscursus was de in dat artikel opgegeven [pushopdracht docker:][docker-push]

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Omdat Azure Container Registry dergelijke naamruimten voor opslagplaatsen op meerdere niveaus ondersteunt, u verzamelingen met afbeeldingen met betrekking tot een specifieke app of een verzameling apps aanpassen aan verschillende ontwikkel- of operationele teams. Zie Containerregisters voor [privédockerin Azure](container-registry-intro.md)voor meer informatie over opslagplaatsen in containerregisters.

Ga als lid van het werk om een opslagplaats weer te geven:

1. Aanmelden bij de [Azure-portal][portal]
1. Selecteer het **Azure Container Registry** waarnaar u de Nginx-afbeelding hebt gepusht
1. Selecteer **Repositories** om een lijst met de opslagplaatsen te bekijken die de afbeeldingen in het register bevatten
1. Een opslagplaats selecteren om de afbeeldingstags in die opslagplaats te bekijken

Als u bijvoorbeeld de Nginx-afbeelding hebt ingedrukt zoals deze is geïnstrueerd in [Push en een afbeelding trekt,](container-registry-get-started-docker-cli.md)moet u iets zien dat lijkt op:

![Repositories in de portal](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Volgende stappen

Nu u de basisbeginselen kent van het bekijken en werken met opslagplaatsen in de portal, probeert u Azure Container Registry te gebruiken met een [AKS-cluster (Azure Kubernetes Service).](../aks/tutorial-kubernetes-prepare-app.md)

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com

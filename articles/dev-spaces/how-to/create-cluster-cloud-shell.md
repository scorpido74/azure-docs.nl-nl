---
title: Een Kubernetes-cluster maken met Azure dev Spaces ingeschakeld-Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Meer informatie over hoe u snel een Kubernetes-cluster kunt maken dat rechtstreeks vanuit uw browser kan worden geactiveerd zonder dat u iets hoeft te installeren.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, servicemesh, servicemeshroutering, kubectl, k8s
ms.openlocfilehash: f6da9055e11b5c514d71122c3650bbc326fb8de8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86232284"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Een Kubernetes-cluster maken met Azure dev Spaces ingeschakeld met Azure Cloud Shell

U kunt [Azure Cloud shell](/azure/cloud-shell) gebruiken om een Azure Kubernetes-service cluster te maken met behulp van de knop **try it** van deze pagina. Als u niet bent aangemeld, volgt u de aanwijzingen om u aan te melden met een Azure-account. vervolgens typt u de opdrachten op de Azure Cloud Shell prompt wanneer deze wordt weer gegeven.

## <a name="create-the-cluster"></a>Het cluster maken

Maak eerst de resource groep in een [regio die Azure-ontwikkel ruimten ondersteunt][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Maak een Kubernetes-cluster met de volgende opdracht:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Het duurt een paar minuten om het cluster te maken.  Wanneer dit is voltooid, wordt de uitvoer weer gegeven in de JSON-indeling. Zoek `provisioningState` en controleer het `Succeeded` .

## <a name="next-steps"></a>Volgende stappen

Zie [Azure dev Spaces](../index.yml) voor koppelingen naar volledige zelf studies.

> [!IMPORTANT]
> Veel van de Azure dev Spaces Quick starts en zelf studies gebruiken de Azure dev Spaces CLI om bewerkingen uit te voeren. U kunt de CLI voor Azure Dev Spaces niet installeren in Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service

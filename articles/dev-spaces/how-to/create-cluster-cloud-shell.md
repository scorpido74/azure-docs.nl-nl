---
title: Een Kubernetes-cluster maken dat is ingeschakeld voor Azure-ontwikkel ruimten met behulp van Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Meer informatie over hoe u snel een Kubernetes-cluster kunt maken dat rechtstreeks vanuit uw browser kan worden geactiveerd zonder dat u iets hoeft te installeren.
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, helm, service-net, service mesh routing, kubectl, K8S
ms.openlocfilehash: dbdc9226e417b3142284386ae3586819cda802d9
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280103"
---
# <a name="create-a-kubernetes-cluster-using-azure-cloud-shell"></a>Een Kubernetes-cluster maken met behulp van Azure Cloud Shell

U kunt [Azure Cloud shell](/azure/cloud-shell) gebruiken om een Azure Kubernetes-service cluster te maken met behulp van de knop **try it** van deze pagina. Als u niet bent aangemeld, volgt u de aanwijzingen om u aan te melden met een Azure-account. vervolgens typt u de opdrachten op de Azure Cloud Shell prompt wanneer deze wordt weer gegeven.

## <a name="create-the-cluster"></a>Het cluster maken

Maak eerst de resource groep in een [regio die Azure-ontwikkel ruimten ondersteunt][supported-regions].

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Maak een Kubernetes-cluster met de volgende opdracht:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --disable-rbac --generate-ssh-keys
```

Het duurt een paar minuten om het cluster te maken.  Als het gereed is wordt de uitvoer weergegeven in de JSON-indeling. Zoek naar `provisioningState` en controleer of er `Succeeded` staat.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure dev Spaces](/azure/dev-spaces/) voor koppelingen naar volledige zelf studies.

> [!IMPORTANT]
> Veel van de Azure dev Spaces Quick starts en zelf studies gebruiken de Azure dev Spaces CLI om bewerkingen uit te voeren. U kunt de CLI voor Azure dev Spaces niet installeren in de Azure Cloud Shell.


[supported-regions]: ../about.md#supported-regions-and-configurations
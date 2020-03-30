---
title: Een Kubernetes-cluster maken met Azure Dev Spaces ingeschakeld - Azure Cloud Shell
services: azure-dev-spaces
ms.date: 10/04/2018
ms.topic: conceptual
description: Leer hoe u snel een Kubernetes-cluster maakt dat rechtstreeks vanuit uw browser is ingeschakeld voor Azure Dev Spaces zonder iets te installeren.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 5e2e5cfd22eeedd3554737458caeca0b891b62fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77605297"
---
# <a name="create-a-kubernetes-cluster-with-azure-dev-spaces-enabled-with-azure-cloud-shell"></a>Een Kubernetes-cluster maken met Azure Dev Spaces ingeschakeld met Azure Cloud Shell

U [Azure Cloud Shell](/azure/cloud-shell) gebruiken om een Azure Kubernetes Service-cluster te maken met de knop Try **It** op deze pagina. Als u niet bent aangemeld, volgt u de aanwijzingen om u aan te melden met een Azure-account en typt u de opdrachten op de prompt van Azure Cloud Shell wanneer deze wordt weergegeven.

## <a name="create-the-cluster"></a>Het cluster maken

Maak eerst de resourcegroep in een [regio die Azure Dev Spaces ondersteunt.][supported-regions]

```azurecli-interactive
az group create --name MyResourceGroup --location <region>
```

Maak een Kubernetes-cluster met de volgende opdracht:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyAKS --location <region> --generate-ssh-keys
```

Het duurt een paar minuten om het cluster te maken.  Als deze is voltooid, wordt de uitvoer weergegeven in de JSON-indeling. Zoek `provisioningState` naar en controleer `Succeeded`of het.

## <a name="next-steps"></a>Volgende stappen

Zie [Azure Dev Spaces](/azure/dev-spaces/) voor koppelingen naar volledige zelfstudies.

> [!IMPORTANT]
> Veel van de Azure Dev Spaces snelstart en tutorials gebruiken de Azure Dev Spaces CLI om bewerkingen uit te voeren. U de AZURE Dev Spaces CLI niet installeren in de Azure Cloud Shell.


[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
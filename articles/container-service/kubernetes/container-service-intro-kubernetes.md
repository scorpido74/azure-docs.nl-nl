---
title: (AFGESCHAFT) Inleiding tot Azure Container Service voor Kubernetes
description: Met Azure Container Service voor Kubernetes kunt u eenvoudig toepassingen op basis van containers implementeren en beheren in Azure.
author: gabrtv
ms.service: container-service
ms.topic: overview
ms.date: 07/21/2017
ms.author: gamonroy
ms.custom: mvc
ms.openlocfilehash: c0ef7255a087dd5dc26532316deab337f9eff715
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "76271571"
---
# <a name="deprecated-introduction-to-azure-container-service-for-kubernetes"></a>(AFGESCHAFT) Inleiding tot Azure Container Service voor Kubernetes

> [!TIP]
> Voor de bijgewerkte versie van dit artikel, dat gebruikmaakt van Azure Kubernetes Service, raadpleegt u [Overzicht van Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Met Azure Container Service voor Kubernetes kunt u eenvoudig een cluster virtuele machines maken, configureren en beheren die vooraf zijn geconfigureerd voor het uitvoeren van beperkte toepassingen. U kunt hierbij dan uitgaan van uw eigen vaardigheden of gebruikmaken van een grote en groeiende groep community experts om toepassingen op basis van containers te implementeren en beheren in Microsoft Azure.

Met Azure Container Service profiteert u van de hoogwaardige functies van Azure en behoudt u draagbaarheid van toepassingen via Kubernetes en het Docker-installatiekopieformaat.

## <a name="using-azure-container-service-for-kubernetes"></a>Azure Container Service voor Kubernetes gebruiken
Azure Container Service is bedoeld om een hostomgeving voor containers te bieden door gebruik te maken van de open source tools en technologieën die tegenwoordig populair zijn onder onze klanten. Om dit te bereiken, maken we de standaard API-eindpunten van Kubernetes beschikbaar. Met behulp van deze standaard eindpunten kunt u gebruikmaken van alle software die geschikt is voor communicatie met een Kubernetes-cluster. U kunt bijvoorbeeld [kubectl](https://kubernetes.io/docs/user-guide/kubectl-overview/), [helm](https://helm.sh/) of [draft](https://github.com/Azure/draft) gebruiken.

## <a name="creating-a-kubernetes-cluster-using-azure-container-service"></a>Een Kubernetes-cluster maken met Azure Container Service
Als u Azure Container Service wilt gebruiken, implementeert u eerst een Azure Container Service-cluster met de [Azure CLI](container-service-kubernetes-walkthrough.md) of via de portal (zoek in Marketplace naar **Azure Container Service**). Als u een ervaren gebruiker bent die meer controle wil over de Azure Resource Manager-sjablonen, kunt u het open source [acs-engine](https://github.com/Azure/acs-engine)-project gebruiken om uw eigen aangepaste Kubernetes-cluster te bouwen en het te implementeren via de `az` CLI.

### <a name="using-kubernetes"></a>Kubernetes gebruiken
Kubernetes automatiseert het implementeren, schalen en beheren van toepassingen in containers. De applicatie bevat een uitgebreide set functies, zoals:
* Automatische binpacking
* Automatisch herstel
* Horizontaal schalen
* Servicedetectie en taakverdeling
* Geautomatiseerde implementaties en terugdraaiacties
* Geheimen- en configuratiebeheer
* Opslagindeling
* Batchuitvoering

Architectuurdiagram van Kubernetes geïmplementeerd via Azure Container Service:

![Azure Container Service geconfigureerd voor het gebruik van Kubernetes.](media/acs-intro/kubernetes.png)

## <a name="videos"></a>Video's

Ondersteuning voor Kubernetes in Azure Container Service (Azure Friday, januari 2017):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Kubernetes-Support-in-Azure-Container-Services/player]
>
>

Hulpprogramma's voor het ontwikkelen en implementeren van toepassingen in Kubernetes (Azure OpenDev, juni 2017):

> [!VIDEO https://channel9.msdn.com/Events/AzureOpenDev/June2017/Tools-for-Developing-and-Deploying-Applications-on-Kubernetes/player]
>
>

## <a name="next-steps"></a>Volgende stappen

Bekijk de [Kubernetes-snelstartgids](container-service-kubernetes-walkthrough.md) en begin vandaag nog met het verkennen van Azure Container Service.

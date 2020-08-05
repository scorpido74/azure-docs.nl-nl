---
title: Wat is Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Informatie over hoe Azure Dev Spaces een snelle, iteratieve Kubernetes-ontwikkelervaring voor teams biedt in Azure Kubernetes Service-clusters
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 691845cd888e4d24c1144f2805402a3baf14a86e
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87006961"
---
# <a name="what-is-azure-dev-spaces"></a>Wat is Azure Dev Spaces?

Azure Dev Spaces biedt een snelle, iteratieve Kubernetes-ontwikkelervaring voor teams in Azure Kubernetes Service-clusters. Met Azure Dev Spaces kunt u ook fouten opsporen en alle onderdelen van uw toepassing in AKS testen met minimale instellingen van de ontwikkelcomputer, zonder afhankelijkheden te repliceren of te simuleren.

![In het diagram ziet u twee versies van een toepassing die onafhankelijk zijn ontwikkeld. Ze worden vervolgens gecombineerd tot één toepassing in een ontwikkelomgeving van Azure Dev Spaces.](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hoe Azure Dev Spaces de ontwikkeling van Kubernetes vereenvoudigt

Met Azure Dev Spaces kunnen teams zich richten op de ontwikkeling en snelle herhaling van hun microservicetoepassing door teams in staat te stellen om rechtstreeks te werken met hun hele microservicesarchitectuur of -toepassing die in AKS wordt uitgevoerd. Azure Dev Spaces biedt een manier om onafhankelijke delen van uw microservicesarchitectuur in isolatie onafhankelijk bij te werken zonder dat dit van invloed is op de rest van het AKS-cluster of andere ontwikkelaars. Azure Dev Spaces is bedoeld voor ontwikkeling en testen in ontwikkel- en testomgevingen op een lager niveau en is niet ontworpen om te worden uitgevoerd op voor productie bestemde AKS-clusters.

Omdat teams kunnen samenwerken met de hele toepassing en direct samenwerken in AKS, zorgt Azure Dev Spaces voor het volgende:

* Minimale instellingen van de lokale computer
* Minder voorbereidingstijd voor nieuwe ontwikkelaars in het team
* Hogere snelheid van een team door een snellere herhaling
* Minder redundante ontwikkel -en integratie omgevingen omdat teamleden een cluster kunnen delen
* Geen noodzaak om afhankelijkheden te repliceren of te deregistreren
* Betere samenwerking tussen ontwikkelteams en de teams waarmee ze samenwerken, zoals DevOps-teams

Azure Dev Spaces biedt hulpprogramma's om Docker- en Kubernetes-assets te genereren voor uw projecten. Met deze hulpprogramma's kunt u eenvoudig nieuwe en bestaande toepassingen toevoegen aan een ontwikkelruimte en andere AKS-clusters.

Zie [Hoe Azure Dev Spaces werkt en is geconfigureerd][how-dev-spaces-works] voor meer informatie over de werking van Azure Dev Spaces.

## <a name="supported-regions-and-configurations"></a>Ondersteunde regio's en configuraties

Azure Dev Spaces wordt alleen ondersteund door AKS-clusters in [sommige regio's][supported-regions]. Azure Dev Spaces ondersteunt het gebruik van [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Visual Studio Code](https://code.visualstudio.com/download) met een [Azure Dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) die is geïnstalleerd in Linux, macOS of Windows 8 of hoger, om uw toepassingen te ontwikkelen en in AKS uit te voeren. Het biedt ook ondersteuning voor het gebruik van [Visual Studio 2019](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) geïnstalleerd in Windows met de Azure Development-workload.

## <a name="next-steps"></a>Volgende stappen

Leer meer over snelle, iteratieve ontwikkeling voor teams met Azure Dev Spaces met de [snelstart voor teamontwikkeling][team-development-quickstart].

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md

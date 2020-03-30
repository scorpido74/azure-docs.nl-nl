---
title: Inleiding tot Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Ontdek hoe Azure Dev Spaces een snelle, iteratieve Kubernetes-ontwikkelervaring biedt voor teams in Azure Kubernetes Service-clusters
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, kubectl, k8s
manager: gwallace
ms.openlocfilehash: 586b19070ec36517add21f7aac86ddf15121be2d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240700"
---
# <a name="introduction-to-azure-dev-spaces"></a>Inleiding tot Azure Dev Spaces

Azure Dev Spaces biedt een snelle, iteratieve Kubernetes-ontwikkelervaring voor teams in AKS-clusters (Azure Kubernetes Service). Azure Dev Spaces stelt u ook in staat om alle onderdelen van uw toepassing in AKS te debuggen en te testen met minimale installatie van ontwikkelingsmachines, zonder afhankelijkheden te repliceren of te bespotten.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hoe Azure Dev Spaces de ontwikkeling van Kubernetes vereenvoudigt

Azure Dev Spaces helpt teams zich te concentreren op de ontwikkeling en snelle iteratie van hun microservicetoepassing door teams in staat te stellen rechtstreeks te werken met hun volledige microservices-architectuur of -toepassing die in AKS wordt uitgevoerd. Azure Dev Spaces biedt ook een manier om delen van uw microservices-architectuur afzonderlijk bij te werken zonder dat dit gevolgen heeft voor de rest van het AKS-cluster of andere ontwikkelaars. Azure Dev Spaces is bedoeld voor ontwikkeling en testen in ontwikkel- en testomgevingen op een lager niveau en is niet bedoeld om op productie-AKS-clusters te draaien.

Omdat teams met de hele toepassing kunnen werken en rechtstreeks kunnen samenwerken in AKS, azure Dev Spaces:

* Minimaliseert lokale machine-installatie
* Vermindert de insteltijd voor nieuwe ontwikkelaars in het team
* Verhoogt de snelheid van een team door snellere iteratie
* Vermindert het aantal redundante ontwikkel- en integratieomgevingen, omdat teamleden een cluster kunnen delen
* Hiermee verwijdert u de noodzaak om afhankelijkheden te repliceren of te bespotten
* Verbetert de samenwerking tussen ontwikkelingsteams en de teams waarmee ze werken, zoals DevOps-teams

Azure Dev Spaces biedt tooling om Docker- en Kubernetes-assets voor uw projecten te genereren. Met deze tooling u eenvoudig nieuwe en bestaande toepassingen toevoegen aan zowel een dev-ruimte als andere AKS-clusters.

Zie [Hoe Azure Dev Spaces werkt en is geconfigureerd][how-dev-spaces-works]voor meer informatie over hoe Azure Dev Spaces werkt.

## <a name="supported-regions-and-configurations"></a>Ondersteunde regio's en configuraties

Azure Dev Spaces wordt alleen ondersteund door AKS-clusters in [sommige regio's.][supported-regions] Azure Dev Spaces ondersteunt het gebruik van [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Visual Studio Code](https://code.visualstudio.com/download) met een [Azure Dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) die is geïnstalleerd in Linux, MacOS of Windows 8 of hoger, om uw toepassingen te ontwikkelen en in AKS uit te voeren. Het ondersteunt ook het gebruik van [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) geïnstalleerd op Windows 8 of hoger. Voor Visual Studio 2019 hebt u de Azure Development-workload nodig. Voor Visual Studio 2017 heb je de Web Development-workload en [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools)nodig.

## <a name="next-steps"></a>Volgende stappen

Lees meer over snelle, iteratieve ontwikkeling voor teams met Azure Dev Spaces met de teamontwikkeling snel van start.

> [!div class="nextstepaction"]
> [Team Ontwikkeling snel van start](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md

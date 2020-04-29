---
title: Wat is Azure Dev Spaces?
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Meer informatie over hoe Azure dev Spaces een snelle, iteratieve Kubernetes ontwikkelings ervaring biedt voor teams in azure Kubernetes-Service clusters
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 8b22181bcddda9e4156c0e0dbe61d7d813498d96
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80529734"
---
# <a name="what-is-azure-dev-spaces"></a>Wat is Azure Dev Spaces?

Azure dev Spaces biedt een snelle, iteratieve Kubernetes ontwikkelings ervaring voor teams in azure Kubernetes Service-clusters (AKS). Met Azure dev Spaces kunt u ook fouten opsporen en testen van alle onderdelen van uw toepassing in AKS met minimale ontwikkeling van de computer, zonder dat afhankelijkheden worden gerepliceerd of gesimuleerd.

![](media/azure-dev-spaces/collaborate-graphic.gif)

## <a name="how-azure-dev-spaces-simplifies-kubernetes-development"></a>Hoe Azure Dev Spaces de ontwikkeling van Kubernetes vereenvoudigt

Met Azure dev Spaces kunnen teams zich richten op de ontwikkeling en snelle herhaling van hun micro service-toepassing door teams in staat te stellen om rechtstreeks te werken met hun hele microservices-architectuur of toepassing die wordt uitgevoerd in AKS. Azure dev Spaces biedt een manier om onafhankelijke delen van uw micro service architectuur in isolatie onafhankelijk bij te werken zonder dat dit van invloed is op de rest van het AKS-cluster of andere ontwikkel aars. Azure dev Spaces is bedoeld voor ontwikkeling en testen in ontwikkel-en test omgevingen op een lager niveau en is niet ontworpen om te worden uitgevoerd op productie AKS-clusters.

Omdat teams kunnen samen werken met de hele toepassing en direct samen werken in AKS, Azure dev Spaces:

* Hiermee wordt de installatie van de lokale machine geminimaliseerd
* Hiermee verkleint u de voorbereidings tijd voor nieuwe ontwikkel aars in het team
* Verhoogt de snelheid van een team door een snellere herhaling
* Vermindert het aantal redundante ontwikkel-en integratie omgevingen omdat team leden een cluster kunnen delen
* Hiermee verwijdert u de nood zaak om afhankelijkheden te repliceren of te deregistreren
* Verbetert de samen werking tussen ontwikkel teams en de teams waarmee ze werken, zoals DevOps teams

Azure dev Spaces biedt hulp middelen om docker-en Kubernetes-assets te genereren voor uw projecten. Met dit hulp programma kunt u eenvoudig nieuwe en bestaande toepassingen toevoegen aan een dev-ruimte en andere AKS-clusters.

Zie [hoe Azure dev Spaces werkt en is geconfigureerd][how-dev-spaces-works]voor meer informatie over de werking van Azure dev Spaces.

## <a name="supported-regions-and-configurations"></a>Ondersteunde regio's en configuraties

Azure dev Spaces wordt alleen ondersteund door AKS-clusters in [sommige regio's][supported-regions]. Azure dev Spaces biedt ondersteuning voor het gebruik van de [Azure cli](/cli/azure/install-azure-cli?view=azure-cli-latest) -of [Visual Studio-code](https://code.visualstudio.com/download) met de [Azure dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) die is geïnstalleerd op Linux, macOS of Windows 8 of hoger om uw toepassingen op AKS te bouwen en uit te voeren. Het biedt ook ondersteuning voor het gebruik van [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) geïnstalleerd op Windows 8 of hoger. Voor Visual Studio 2019 hebt u de werk belasting Azure Development nodig. Voor Visual Studio 2017 hebt u de werk belasting voor Web Development en [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools)nodig.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over snelle, iteratieve ontwikkeling voor teams met Azure-ontwikkel ruimten met de [Snelstartgids voor team ontwikkeling][team-development-quickstart].

[how-dev-spaces-works]: how-dev-spaces-works.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-development-quickstart]: quickstart-team-development.md

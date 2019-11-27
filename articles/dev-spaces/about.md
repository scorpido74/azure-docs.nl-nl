---
title: Inleiding tot Azure dev Spaces
services: azure-dev-spaces
ms.date: 05/07/2019
ms.topic: overview
description: Inleiding tot Azure dev Spaces
keywords: Docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers, kubectl, K8S
manager: gwallace
ms.openlocfilehash: 08214e4be866da592e4852ef718ee82fa2a27d08
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74325795"
---
# <a name="introduction-to-azure-dev-spaces"></a>Inleiding tot Azure dev Spaces

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

Azure dev Spaces wordt alleen ondersteund door AKS-clusters in **VS-Oost**, VS- **Oost 2**, VS- **Midden**, Zuid- **Centraal vs**, VS- **West 2**, **Europa-Noord**, **Europa-West**, **UK-Zuid**, **Azië-Oost**, **Zuidoost-Azië**, **Australië-Oost**, **Canada-centraal**en **Canada-Oost** regio's. Azure Dev Spaces ondersteunt het gebruik van [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) of [Visual Studio Code](https://code.visualstudio.com/download) met een [Azure Dev Spaces-extensie](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) die is geïnstalleerd in Linux, MacOS of Windows 8 of hoger, om uw toepassingen te ontwikkelen en in AKS uit te voeren. Het biedt ook ondersteuning voor het gebruik van [Visual Studio](https://aka.ms/vsdownload?utm_source=mscom&utm_campaign=msdocs) geïnstalleerd op Windows 8 of hoger. Voor Visual Studio 2019 hebt u de werk belasting Azure Development nodig. Voor Visual Studio 2017 hebt u de werk belasting voor Web Development en [Visual Studio Tools voor Kubernetes](https://aka.ms/get-vsk8stools)nodig.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over snelle, iteratieve ontwikkeling voor teams met Azure-ontwikkel ruimten met de Snelstartgids voor team ontwikkeling.

> [!div class="nextstepaction"]
> [Quick start voor team ontwikkeling](quickstart-team-development.md)


[how-dev-spaces-works]: how-dev-spaces-works.md
[team-development-quickstart]: quickstart-team-development.md

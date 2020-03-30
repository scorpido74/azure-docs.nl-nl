---
title: Hoe het opsporen van uw code op afstand werkt met Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen voor het opsporen van externe fouten op Azure Kubernetes-service met Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241398"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Hoe het opsporen van uw code op afstand werkt met Azure Dev Spaces

Azure Dev Spaces biedt u meerdere manieren om Kubernetes-toepassingen snel te herhalen en te debuggen en samen te werken met uw team op een AKS-cluster (Azure Kubernetes Service). Zodra uw project in een dev-ruimte wordt uitgevoerd, biedt Azure Dev Spaces een manier om een lopende toepassing in AKS te koppelen en te debuggen.

In dit artikel wordt beschreven hoe het opsporen van externe debuggen met Dev Spaces werkt.

## <a name="debug-your-code"></a>Uw code debuggen

Voor Java-, .NET Core- en Node.js-toepassingen u uw toepassing die rechtstreeks in uw dev-ruimte wordt uitgevoerd, debuggen met behulp van Visual Studio Code of Visual Studio. Visual Studio Code en Visual Studio bieden tooling om verbinding te maken met uw dev-ruimte, uw toepassing te starten en een foutopsporing toe te voegen. Na `azds prep`het uitvoeren u uw project openen in Visual Studio Code of Visual Studio. Visual Studio Code of Visual Studio genereert hun eigen configuratiebestanden voor het verbinden die los staat van het uitvoeren. `azds prep` Vanuit Visual Studio Code of Visual Studio u breekpunten instellen en uw toepassing starten op uw ontwikkelruimte.

![Uw code debuggen](media/get-started-node/debug-configuration-nodejs2.png)

Wanneer u uw toepassing start met Visual Studio Code of Visual Studio voor foutopsporing, worden deze `azds up`gestart en verbinding met uw ontwikkelruimte gemaakt op dezelfde manier als hardlopen. Ook de client-side tooling in Visual Studio Code en Visual Studio bieden elk een extra parameter met specifieke informatie voor het debuggen. De parameter bevat de naam van de foutopsporing, de locatie van de foutopsporing in de afbeelding van de foutopsporing en de doellocatie in de container van de toepassing om de map debugger te monteren.

De foutopsporingsafbeelding wordt automatisch bepaald door de tooling aan de clientzijde. Het maakt gebruik van een methode die vergelijkbaar is met `azds prep`die welke wordt gebruikt tijdens Dockerfile en Helm grafiek genereren bij het uitvoeren van . Nadat de foutopsporing is gemonteerd in de afbeelding van `azds exec`de toepassing, wordt deze uitgevoerd met behulp van .

## <a name="next-steps"></a>Volgende stappen

Zie de volgende snelstarts om aan de slag te gaan met Azure Dev Spaces voor het opsporen van uw project op afstand:

* [Snel herhalen en debuggen met Visual Studio Code en Java][quickstart-java]
* [Snel herhalen en debuggen met Visual Studio Code en .NET][quickstart-netcore]
* [Snel herhalen en debuggen met Visual Studio Code en Node.js][quickstart-node]
* [Snel herhalen en debuggen met Visual Studio en .NET Core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md

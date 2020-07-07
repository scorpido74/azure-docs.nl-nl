---
title: Hoe kan ik de code op afstand opsporen met Azure dev Spaces?
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Beschrijft de processen voor externe fout opsporing op de Azure Kubernetes-service met Azure dev Spaces
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: a9f303ea6f954285a319be137a594f7ce127f740
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80241398"
---
# <a name="how-remote-debugging-your-code-with-azure-dev-spaces-works"></a>Hoe kan ik de code op afstand opsporen met Azure dev Spaces?

Met Azure dev Spaces kunt u op verschillende manieren snel en probleem oplossing voor Kubernetes-toepassingen en samen werken met uw team op een AKS-cluster (Azure Kubernetes service). Zodra het project wordt uitgevoerd in een dev Space, biedt Azure dev Spaces een manier om een actieve toepassing in AKS te koppelen en fouten op te sporen.

In dit artikel wordt beschreven hoe externe fout opsporing met ontwikkel ruimten werkt.

## <a name="debug-your-code"></a>Fouten opsporen in uw code

Voor Java, .NET core en Node.js toepassingen kunt u fouten opsporen in uw toepassing die rechtstreeks wordt uitgevoerd in uw ontwikkel ruimte met Visual Studio code of Visual Studio. Visual Studio code en Visual Studio bieden hulp middelen om verbinding te maken met uw ontwikkel ruimte, uw toepassing te starten en een fout opsporingsprogramma te koppelen. Na `azds prep` het uitvoeren van kunt u het project openen in Visual Studio code of Visual Studio. Visual Studio code of Visual Studio genereert hun eigen configuratie bestanden om verbinding te maken die niet wordt uitgevoerd `azds prep` . Vanuit Visual Studio code of Visual Studio kunt u onderbrekings punten instellen en uw toepassing starten op uw dev-ruimte.

![Fout opsporing voor uw code](media/get-started-node/debug-configuration-nodejs2.png)

Wanneer u uw toepassing start met Visual Studio code of Visual Studio voor het opsporen van fouten, worden de start-en verbinding maken met uw ontwikkel ruimte op dezelfde manier uitgevoerd als met `azds up` . Daarnaast bieden het programma aan de client zijde in Visual Studio code en Visual Studio elk een extra para meter met specifieke informatie voor fout opsporing. De para meter bevat de naam van de afbeelding van het fout opsporingsprogramma, de locatie van het fout opsporingsprogramma in de installatie kopie van de fout opsporing en de doel locatie binnen de container van de toepassing om de map met fout opsporing te koppelen.

De afbeelding van het fout opsporingsprogramma wordt automatisch bepaald door het hulp programma aan de client zijde. Er wordt gebruikgemaakt van een methode die vergelijkbaar is met die in de Dockerfile-en helm-grafiek wordt gegenereerd wanneer deze wordt uitgevoerd `azds prep` . Nadat het fout opsporingsprogramma in de installatie kopie van de toepassing is gekoppeld, wordt het uitgevoerd met `azds exec` .

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende Snelstartgids om aan de slag te gaan met Azure dev Spaces voor het op afstand opsporen van fouten in uw project:

* [Snel herhalen en fouten opsporen met Visual Studio code en Java][quickstart-java]
* [Snel herhalen en fouten opsporen met Visual Studio code en .NET][quickstart-netcore]
* [Snel herhalen en fouten opsporen met Visual Studio code en Node.js][quickstart-node]
* [Snel herhalen en fouten opsporen met Visual Studio en .NET core][quickstart-vs]


[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md
[quickstart-vs]: quickstart-netcore-visualstudio.md

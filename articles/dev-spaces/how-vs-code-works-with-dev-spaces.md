---
title: Hoe Visual Studio code werkt met Azure dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Meer informatie over hoe u met Visual Studio code en Azure dev Spaces fouten oplost en snel uw Kubernetes-toepassingen kunt herhalen
keywords: Azure dev Spaces, dev Spaces, docker, Kubernetes, azure, AKS, Azure Kubernetes service, containers
ms.openlocfilehash: 73a2ae1f603626acf7ec4d42838b301c88ee21da
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84307347"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hoe Visual Studio code werkt met Azure dev Spaces

U kunt Visual Studio code en de [Azure dev Spaces-extensie][azds-extension] gebruiken om uw services voor te bereiden, uit te voeren en fouten op te sporen met Azure dev Spaces. Met Visual Studio code en de Azure dev Spaces-extensie kunt u het volgende doen:

* Assets genereren voor het uitvoeren en opsporen van fouten in AKS
* Uw Java-, Node.js-en .NET Core-Services uitvoeren in een ontwikkel ruimte
* Fout opsporing voor uw Java-, Node.js-en .NET Core-Services die worden uitgevoerd in een ontwikkel ruimte

## <a name="generate-assets"></a>Assets genereren

Visual Studio code en de Azure dev Space-extensie genereren de volgende assets voor uw project:

* Dockerfiles voor Java-toepassingen met Maven, Node.js toepassingen en .NET core-toepassingen
* Helm-grafieken voor bijna elke taal met een Dockerfile
* Een `azds.yaml` bestand, het [configuratie bestand voor Azure dev Spaces][azds-yaml] voor uw project
* Een `.vscode` map met de Visual Studio code-start configuratie van uw project voor Java-toepassingen met behulp van Maven, Node.js toepassingen en .net core-toepassingen

De Dockerfile-, helm-grafiek en- `azds.yaml` bestanden zijn dezelfde activa die worden gegenereerd wanneer ze worden uitgevoerd `azds prep` . Deze bestanden kunnen ook buiten Visual Studio code worden gebruikt om uw project uit te voeren in AKS, zoals het uitvoeren van `azds up` . De `.vscode` map wordt alleen gebruikt door Visual Studio code voor het uitvoeren van uw project in AKS vanuit Visual Studio code.

## <a name="run-your-service-in-aks"></a>Uw service uitvoeren in AKS

Nadat u de assets voor uw project hebt gegenereerd, kunt u uw Java-, Node.js-en .NET Core-Services uitvoeren in een bestaande ontwikkel ruimte van Visual Studio code. Op de pagina *fout opsporing* van Visual Studio code kunt u de start configuratie aanroepen vanuit de `.vscode` Directory om uw project uit te voeren.

U moet uw AKS-cluster maken en Azure-ontwikkel ruimten in uw cluster buiten Visual Studio code inschakelen. U kunt bestaande Dockerfiles-, helm-grafieken en `azds.yaml` bestanden die buiten Visual Studio code zijn gemaakt, hergebruiken, zoals de activa die worden gegenereerd door uit te voeren `azds prep` . Als u activa opnieuw wilt gebruiken die buiten Visual Studio code zijn gegenereerd, hebt u nog steeds een `.vscode` map nodig. Deze `.vscode` map kan opnieuw worden gegenereerd door Visual Studio code en de Azure dev Spaces-extensie en uw bestaande assets worden niet overschreven.

Voor .NET core-projecten moet de C#- [extensie][csharp-extension] zijn geïnstalleerd om uw .net-service vanuit Visual Studio code uit te voeren. Voor Java-projecten met maven moet u ook de [Java-fout opsporingsprogramma voor Azure dev Spaces-extensie][java-extension] installeren [en Maven geïnstalleerd en geconfigureerd][maven] om uw Java-service vanuit Visual Studio code uit te voeren.

## <a name="debug-your-service-in-aks"></a>Fout opsporing voor uw service in AKS

Nadat u uw project hebt gestart, kunt u fouten opsporen in uw Java-, Node.js-en .NET Core Services die rechtstreeks vanuit Visual Studio code worden uitgevoerd in een dev-ruimte. De start configuratie in de `.vscode` Directory biedt extra informatie over fout opsporing voor het uitvoeren van een service met fout opsporing ingeschakeld in een dev-ruimte. Visual Studio code wordt ook gekoppeld aan het debugproces in de container die wordt uitgevoerd in uw ontwikkel ruimten, zodat u Verbreek punten kunt instellen, variabelen kunt inspecteren en andere fout opsporing kunt uitvoeren.

## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Visual Studio code gebruiken met Azure dev Spaces

U kunt Visual Studio code en de Azure dev Spaces-extensie in de volgende Quick starts gebruiken met Azure dev Spaces:

* [Snel herhalen en fouten opsporen met Visual Studio code en Java][quickstart-java]
* [Snel herhalen en fouten opsporen met Visual Studio code en .NET][quickstart-netcore]
* [Snel herhalen en fouten opsporen met Visual Studio code en Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md

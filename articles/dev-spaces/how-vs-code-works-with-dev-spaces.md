---
title: Hoe Visual Studio Code werkt met Azure Dev Spaces
services: azure-dev-spaces
ms.date: 07/08/2019
ms.topic: conceptual
description: Ontdek hoe Visual Studio Code en Azure Dev Spaces u helpen uw Kubernetes-toepassingen te debuggen en snel te herhalen
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers
ms.openlocfilehash: 91440e59fdb8c21579ef1f04e78e66f933221ba0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240445"
---
# <a name="how-visual-studio-code-works-with-azure-dev-spaces"></a>Hoe Visual Studio Code werkt met Azure Dev Spaces

U Visual Studio Code en de [Azure Dev Spaces-extensie][azds-extension] gebruiken om uw services voor te bereiden, uit te voeren en te debuggen met Azure Dev Spaces. Met Visual Studio Code en de Azure Dev Spaces-extensie u:

* Assets genereren voor het uitvoeren en foutopsporings van services in AKS
* Voer uw Java-, Node.js- en .NET Core-services uit in een dev-ruimte
* Debuging uw Java-, Node.js- en .NET Core-services die worden uitgevoerd in een dev-ruimte

## <a name="generate-assets"></a>Activa genereren

Visual Studio Code en de Azure Dev Spaces-extensie genereren de volgende elementen voor uw project:

* Dockerfiles voor Java-toepassingen met Maven-, Node.js-toepassingen en .NET Core-toepassingen
* Helm grafieken voor bijna elke taal met een Dockerfile
* Een `azds.yaml` bestand, het [configuratiebestand Azure Dev Spaces][azds-yaml] voor uw project
* Een `.vscode` map met de Visual Studio Code startconfiguratie van uw project voor Java-toepassingen met Maven-, Node.js-toepassingen en .NET Core-toepassingen

De Dockerfile, helmdiagram `azds.yaml` en bestanden zijn dezelfde `azds prep`elementen die worden gegenereerd bij het uitvoeren van . Deze bestanden kunnen ook buiten de Visual Studio-code worden gebruikt `azds up`om uw project in AKS uit te voeren, zoals het uitvoeren van . De `.vscode` map wordt alleen gebruikt door Visual Studio-code om uw project in AKS uit Te voeren vanuit Visual Studio Code.

## <a name="run-your-service-in-aks"></a>Uw service uitvoeren in AKS

Nadat u de assets voor uw project hebt gegenereerd, u uw Java-, Node.js- en .NET Core-services uitvoeren in een bestaande ontwikkelruimte vanuit Visual Studio Code. Op de pagina *Foutopsporing* van Visual Studio Code kunt `.vscode` u de startconfiguratie van de map aanroepen om uw project uit te voeren.

U moet uw AKS-cluster maken en Azure Dev Spaces in uw cluster inschakelen buiten Visual Studio Code. U bijvoorbeeld de Azure CLI of de Azure-portal gebruiken om deze installatie uit te stellen. U bestaande Dockerfiles, Helm-diagrammen en `azds.yaml` bestanden die buiten Visual Studio `azds prep`Code zijn gemaakt, opnieuw gebruiken, zoals de elementen die worden gegenereerd door het uitvoeren van. Als u elementen die buiten Visual Studio Code zijn `.vscode` gegenereerd, opnieuw gebruiken, moet u nog steeds een map hebben. Deze `.vscode` map kan worden geregenereerd door Visual Studio-code en de Azure Dev Spaces-extensie en overschrijft uw bestaande assets niet.

Voor .NET Core-projecten moet u de [C#-extensie][csharp-extension] hebben geïnstalleerd om uw .NET-service uit Visual Studio Code uit te voeren. Ook voor Java-projecten met Maven moet u de [Java-debugger voor Azure Dev Spaces-extensie][java-extension] hebben geïnstalleerd [en maven hebben geïnstalleerd en geconfigureerd][maven] om uw Java-service uit Visual Studio Code uit te voeren.

## <a name="debug-your-service-in-aks"></a>Uw service debuggen in AKS

Nadat u uw project hebt gestart, u uw Java-, Node.js- en .NET Core-services debuggen die rechtstreeks vanuit Visual Studio Code in een dev-ruimte worden uitgevoerd. De startconfiguratie `.vscode` in de map biedt de extra foutopsporingsinformatie voor het uitvoeren van een service met foutopsporing ingeschakeld in een dev-ruimte. Visual Studio Code hecht ook aan het foutopsporingsproces in de lopende container in uw dev-ruimten, zodat u breekpunten instellen, variabelen inspecteren en andere foutopsporingsbewerkingen uitvoeren.


## <a name="use-visual-studio-code-with-azure-dev-spaces"></a>Visual Studio-code gebruiken met Azure Dev-spaties

Visual Studio Code en de Azure Dev Spaces-extensie werken met Azure Dev Spaces in de volgende snelstarts:

* [Snel herhalen en debuggen met Visual Studio Code en Java][quickstart-java]
* [Snel herhalen en debuggen met Visual Studio Code en .NET][quickstart-netcore]
* [Snel herhalen en debuggen met Visual Studio Code en Node.js][quickstart-node]

[azds-extension]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azds-yaml]: how-dev-spaces-works-prep.md#prepare-your-code
[csharp-extension]: https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp
[java-extension]: https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-debugger-azds
[maven]: https://maven.apache.org
[quickstart-java]: quickstart-java.md
[quickstart-netcore]: quickstart-netcore.md
[quickstart-node]: quickstart-nodejs.md

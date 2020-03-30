---
title: Automatiseer de levenscyclus van uw apps met Visual Studio App Center en Azure-services
description: Meer informatie over de services zoals App Center waarmee u continue build en integratie voor uw mobiele toepassingen instellen.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 0560f47b832ec2965d9b567e1aeff78baa9c247c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240926"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatiseer de levenscyclus van uw apps met continue build en integratie

Als ontwikkelaars schrijf je code en check je deze in de coderepository, maar de commits die in de repo zijn ingecheckt, zijn mogelijk niet altijd consistent. Wanneer meerdere ontwikkelaars aan hetzelfde project werken, kunnen problemen integratie veroorzaken. Teams kunnen in situaties komen waar dingen niet werken, bugs zich opstapelen en projectontwikkeling vertraging oploopt. Ontwikkelaars moeten wachten tot de volledige softwarecode is gebouwd en getest om te controleren op fouten, waardoor het proces traag en minder iteratief. 

Met continue build en integratie kunnen ontwikkelaars builds vereenvoudigen en hun code testen door hun wijzigingen in de broncoderepository in te voeren en tests en verificaties in de build-omgeving te plaatsen. Op deze manier doen ze altijd tests tegen hun code. Alle wijzigingen in de broncode worden continu gebouwd wanneer er een commit is gemaakt in de repository. Bij elke check-in valideert en voert de CI-server (Continuous Integration) elke test uit die de ontwikkelaar heeft gemaakt. Als de tests niet slagen, wordt de code teruggestuurd voor verdere wijzigingen. Op deze manier breken de ontwikkelaars niet de builds die zijn gemaakt. Ze hoeven ook niet alle tests lokaal uit te voeren op hun computers, wat de productiviteit van ontwikkelaars verhoogt. 

## <a name="key-benefits"></a>Belangrijkste voordelen
- Automatiseer uw builds, tests en implementaties voor pijplijnen.
- Detecteer bugs en los problemen vroegtijdig op om snellere releasesnelheden te garanderen.
- Bega vaker code en bouw applicaties snel.
- Profiteer van flexibiliteit om code snel te wijzigen zonder problemen.
- Profiteer van een snellere time-to-market, zodat alleen code van goede kwaliteit het helemaal doorhaalt.
- Maak kleine codewijzigingen efficiënter omdat kleine stukjes code in één keer zijn geïntegreerd.
- Verhoog de transparantie en verantwoordingsplicht van het team, zodat u continue feedback krijgt van uw klanten en uw team.

Gebruik de volgende services om een pijplijn voor continue integratie in uw mobiele apps mogelijk te maken.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Build](/appcenter/build/) helpt u bij het bouwen van native en cross-platform applicaties waar uw team aan werkt met behulp van een beveiligde cloudinfrastructuur. Je je repo eenvoudig verbinden in Visual Studio App Center en beginnen met het bouwen van je app in de cloud op elke commit. U hoeft zich geen zorgen te maken over het lokaal configureren van buildservers, ingewikkelde configuraties en code die voortbouwt op de machine van een collega, maar niet op de uwe.

Met de extra kracht van Visual Studio App Center-services u uw workflow verder automatiseren. Met App Center Distribueren u builds automatisch vrijgeven aan testers en openbare app stores. U ook geautomatiseerde gebruikersinterfacetests uitvoeren op duizenden echte apparaat- en osconfiguraties in de cloud met App Center Test.

**Belangrijke functies**
- Stel continue integratie in enkele minuten in en bouw toepassingen vaker en sneller.
- Integreer met GitHub, BitBucket, Azure DevOps en GitLab.
- Maak snel en veilig bouwen op beheerde, cloud-hosted machines.
- Stel uw builds in staat om een test te starten en controleer of de app bouwt op echte iOS- en Android-apparaten.
- Krijg native en cross-platform ondersteuning voor iOS, Android, macOS, Windows, Xamarin en React Native.
- Pas je builds aan door post-clone, pre-build en post-build scripts toe te voegen.

**Verwijzingen**
- [Meld u aan bij Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center Build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure-pijplijnen
 [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/), een service in Azure DevOps, is een volledig uitgeruste continue integratie- en cd-service (continuous delivery) die werkt met uw favoriete Git-provider. Het kan worden geïmplementeerd voor de meeste grote cloudservices, waaronder Azure. U beginnen met uw code op GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud of Azure Repos. Vervolgens u de build, het testen en de implementatie van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services (AWS).

**Belangrijke functies**
- **Vereenvoudigde taakgebaseerde ervaring voor het instellen van een CI-server:** Stel een CI-server in voor zowel native (Android, iOS en Windows) als cross-platform (Xamarin, Cordova en React Native) mobiele applicaties, naast Microsoft en niet-Microsoft (Node.js, Java)-gebaseerde servertechnologieën.
- **Elke taal, platform en cloud:** Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android en iOS-toepassingen bouwen, testen en implementeren. Parallel draaien op Linux, macOS en Windows. Implementeren voor cloudproviders zoals Azure, AWS en Google Cloud Platform. Distribueer mobiele applicaties via bètakanalen en app stores.
- **Ondersteuning voor native containers:** Maak nieuwe containers met gemak, en duw ze naar elk register. Implementeer containers naar onafhankelijke hosts of Kubernetes.
- **Geavanceerde werkstromen:** Maak eenvoudig bouwketens en meerfasenbuilds. Krijg ondersteuning voor YAML, test integratie, release gates, rapportage en meer.
- **Uitbreidbaar:** Gebruik een reeks build-, test- en implementatietaken die door de community zijn gebouwd, waaronder honderden extensies van Slack naar SonarCloud. U zelfs implementeren vanuit andere CI-systemen, zoals Jenkins. Webhooks en REST API's kunnen u helpen integreren.
- **Gratis cloud-hosted builds:** Deze builds zijn beschikbaar voor openbare en private repositories.
- **Ondersteuning voor implementatie naar andere cloudleveranciers:** Leveranciers zijn AWS en Google Cloud Platform.

**Verwijzingen**
- [Aan de slag met Azure Pipelines-handleiding](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Aan de slag met Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Snel gestart](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Zie het artikel waarin [App Center Build versus Azure Pipelines](/appcenter/build/choose-between-services)worden vergeleken om u te helpen de juiste service voor uw toepassingsbuilds te kiezen.

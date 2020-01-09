---
title: De levens cyclus van uw apps automatiseren met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals App Center, waarmee u doorlopende build en integratie kunt instellen voor uw mobiele toepassingen.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 9b0a9e10a88836ce83e636db20180c3692ab4429
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75453195"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatiseer de levens cyclus van uw apps met doorlopende build en integratie

Als ontwikkel aars schrijft u code en controleert u deze in de code opslagplaats, maar de door voering die in de opslag plaats wordt ingecheckt, is mogelijk niet altijd consistent. Wanneer meerdere ontwikkel aars aan hetzelfde project werken, kunnen er problemen ontstaan met de integratie. Teams kunnen zich in situaties voordoen waarin geen dingen werken, het opdelen van fouten en de project ontwikkeling worden vertraagd. Ontwikkel aars moeten wachten tot de volledige software code is gebouwd en getest om te controleren op fouten, waardoor het proces langzaam en minder iteratief wordt. 

Met doorlopend bouwen en integreren kunnen ontwikkel aars de builds vereenvoudigen en hun code testen door hun wijzigingen door te voeren in de opslag plaats van de bron code en tests en verificaties in de build-omgeving te zetten. Op deze manier worden de tests altijd uitgevoerd op basis van hun code. Alle wijzigingen die zijn aangebracht aan de bron code, worden doorlopend gebouwd wanneer er een door Voer aan de opslag plaats wordt gemaakt. Bij elke inchecking valideert de CI-server (Continuous Integration) de test die de ontwikkelaar heeft gemaakt en voert deze uit. Als de tests niet worden door gegeven, wordt de code teruggestuurd voor verdere wijzigingen. Op deze manier worden de gemaakte builds niet verbroken door de ontwikkel aars. Ze hoeven ook niet alle tests lokaal op hun computers uit te voeren, waardoor de productiviteit van de ontwikkel aars wordt verhoogd. 

## <a name="key-benefits"></a>Belangrijkste voordelen
- Automatiseer uw builds, tests en implementaties voor pijp lijnen.
- Detecteer bugs en los problemen op om de release tarieven te verhogen.
- Leg code vaker toe en bouw snel toepassingen.
- De flexibiliteit om code snel te wijzigen zonder problemen te voor komen.
- U kunt sneller op de markt brengen, zodat alleen de goede kwaliteit van de code optimaal is.
- Maak kleine code wijzigingen efficiënter, omdat kleine stukjes code tegelijk worden geïntegreerd.
- Verg root de transparantie en verantwoording van het team zodat u doorlopende feedback krijgt van uw klanten en uw team.

Gebruik de volgende services om een continue integratie pijplijn in uw mobiele apps in te scha kelen.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center-build](/appcenter/build/) helpt u bij het bouwen van systeem eigen en platformoverschrijdende toepassingen waaraan uw team werkt met behulp van een veilige Cloud infrastructuur. U kunt uw opslag plaats eenvoudig verbinden in Visual Studio App Center en uw app in de Cloud bouwen tijdens elke door voer. U hoeft zich geen zorgen te maken over het configureren van on-site build-servers, gecompliceerde configuraties en code die is gebaseerd op de computer van een collega, maar niet op uw eigen locatie.

Met de toegevoegde kracht van Visual Studio App Center Services kunt u uw werk stroom verder automatiseren. U kunt builds voor testers en open bare App Stores automatisch vrijgeven met App Center distribueren. U kunt ook geautomatiseerde UI-tests uitvoeren op duizenden echte apparaten en besturingssysteem configuraties in de Cloud met App Center test.

**Belangrijke functies**
- Stel in een paar minuten doorlopende integratie in en bouw toepassingen vaker en sneller.
- Integreer met GitHub, BitBucket, Azure DevOps en GitLab.
- Maak snelle en veilige builds op beheerde, in de Cloud gehoste machines.
- Schakel uw builds in om de test te starten en te controleren of de app is gebouwd in echte iOS-en Android-apparaten.
- Profiteer van systeem eigen en platformoverschrijdende ondersteuning voor iOS, Android, macOS, Windows, Xamarin en reageren systeem eigen.
- Pas uw builds aan door scripts voor post-Clone, pre-build en post-build toe te voegen.

**Referentie**
- [Meld u aan met Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/), een service in azure DevOps, is een volledig uitgeruste service voor continue integratie en continue levering (cd) die geschikt is voor uw favoriete Git-provider. Het kan worden geïmplementeerd in de meeste belang rijke Cloud Services, waaronder Azure. U kunt beginnen met uw code op GitHub, GitHub Enter prise server, GitLab, bitbucket Cloud of Azure opslag plaatsen. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services (AWS).

**Belangrijke functies**
- **Vereenvoudigde op taken gebaseerde ervaring voor het instellen van een CI-server:** Stel een CI-server in voor zowel systeem eigen (Android-, iOS-en Windows-) als andere platform-apps (Xamarin, Cordova en reagerend systeem eigen), naast de server technologieën van micro soft en niet van micro soft (node. js, Java).
- **Elke taal, elk platform en elke Cloud:** Node. js-, Python-, Java-, PHP-, Ruby-, go-, C/C++, C#, Android-en IOS-toepassingen bouwen, testen en implementeren. Voer taken parallel uit op Linux, macOS en Windows. Implementeer op cloud providers zoals Azure, AWS en Google Cloud Platform. Distribueer mobiele toepassingen via bèta kanalen en app stores.
- **Ondersteuning voor native container:** Maak met gemak nieuwe containers en push ze naar een REGI ster. Implementeer containers op onafhankelijke hosts of Kubernetes.
- **Geavanceerde werk stromen:** Maak eenvoudig bouw ketens en Multiphase builds. Krijg ondersteuning voor YAML, test integratie, release-Gates, rapportage en meer.
- **Uitbreidbaar:** Gebruik een reeks bouw-, test-en implementatie taken die door de community zijn gebouwd, waaronder honderden uitbrei dingen van vertraging tot SonarCloud. U kunt zelfs implementeren vanuit andere CI-systemen, zoals Jenkins. Webhooks en REST-Api's kunnen u helpen bij de integratie.
- **Gratis in de Cloud gehoste builds:** Deze builds zijn beschikbaar voor open bare en privé-opslag plaatsen.
- **Ondersteuning voor implementatie naar andere Cloud leveranciers:** Leveranciers zijn AWS en Google Cloud Platform.

**Referentie**
- [Aan de slag met de gids voor Azure-pijp lijnen](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Aan de slag met Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Snelstartgidsen](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Zie het artikel dat [app Center build versus Azure-pijp lijnen](/appcenter/build/choose-between-services)vergelijkt om u te helpen bij het kiezen van de juiste service voor uw toepassings builds.

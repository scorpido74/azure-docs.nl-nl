---
title: De levens cyclus van uw apps automatiseren met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals App Center, waarmee u doorlopende build en integratie kunt instellen voor uw mobiele toepassingen.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 07e8e0edc4638c6c0bf9acc205175aa731c0f8bc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795547"
---
# <a name="automate-the-lifecycle-of-your-apps-with-continuous-build-and-integration"></a>Automatiseer de levens cyclus van uw apps met doorlopende build en integratie

Als ontwikkel aars schrijft u code en incheckt u deze in de code opslagplaats, maar de door voering die in de opslag plaats is ingecheckt, is mogelijk niet altijd consistent. Wanneer er meerdere ontwikkel aars aan hetzelfde project werken, worden er problemen met de integratie gemaakt en kan het team worden uitgevoerd in situaties waar geen dingen werken, de fouten blijven Piling en de ontwikkeling van het project wordt vertraagd. Ontwikkel aars moeten wachten tot de gehele software code is gebouwd en getest om te controleren op fouten en Hiermee wordt het proces trager en minder iteratief. 

Met **doorlopend bouwen en integreren**kunnen ontwikkel aars eenvoudiger hun code bouwen en testen door hun wijzigingen niet alleen door te voeren in de opslag plaats van de bron code, maar ook om testen en verificaties in de build-omgeving te plaatsen, zodat ze altijd worden uitgevoerd. tests worden uitgevoerd op basis van hun code. Alle wijzigingen aan de bron code worden doorlopend gemaakt wanneer er een door Voer aan de opslag plaats wordt gemaakt. Bij elke check-in valideert de CI-server de test die de ontwikkelaar heeft gemaakt en voert deze uit. Als de tests niet worden door gegeven, wordt de code teruggestuurd voor verdere wijzigingen. Zo kan de ontwikkelaar de gemaakte builds niet onderbreken en niet alle tests lokaal op hun computer uitvoeren, waardoor de productiviteit van ontwikkel aars wordt verhoogd. 

## <a name="key-benefits"></a>Belangrijkste voordelen
- **Automatisch** pijp lijn bouwen, testen en implementeren.
- **Detecteer bugs en los problemen** op om de release tarieven te verhogen.
- **Leg code** vaker toe en bouw snel toepassingen.
- **Flexibiliteit** om snel zonder problemen code te wijzigen.
- Een **snellere time-to-Market** zorgt ervoor dat de code van alleen goede kwaliteit deze helemaal kan passeren.
- **Kleine code wijzigingen** omdat hiermee kleine stukjes code tegelijk kunnen worden geïntegreerd.
- **Verg root de transparantie en verantwoording** van het team kunt u **voortdurend feedback** krijgen die niet alleen van uw klanten maar ook uw team is.

Gebruik de volgende services om continue integratie pijplijn in te scha kelen in uw mobiele apps.

## <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center build](/appcenter/build/) -service helpt u bij het bouwen van systeem eigen en platformoverschrijdende toepassingen waaraan uw team werkt, met behulp van een veilige Cloud infrastructuur. U kunt uw opslag plaats eenvoudig aansluiten in App Center en uw app in de Cloud bouwen op elke door voering zonder dat u zich zorgen hoeft te maken over het configureren van build-servers, gecompliceerde configuraties en code die voortbouwt op de computer van een collega, maar niet aan uw bedrijf.

Met de toegevoegde kracht van App Center Services kunt u uw werk stroom verder automatiseren. Release automatisch builds met testers en open bare App Stores met App Center distribueren, of voer automatische UI-tests uit op duizenden echte apparaten en besturingssysteem configuraties in de Cloud met App Center test.

**Belangrijkste functies**
- Stel binnen enkele minuten **een continue integratie** in en bouw toepassingen vaker en sneller.
- Integreer met **github, BitBucket, Azure DevOps en GitLab**.
- **Snelle en veilige builds** op beheerde, in de Cloud gehoste computers.
- **Schakel uw builds in op ' Test starten '** en controleer of de app is gebouwd in echte IOS-en Android-apparaten.
- **Systeem eigen en platformoverschrijdende ondersteuning** : IOS, Android, MacOS, Windows, Xamarin, reageert systeem eigen.
- **Pas uw builds** aan door scripts voor post-Clone, pre-build en post-build toe te voegen.

**Referentie**
- [Meld u aan met App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center build](/appcenter/build/)

## <a name="azure-pipelines"></a>Azure Pipelines
 [Azure pipelines](https://azure.microsoft.com/services/devops/pipelines/), een service in azure DevOps, is een volledig functionele service voor continue integratie (CI) en continue levering (cd) die geschikt is voor uw favoriete Git-provider en kan worden geïmplementeerd voor de meeste belang rijke Cloud Services, waaronder Azure. U kunt beginnen met uw code op GitHub, GitHub Enter prise server, GitLab, bitbucket Cloud of Azure opslag plaatsen. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

**Belangrijkste functies**
- Een vereenvoudigde op taken gebaseerde ervaring voor het instellen van een CI-server voor zowel systeem eigen (Android-, iOS-en Windows-) als platformoverschrijdende (Xamarin, Cordova en gereageerde) mobiele toepassingen, naast micro soft en niet-micro soft (node. js, Java) gebaseerde server gebruikte.
- **Elke taal, platform en Cloud** : node. js-, Python-, Java-, PHP-, Ruby-, go-, C/C++, C#, Android-en IOS-toepassingen bouwen, testen en implementeren. Voer taken parallel uit op Linux, macOS en Windows. Implementeer op cloud providers zoals Azure, AWS en GCP. Distribueer mobiele toepassingen via bèta kanalen en app stores.
- **Ondersteuning voor native container** : Maak nieuwe containers met gemak en push ze naar een REGI ster. Implementeer containers op onafhankelijke hosts of Kubernetes.
- **Geavanceerde werk stromen** -eenvoudig bouwen koppelen en meerdere fasen. Ondersteuning voor YAML, testen van de integratie, release Gates, rapportage en meer.
- **Uitbreidbaar** : gebruik een reeks bouw-, test-en implementatie taken die door de community zijn gebouwd: honderden uitbrei dingen van vertraging tot SonarCloud. U kunt zelfs implementeren vanuit andere CI-systemen, zoals Jenkins, en webhooks en REST-Api's gebruiken om u te helpen bij de integratie van
- **Gratis in de Cloud gehoste builds** voor open bare en particuliere opslag plaatsen.
- **Ondersteunt implementatie naar andere Cloud leveranciers** , zoals Amazon Web Services, Google Cloud platform enz.

**Referentie**
- [Aan de slag met de gids voor Azure-pijp lijnen](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Aan de slag met Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
- [Snelstartgidsen](/azure/devops/pipelines/create-first-pipeline?view=azure-devops&tabs=tfs-2018-2)

Volg de keuze van de juiste service voor de builds van uw toepassing door het artikel te volgen dat [app Center build versus Azure-pijp lijnen](/appcenter/build/choose-between-services)vergelijkt.

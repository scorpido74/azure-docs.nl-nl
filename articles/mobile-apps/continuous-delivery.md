---
title: Automatiseer de implementatie en release van uw mobiele toepassingen met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals App Center, waarmee u de continue leverings pijplijn kunt instellen voor uw mobiele toepassingen.
author: elamalani
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 3b8305d02345abe98b674a8ed61626eaf6b5528d
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795599"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatiseer de implementatie en release van uw mobiele toepassingen met continue leverings Services

Als ontwikkel aars schrijft u code en incheckt u deze in de code opslagplaats, maar de door voering die in de opslag plaats is ingecheckt, is mogelijk niet altijd consistent. Wanneer er meerdere ontwikkel aars aan hetzelfde project werken, worden er problemen met de integratie gemaakt en kan het team worden uitgevoerd in situaties waar geen dingen werken, de fouten blijven Piling en de ontwikkeling van het project wordt vertraagd. Ontwikkel aars moeten wachten tot de volledige software code is gebouwd en getest om te controleren op fouten en Hiermee wordt het proces trager en minder iteratief.

Met **continue levering**automatiseert u de implementatie en release van uw mobiele toepassingen, ongeacht of u de toepassing distribueert naar een groep testers of werk nemers van het bedrijf (voor bèta tests) of de App Store (voor productie). Het maakt implementaties minder riskant, stimuleert snelle iteraties en biedt u de mogelijkheid om op een continue manier nieuwe wijzigingen aan te brengen aan uw klanten.

## <a name="distribute-application-binaries-to-beta-testers"></a>Binaire bestanden van toepassingen distribueren naar bèta testers
Bèta test uw mobiele toepassing is een van de kritieke stappen tijdens het ontwikkelings proces van de toepassing. Het helpt om in een vroeg stadium en de feedback van uw toepassing fouten en problemen op te sporen die de kwaliteit van uw toepassing verg root voor productie gebruik.

Gebruik de volgende services om continue leverings pijplijn in te scha kelen in uw mobiele apps.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribueren](/appcenter/distribution/) is een hulp programma waarmee ontwikkel aars snel builds kunnen opheffen op apparaten van eind gebruikers. Met een volledige installatie Portal-ervaring distribueren is niet alleen een krachtige oplossing voor de distributie van bèta-app-testen, maar ook een handig alternatief voor distributie via de open bare app stores. Ontwikkel aars kunnen hun distributie werk stroom nog verder automatiseren met App Center build en open bare toepassingen Store-integraties.

**Belangrijkste functies**
- **Distribueer uw app naar bèta testers en gebruikers** en zorg ervoor dat alle testers de nieuwste versie van uw toepassing hebben.
- **Informeer testers van nieuwe releases** zonder dat de testers de laad stroom opnieuw gaan door lopen.
- **Distributie groepen beheren** voor verschillende versies van uw toepassing.
- **Distributie naar winkels** 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [InTune](/appcenter/distribution/stores/intune)
- **Platform ondersteuning** : IOS, Android, MacOS, TvOS, Xamarin, native, unit, Cordova.
- IOS-apparaten automatisch registreren bij uw inrichtings profiel.

**Referentie**
- [Meld u aan met App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center distribueren](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure Pipelines

[Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) is een volledig uitgeruste service voor continue integratie (CI) en continue levering (cd) die geschikt is voor uw favoriete Git-provider en kan worden geïmplementeerd voor de meeste belang rijke Cloud Services, waaronder Azure-Services. U kunt beginnen met uw code op GitHub, GitHub Enter prise server, GitLab, bitbucket Cloud of Azure opslag plaatsen. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services.

**Belangrijkste functies**
- Vereenvoudigde op taken gebaseerde ervaring voor het instellen van een CI-server voor zowel **systeem eigen (Android-, Ios-en Windows-) als platformoverschrijdende (Xamarin, Cordova en gereageerd systeem eigen) mobiele toepassingen**.
- **Elke taal, platform en Cloud** : node. js, Python, Java, PHP, Ruby, go, C/C++, C#, Android en IOS-apps bouwen, testen en implementeren. Voer taken parallel uit op Linux, macOS en Windows. Implementeer op cloud providers zoals Azure, AWS en GCP. Distribueer mobiele toepassingen via bèta kanalen en app stores.
- **Ondersteuning voor native container** : Maak nieuwe containers met gemak en push ze naar een REGI ster. Implementeer containers op onafhankelijke hosts of Kubernetes.
- **Geavanceerde werk stromen en functies** -eenvoudig bouwen koppelen en meerdere fasen. Ondersteuning voor YAML, testen van de integratie, release Gates, rapportage en meer.
- **Uitbreidbaar** : gebruik een reeks bouw-, test-en implementatie taken die door de community zijn gebouwd: honderden uitbrei dingen van vertraging tot SonarCloud. U kunt zelfs implementeren vanuit andere CI-systemen, zoals Jenkins. Webhooks en REST-Api's helpen u bij het integreren van
- **Gratis in de Cloud gehoste builds** voor open bare en particuliere opslag plaatsen.
- **Ondersteunt implementatie naar andere Cloud leveranciers** zoals AWS, GCP enzovoort.

**Referentie**
- [Aan de slag met de gids voor Azure-pijp lijnen](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Aan de slag met Azure DevOps](https://app.vsaex.visualstudio.com/signup/) 
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribueer uw toepassing rechtstreeks naar App Stores
Zodra uw toepassing gereed is voor productie gebruik en u wilt dat deze openbaar wordt gebruikt, moet deze worden ingediend bij App Stores waar deze door klanten kan worden gedownload. Er zijn meerdere manieren om uw toepassing rechtstreeks naar App Stores te distribueren. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Met [app Center distribueren](/appcenter/distribution/stores/) service kunt u uw mobiele toepassingen rechtstreeks naar App Stores publiceren. Zodra uw toepassing gereed is om te worden gedownload door eind gebruikers, kunt u de binaire bestanden van de toepassing rechtstreeks vanuit de portal van App Center publiceren.  

U kunt rechtstreeks distribueren naar:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
Met de App Store die door Apple is ontwikkeld en onderhouden, kunnen gebruikers toepassingen zoeken en downloaden die zijn ontwikkeld voor iOS-, MacOS-, WatchOS-en tvOS-apparaten. Ontwikkel aars moeten hun iOS-apps verzenden naar Apple Store voor openbaar gebruik.

### <a name="google-play"></a>Google Play

Google Play is de officiële App Store voor Android-besturings systemen, waarmee gebruikers toepassingen kunnen zoeken en downloaden die zijn ontwikkeld voor Android-apparaten en die zijn gepubliceerd via Google.

### <a name="intune"></a>Intune

[Microsoft intune](/intune/app-management) is een Cloud service in het Enter prise Mobility Management (EMM) ruimte waarmee uw werk nemers productief kunnen zijn terwijl uw bedrijfs gegevens beveiligd blijven. Met intune kunt u 
- De mobiele apparaten en Pc's beheren die door uw werk nemers worden gebruikt om toegang te krijgen tot Bedrijfs gegevens.
- De mobiele toepassingen beheren die door uw werk nemers worden gebruikt.
- Bescherm uw bedrijfs gegevens door de manier waarop uw werk nemers toegang hebben tot uw bedrijf te beheren en te delen.
- Zorg ervoor dat apparaten en toepassingen voldoen aan de beveiligings vereisten van het bedrijf.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Updates rechtstreeks implementeren op apparaten van gebruikers

### <a name="codepush"></a>CodePush
Met de [CodePush](/appcenter/distribution/codepush/) -service in app Center kunt u Apache Cordova en systeem eigen ontwikkel aars reageren op het rechtstreeks implementeren van mobiele toepassings updates op de apparaten van hun gebruikers. Het werkt als een centrale opslag plaats waarmee ontwikkel aars bepaalde updates kunnen publiceren (bijvoorbeeld JS-, HTML-, CSS-en afbeeldings wijzigingen). Vervolgens kunnen toepassingen een query uitvoeren op updates vanuit de opslag plaats met behulp van de meegeleverde client-Sdk's. Op die manier kunt u een meer deterministisch en direct engagement model met uw eind gebruikers hebben, terwijl u fouten opadresseert of kleine functies toevoegt, zonder dat u een binair element hoeft op te bouwen of het opnieuw te distribueren via een open bare App Store.

**Belangrijkste functies**
- Staat Cordova toe en reageert systeem eigen ontwikkel aars om mobiele toepassings updates rechtstreeks te implementeren op de apparaten van hun gebruikers zonder dat ze een Store hoeven op te heffen.
- Dit is handig voor het oplossen van fouten of het toevoegen/verwijderen van kleine functies die niet nodig zijn om binaire bestanden opnieuw te bouwen en opnieuw te distribueren via de respectieve winkels.

**Referentie**
- [Meld u aan met App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met code push in App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
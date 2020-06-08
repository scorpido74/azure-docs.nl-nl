---
title: Automatiseer de implementatie en release van uw mobiele toepassingen met Visual Studio App Center en Azure-Services
description: Meer informatie over de services, zoals App Center, waarmee u de continue leverings pijplijn kunt instellen voor uw mobiele toepassingen.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: mobile-services
ms.topic: article
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: ed268bf2be69aece8a5f7e51c25ef0ff539e8c7e
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84483125"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatiseer de implementatie en release van uw mobiele toepassingen met continue leverings Services

Als ontwikkel aars schrijft u code en controleert u deze in de code opslagplaats, maar de door voering die in de opslag plaats wordt ingecheckt, is mogelijk niet altijd consistent. Wanneer meerdere ontwikkel aars aan hetzelfde project werken, kunnen er problemen ontstaan met de integratie. Teams kunnen zich in situaties voordoen waarin geen dingen werken, het opdelen van fouten en de project ontwikkeling worden vertraagd. Ontwikkel aars moeten wachten tot de volledige software code is gebouwd en getest om te controleren op fouten, waardoor het proces langzaam en minder iteratief wordt.

Met continue levering automatiseert u de implementatie en release van uw mobiele toepassingen. Het maakt niet uit of u de toepassing distribueert naar een groep testers of werk nemers van het bedrijf (voor bèta tests) of naar een App Store (voor productie). Continue levering maakt implementaties minder riskant en stimuleert snelle iteraties. U kunt ook op een continue manier nieuwe wijzigingen aan uw klanten vrijgeven.

## <a name="distribute-application-binaries-to-beta-testers"></a>Binaire bestanden van toepassingen distribueren naar bèta testers
Bèta test uw mobiele toepassing is een van de kritieke stappen tijdens het ontwikkelings proces van de toepassing. Het helpt om in een vroeg stadium fouten en problemen in uw toepassing te vinden. De feedback verbetert de kwaliteit van uw toepassing wanneer u deze voor bereid bent voor productie gebruik.

Gebruik de volgende services om een continue leverings pijplijn in te scha kelen in uw mobiele apps.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center distribueren](/appcenter/distribution/) is een hulp programma waarmee ontwikkel aars snel builds op apparaten kunnen loslaten. Met een volledige installatie Portal-ervaring App Center distribueren is een krachtige oplossing voor de distributie van bèta-app-testen. Het is ook een handig alternatief voor distributie via open bare app stores. Ontwikkel aars kunnen hun distributie werk stroom nog verder automatiseren met App Center build en open bare toepassingen Store-integraties.

**Belangrijke functies**
- Distribueer uw app naar bèta testers en gebruikers en zorg ervoor dat alle testers de nieuwste versie van uw toepassing hebben.
- Informeer testers van nieuwe releases zonder dat de testers de laad stroom opnieuw gaan door lopen.
- Distributie groepen beheren voor verschillende versies van uw toepassing.
- Distribueren naar winkels: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Profiteer van platform ondersteuning voor iOS, Android, macOS, tvOS, Xamarin, systeem eigen, eenheids-en Cordova.
- IOS-apparaten automatisch registreren bij uw inrichtings profiel.

**Verwijzingen**
- [Meld u aan met Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center distribueren](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure-pijplijnen

[Azure-pijp lijnen](https://azure.microsoft.com/services/devops/pipelines/) is een volledig uitgeruste service voor continue integratie (CI) en continue levering (cd) die geschikt is voor uw favoriete Git-provider. Azure-pijp lijnen kunnen worden geïmplementeerd voor de meeste belang rijke Cloud Services, zoals Azure-Services. U kunt beginnen met uw code op GitHub, GitHub Enter prise server, GitLab, bitbucket Cloud of Azure opslag plaatsen. Vervolgens kunt u het bouwen, testen en implementeren van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services (AWS).

**Belangrijke functies**
- **Vereenvoudigde op taken gebaseerde ervaring voor het instellen van een CI-server:** Stel een CI-server voor zowel systeem eigen (Android-, iOS-en Windows-) als platform voor mobiele toepassingen (Xamarin, Cordova en reageren systeem eigen) in.
- **Elke taal, elk platform en elke Cloud:** Node. js-, Python-, Java-, PHP-, Ruby-, go-, C/C++-, C#-, Android-en iOS-apps bouwen, testen en implementeren. Parallel uitvoeren in Linux, macOS en Windows. Implementeer op cloud providers zoals Azure, AWS en Google Cloud Platform. Distribueer mobiele toepassingen via bèta kanalen en app stores.
- **Ondersteuning voor native container:** Maak met gemak nieuwe containers en push ze naar een REGI ster. Implementeer containers op onafhankelijke hosts of Kubernetes.
- **Geavanceerde werk stromen en functies:** Maak eenvoudig bouw ketens en Multiphase builds. Krijg ondersteuning voor YAML, test integratie, release-Gates, rapportage en meer.
- **Uitbreidbaar:** Gebruik een reeks bouw-, test-en implementatie taken die door de community zijn gebouwd, waaronder honderden uitbrei dingen van vertraging tot SonarCloud. U kunt zelfs implementeren vanuit andere CI-systemen, zoals Jenkins. Webhooks en REST-Api's kunnen u helpen bij de integratie.
- **Gratis in de Cloud gehoste builds:** Deze builds zijn beschikbaar voor open bare en privé-opslag plaatsen.
- **Ondersteuning voor implementatie naar andere Cloud leveranciers:** Leveranciers zijn AWS en Google Cloud Platform.

**Verwijzingen**
- [Aan de slag met de gids voor Azure-pijp lijnen](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Aan de slag met Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Distribueer uw toepassing rechtstreeks naar App Stores
Nadat uw toepassing gereed is voor productie gebruik en u wilt dat deze openbaar wordt gebruikt, moet deze worden ingediend bij App Stores waar deze door klanten kan worden gedownload. Er zijn meerdere manieren om uw toepassing rechtstreeks naar App Stores te distribueren. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Met [app Center distribueren](/appcenter/distribution/stores/)kunt u uw mobiele toepassingen rechtstreeks naar App Stores publiceren. Nadat uw toepassing gereed is om te worden gedownload door gebruikers, kunt u de binaire bestanden van de toepassing rechtstreeks vanuit de portal van Visual Studio App Center publiceren. 

U kunt rechtstreeks distribueren naar:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
In de App Store die door Apple is ontwikkeld en onderhouden, kunnen gebruikers toepassingen zoeken en downloaden die zijn ontwikkeld voor iOS-, MacOS-, WatchOS-en tvOS-apparaten. Ontwikkel aars moeten hun iOS-apps indienen bij de Apple App Store voor openbaar gebruik.

### <a name="google-play"></a>Google Play

Google Play is de officiële App Store voor Android-besturings systemen, waar gebruikers toepassingen kunnen zoeken en downloaden die zijn ontwikkeld voor Android-apparaten die zijn gepubliceerd via Google.

### <a name="intune"></a>Intune

[Microsoft intune](/intune/app-management) is een Cloud service in de Enter prise Mobility Management-ruimte waarmee uw werk nemers productief kunnen zijn terwijl uw bedrijfs gegevens beveiligd blijven. Met Intune kunt u het volgende doen:
- De mobiele apparaten en pc's beheren die door uw werknemers worden gebruikt voor toegang tot bedrijfsgegevens.
- De mobiele toepassingen beheren die door uw werk nemers worden gebruikt.
- Bescherm uw bedrijfs gegevens door de manier waarop uw werk nemers toegang hebben tot uw bedrijf te beheren en te delen.
- Zorg ervoor dat apparaten en toepassingen voldoen aan de beveiligings vereisten van het bedrijf.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Updates rechtstreeks implementeren op apparaten van gebruikers

### <a name="codepush"></a>CodePush
Met [CodePush](/appcenter/distribution/codepush/) in app Center kunnen Apache Cordova en systeem eigen ontwikkel aars reageren op mobiele toepassings updates rechtstreeks op de apparaten van hun gebruikers. Het fungeert als een centrale opslag plaats waarmee ontwikkel aars bepaalde updates kunnen publiceren, zoals Java script, HTML, CSS en afbeeldings wijzigingen. Vervolgens kunnen toepassingen een query uitvoeren op updates uit de opslag plaats met behulp van de meegeleverde client-Sdk's. Op deze manier kunt u een meer deterministisch en direct engagement model met uw gebruikers hebben terwijl u fouten verhelpt of kleine functies toevoegt. U hoeft geen binaire bestanden opnieuw samen te stellen of deze opnieuw te distribueren via open bare app stores.

**Belangrijke functies**
- Cordova en reageren systeem eigen ontwikkel aars kunnen mobiele-toepassings updates rechtstreeks op de apparaten van de gebruikers implementeren zonder dat ze een archief hoeven op te heffen.
- Nuttig voor het oplossen van fouten of het toevoegen en verwijderen van kleine functies die u niet nodig hebt om binaire bestanden opnieuw te bouwen en opnieuw te distribueren via de respectieve winkels.

**Verwijzingen**
- [Meld u aan met Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met CodePush in App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
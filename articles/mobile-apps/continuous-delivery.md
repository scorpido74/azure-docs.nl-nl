---
title: Automatiseer de implementatie en release van uw mobiele toepassingen met Visual Studio App Center en Azure-services
description: Meer informatie over de services zoals App Center waarmee u een pijplijn voor continue levering voor uw mobiele toepassingen instellen.
author: codemillmatt
ms.assetid: 34a8a070-9b3c-4faf-8588-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: ccfd0fc0450a6fbd34192dce7e375fe2de7f47c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235343"
---
# <a name="automate-the-deployment-and-release-of-your-mobile-applications-with-continuous-delivery-services"></a>Automatiseer de implementatie en release van uw mobiele applicaties met continue leveringsservices

Als ontwikkelaars schrijf je code en check je deze in de coderepository, maar de commits die in de repo zijn ingecheckt, zijn mogelijk niet altijd consistent. Wanneer meerdere ontwikkelaars aan hetzelfde project werken, kunnen problemen integratie veroorzaken. Teams kunnen in situaties komen waar dingen niet werken, bugs zich opstapelen en projectontwikkeling vertraging oploopt. Ontwikkelaars moeten wachten tot de volledige softwarecode is gebouwd en getest om te controleren op fouten, waardoor het proces traag en minder iteratief.

Met continue levering automatiseert u de implementatie en release van uw mobiele applicaties. Het maakt niet uit of u de toepassing distribueert naar een groep testers of werknemers van het bedrijf (voor bètatests) of naar een app store (voor productie). Continue levering maakt implementaties minder riskant en moedigt snelle iteraties aan. U ook voortdurend nieuwe wijzigingen aan uw klanten vrijgeven.

## <a name="distribute-application-binaries-to-beta-testers"></a>Toepassingsbinaries distribueren naar bètatesters
Bètatesten van uw mobiele applicatie is een van de kritieke stappen tijdens het ontwikkelingsproces van toepassingen. Het helpt om bugs en problemen in uw toepassing vroeg te vinden. De feedback verbetert de kwaliteit van uw toepassing wanneer u deze klaar maakt voor productiegebruik.

Gebruik de volgende services om een pijplijn voor continue levering in uw mobiele apps in te schakelen.

### <a name="visual-studio-app-center"></a>Visual Studio App Center
[App Center Distribute](/appcenter/distribution/) is een hulpmiddel voor ontwikkelaars om builds snel vrij te geven op apparaten. Met een complete installatieportalervaring is App Center Distribute een krachtige oplossing voor de distributie van bèta-apps. Het is ook een handig alternatief voor distributie via openbare app stores. Ontwikkelaars kunnen hun distributieworkflow nog verder automatiseren met App Center Build en integraties in openbare applicatieopslag.

**Belangrijke functies**
- Distribueer uw app naar bètatesters en gebruikers en zorg ervoor dat al uw testers zich op de nieuwste versie van uw toepassing bevinden.
- Op de hoogte testers van nieuwe releases zonder testers gaan door de download stroom weer.
- Distributiegroepen beheren voor verschillende versies van uw toepassing.
- Distribueren naar winkels: 
    - [Apple](/appcenter/distribution/stores/apple)
    - [Google Play](/appcenter/distribution/stores/googleplay)
    - [Intune](/appcenter/distribution/stores/intune)
- Krijg platformondersteuning voor iOS, Android, macOS, tvOS, Xamarin, React Native, Unity en Cordova.
- Registreer automatisch iOS-apparaten in uw inrichtingsprofiel.

**Verwijzingen**
- [Meld u aan bij Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met App Center Distribueren](/appcenter/build/)

### <a name="azure-pipelines"></a>Azure-pijplijnen

[Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) is een volledig uitgeruste CI-service (continuous integration) en een continue levering (CD) die werkt met uw favoriete Git-provider. Azure Pipelines kunnen worden geïmplementeerd voor de meeste grote cloudservices, zoals Azure-services. U beginnen met uw code op GitHub, GitHub Enterprise Server, GitLab, Bitbucket Cloud of Azure Repos. Vervolgens u de build, het testen en de implementatie van uw code automatiseren naar Microsoft Azure, Google Cloud Platform of Amazon Web Services (AWS).

**Belangrijke functies**
- **Vereenvoudigde taakgebaseerde ervaring voor het instellen van een CI-server:** Stel een CI-server in voor zowel native (Android, iOS en Windows) als cross-platform (Xamarin, Cordova en React Native) mobiele applicaties.
- **Elke taal, platform en cloud:** Node.js, Python, Java, PHP, Ruby, Go, C/C++, C#, Android en iOS-apps bouwen, testen en implementeren. Parallel draaien op Linux, macOS en Windows. Implementeren voor cloudproviders zoals Azure, AWS en Google Cloud Platform. Distribueer mobiele applicaties via bètakanalen en app stores.
- **Ondersteuning voor native containers:** Maak nieuwe containers met gemak, en duw ze naar elk register. Implementeer containers naar onafhankelijke hosts of Kubernetes.
- **Geavanceerde workflows en functies:** Maak eenvoudig bouwketens en meerfasenbuilds. Krijg ondersteuning voor YAML, test integratie, release gates, rapportage en meer.
- **Uitbreidbaar:** Gebruik een reeks build-, test- en implementatietaken die door de community zijn gebouwd, waaronder honderden extensies van Slack naar SonarCloud. U zelfs implementeren vanuit andere CI-systemen, zoals Jenkins. Webhooks en REST API's kunnen u helpen integreren.
- **Gratis cloud-hosted builds:** Deze builds zijn beschikbaar voor openbare en private repositories.
- **Ondersteuning voor implementatie naar andere cloudleveranciers:** Leveranciers zijn AWS en Google Cloud Platform.

**Verwijzingen**
- [Aan de slag met Azure Pipelines-handleiding](/azure/devops/pipelines/get-started/pipelines-get-started?view=azure-devops)
- [Aan de slag met Azure DevOps](https://app.vsaex.visualstudio.com/signup/)
  
## <a name="distribute-your-application-directly-to-app-stores"></a>Uw toepassing rechtstreeks distribueren naar App Stores
Nadat uw toepassing klaar is voor productiegebruik en u wilt dat deze openbaar wordt gebruikt, moet deze worden ingediend bij app stores waar deze door klanten kan worden gedownload. Er zijn meerdere manieren om uw toepassing rechtstreeks naar app stores te distribueren. 

### <a name="visual-studio-app-center"></a>Visual Studio App Center
Met [App Center Distribute](/appcenter/distribution/stores/)u uw mobiele applicaties rechtstreeks publiceren naar app stores. Nadat uw toepassing klaar is om door gebruikers te worden gedownload, u uw toepassingsbinaries rechtstreeks publiceren via de Visual Studio App Center-portal. 

U rechtstreeks distribueren naar:
- [Apple App Store](/appcenter/distribution/stores/apple)
- [Google Play Store](/appcenter/distribution/stores/googleplay)
- [Microsoft Intune](/appcenter/distribution/stores/intune)
    
### <a name="apple-app-store"></a>Apple App Store
In de door Apple ontwikkelde en onderhouden app store kunnen gebruikers door toepassingen bladeren en downloaden die zijn ontwikkeld voor iOS-, MacOS-, WatchOS- en tvOS-apparaten. Ontwikkelaars moeten hun iOS-apps indienen bij de Apple App Store voor openbaar gebruik.

### <a name="google-play"></a>Google Play

Google Play is de officiële app store voor Android OS, waar gebruikers kunnen bladeren en applicaties kunnen downloaden die zijn ontwikkeld voor Android-apparaten die via Google worden gepubliceerd.

### <a name="intune"></a>Intune

[Microsoft Intune](/intune/app-management) is een cloudservice in de ruimte voor mobiliteitsbeheer voor ondernemingen die uw personeel in staat stelt productief te zijn en tegelijkertijd uw bedrijfsgegevens te beschermen. Met Intune kunt u het volgende doen:
- De mobiele apparaten en pc's beheren die door uw werknemers worden gebruikt voor toegang tot bedrijfsgegevens.
- Beheer de mobiele applicaties die uw personeel gebruikt.
- Bescherm uw bedrijfsgegevens door te controleren hoe uw personeel deze toegang heeft en deelt.
- Zorg ervoor dat apparaten en toepassingen voldoen aan de beveiligingsvereisten van het bedrijf.
    
## <a name="deploy-updates-directly-to-users-devices"></a>Updates rechtstreeks implementeren op apparaten van gebruikers

### <a name="codepush"></a>CodePush (CodePush)
Met [CodePush](/appcenter/distribution/codepush/) in App Center kunnen Apache Cordova- en React Native-ontwikkelaars updates van mobiele applicaties rechtstreeks implementeren op de apparaten van hun gebruikers. Het fungeert als een centrale opslagplaats waarontwikkelaars bepaalde updates voor kunnen publiceren, zoals JavaScript-, HTML-, CSS- en afbeeldingswijzigingen. Vervolgens kunnen toepassingen vragen voor updates uit de repository met behulp van de meegeleverde client SDKs. Op deze manier u een meer deterministisch en direct engagement model met uw gebruikers, terwijl u bugs adres of kleine functies toe te voegen. U hoeft een binaire binaire niet opnieuw te bouwen of opnieuw te distribueren via openbare app-winkels.

**Belangrijke functies**
- Cordova- en React Native-ontwikkelaars kunnen updates van mobiele applicaties rechtstreeks implementeren op de apparaten van hun gebruikers zonder dat ze in een winkel worden uitgebracht.
- Handig voor het oplossen van bugs of het toevoegen en verwijderen van kleine functies die u niet nodig hebben om binaire te herbouwen en te herverdelen via de respectieve winkels.

**Verwijzingen**
- [Meld u aan bij Visual Studio App Center](https://appcenter.ms/signup?utm_source=Mobile%20Development%20Docs&utm_medium=Azure&utm_campaign=New%20azure%20docs)
- [Aan de slag met CodePush in App Center](/appcenter/distribution/codepush/)
- [CodePush CLI](/appcenter/distribution/codepush/cli)
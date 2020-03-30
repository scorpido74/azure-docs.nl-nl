---
title: Een back-end voor een serverloze mobiele toepassing bouwen met Azure-functies en andere services
description: Meer informatie over de compute services die worden gebruikt om een solide, serverloze mobiele applicatie back-end te bouwen.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: d6a2bbc984a8808d2cd8a9b292e55d80f43f2e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240152"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Mobiele back-endcomponenten bouwen met compute services
Elke mobiele toepassing heeft een back-end nodig die verantwoordelijk is voor gegevensopslag, bedrijfslogica en beveiliging. Als u de infrastructuur beheert om back-endcode te hosten en uit te voeren, moet u meerdere servers opschalen, inrichten en schalen. Je moet ook OS-updates en de betrokken hardware beheren en beveiligingspatches toepassen. Vervolgens moet u al deze infrastructuurcomponenten controleren op prestaties, beschikbaarheid en fouttolerantie. 

Serverless architectuur is handig voor dit type scenario omdat u geen servers te beheren en geen OS of gerelateerde software of hardware-updates te beheren. Serverless architectuur bespaart ontwikkelaar tijd en kosten, wat betekent dat de tijd om de markt en gerichte energie op het bouwen van toepassingen.

## <a name="benefits-of-compute"></a>Voordelen van rekenkracht
- Abstractie van servers betekent dat u zich geen zorgen hoeft te maken over hosting, patching en beveiliging, waardoor u zich uitsluitend op de code concentreren.
- Directe en efficiënte schaling zorgt ervoor dat resources automatisch of op aanvraag worden ingericht op elke schaal die u nodig hebt.
- Hoge beschikbaarheid en fouttolerantie.
- Microfacturering zorgt ervoor dat u alleen wordt gefactureerd voor wanneer uw code daadwerkelijk wordt uitgevoerd.
- Code wordt uitgevoerd in de cloud geschreven in de taal van uw keuze.

Gebruik de volgende services om serverloze rekenmogelijkheden in te schakelen in uw mobiele apps.

## <a name="azure-functions"></a>Azure Functions
[Azure Functions](https://azure.microsoft.com/services/functions/) is een gebeurtenisgestuurde rekenervaring die u gebruiken om uw code uit te voeren, geschreven in de programmeertaal van uw keuze, zonder u zorgen te hoeven maken over servers. U hoeft de toepassing of de infrastructuur niet te beheren om deze op uit te voeren. Functies schalen op aanvraag en u betaalt alleen voor de tijd dat uw code wordt uitgevoerd. Azure-functies zijn een geweldige manier om een API voor een mobiele toepassing te implementeren. Ze zijn eenvoudig te implementeren en te onderhouden en zijn toegankelijk via HTTP.

**Belangrijke functies**
- Gebeurtenisgestuurd en schaalbaar waar u triggers en bindingen gebruiken om te bepalen wanneer een functie wordt aangeroepen en met welke gegevens deze wordt verbonden.
- Neem je eigen afhankelijkheden mee omdat Functions NuGet en NPM ondersteunt, zodat je je favoriete bibliotheken gebruiken.
- Geïntegreerde beveiliging, zodat u http-geactiveerde functies beschermen met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en Microsoft-account.
- Vereenvoudigde integratie met verschillende [Azure-services](/azure/azure-functions/functions-overview) en -services (SaaS)-aanbiedingen.
- Flexibele ontwikkeling, zodat u uw functies direct in de Azure-portal coderen of continue integratie instellen en uw code implementeren via GitHub, Azure DevOps Services en andere ondersteunde ontwikkelingstools.
- De runtime van functies is open source en beschikbaar op [GitHub.](https://github.com/azure/azure-webjobs-sdk-script)
- Verbeterde ontwikkelingservaring waar u lokaal coderen, testen en debuggen met behulp van hun voorkeurseditor of gebruiksvriendelijke webinterface met monitoring met geïntegreerde tools en ingebouwde DevOps-mogelijkheden.
- Verschillende programmeertalen en hostingopties voor ontwikkeling, zoals C#, Node.js, Java, JavaScript of Python.
- Prijsmodel voor betalen per gebruik betekent dat u alleen betaalt voor de tijd die u besteedt aan het uitvoeren van uw code.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Functions](/azure/azure-functions/)
- [Ontwikkelaarshandleiding voor Azure Functions](/azure/azure-functions/functions-reference)
- [Snel gestart](/azure/azure-functions/functions-create-first-function-vs-code)
- [Monsters](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Met [Azure App Service](https://azure.microsoft.com/services/app-service/)u web-apps en RESTful API's bouwen en hosten in de programmeertaal van uw keuze zonder de infrastructuur te beheren. Het biedt automatisch schalen en hoge beschikbaarheid, ondersteunt zowel Windows als Linux en maakt geautomatiseerde implementaties van GitHub, Azure DevOps of een Git repo mogelijk.

**Belangrijke functies**
- Ondersteuning voor meerdere talen en framework voor ASP.NET, ASP.NET Core, Java, Ruby, Node.js, PHP of Python. U kunt ook PowerShell en andere scripts of uitvoerbare bestanden als achtergrondservices uitvoeren.
- DevOps-optimalisatie door middel van continue integratie en implementatie met Azure DevOps, GitHub, BitBucket, Docker Hub of Azure Container Registry. Beheer uw apps in App Service met Azure PowerShell of de platformoverschrijdende opdrachtregelinterface (CLI).
- Globale schaal met hoge beschikbaarheid om handmatig of automatisch op te schalen of uit te schalen.
- Verbindingen met SaaS-platforms en on-premises gegevens om te kiezen uit meer dan 50 connectoren voor bedrijfssystemen zoals SAP, SaaS-services zoals Salesforce en internetservices zoals Facebook. Toegang tot on-premises gegevens met behulp van hybride verbindingen en Azure Virtual Networks.
- Azure App Service is ISO- en SOC- en PCI-compatibel. Verifieer gebruikers met Azure Active Directory of met aanmelding voor sociale media zoals Google, Facebook, Twitter en Microsoft. Maak IP-adresbeperkingen en  beheer service-identiteiten.
- Toepassingssjablonen om te kiezen uit een uitgebreide lijst met toepassingssjablonen in Azure Marketplace, zoals WordPress, Joomla en Drupal.
- Visual Studio-integratie met speciale tools in Visual Studio stroomlijnt het werk van het maken, implementeren en debuggen.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Azure App Service-documentatie](/azure/app-service/)
- [Snel gestart](/azure/app-service/app-service-web-get-started-dotnet)
- [Monsters](/azure/app-service/samples-cli)
- [Zelfstudies](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) beheert uw gehoste Kubernetes-omgeving. Met AKS kunt u snel en eenvoudig in containers geplaatste toepassingen implementeren en beheren, zonder dat u kennis hoeft te hebben van het indelen van containers. Het elimineert ook de last van lopende operaties en onderhoud. AKS-bepalingen, upgrades en schaalbronnen op aanvraag, zonder uw toepassingen offline te halen.

**Belangrijke functies**
- Migreer eenvoudig bestaande toepassingen naar containers en voer deze uit binnen AKS.
- Vereenvoudig de implementatie en het beheer van op microservices gebaseerde toepassingen.
- Secure DevOps for AKS to achieve balance between speed and security and deliver code faster at scale.
- Schaal eenvoudig met AKS en Azure Container Instances om pods in containerinstanties in te richten die binnen enkele seconden worden gestart.
- IoT-apparaten on demand implementeren en beheren.
- Train machine learning modellen met behulp van tools zoals TensorFlow en KubeFlow.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Azure Kubernetes-servicedocumentatie](/azure/aks/)
- [Snel gestart](/azure/aks/kubernetes-walkthrough-portal)
- [Zelfstudies](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure Container Instances](https://azure.microsoft.com/services/container-instances/) is een geweldige oplossing voor elk scenario dat kan werken in geïsoleerde containers, zoals eenvoudige toepassingen, taakautomatisering en buildtaken. Ontwikkel apps snel zonder VM's te beheren.

**Belangrijke functies**
- Snelle opstarttijden zoals Container Instances kunnen containers in Azure binnen enkele seconden starten, zonder dat vm's hoeven te worden in- en geïnstalleerd.
- Openbare IP-connectiviteit en aangepaste DNS-naam.
- Beveiliging op hypervisorniveau die garandeert dat uw toepassing net zo geïsoleerd is in een container als in een VM.
- Aangepaste maten voor optimaal gebruik door exacte specificaties van CPU-cores en geheugen toe te staan. U betaalt op basis van wat u nodig hebt en wordt per seconde gefactureerd, zodat u uw uitgaven kunt verfijnen op basis van werkelijke behoefte.
- Permanente opslag om de status op te halen en voort te houden. Container Instances biedt directe montage van Azure Files-shares.
- Linux- en Windows-containers gepland met dezelfde API.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Azure Container Instances-documentatie](/azure/container-instances/)
- [Snel gestart](/azure/container-instances/container-instances-quickstart-portal)
- [Monsters](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Zelfstudies](/azure/container-instances/container-instances-tutorial-prepare-app)
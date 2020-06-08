---
title: Een back-end van een serverloze mobiele toepassing bouwen met Azure Functions en andere services
description: Meer informatie over de compute-services die worden gebruikt voor het bouwen van een solide back-end zonder serverloze mobiele toepassing.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: f27ef3868cb53c76ecd3ba5d3b24cd075f8a5531
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/07/2020
ms.locfileid: "84485301"
---
# <a name="build-mobile-back-end-components-with-compute-services"></a>Mobiele back-end-onderdelen bouwen met Compute-Services
Voor elke mobiele toepassing is een back-end nodig die verantwoordelijk is voor gegevens opslag, bedrijfs logica en beveiliging. Voor het beheren van de infra structuur voor het hosten en uitvoeren van back-end-code moet u meerdere servers verg Roten of verkleinen, inrichten en schalen. U moet ook updates van het besturings systeem en de betrokken hardware beheren en beveiligings patches Toep assen. Vervolgens moet u al deze onderdelen van de infra structuur bewaken voor prestaties, Beschik baarheid en fout tolerantie. 

Serverloze architectuur is handig voor dit type scenario omdat u geen servers hebt om te beheren en geen besturings systeem of gerelateerde software of hardware-updates te beheren. Serverloze architectuur bespaart tijd en kosten voor ontwikkel aars, wat inhoudt dat de marketing en gerichte energie voor het bouwen van toepassingen sneller verloopt.

## <a name="benefits-of-compute"></a>Voor delen van compute
- Abstractie van servers houdt in dat u zich geen zorgen hoeft te maken over het hosten, patchen en beveiligen, zodat u zich alleen kunt richten op de code.
- Onmiddellijke en efficiënte schaal baarheid zorgt ervoor dat resources automatisch of op aanvraag worden ingericht op elke schaal die u nodig hebt.
- Hoge Beschik baarheid en fout tolerantie.
- Micro facturering zorgt ervoor dat u alleen factureert wanneer uw code daad werkelijk wordt uitgevoerd.
- Code wordt uitgevoerd in de cloud die is geschreven in de taal van uw keuze.

Gebruik de volgende services om serverloze reken mogelijkheden in uw mobiele apps in te scha kelen.

## <a name="azure-functions"></a>Azure Functions
[Azure functions](https://azure.microsoft.com/services/functions/) is een gebeurtenis gerichte Compute-ervaring die u kunt gebruiken om uw code uit te voeren, geschreven in de programmeer taal van uw keuze, zonder dat u zich zorgen hoeft te maken over servers. U hoeft de toepassing of de infra structuur niet te beheren om deze uit te voeren. Functies worden op aanvraag geschaald en u betaalt alleen voor de tijd dat uw code wordt uitgevoerd. Azure functions is een uitstekende manier om een API voor een mobiele toepassing te implementeren. Ze zijn eenvoudig te implementeren en te onderhouden en zijn toegankelijk via HTTP.

**Belangrijke functies**
- Het gebruik van gebeurtenissen en schaal baarheid waarbij u triggers en bindingen kunt gebruiken om te definiëren wanneer een functie wordt aangeroepen en welke gegevens er worden verbonden.
- Breng uw eigen afhankelijkheden, want functies bieden ondersteuning voor NuGet en NPM, zodat u uw favoriete bibliotheken kunt gebruiken.
- Geïntegreerde beveiliging, zodat u met HTTP geactiveerde functies kunt beveiligen met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en micro soft-account.
- Vereenvoudigde integratie met verschillende [Azure-Services](/azure/azure-functions/functions-overview) en software als een service (SaaS)-aanbiedingen.
- Flexibele ontwikkeling, zodat u uw functies rechtstreeks kunt coderen in de Azure Portal, een continue integratie instellen en uw code implementeert via GitHub, Azure DevOps Services en andere ondersteunde ontwikkel Programma's.
- Functions runtime is open source en beschikbaar op [github](https://github.com/azure/azure-webjobs-sdk-script).
- Verbeterde ontwikkel ervaring waarbij u lokaal kunt coderen, testen en fouten opsporen met behulp van de voorkeurs editor of de gebruiks vriendelijke webinterface met bewaking met geïntegreerde hulpprogram ma's en ingebouwde DevOps-functies.
- Diverse programmeer talen en hosting opties voor het ontwikkelen, zoals C#, node. js, Java, java script of python.
- Prijs model voor betalen per gebruik betekent dat u alleen betaalt voor de tijd die nodig is om uw code uit te voeren.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com)
- [Documentatie voor Azure Functions](/azure/azure-functions/)
- [Ontwikkelaarshandleiding voor Azure Functions](/azure/azure-functions/functions-reference)
- [Snelstartgidsen](/azure/azure-functions/functions-create-first-function-vs-code)
- [Voorbeelden](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="azure-app-service"></a>Azure App Service
Met [Azure app service](https://azure.microsoft.com/services/app-service/)kunt u web-apps en rest-api's maken en hosten in de programmeer taal van uw keuze zonder dat u infra structuur hoeft te beheren. Het biedt automatisch schalen en hoge Beschik baarheid, ondersteunt zowel Windows als Linux, en maakt automatische implementaties mogelijk vanuit GitHub, Azure DevOps of een Git opslag plaats.

**Belangrijke functies**
- Ondersteuning voor meerdere talen en Framework voor ASP.NET, ASP.NET Core, Java, Ruby, node. js, PHP of python. U kunt ook PowerShell en andere scripts of uitvoerbare bestanden als achtergrondservices uitvoeren.
- DevOps optimalisatie via continue integratie en implementatie met Azure DevOps, GitHub, BitBucket, docker hub of Azure Container Registry. Beheer uw apps in App Service met Azure PowerShell of de platformoverschrijdende opdrachtregelinterface (CLI).
- Wereld wijde schaal met hoge Beschik baarheid om hand matig of automatisch omhoog of omlaag te schalen.
- Verbindingen met SaaS-platforms en on-premises gegevens om te kiezen uit meer dan 50 connectors voor bedrijfs systemen zoals SAP, SaaS-services zoals Sales Force en Internet services zoals Facebook. Toegang tot on-premises gegevens met hybride verbindingen en Azure Virtual Networks.
- Azure App Service zijn compatibel met ISO, SOC en PCI. Verifieer gebruikers met Azure Active Directory of met aanmelden voor sociale media, zoals Google, Facebook, Twitter en micro soft. Maak IP-adresbeperkingen en  beheer service-identiteiten.
- Toepassings sjablonen om te kiezen uit een uitgebreide lijst met toepassings sjablonen in azure Marketplace, zoals WordPress, Joomla en Drupal.
- Visual Studio-integratie met speciale hulpprogram ma's in Visual Studio stroomlijnt het werk van het maken, implementeren en opsporen van fouten.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie over Azure App Service](/azure/app-service/)
- [Snelstartgidsen](/azure/app-service/app-service-web-get-started-dotnet)
- [Voorbeelden](/azure/app-service/samples-cli)
- [Zelfstudies](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service"></a>Azure Kubernetes Service
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) beheert uw gehoste Kubernetes-omgeving. Met AKS kunt u snel en eenvoudig in containers geplaatste toepassingen implementeren en beheren, zonder dat u kennis hoeft te hebben van het indelen van containers. Het elimineert ook de belasting van lopende bewerkingen en onderhoud. Met AKS kunt u op aanvraag resources inrichten, bijwerken en schalen, zonder uw toepassingen offline te halen.

**Belangrijke functies**
- Migreer eenvoudig bestaande toepassingen naar containers en voer deze uit in AKS.
- Vereenvoudig de implementatie en het beheer van toepassingen op basis van micro Services.
- Beveilig DevOps voor AKS om een evenwicht te krijgen tussen snelheid en beveiliging en code sneller op schaal te leveren.
- Schaal eenvoudig met behulp van AKS en Azure Container Instances voor het inrichten van een Peul binnen Container Instances dat in een paar seconden begint.
- IoT-apparaten op aanvraag implementeren en beheren.
- Train machine learning-modellen met het gebruik van hulpprogram ma's zoals tensor flow en KubeFlow.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie voor Azure Kubernetes service](/azure/aks/)
- [Snelstartgidsen](/azure/aks/kubernetes-walkthrough-portal)
- [Zelfstudies](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure container instances](https://azure.microsoft.com/services/container-instances/) is een fantastische oplossing voor elk scenario dat kan worden gebruikt in geïsoleerde containers, zoals eenvoudige toepassingen, taak automatisering en het bouwen van taken. Ontwikkel snel apps zonder virtuele machines te beheren.

**Belangrijke functies**
- Snelle opstart tijden als Container Instances in een paar seconden containers in azure kunt starten, zonder de nood zaak om Vm's in te richten en te beheren.
- Open bare IP-connectiviteit en aangepaste DNS-naam.
- Beveiliging op Hyper Visor niveau waarmee wordt gegarandeerd dat uw toepassing is geïsoleerd in een container, omdat deze zich in een VM bevindt.
- Aangepaste grootten voor optimaal gebruik door exacte specificaties van CPU-kernen en geheugen toe te staan. U betaalt op basis van wat u nodig hebt en wordt per seconde gefactureerd, zodat u uw uitgaven kunt verfijnen op basis van werkelijke behoefte.
- Permanente opslag om op te halen en de status te behouden. Container Instances biedt direct koppelen van Azure Files-shares.
- Linux-en Windows-containers die zijn gepland met dezelfde API.

**Verwijzingen**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie voor Azure Container Instances](/azure/container-instances/)
- [Snelstartgidsen](/azure/container-instances/container-instances-quickstart-portal)
- [Voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Zelfstudies](/azure/container-instances/container-instances-tutorial-prepare-app)
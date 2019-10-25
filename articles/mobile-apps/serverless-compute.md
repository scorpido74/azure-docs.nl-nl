---
title: Back-end van mobiele app zonder server bouwen met Azure Functions en andere services
description: Meer informatie over de compute-Services voor het bouwen van een solide, serverloze mobiele toepassings back-end.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 444f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: b8378be675b86ada10b8442c40a54b635ee115a9
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795859"
---
# <a name="build-mobile-backend-components-with-compute-services"></a>Mobiele back-end-onderdelen bouwen met Compute-Services
Voor elke mobiele toepassing is een back-end nodig die verantwoordelijk is voor gegevens opslag, bedrijfs logica en beveiliging. Voor het beheren van de infra structuur voor het hosten en uitvoeren van de back-end-code moet u de grootte, het inrichten en het schalen van een aantal servers, het beheren van updates van het besturings systeem en de betrokken hardware, beveiligings patches Toep assen en vervolgens al deze infrastructuur onderdelen voor prestaties controleren. Beschik baarheid en fout tolerantie. Dit komt doordat de serverloze architectuur handig is, omdat ontwikkel aars geen servers hoeven te beheren, geen besturings systeem of gerelateerde software/hardware-updates te beheren. Het bespaart veel tijd en kosten voor ontwikkel aars, wat inhoudt dat de IT-naar-Market en gerichte energie voor het bouwen van toepassingen sneller verloopt.

## <a name="benefits-of-compute"></a>Voor delen van compute
- Abstractie van servers: u hoeft zich geen zorgen te maken over hosting, patching en beveiliging, waardoor ontwikkel aars zich alleen kunnen concentreren op de code.
- Onmiddellijke en efficiënte schaal baarheid zorgt ervoor dat resources automatisch of op aanvraag worden ingericht op elke schaal die u nodig hebt.
- Hoge Beschik baarheid en fout tolerantie.
- Micro facturering zorgt ervoor dat u alleen factureert wanneer uw code daad werkelijk wordt uitgevoerd.
- Schrijf code die in de Cloud wordt uitgevoerd in de taal van uw keuze.

Gebruik de volgende services om serverloze reken mogelijkheden in uw mobiele apps in te scha kelen.

## <a name="azure-functions"></a>Azure Functions
[Azure functions](https://azure.microsoft.com/services/functions/) is een gebeurtenis gerichte Compute-ervaring waarmee u uw code kunt uitvoeren, geschreven in de programmeer taal van uw keuze, zonder dat u zich zorgen hoeft te maken over servers. Ontwikkel aars hoeven de toepassing of de infra structuur niet te beheren om deze uit te voeren. Functies worden op aanvraag geschaald en u betaalt alleen voor de tijd dat uw code wordt uitgevoerd. Azure Functions zijn een uitstekende manier om een API voor een mobiele toepassing te implementeren, omdat ze heel eenvoudig zijn te implementeren en onderhouden en toegankelijk zijn via HTTP.

**Belangrijkste functies**
- **Het gebruik van gebeurtenissen en schaal baarheid** waarbij ontwikkel aars **Triggers en bindingen** kunnen gebruiken om te definiëren wanneer een functie wordt aangeroepen en welke gegevens deze verbinding maakt.
- **Breng uw eigen afhankelijkheden mee**: Functions ondersteunt NuGet en NPM, zodat u uw favoriete bibliotheken kunt gebruiken.
- Met **geïntegreerde beveiliging** kunt u via http geactiveerde functies beveiligen met OAuth-providers zoals Azure Active Directory, Facebook, Google, Twitter en micro soft-account.
- **Vereenvoudigde integratie** met verschillende [Azure-Services](/azure/azure-functions/functions-overview#integrations) en software-as-a-Service (SaaS)-aanbiedingen.
- Met **flexibele ontwikkeling** kunt u uw functies direct in de portal coderen of doorlopende integratie instellen en uw code implementeren via github, Azure DevOps Services en andere ondersteunde ontwikkel Programma's.
- De runtime van functions is **open-source** en beschikbaar op [github](https://github.com/azure/azure-webjobs-sdk-script).
-  **Verbeterde ontwikkel ervaring** waarbij ontwikkel aars lokaal code kunnen gebruiken, testen en fouten opsporen met behulp van de voorkeurs editor of de gebruiks vriendelijke webinterface met bewaking met geïntegreerde hulpprogram ma's en ingebouwde DevOps mogelijkheden.
- **Diverse programmeer talen en hosting opties** : Ontwikkelen met C#, node. js, Java, java script of python.
- **Betalen per gebruik**: betaal alleen voor de tijd die nodig is voor het uitvoeren van uw code.

**Referentie**
- [Azure-portal](https://portal.azure.com)
- [Documentatie](/azure/azure-functions/)
- [Ontwikkelaars handleiding Azure Functions](/azure/azure-functions/functions-reference)
- [Snelstartgidsen](/azure/azure-functions/functions-create-first-function-vs-code)
- [Voorbeelden](/samples/browse/?products=azure-functions&languages=csharp)

## <a name="app-service"></a>App Service
[Azure app service](https://azure.microsoft.com/services/app-service/) Met Azure App Service kunt u web-apps bouwen en hosten, en de resterende Api's in de programmeer taal van uw keuze zonder dat u infra structuur hoeft te beheren. Het biedt automatisch schalen en een hoge beschikbaarheid, ondersteuning voor zowel Windows als Linux en maakt automatische implementaties mogelijk vanuit GitHub, Azure DevOps of een willekeurige Git-repo.

**Belangrijkste functies**
- Ondersteuning voor **meerdere talen en frameworks** voor ASP.NET, ASP.net core, Java, Ruby, node. js, PHP of python. U kunt ook Power shell en andere scripts of uitvoer bare bestanden uitvoeren als achtergrond Services.
- **DevOps-optimalisatie** : Stel continue integratie en implementatie in met Azure DevOps, github, BitBucket, docker Hub of Azure container Registry. Beheer uw apps in App Service met behulp van Azure PowerShell of de platformoverschrijdende opdracht regel interface (CLI).
- **Wereld wijde schaal met hoge Beschik baarheid** om hand matig of automatisch omhoog of omlaag te schalen.
- **Verbindingen met SaaS-platforms en on-premises gegevens** om te kiezen uit meer dan 50 connectors voor bedrijfs systemen (zoals SAP), Saas-Services (zoals Sales Force) en Internet Services (zoals Facebook). Toegang tot on-premises gegevens met behulp van Hybride verbindingen en virtuele netwerken van Azure.
- **Beveiliging en naleving** -Azure app service zijn ISO, SOC en PCI compatibel. Verifieer gebruikers met Azure Active Directory of met sociale aanmelding (Google, Facebook, Twitter en micro soft). IP-adres beperkingen maken en service-identiteiten beheren.
- **Toepassings sjablonen** om te kiezen uit een uitgebreide lijst met toepassings sjablonen in de Azure Marketplace, zoals WordPress, Joomla en Drupal.
- **Visual Studio-integratie** met speciale hulpprogram Ma's in Visual Studio stroomlijnt het werk van het maken, implementeren en opsporen van fouten.

**Referentie**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie](/azure/app-service/)
- [Snelstartgidsen](/azure/app-service/app-service-web-get-started-dotnet)
- [Voorbeelden](/azure/app-service/samples-cli)
- [Zelfstudies](/azure/app-service/app-service-web-tutorial-dotnetcore-sqldb)

## <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)
[Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) beheert uw gehoste Kubernetes-omgeving en zorgt dat de implementatie en het beheer van toepassingen in containers snel en eenvoudig zijn en geen kennis over het beheer van containers vereisen. Het verlicht ook de last van actieve bewerkingen en onderhoud door inrichten, upgraden en bronnen op aanvraag schalen mogelijk te maken, zonder uw toepassingen offline te brengen. 

**Belangrijkste functies**
- **Migreer eenvoudig bestaande toepassingen** naar containers en voer deze uit in AKS.
- **Vereenvoudig de implementatie en het beheer** van toepassingen op basis van micro Services.
- **Beveilig DevOps voor AKS** om een evenwicht te krijgen tussen snelheid en beveiliging en code sneller op schaal te leveren.
- **Schaal eenvoudig met behulp van AKS en ACI** voor het inrichten van een Peul in ACI dat binnen enkele seconden wordt gestart.
- **IOT-apparaten implementeren en beheren** op aanvraag.
- **Machine learning-model training** met behulp van hulpprogram Ma's zoals tensor flow en KubeFlow.

**Referentie**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie](/azure/aks/)
- [Snelstartgidsen](/azure/aks/kubernetes-walkthrough-portal)
- [Zelfstudies](/azure/aks/tutorial-kubernetes-prepare-app)

## <a name="azure-container-instances"></a>Azure Container Instances
[Azure container instances (ACI)](https://azure.microsoft.com/services/container-instances/) is een geweldige oplossing voor elk scenario dat kan worden gebruikt in geïsoleerde containers, waaronder eenvoudige toepassingen, taak automatisering en het bouwen van taken. Ontwikkel snel apps zonder virtuele machines te beheren.

**Belangrijkste functies**
- **Snelle opstart tijden** als ACI kunnen binnen enkele seconden containers in azure starten, zonder dat u vm's hoeft in te richten en te beheren.
- **Open bare IP-connectiviteit en aangepaste DNS-naam**.
- **Beveiliging op Hyper Visor niveau** waarmee wordt gegarandeerd dat uw toepassing is geïsoleerd in een container, omdat deze zich in een VM bevindt.
- **Aangepaste grootten** voor optimaal gebruik door exacte specificaties van CPU-kernen en geheugen toe te staan. U betaalt op basis van wat u nodig hebt en wordt per seconde gefactureerd, zodat u uw uitgaven kunt verfijnen op basis van werkelijke behoefte.
- De **permanente opslag** om op te halen en de status te behouden, biedt ACI direct koppelen van Azure files-shares.
- **Linux-en Windows-containers** die zijn gepland met dezelfde API.

**Referentie**
- [Azure-portal](https://portal.azure.com/)
- [Documentatie](/azure/container-instances/)
- [Snelstartgidsen](/azure/container-instances/container-instances-quickstart-portal)
- [Voorbeelden](https://azure.microsoft.com/resources/samples/?sort=0&term=aci)
- [Zelfstudies](/azure/container-instances/container-instances-tutorial-prepare-app)
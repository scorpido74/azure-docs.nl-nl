---
title: Integratie van Azure DevTest Labs en DevOps | Microsoft Documenten
description: Meer informatie over het gebruik van labs van Azure DevTest Labs binnen een cd-pijplijn (continuous integration)/ continuous delivery (CD) in een bedrijfsomgeving.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: spelluru
ms.openlocfilehash: 62c44bfea28d47d7c32aa7ef440a40d45c314683
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67078922"
---
# <a name="integration-of-azure-devtest-labs-and-azure-devops"></a>Integratie van Azure DevTest Labs en Azure DevOps
DevOps is een software development methodologie die software development (Dev) integreert met operations (Ops) voor een systeem. Dit systeem kan nieuwe functies, updates en oplossingen leveren die aansluiten bij de bedrijfsdoelstellingen. Deze methodologie omvat alles, van het ontwerpen van nieuwe functies op basis van doelen, gebruikspatronen en feedback van klanten; het systeem op te lossen, te herstellen en te verharden wanneer zich problemen voordoen. Een gemakkelijk te herkennen onderdeel van deze methodologie is de continuous integration (CI)/ continuous delivery (CD) pipeline. Een CI/CD-pijplijn bevat informatie, code en resources van een commit via een reeks stappen die het bouwen, testen en implementeren omvatten om het systeem te produceren. Dit artikel richt zich op verschillende manieren om laboratoria effectief te gebruiken in een pijplijn in een bedrijfsomgeving. 

## <a name="benefits-of-using-labs-in-devops-workflow"></a>Voordelen van het gebruik van labs in de workflow van DevOps 

### <a name="focused-access"></a>Gerichte toegang 
Met behulp van een lab als onderdeel kan een specifiek ecosysteem te associëren met een beperkte groep mensen. Meestal heeft een team of groep die in een gemeenschappelijke ruimte of een specifieke functie werkt, een lab toegewezen.   

### <a name="infrastructure-replication-in-the-cloud"></a>Infrastructuurreplicatie in de cloud 
Een ontwikkelaar kan snel een ontwikkel-ecosysteem opzetten met een dev-box met broncode en tools. De ontwikkelaar kan ook een omgeving maken die bijna identiek is aan de productieconfiguratie. Het helpt bij de snellere innerlijke lus ontwikkeling. 

### <a name="pre-production"></a>Pre-productie 
Het hebben van een lab in de CI/CD-pijplijn maakt het gemakkelijker om meerdere verschillende pre-productieomgevingen of machines tegelijkertijd te laten werken voor asynchrone tests. Verschillende ondersteuningsinfrastructuren zoals buildagents kunnen worden geïmplementeerd en beheerd binnen een lab. 

## <a name="devops-with-devtest-labs"></a>DevOps met DevTest Labs 

### <a name="development--operation"></a>Ontwikkeling / Werking 
Een lab moet worden gericht op een team dat werkt in een functie gebied. Deze algemene focus maakt het mogelijk om gebiedsspecifieke bronnen te delen, zoals sjablonen voor hulpprogramma's, scripts of Resource Manager. Het maakt snellere veranderingen mogelijk en beperkt de negatieve effecten tot een kleinere groep. Met deze gedeelde bronnen kan de ontwikkelaar vm's voor ontwikkeling maken met alle benodigde code, hulpprogramma's en configuratie. Ze kunnen dynamisch worden gemaakt of hebben een systeem dat basisafbeeldingen maakt met de aanpassingen. De ontwikkelaar kan niet alleen VM's maken, maar ze kunnen ook DevTest Labs-omgevingen maken op basis van de benodigde sjablonen om de juiste Azure-bronnen in het lab te maken. Eventuele veranderingen of destructief werk kan worden gedaan tegen de lab omgeving zonder dat iemand anders. Houd rekening met het scenario waarin het product een zelfstandig systeem is dat op de machine van de klant is geïnstalleerd. In dit scenario heeft DevTest Labs het maken van vm's verbeterd, waaronder het installeren van extra software met behulp van artefacten en vooraf bouwenvan klantconfiguraties voor het sneller testen van de binnenlus van de code. 
  
## <a name="cicd-pipeline"></a>CI/CD-pijplijn 
De CI/CD-pijplijn is een van de kritieke componenten in DevOps die code verplaatsen van een pull-verzoek van de ontwikkelaar, deze integreert met de bestaande code en deze implementeert naar het productie-ecosysteem. Alle middelen hoeven niet in een lab te zijn. Een Jenkins-host kan bijvoorbeeld buiten het lab worden ingesteld als een meer permanente bron. Hier zijn enkele specifieke voorbeelden van het integreren van laboratoria in de pijplijn. 

### <a name="build"></a>Ontwikkelen 
De build pipeline is gericht op het creëren van een pakket van componenten die samen worden getest om te worden overhandigd aan de release pijplijn. Labs kunnen deel uitmaken van de buildpijplijn als locatie voor buildagents en andere ondersteuningsbronnen. Het hebben van de mogelijkheid om dynamisch te bouwen van de infrastructuur zorgt voor meer controle. Met de mogelijkheid om meerdere omgevingen in een lab te hebben, kan elke build asynchroon worden uitgevoerd terwijl u de build-ID gebruikt als onderdeel van de omgevingsinformatie om de bronnen van de specifieke build op unieke wijze te identificeren.   

Voor build agents verhoogt het vermogen van het lab om de toegang te beperken de beveiliging en vermindert het de kans op onbedoelde corruptie.  

### <a name="test"></a>Test 
Met DevTest Labs kan een CI/CD-pijplijn worden geautomatiseerd voor het maken van Azure Resource (VM's, omgevingen) die kunnen worden gebruikt voor geautomatiseerde en handmatige tests. De VM's worden gemaakt met artefacten of formules die informatie uit het buildproces gebruiken om de verschillende aangepaste configuraties te maken die nodig zijn voor het testen.   

### <a name="release"></a>Release 
DevTest Labs wordt vaak gebruikt voor verificatie in de releasesectie voordat de code wordt geïmplementeerd. Het is vergelijkbaar met testen in de sectie Build. Productiebronnen mogen niet worden geïmplementeerd in DevTest Labs. 

### <a name="customization"></a>Aanpassing 
In Azure DevOps zijn er bestaande taken waarmee VM's en omgevingen binnen specifieke laboratoria kunnen worden gemanipuleerd. Hoewel Azure DevOps Services een manier zijn om de CI/CD-pijplijn te beheren, u het lab integreren in elk systeem dat de mogelijkheid ondersteunt om REST-API's aan te roepen, PowerShell-scripts uit te voeren of Azure CLI te gebruiken. 

Terwijl sommige CI/CD-pijplijnbeheerders bestaande open-source plug-ins hebben die resources binnen Azure en DevTest Labs kunnen beheren. Mogelijk moet u een aangepaste scripting gebruiken om aan de specifieke behoeften van de pijplijn te voldoen.  Met dat in gedachten, bij het uitvoeren van een taak, een service principal wordt gebruikt met de juiste rol om toegang te krijgen tot het lab. De service principal heeft meestal de bijdrager rol toegang tot het lab. Zie [Azure DevTests Labs integreren in uw Azure DevOps-pijplijn voor continue integratie en levering](devtest-lab-integrate-ci-cd-vsts.md)voor meer informatie. 
 
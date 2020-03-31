---
title: Informatie over Azure DevTest Labs | Microsoft Documenten
description: Ontdek hoe DevTest Labs het eenvoudig kan maken om virtuele Azure-machines te maken, te beheren en te bewaken
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 1b9eed3b-c69a-4c49-a36e-f388efea6f39
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2019
ms.author: spelluru
ms.openlocfilehash: b7cd6bb1fd0377ca1440d9c667453df922aacbd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561452"
---
# <a name="about-azure-devtest-labs"></a>Over Azure DevTest Labs
Azure DevTest Labs stelt ontwikkelaars in teams in staat om virtuele machines (VM's) en PaaS-resources efficiënt zelf te beheren zonder te wachten op goedkeuringen.

DevTest Labs maakt labs die bestaan uit vooraf geconfigureerde bases of Azure Resource Manager-sjablonen. Deze hebben alle benodigde tools en software die u gebruiken om omgevingen te maken. U omgevingen maken in een paar minuten, in tegenstelling tot uren of dagen.

Met DevTest Labs u de nieuwste versies van uw toepassingen testen door de volgende taken uit te voeren:

- Snel Windows- en Linux-omgevingen inrichten met behulp van herbruikbare sjablonen en artefacten.
- Integreer uw implementatiepijplijn eenvoudig met DevTest Labs om op aanvraag omgevingen in te richten.
- Schaal uw belastingstests op door meerdere testagents in te richten en vooraf ingerichte omgevingen te maken voor training en demo's.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/What-is-Azure-DevTest-Labs/player]

## <a name="capabilities"></a>Functionaliteit
DevTest Labs biedt de volgende mogelijkheden aan ontwikkelaars die met VM's werken:

- Maak vm's snel door minder dan vijf eenvoudige stappen te volgen.
- Kies uit een samengestelde lijst met VM-bases die zijn geconfigureerd, goedgekeurd en geautoriseerd door de teamleider of centrale IT.
- Maak VM's op basis van vooraf gemaakte aangepaste afbeeldingen waarop alle software en tools al zijn geïnstalleerd. 
- Maak VM's op basis van formules die in wezen aangepaste afbeeldingen zijn in combinatie met de nieuwste builds van de software die is geïnstalleerd wanneer de VM's worden gemaakt. 
- Installeer artefacten die zijn geïmplementeerd op VM's nadat ze zijn ingericht.
- Stel automatische afsluitings- en automatische startschema's in op VM's.
- Claim een vooraf gemaakte VM zonder het creatieproces te doorlopen.

DevTest Labs biedt de volgende mogelijkheden aan ontwikkelaars die met PaaS-omgevingen werken:

- Gebruik Resource Manager om snel PaaS-omgevingen te maken door minder dan drie eenvoudige stappen te volgen.
- Kies uit een samengestelde lijst met Resource Manager-sjablonen, die zijn geconfigureerd en geautoriseerd door de teamleider of centrale IT.
- Draai een lege resourcegroep (sandbox) op met behulp van een Resource Manager-sjabloon om Azure te verkennen in de context van een lab.

DevTest Labs stelt centrale IT ook in staat om afval te beheersen, de kosten op resources te optimaliseren en binnen budgetten te blijven door de volgende taken uit te voeren:  

- Automatische afsluitings- en automatische startschema's instellen op VM's.
- Het instellen van beleid op het aantal VM's dat gebruikers kunnen maken.
- Stel beleid in op de formaten en galerijafbeeldingen van VM's waaruit gebruikers kiezen.
- Kosten bijhouden en doelen stellen op laboratoria.
- Op de hoogte worden gesteld van de hoge verwachte kosten voor laboratoria, zodat u de nodige acties ondernemen.

DevTest Labs biedt de volgende voordelen bij het maken, configureren en beheren van omgevingen in de cloud.

## <a name="cost-control-and-governance"></a>Kostenbeheersing en governance
DevTest Labs maakt het eenvoudiger om de kosten te beheersen door u de volgende taken te laten uitvoeren:

- [Stel beleidsregels in op uw labs,](devtest-lab-get-started-with-lab-policies.md)zoals het aantal VM's per gebruiker of per lab. 
- Maak [beleid om vm's automatisch af te sluiten](devtest-lab-set-lab-policy.md) en te starten.
- Houd de kosten bij voor VM's en PaaS-bronnen die in laboratoria zijn opgemaakt om binnen [uw budget](devtest-lab-configure-cost-management.md)te blijven.
- Blijf binnen de context van uw labs, zodat u niet spin-up middelen buiten hen.

## <a name="quickly-get-to-ready-to-test"></a>Snel aan de slag met de test
Met DevTest Labs u vooraf ingerichte omgevingen maken die zijn uitgerust met alles wat uw team nodig heeft om toepassingen te ontwikkelen en te testen. Claim gewoon [de omgevingen](devtest-lab-add-claimable-vm.md) waar de laatste goede build van uw applicatie is geïnstalleerd en begin te werken. Of gebruik containers voor nog snellere, slankere omgevingcreatie.

## <a name="create-once-use-everywhere"></a>Maak één keer, gebruik overal
Vastleggen en delen [environment templates](devtest-lab-create-environment-from-arm.md) van PaaS-omgevingssjablonen en [-artefacten](add-artifact-repository.md) binnen uw team of organisatie , allemaal in bronbeheer, om eenvoudig ontwikkel- en testomgevingen te maken.

## <a name="worry-free-self-service"></a>Zorgeloze selfservice
Met DevTest Labs kunnen uw ontwikkelaars en testers snel en eenvoudig [IaaS-vm's](devtest-lab-add-vm.md) en [PaaS-bronnen](devtest-lab-create-environment-from-arm.md) maken met behulp van een set vooraf geconfigureerde bronnen.

## <a name="use-iaas-and-paas-resources"></a>IaaS- en PaaS-bronnen gebruiken 
Ontwikkelaars kunnen paas-bronnen, zoals Azure Service Fabric-clusters, de Web Apps-functie van Azure App Service en SharePoint-farms, ook opdraaien met behulp van Resource Manager-sjablonen. Om aan de slag te gaan met PaaS in labs, gebruikt u de sjablonen uit de [openbare omgevingrepository](devtest-lab-configure-use-public-environments.md) of [verbindt u het lab met uw eigen Git-repository.](devtest-lab-create-environment-from-arm.md#configure-your-own-template-repositories) U ook de kosten van deze resources bijhouden om binnen uw budget te blijven.

## <a name="integrate-with-your-existing-toolchain"></a>Integreren met uw bestaande toolchain
Gebruik vooraf gemaakte plug-ins of de API om ontwikkel-/testomgevingen rechtstreeks te voorzien vanuit uw gewenste [continue integratietool (CI),](devtest-lab-integrate-ci-cd-vsts.md)geïntegreerde ontwikkelomgeving (IDE) of geautomatiseerde releasepijplijn. U ook gebruik maken van de uitgebreide command-line tool.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- Zie [DevTest Labs-concepten voor](devtest-lab-concepts.md)meer informatie over DevTest Labs.
- Zie [Zelfstudie: Een lab instellen met Azure DevTest Labs](tutorial-create-custom-lab.md)voor een walkthrough met stapsgewijze instructies.
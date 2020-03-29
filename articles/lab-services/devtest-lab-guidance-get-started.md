---
title: Populaire scenario's voor het gebruik van Azure DevTest Labs
description: In dit artikel worden de primaire scenario's voor het gebruik van Azure DevTest Labs en twee algemene paden om de service in uw organisatie te gebruiken.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773805"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Populaire scenario's voor het gebruik van Azure DevTest Labs
Afhankelijk van de behoeften van een onderneming kan DevTest Labs worden geconfigureerd om aan verschillende vereisten te voldoen.  In dit artikel worden de populaire scenario's besproken. Elk scenario heeft betrekking op voordelen die worden gebracht door het gebruik van DevTest Labs en middelen om te gebruiken om deze scenario's te implementeren.  

- Ontwikkelaarsdesktops
- Testomgevingen
- Trainingen, hands-on labs en hackathons
- Sandboxed-onderzoeken
- Leslokaallabs

## <a name="developer-desktops"></a>Ontwikkelaarsdesktops
Ontwikkelaars hebben vaak verschillende eisen voor ontwikkelingsmachines voor verschillende projecten. Met DevTest Labs hebben ontwikkelaars toegang tot on-demand virtuele machines die zijn geconfigureerd om aan hun meest voorkomende scenario's te voldoen. DevTest Labs biedt de volgende voordelen:

- Organisaties kunnen gemeenschappelijke ontwikkelingsmachines bieden die consistentie tussen teams garanderen.
- Ontwikkelaars kunnen hun ontwikkelmachines snel op aanvraag inrichten of [een bestaande vooraf geconfigureerde machine claimen.](devtest-lab-add-claimable-vm.md)
- Ontwikkelaars kunnen resources op een selfservicemanier inrichten zonder machtigingen op abonnementsniveau nodig te hebben.
- IT of beheerders kunnen [de netwerktopologie vooraf definiëren](devtest-lab-configure-vnet.md) en ontwikkelaars kunnen het direct op een eenvoudige en intuïtieve manier gebruiken zonder speciale toegang.
- Ontwikkelaars kunnen hun ontwikkelmachines eenvoudig [aanpassen](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) als dat nodig is.
- Beheerders kunnen de kosten beheersen door ervoor te zorgen dat:
    - Ontwikkelaars [kunnen niet meer VM's krijgen](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) dan ze nodig hebben voor ontwikkeling
    - [VM's worden afgesloten](devtest-lab-set-lab-policy.md#set-auto-shutdown) wanneer ze niet in gebruik zijn
    - Alleen [een subset van VM-instantiegroottes toestaan](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) voor de specifieke labs
    - [Kostendoelen en meldingen](devtest-lab-configure-cost-management.md) beheren voor elk lab.

Zie [Azure DevTest Labs gebruiken voor ontwikkelaars voor](devtest-lab-developer-lab.md)meer informatie. 

## <a name="test-environments"></a>Testomgevingen
Het maken en beheren van testomgevingen in een onderneming kan een aanzienlijke inspanning vergen. Met DevTest Labs kunnen testomgevingen eenvoudig worden gemaakt, bijgewerkt of gedupliceerd. Hiermee hebben teams toegang tot een volledig geconfigureerde omgeving wanneer dat nodig is. In dit scenario biedt DevTest Labs de volgende voordelen:

- Organisaties kunnen gemeenschappelijke testomgevingen bieden die consistentie tussen teams garanderen.
- Testers kunnen de nieuwste versie van hun toepassing testen door snel Windows- en Linux-omgevingen in te richten met behulp van herbruikbare sjablonen.
- Beheerders kunnen het lab verbinden met Azure DevOps om DevOps-scenario's in te schakelen
- Labeigenaren kunnen de kosten beheersen door ervoor te zorgen dat:
    - [VM's in omgevingen worden uitgeschakeld](devtest-lab-set-lab-policy.md#set-auto-shutdown) wanneer ze niet in gebruik zijn
    - Alleen [een subset van VM-instantiegroottes toestaan voor](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) de specifieke labs
    - [Kostendoelen en meldingen](devtest-lab-configure-cost-management.md) beheren voor elk lab.

Zie [Azure DevTest Labs gebruiken voor VM- en PaaS-testomgevingen voor](devtest-lab-test-env.md)meer informatie.

## <a name="sandboxed-investigations"></a>Sandboxed-onderzoeken
Ontwikkelaars onderzoeken vaak verschillende technologieën of infrastructuurontwerp. Standaard worden alle omgevingen die met DevTest Labs zijn gemaakt, gemaakt in hun eigen resourcegroep. De DevTest Labs-gebruiker krijgt alleen leestoegang tot deze bronnen. Voor ontwikkelaars die meer controle nodig hebben, kan een labbrede instelling echter worden bijgewerkt om [bijdragende rechten](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) te geven aan de oorspronkelijke DevTest Labs-gebruiker voor elke omgeving die ze maken.  Met DevTest Labs kunnen ontwikkelaars automatisch toestemming krijgen voor ontwikkelaars voor omgevingen die ze in het lab maken.  Met dit scenario kunnen ontwikkelaars Azure-resources toevoegen en/of wijzigen wanneer ze dat nodig hebben voor hun ontwikkel- of testomgevingen. Met [de pagina kosten per resource](devtest-lab-configure-cost-management.md#view-cost-by-resource) kunnen labeigenaren de kosten bijhouden van elke omgeving die wordt gebruikt voor onderzoeken.

Zie [Toegangsrechten instellen voor een milieuresourcegroep voor](https://aka.ms/dtl-sandbox)meer informatie.

## <a name="trainings-hands-on-labs-and-hackathons"></a>Trainingen, hands-on labs en hackathons 
Een lab in Azure DevTest Labs fungeert als een geweldige container voor tijdelijke activiteiten zoals workshops, hands-on labs, trainingen of hackathons.  Met de service u een lab maken waar u aangepaste sjablonen bieden die elke cursist kan gebruiken om identieke en geïsoleerde omgevingen voor training te maken. In dit scenario biedt DevTest Labs de volgende voordelen:

- [Beleid](devtest-lab-set-lab-policy.md) zorgt ervoor dat stagiairs alleen het aantal resources krijgen, zoals virtuele machines, dat ze nodig hebben.
- Vooraf geconfigureerde en gemaakte machines worden [geclaimd](devtest-lab-add-claimable-vm.md) met één actie van trainee.
- Labs worden gedeeld met stagiairs door toegang te krijgen tot [URL voor het lab.](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab)
- [Vervaldatums](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) op virtuele machines zorgen ervoor dat machines worden verwijderd nadat ze niet meer nodig zijn.
- Het is gemakkelijk om een lab en alle [gerelateerde bronnen](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) te [verwijderen](devtest-lab-delete-lab-vm.md#delete-a-lab) wanneer de training voorbij is.

Zie [Azure DevTest Labs gebruiken voor training voor](devtest-lab-training-lab.md)meer informatie.  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Proof of concept vs. geschaalde implementatie
Zodra u besluit om DevTest Labs te verkennen, zijn er twee algemene paden voorwaarts: Proof of Concept vs Scaled Deployment.  

Een **geschaalde implementatie** bestaat uit weken/maanden van het beoordelen en plannen met de bedoeling de implementatie van DevTest Labs naar de hele onderneming die honderden of duizenden ontwikkelaars heeft.

Een **proof of concept** implementatie richt zich op een geconcentreerde inspanning van één team om organisatiewaarde vast te stellen. Hoewel het verleidelijk kan zijn om aan een geschaalde implementatie te denken, heeft de aanpak de neiging om vaker te mislukken dan de proof of concept-optie. Daarom raden we u aan klein te beginnen, te leren van het eerste team, dezelfde aanpak te herhalen met twee tot drie extra teams en vervolgens een geschaalde implementatie te plannen op basis van de opgedane kennis. Voor een succesvolle proof of concept raden we u aan een of twee teams te kiezen en hun scenario's (ontwikkelaarsomgeving versus testomgevingen) te identificeren, hun huidige use cases vast te leggen en DevTest Labs te implementeren.

## <a name="next-steps"></a>Volgende stappen
Lees de volgende artikelen:

- [DevTest Labs-concepten](devtest-lab-concepts.md)
- [Veelgestelde vragen over DevTest Labs](devtest-lab-faq.md)


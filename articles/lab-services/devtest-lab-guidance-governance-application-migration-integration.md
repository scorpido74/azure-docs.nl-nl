---
title: Toepassingsmigratie en -integratie in Azure DevTest Labs
description: Dit artikel biedt richtlijnen voor het beheer van azure DevTest Labs-infrastructuur in het kader van toepassingsmigratie en -integratie.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 14641e9096fa9366334e9f7460ae55cda0e6c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644883"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---application-migration-and-integration"></a>Governance van Azure DevTest Labs-infrastructuur - Implementatie en integratie van toepassingen
Zodra uw ontwikkeling / test lab omgeving is vastgesteld, moet je nadenken over de volgende vragen:

- Hoe benut je de omgeving binnen je projectteam?
- Hoe zorg je ervoor dat je het vereiste organisatiebeleid volgt en de flexibiliteit behoudt om waarde toe te voegen aan je toepassing?

## <a name="azure-marketplace-images-vs-custom-images"></a>Azure Marketplace-afbeeldingen versus aangepaste afbeeldingen

### <a name="question"></a>Vraag
Wanneer moet ik een Azure Marketplace-afbeelding gebruiken versus mijn eigen aangepaste organisatieafbeelding?

### <a name="answer"></a>Antwoord
Azure Marketplace moet standaard worden gebruikt, tenzij u specifieke problemen of organisatorische vereisten hebt. Enkele veelvoorkomende voorbeelden zijn;

- Complexe software-installatie waarvoor een toepassing moet worden opgenomen als onderdeel van de basisafbeelding.
- Het installeren en instellen van een toepassing kan vele uren duren, wat geen efficiënt gebruik is van de rekentijd die moet worden toegevoegd aan een Azure Marketplace-afbeelding.
- Ontwikkelaars en testers hebben snel toegang tot een virtuele machine nodig en willen de insteltijd van een nieuwe virtuele machine minimaliseren.
- Naleving of wettelijke voorwaarden (bijvoorbeeld beveiligingsbeleid) die voor alle machines moeten gelden.

Het gebruik van aangepaste afbeeldingen moet niet lichtvaardig worden beschouwd. Ze introduceren extra complexiteit, omdat je nu VHD-bestanden moet beheren voor die onderliggende basisafbeeldingen. U moet ook routinematig patch die basisbeelden met software-updates. Deze updates omvatten nieuwe besturingssysteemupdates en eventuele updates of configuratiewijzigingen die nodig zijn voor het softwarepakket zelf.

## <a name="formula-vs-custom-image"></a>Afbeelding van Formule versus aangepaste

### <a name="question"></a>Vraag
Wanneer moet ik een formule versus aangepaste afbeelding gebruiken?

### <a name="answer"></a>Antwoord
De beslissende factor in dit scenario is doorgaans kosten en hergebruik.

Als u een scenario hebt waarbij veel gebruikers/laboratoria een afbeelding met veel software bovenop de basisafbeelding nodig hebben, u de kosten verlagen door een aangepaste afbeelding te maken. Dit betekent dat de afbeelding eenmaal wordt gemaakt. Het vermindert de insteltijd van de virtuele machine en de kosten die worden gemaakt als gevolg van de virtuele machine draait wanneer setup optreedt.

Echter, een extra factor om op te merken is de frequentie van wijzigingen in uw softwarepakket. Als u dagelijks bouwt en vereist dat deze software op de virtuele machines van uw gebruikers staat, u overwegen een formule te gebruiken in plaats van een aangepaste afbeelding.

## <a name="use-custom-organizational-images"></a>Aangepaste organisatieafbeeldingen gebruiken

### <a name="question"></a>Vraag
Hoe kan ik een eenvoudig herhaalbaar proces opzetten om mijn aangepaste organisatieafbeeldingen in een DevTest Labs-omgeving te brengen?

### <a name="answer"></a>Antwoord
Zie [deze video op Image Factory patroon](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/). Dit scenario is een geavanceerd scenario en de meegeleverde scripts zijn alleen voorbeeldscripts. Als er wijzigingen nodig zijn, moet u de scripts beheren en onderhouden die in uw omgeving worden gebruikt.

DevTest Labs gebruiken om een aangepaste afbeeldingspijplijn te maken in Azure Pipelines:

- [Inleiding: Ontvang VM's binnen enkele minuten klaar door een afbeeldingsfabriek in Azure DevTest Labs in te stellen](https://blogs.msdn.microsoft.com/devtestlab/2016/09/14/introduction-get-vms-ready-in-minutes-by-setting-up-image-factory-in-azure-devtest-labs/)
- [Image Factory – Deel 2! Azure-pijplijnen en fabriekslab instellen om VM's te maken](https://blogs.msdn.microsoft.com/devtestlab/2017/10/25/image-factory-part-2-setup-vsts-to-create-vms-based-on-devtest-labs/)
- [Image Factory - Deel 3: Aangepaste afbeeldingen opslaan en distribueren naar meerdere labs](https://blogs.msdn.microsoft.com/devtestlab/2018/01/10/image-factory-part-3-save-custom-images-and-distribute-to-multiple-labs/)
- [Video: Aangepaste imagefabriek met Azure DevTest Labs](https://blogs.msdn.microsoft.com/devtestlab/2017/04/17/video-custom-image-factory-with-azure-devtest-labs/)

## <a name="patterns-to-set-up-network-configuration"></a>Patronen voor het instellen van netwerkconfiguratie

### <a name="question"></a>Vraag
Hoe zorg ik ervoor dat de ontwikkeling en test virtuele machines niet in staat zijn om het openbare internet te bereiken? Zijn er aanbevolen patronen om netwerkconfiguratie in te stellen?

### <a name="answer"></a>Antwoord
Ja. Er zijn twee aspecten te overwegen - inkomend en uitgaand verkeer.

**Binnenkomend verkeer** - Als de virtuele machine geen openbaar IP-adres heeft, kan het niet worden bereikt door het internet. Een gemeenschappelijke aanpak is ervoor te zorgen dat een beleid op abonnementsniveau wordt ingesteld, zodat geen enkele gebruiker in staat is om een openbaar IP-adres te maken.

**Uitgaand verkeer** - Als u wilt voorkomen dat virtuele machines rechtstreeks naar openbaar internet gaan en verkeer via een bedrijfsfirewall afdwingen, u het verkeer on-premises via een snelroute of VPN routeren, door gebruik te maken van gedwongen routering.

> [!NOTE]
> Als u een proxyserver hebt die verkeer blokkeert zonder proxy-instellingen, vergeet dan niet uitzonderingen toe te voegen aan het artefact-opslagaccount van het lab.

U ook netwerkbeveiligingsgroepen gebruiken voor virtuele machines of subnetten. Deze stap voegt een extra beschermingslaag toe om verkeer toe te staan / te blokkeren.

## <a name="new-vs-existing-virtual-network"></a>Nieuw versus bestaand virtueel netwerk

### <a name="question"></a>Vraag
Wanneer moet ik een nieuw virtueel netwerk maken voor mijn DevTest Labs-omgeving versus het gebruik van een bestaand virtueel netwerk?

### <a name="answer"></a>Antwoord
Als uw VM's moeten communiceren met bestaande infrastructuur, moet u overwegen om een bestaand virtueel netwerk te gebruiken in uw DevTest Labs-omgeving. Als u ExpressRoute gebruikt, u bovendien de hoeveelheid VNets / Subnetten minimaliseren, zodat u uw IP-adresruimte die wordt toegewezen voor gebruik in de abonnementen niet fragmenteert. U moet ook overwegen om het VNet-peeringpatroon hier te gebruiken (Hub-Spoke-model). Deze aanpak maakt vnet/subnetcommunicatie tussen abonnementen binnen een bepaalde regio mogelijk, hoewel peering tussen regio's een up-coming-functie is in Azure-netwerken.

Anders kan elke DevTest Labs-omgeving zijn eigen virtuele netwerk hebben. Houd er echter rekening mee dat er [grenzen](../azure-resource-manager/management/azure-subscription-service-limits.md) zijn aan het aantal virtuele netwerken per abonnement. Het standaardbedrag is 50, maar deze limiet kan worden verhoogd tot 100.

## <a name="shared-public-or-private-ip"></a>Gedeelde, openbare of particuliere IP

### <a name="question"></a>Vraag
Wanneer moet ik een gedeelde IP vs. openbare IP vs. private IP gebruiken?

### <a name="answer"></a>Antwoord
Als u een site-to-site VPN of Express Route gebruikt, u overwegen om privé-IP's te gebruiken, zodat uw machines toegankelijk zijn via uw interne netwerk en ontoegankelijk zijn via openbaar internet.

> [!NOTE]
> Labeigenaren kunnen dit subnetbeleid wijzigen om ervoor te zorgen dat niemand per ongeluk openbare IP-adressen voor hun VM's maakt. De eigenaar van het abonnement moet een abonnementsbeleid maken dat voorkomt dat openbare IP's worden gemaakt.

Bij het gebruik van gedeelde openbare IP's delen de virtuele machines in een lab een openbaar IP-adres. Deze aanpak kan handig zijn wanneer u moet voorkomen dat de limieten voor openbare IP-adressen voor een bepaald abonnement worden overschreden.

## <a name="limits-of-number-of-virtual-machines-per-user-or-lab"></a>Limieten van het aantal virtuele machines per gebruiker of lab

### <a name="question"></a>Vraag
Is er een regel in termen van hoeveel virtuele machines ik moet instellen per gebruiker, of per lab?

### <a name="answer"></a>Antwoord
Bij het overwegen van het aantal virtuele machines per gebruiker of per lab, zijn er drie belangrijke punten van zorg:

- De **totale kosten** die het team kan besteden aan resources in het lab. Het is gemakkelijk om veel machines te draaien. Om de kosten te beheersen, is één mechanisme het beperken van het aantal VM's per gebruiker en/of per lab
- Het totale aantal virtuele machines in een lab wordt beïnvloed door de [beschikbare quota op abonnementsniveau.](../azure-resource-manager/management/azure-subscription-service-limits.md) Een van de bovengrenzen is 800 resourcegroepen per abonnement. DevTest Labs maakt momenteel een nieuwe brongroep voor elke VM (tenzij gedeelde openbare IP's worden gebruikt). Als er 10 labs in een abonnement, labs kunnen passen ongeveer 79 virtuele machines in elk lab (800 bovengrens - 10 resource groepen voor de 10 labs zelf) = 79 virtuele machines per lab.
- Als het lab is aangesloten op on-premises via Express Route (bijvoorbeeld), zijn er **gedefinieerde IP-adresruimten beschikbaar** voor het VNet/Subnet. Om ervoor te zorgen dat VM's in het lab niet mislukken (fout: kan geen IP-adres krijgen), kunnen labeigenaren de max VM's per lab opgeven die zijn afgestemd op de beschikbare IP-adresruimte.

## <a name="use-resource-manager-templates"></a>Resource Manager-sjablonen gebruiken

### <a name="question"></a>Vraag
Hoe kan ik Resource Manager-sjablonen gebruiken in mijn DevTest Labs-omgeving?

### <a name="answer"></a>Antwoord
U implementeert uw Resource Manager-sjablonen in een DevTest Labs-omgeving met behulp van stappen die worden genoemd in de functie Omgevingen in het artikel [DevTest labs.](devtest-lab-test-env.md) In principe controleert u uw Resource Manager-sjablonen in een Git-opslagplaats (Azure Repos of GitHub) en voegt u een [privéopslagplaats voor uw sjablonen](devtest-lab-test-env.md) toe aan het lab.

Dit scenario is mogelijk niet handig als u DevTest Labs gebruikt om ontwikkelingsmachines te hosten, maar kan nuttig zijn als u een faseringsomgeving bouwt, die representatief is voor de productie.

Het is ook vermeldenswaard dat het aantal virtuele machines per lab of per gebruiker optie alleen beperkt het aantal machines native gemaakt in het lab zelf, en niet door een omgeving (Resource Manager sjablonen).

## <a name="next-steps"></a>Volgende stappen
Zie [Omgevingen gebruiken in DevTest Labs](devtest-lab-test-env.md).
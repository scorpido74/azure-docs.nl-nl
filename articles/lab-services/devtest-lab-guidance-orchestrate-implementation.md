---
title: Implementatie van Azure DevTest Labs orkestreren
description: In dit artikel vindt u richtlijnen voor het orkestreren van de implementatie van Azure DevTest Labs in uw organisatie.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: fc6cbbd0aa9cb0750e497c7cc7edbd42f21bda55
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024996"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>De implementatie van Azure DevTest Labs orkestreren
Dit artikel biedt een aanbevolen aanpak voor snelle implementatie en implementatie van Azure DevTest Labs. De volgende afbeelding benadrukt het algemene proces als prescriptieve richtlijnen, waarbij de flexibiliteit wordt nageleefd voor het ondersteunen van verschillende industrievereisten en -scenario's.

![Stappen voor het implementeren van Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Veronderstellingen
In dit artikel wordt ervan uitgegaan dat u de volgende items hebt voordat u een DevTest Labs-pilot implementeert:

- **Azure-abonnement**: Het pilotteam heeft toegang tot het implementeren van resources in een Azure-abonnement. Als de workloads alleen worden ontwikkeld en getest, is het raadzaam om de Enterprise DevTest-aanbieding te selecteren voor extra beschikbare afbeeldingen en lagere tarieven op virtuele Windows-machines.
- **On-premises toegang:** Indien nodig is on-premises toegang al geconfigureerd. De on-premises toegang kan worden bereikt via een Site-to-site VPN-verbinding of via Express Route. Connectiviteit via Express Route kan meestal vele weken duren om vast te stellen, het is raadzaam om de Express Route op zijn plaats te hebben voordat u met het project begint.
- **Pilot Teams**: Het initiële ontwikkelingsprojectteam(en) dat Gebruik maakt van DevTest Labs is geïdentificeerd, samen met de toepasselijke ontwikkelings- of testactiviteiten en stelt vereisten/doelen/doelstellingen vast voor die teams.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Mijlpaal 1: Eerste netwerktopologie en ontwerp vaststellen
Het eerste aandachtsgebied bij het implementeren van een Azure DevTest Labs-oplossing is het vaststellen van de geplande connectiviteit voor de virtuele machines. In de volgende stappen worden de nodige procedures beschreven:

1. Definieer **initiële IP-adresbereiken** die zijn toegewezen aan het DevTest Labs-abonnement in Azure. Deze stap vereist het voorspellen van het verwachte gebruik in aantal VM's, zodat u een groot genoeg blok bieden voor toekomstige uitbreiding.
2. Identificeer **methoden voor gewenste toegang tot** de DevTest Labs (bijvoorbeeld externe / interne toegang). Een belangrijk punt in deze stap is om te bepalen of virtuele machines openbare IP-adressen hebben (dat wil zeggen, rechtstreeks toegankelijk vanaf het internet).
3. Identificeer en stel **methoden voor connectiviteit** vast met de rest van de Azure-cloudomgeving en on-premises. Als de geforceerde routering met Express Route is ingeschakeld, is het waarschijnlijk dat de virtuele machines de juiste proxyconfiguraties nodig hebben om de bedrijfsfirewall te doorlopen.
4. Als VM's domeinmoeten worden **samengevoegd,** bepaalt u of ze lid worden van een clouddomein (bijvoorbeeld AAD Directory Services) of een on-premises domein. Bepaal voor on-premises welke organisatie-eenheid (OU) binnen de active directory waartoe de virtuele machines lid worden. Controleer bovendien of gebruikers toegang hebben tot lid worden (of een serviceaccount kunnen aanmaken dat machinerecords in het domein kan maken)

## <a name="milestone-2-deploy-the-pilot-lab"></a>Mijlpaal 2: Zet het pilotlab in
Zodra de netwerktopologie op zijn plaats is, kan het eerste/pilotlab worden gemaakt door de volgende stappen te nemen:

1. Maak een eerste DevTest Labs-omgeving.
2. Bepaal toegestane VM-afbeeldingen en -formaten voor gebruik in het lab. Bepaal of aangepaste afbeeldingen kunnen worden geüpload naar Azure voor gebruik met DevTest Labs.
3. Beveilig de toegang tot het lab door de eerste Role Base Access Controls (RBAC) te maken voor het lab (labeigenaren en labgebruikers). We raden u aan gesynchroniseerde active directory-accounts te gebruiken met Azure Active Directory voor identiteit met DevTest Labs.
4. Configureer DevTest Labs om beleidsregels te gebruiken, zoals planningen, kostenbeheer, claimbare VM's, aangepaste afbeeldingen of formules.
5. Een online repository instellen, zoals Azure Repos/Git.
6. Bepaal het gebruik van openbare of private repositories of een combinatie van beide. Organiseer JSON-sjablonen voor implementaties en langdurige ondersteuning.
7. Maak indien nodig aangepaste artefacten. Deze stap is optioneel. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Mijlpaal 3: Documentatie, ondersteuning, leren en verbeteren
De eerste pilotteams hebben mogelijk diepgaande ondersteuning nodig om aan de slag te gaan. Gebruik hun ervaringen om ervoor te zorgen dat de juiste documentatie en ondersteuning aanwezig is voor de verdere implementatie van Azure DevTest Labs.

1. Introduceer de pilotteams in hun nieuwe DevTest Labs-bronnen (demo's, documentatie)
2. Op basis van de ervaringen van pilootteams, plannen en leveren van documentatie als dat nodig is
3. Formaliseer het proces voor het inwerken van nieuwe teams (het maken en configureren van labs, het bieden van toegang, enz.)
4. Op basis van de initiële opname, controleren oorspronkelijke prognose van IP-adres ruimte is nog steeds redelijk en nauwkeurig
5. Zorg ervoor dat de juiste nalevings- en beveiligingsbeoordelingen zijn voltooid

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel in deze serie: [Governance of Azure DevTest Labs infrastructure](devtest-lab-guidance-governance-resources.md)

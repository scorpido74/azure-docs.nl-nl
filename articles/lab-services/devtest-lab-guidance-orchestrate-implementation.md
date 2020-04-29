---
title: Implementatie van Azure DevTest Labs organiseren
description: Dit artikel bevat richt lijnen voor het organiseren van de implementatie van Azure DevTest Labs in uw organisatie.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82024996"
---
# <a name="orchestrate-the-implementation-of-azure-devtest-labs"></a>De implementatie van Azure DevTest Labs organiseren
Dit artikel biedt een aanbevolen benadering voor snelle implementatie en implementatie van Azure DevTest Labs. De volgende afbeelding benadrukt het algehele proces als prescriptieve richt lijnen, terwijl de flexibiliteit wordt geobserveerd voor het ondersteunen van verschillende industriële vereisten en scenario's.

![Stappen voor het implementeren van Azure DevTest Labs](./media/devtest-lab-guidance-orchestrate-implementation/implementation-steps.png)

## <a name="assumptions"></a>Veronderstellingen
In dit artikel wordt ervan uitgegaan dat u de volgende items hebt voordat u een DevTest Labs-pilot implementeert:

- **Azure-abonnement**: het proef team heeft toegang tot het implementeren van resources in een Azure-abonnement. Als de workloads alleen ontwikkelen en testen zijn, is het raadzaam om de Enter prise DevTest-aanbieding te selecteren voor extra beschik bare installatie kopieën en lagere tarieven op virtuele Windows-machines.
- **On-premises toegang**: on-premises toegang, indien nodig, is al geconfigureerd. De on-premises toegang kan worden bereikt via een site-naar-site-VPN-verbinding of via Express route. Connectiviteit via Express route kan meestal veel weken duren. u wordt aangeraden de snelle route in te stellen voordat het project wordt gestart.
- **Pilot teams**: de eerste ontwikkelings project team (s) die gebruikmaken van DevTest Labs is geïdentificeerd samen met de toepasselijke ontwikkelings-en test activiteiten en tot stand brengen van vereisten/doelen/doel stellingen voor die teams.

## <a name="milestone-1-establish-initial-network-topology-and-design"></a>Mijl paal 1: initiële netwerk topologie en-ontwerp instellen
Het eerste gebied van focus bij het implementeren van een Azure DevTest Labs oplossing is om de geplande connectiviteit voor de virtuele machines tot stand te brengen. De volgende stappen beschrijven de benodigde procedures:

1. Definieer de **eerste IP-** adresbereiken die zijn toegewezen aan het DevTest Labs-abonnement in Azure. Deze stap vereist een prognose van het verwachte gebruik in aantal Vm's, zodat u een groot genoeg blok kunt opgeven voor toekomstige uitbrei ding.
2. Bepaal de **gewenste methoden voor toegang tot** de DevTest Labs (bijvoorbeeld externe/interne toegang). Een belang rijk punt in deze stap is om te bepalen of de virtuele machines een openbaar IP-adres hebben (dat wil zeggen, direct toegankelijk via internet).
3. Identificeer en stel **methoden van connectiviteit** samen met de rest van de Azure-cloud omgeving en on-premises. Als de geforceerde route ring met Express route is ingeschakeld, is het waarschijnlijk dat de virtuele machines de juiste proxy configuraties hebben om de bedrijfs firewall te passeren.
4. Als Vm's lid moeten zijn van een **domein**, moet u bepalen of ze lid worden van een Cloud domein (bijvoorbeeld Aad Directory Services) of een on-premises domein. Bepaal bij on-premises welke organisatie-eenheid (OE) in Active Directory moet worden gekoppeld aan de virtuele machines. Controleer bovendien of gebruikers toegang hebben tot deelname (of stel een service account in dat de mogelijkheid heeft om computer records in het domein te maken).

## <a name="milestone-2-deploy-the-pilot-lab"></a>Mijl paal 2: het pilot Lab implementeren
Zodra de netwerk topologie is geïmplementeerd, kan het eerste/pilot Lab worden gemaakt door de volgende stappen uit te voeren:

1. Maak een eerste DevTest Labs-omgeving.
2. Toegestane VM-installatie kopieën en-groottes voor gebruik met Lab bepalen. Bepaal of aangepaste installatie kopieën in azure kunnen worden geüpload voor gebruik met DevTest Labs.
3. Veilige toegang tot het lab door het maken van de initiële rol Base Access Controls (RBAC) voor het lab (eigenaar van het lab en Lab-gebruikers). U wordt aangeraden om met DevTest Labs gesynchroniseerde Active Directory-accounts met Azure Active Directory voor identiteit te gebruiken.
4. DevTest Labs configureren voor het gebruik van beleids regels zoals planningen, kosten beheer, instel bare Vm's, aangepaste installatie kopieën of formules.
5. Stel een online opslag plaats in, zoals Azure opslag plaatsen/git.
6. Besluit over het gebruik van open bare of particuliere opslag plaatsen of combi natie van beide. Structuur van JSON-sjablonen organiseren voor implementaties en langere termijn.
7. Maak indien nodig aangepaste artefacten. Deze stap is optioneel. 

## <a name="milestone-3-documentation-support-learn-and-improve"></a>Mijl paal 3: Documentatie, ondersteuning, leren en verbeteren
De eerste test teams hebben mogelijk uitgebreide ondersteuning nodig om aan de slag te gaan. Gebruik hun ervaringen om ervoor te zorgen dat de juiste documentatie en ondersteuning aanwezig is voor een blijvende implementatie van Azure DevTest Labs.

1. Kennismaken met de prototype teams van de nieuwe DevTest Labs-resources (demo's, documentatie)
2. Op basis van de ervaringen van test teams, plan en bezorgt u waar nodig documentatie
3. Formaliseren-proces voor het voorbereiden van nieuwe teams (het maken en configureren van Labs, het verlenen van toegang, enz.)
4. Controleer op basis van de eerste opname of de oorspronkelijke prognose van de IP-adres ruimte nog steeds redelijk en nauw keurig is
5. Zorg ervoor dat de juiste naleving en beveiligings beoordelingen zijn voltooid

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel in deze serie: [governance van Azure DevTest Labs-infra structuur](devtest-lab-guidance-governance-resources.md)

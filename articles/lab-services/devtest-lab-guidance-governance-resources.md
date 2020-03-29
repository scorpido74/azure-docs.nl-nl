---
title: Governance van Azure DevTest Labs-infrastructuur - Resource
description: In dit artikel wordt ingegaan op de afstemming en het beheer van resources voor Azure DevTest Labs binnen uw organisatie.
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
ms.openlocfilehash: 8bb00c770c61a0a5462a01ae552bd7e40a7cdb36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470646"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Beheer van Azure DevTest Labs-infrastructuur - Resources
In dit artikel wordt ingegaan op de afstemming en het beheer van resources voor DevTest Labs binnen uw organisatie. 

## <a name="align-within-an-azure-subscription"></a>Uitlijnen binnen een Azure-abonnement 

### <a name="question"></a>Vraag
Hoe stem ik DevTest Labs-resources uit binnen een Azure-abonnement?

### <a name="answer"></a>Antwoord
Voordat een organisatie Azure gaat gebruiken voor algemene toepassingsontwikkeling, moeten IT-planners eerst bekijken hoe ze de mogelijkheid kunnen introduceren als onderdeel van hun totale portfolio van services. De controlegebieden moeten betrekking hebben op de volgende punten van zorg:

- Hoe meet je de kosten in verband met de levenscyclus van de toepassingsontwikkeling?
- Hoe stemt de organisatie het voorgestelde serviceaanbod af op het bedrijfsbeveiligingsbeleid? 
- Is segmentatie nodig om de ontwikkel- en productieomgevingen te scheiden? 
- Welke controles worden ingevoerd voor het gemak van beheer, stabiliteit en groei op lange termijn?

De **eerste aanbevolen praktijk** is om de Azure-taxonomie van organisaties te controleren waar de scheidingen tussen productie- en ontwikkelingsabonnementen worden beschreven. In het volgende diagram zorgt de voorgestelde taxonomie voor een logische scheiding van ontwikkelings-/test- en productieomgevingen. Met deze aanpak kan een organisatie factureringscodes invoeren om de kosten die aan elke omgeving zijn gekoppeld afzonderlijk bij te houden. Zie [Prescriptief abonnementsbeheer voor](/azure/architecture/cloud-adoption/appendix/azure-scaffold)meer informatie . Bovendien u [Azure-tags](../azure-resource-manager/management/tag-resources.md) gebruiken om resources te ordenen voor tracking- en factureringsdoeleinden.

De **tweede aanbevolen praktijk** is het devTest-abonnement in te schakelen binnen de Azure Enterprise-portal. Hiermee kan een organisatie clientbesturingssystemen uitvoeren die doorgaans niet beschikbaar zijn in een Azure-bedrijfsabonnement. Gebruik vervolgens bedrijfssoftware waarbij u alleen voor de compute betaalt en u zich geen zorgen hoeft te maken over licenties. Het zorgt ervoor dat de facturering voor aangewezen services, waaronder galerijafbeeldingen in IaaS zoals Microsoft SQL Server, alleen is gebaseerd op verbruik. Details over het Azure DevTest-abonnement vindt u [hier](https://azure.microsoft.com/offers/ms-azr-0148p/) voor EA-klanten (Enterprise Agreement) en [hier](https://azure.microsoft.com/offers/ms-azr-0023p/) voor Betalen terwijl u naar klanten gaat.

![Resourceuitlijning met abonnementen](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Dit model biedt een organisatie de flexibiliteit om Azure DevTest Labs op schaal te implementeren. Een organisatie kan honderden laboratoria ondersteunen voor verschillende business units met 100 tot 1000 virtuele machines die parallel draaien. Het bevordert het idee van een gecentraliseerde enterprise lab-oplossing die dezelfde principes van configuratiebeheer en beveiligingscontroles kan delen.

Dit model zorgt er ook voor dat de organisatie hun resourcelimieten die zijn gekoppeld aan hun Azure-abonnement niet uitput. Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over abonnements- en servicelimieten. Het devTest Labs-inrichtingsproces kan een groot aantal resourcegroepen verbruiken. U vragen om limieten te verhogen via een ondersteuningsaanvraag in het Azure DevTest-abonnement. De resources binnen het productieabonnement worden niet beïnvloed naarmate het ontwikkelingsabonnement in gebruik wordt genomen. Zie [Quota en limieten schalen in DevTest Labs voor](devtest-lab-scale-lab.md)meer informatie over het schalen van DevTest Labs.

Een gemeenschappelijke limiet voor abonnementsniveau die moet worden verantwoord, is de manier waarop de toewijzingen voor het netwerk-IP-bereik worden toegewezen om zowel productie- als ontwikkelingsabonnementen te ondersteunen. Deze toewijzingen moeten rekening houden met groei in de loop van de tijd (uitgaande van on-premises connectiviteit of een andere netwerktopologie waarvoor de onderneming hun netwerkstack moet beheren in plaats van in gebreke te blijven bij de implementatie van Azure). De aanbevolen praktijk is om een paar virtuele netwerken die een groot IP-adres voorvoegsel toegewezen en verdeeld met veel grote subnetten in plaats van meerdere virtuele netwerken met kleine subnetten hebben. Met 10 abonnementen u bijvoorbeeld 10 virtuele netwerken definiëren (één voor elk abonnement). Alle labs die geen isolatie nodig hebben, kunnen hetzelfde subnet delen op het vnet van het abonnement.

## <a name="maintain-naming-conventions"></a>Naamgevingsconventies onderhouden

### <a name="question"></a>Vraag
Hoe onderhoud ik een naamgevingsconventie in mijn DevTest Labs-omgeving?

### <a name="answer"></a>Antwoord
U de huidige overeenkomsten voor bedrijfsnaamgeving uitbreiden naar Azure-bewerkingen en deze consistent maken in de Omgeving van DevTest Labs.

Bij het implementeren van DevTest Labs raden we je aan een specifiek startbeleid te hebben. U implementeert dit beleid via een centraal script en JSON-sjablonen om consistentie af te dwingen. Naamgevingsbeleid kan worden geïmplementeerd via Azure-beleid dat op abonnementsniveau wordt toegepast. Zie Voorbeelden van [Azure-beleid](../governance/policy/samples/index.md)voor JSON-voorbeelden voor JSON-voorbeelden .

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Aantal gebruikers per lab en labs per organisatie

### <a name="question"></a>Vraag 
Hoe bepaal ik de verhouding van gebruikers per lab en het totale aantal labs dat nodig is voor een organisatie?

### <a name="answer"></a>Antwoord
We raden aan dat business units en ontwikkelingsgroepen die zijn gekoppeld aan hetzelfde ontwikkelingsproject worden geassocieerd met hetzelfde lab. Hiermee kunnen dezelfde typen beleidsregels, afbeeldingen en afsluitbeleid op beide groepen worden toegepast. 

Het kan ook nodig zijn om geografische grenzen te overwegen. Ontwikkelaars in het noordoosten van de Verenigde Staten (VS) kunnen bijvoorbeeld gebruik maken van een lab dat is ingericht in Oost-US2. En, ontwikkelaars in Dallas, Texas, en Denver, Colorado kan worden gericht op een bron te gebruiken in us South Central. Als er een samenwerkingsverband is met een externe derde partij, kunnen ze worden toegewezen aan een lab dat niet wordt gebruikt door interne ontwikkelaars. 

U een lab ook gebruiken voor een specifiek project binnen Azure DevOps-projecten. Vervolgens past u beveiliging toe via een opgegeven Azure Active Directory-groep, waarmee u toegang heeft tot beide sets resources. Het virtuele netwerk dat aan het lab is toegewezen, kan een andere grens zijn om gebruikers te consolideren.

## <a name="deletion-of-resources"></a>Verwijdering van resources

### <a name="question"></a>Vraag
Hoe kunnen we voorkomen dat de verwijdering van middelen binnen een lab?

### <a name="answer"></a>Antwoord
We raden u aan de juiste machtigingen in te stellen op labniveau, zodat alleen geautoriseerde gebruikers resources kunnen verwijderen of het labbeleid kunnen wijzigen. Ontwikkelaars moeten worden geplaatst in de **DevTest Labs Gebruikers** groep. De leadontwikkelaar of de infrastructuurlead moet de **Eigenaar van DevTest Labs**zijn. We raden u aan slechts twee labeigenaren te hebben. Dit beleid strekt zich uit naar de code repository om corruptie te voorkomen. Lab-toepassingen hebben rechten om resources te gebruiken, maar kunnen het labbeleid niet bijwerken. Zie het volgende artikel met de rollen en rechten die elke ingebouwde groep in een lab heeft: [Eigenaren en gebruikers toevoegen in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Lab verplaatsen naar een andere resourcegroep 

### <a name="question"></a>Vraag
Wordt het ondersteund om een lab te verplaatsen naar een andere Resource Group?

### <a name="answer"></a>Antwoord
Ja. Navigeer naar de pagina Resourcegroep vanaf de startpagina voor uw lab. Selecteer vervolgens **Verplaatsen** op de werkbalk en selecteer het lab dat u naar een andere brongroep wilt verplaatsen. Wanneer u een lab maakt, wordt er automatisch een resourcegroep voor u gemaakt. U het lab echter verplaatsen naar een andere resourcegroep die de ondernemingsnaamgevingsconventies volgt. 

## <a name="next-steps"></a>Volgende stappen
Zie [Kosten en eigendom beheren](devtest-lab-guidance-governance-cost-ownership.md).

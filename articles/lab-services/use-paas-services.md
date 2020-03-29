---
title: PaaS-services (Platform-as-a-Service) gebruiken in Azure DevTest Labs
description: Meer informatie over het gebruik van Platform-as-a-Service (Pass)-services in Azure DevTest Labs.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 88bbf921fedae4bcdba2b6386ce6e08105206cd2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169195"
---
# <a name="use-platform-as-a-service-paas-services-in-azure-devtest-labs"></a>PaaS-services (Platform-as-a-Service) gebruiken in Azure DevTest Labs
PaaS wordt ondersteund in DevTest Labs via de omgevingen functie. Omgevingen in DevTest Labs worden ondersteund door vooraf geconfigureerde Azure Resource Manager-sjablonen in een Git-repository. Omgevingen kunnen zowel PaaS- als IaaS-bronnen bevatten. Hiermee u complexe systemen maken die Azure-bronnen kunnen bevatten, zoals virtuele machines, databases, virtuele netwerken en web-apps, die zijn aangepast om samen te werken. Deze sjablonen maken een consistente implementatie en een verbeterd beheer van omgevingen mogelijk met behulp van broncodebeheer. 

Een goed ingesteld systeem maakt de volgende scenario's mogelijk: 

- Ontwikkelaars hebben onafhankelijke en meerdere omgevingen
- Asynchroon testen op verschillende configuraties
- Integratie in faserings- en productiepijplijnen zonder sjabloonwijzigingen
- Het hebben van zowel ontwikkelmachines als omgevingen in hetzelfde lab verbetert het gemak van beheer en kostenrapportage.  

De DevTest Labs resource provider maakt resources namens de labgebruiker, dus er hoeven geen extra machtigingen te worden gegeven aan de labgebruiker om het gebruik van de PaaS-bronnen mogelijk te maken. De volgende afbeelding toont een cluster servicestructuur als een omgeving in het lab.

![Cluster van servicefabric als omgeving](./media/create-environment-service-fabric-cluster/cluster-created.png)

Zie [MultiVM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)voor meer informatie over het instellen van omgevingen. DevTest Labs heeft een openbare opslagplaats van Azure Resource Manager-sjablonen die u gebruiken om omgevingen te maken zonder zelf verbinding te hoeven maken met een externe GitHub-bron. Zie [Openbare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)voor meer informatie over openbare omgevingen.

In grote organisaties bieden ontwikkelteams doorgaans omgevingen zoals aangepaste/geïsoleerde testomgevingen. Er kunnen omgevingen zijn die door alle teams binnen een business unit of een divisie moeten worden gebruikt. De IT-groep wil mogelijk hun omgevingen bieden die door alle teams in de organisatie kunnen worden gebruikt.  

## <a name="customizations"></a>Aanpassingen

#### <a name="sandbox"></a>Sandbox 
De eigenaar van het lab kan labomgevingen aanpassen om de rol van de gebruiker te wijzigen van **lezer** naar **bijdrager** binnen de resourcegroep. Deze mogelijkheid bevindt zich op de pagina **Lab-instellingen** onder **de configuratie en het beleid** van het lab. Met deze wijziging in de rol kan de gebruiker resources binnen die omgeving toevoegen of verwijderen. Als u de toegang verder wilt beperken, gebruikt u Azure-beleid. Met deze functionaliteit u de resources of configuratie aanpassen zonder toegang op abonnementsniveau.

#### <a name="custom-tokens"></a>Aangepaste tokens
Er is een aantal aangepaste labinformatie die zich buiten de brongroep bevindt en specifiek is voor omgevingen die de sjabloon kan openen. Hier zijn een aantal van hen: 

- Identificatie van het labnetwerk
- Locatie
- Opslagaccount waarin de sjablonenbestanden van Resource Manager zijn opgeslagen. 
 
#### <a name="lab-virtual-network"></a>Lab virtueel netwerk
In [de verbindingsomgevingen met het virtuele netwerkartikel van het lab](connect-environment-lab-virtual-network.md) `$(LabSubnetId)` wordt beschreven hoe u uw Resource Manager-sjabloon wijzigen om het token te gebruiken. Wanneer een omgeving wordt `$(LabSubnetId)` gemaakt, wordt het token vervangen door het eerste subnetteken waarbij het gebruik in de optie **voor het maken van virtuele machines** is ingesteld op **true.** Het stelt onze omgeving in staat om eerder gemaakte netwerken te gebruiken. Als u dezelfde Resource Manager-sjablonen wilt gebruiken in omgevingen `$(LabSubnetId)` in testomgevingen als fasering en productie, gebruikt u als standaardwaarde in een sjabloonparameter Resource Manager. 

#### <a name="environment-storage-account"></a>Milieuopslagaccount
DevTest Labs ondersteunt het gebruik van [geneste Resource Manager-sjablonen.](../azure-resource-manager/templates/linked-templates.md) In het artikel [[Deploy nested Azure Resource Manager-sjablonen voor testomgevingen](deploy-nested-template-environments.md) wordt uitgelegd hoe u `_artifactsLocation` `_artifactsLocationSasToken` een URI gebruiken voor een Sjabloon resourcebeheer in dezelfde map als of in een geneste map van de hoofdsjabloon. Zie de sectie **Implementatieartefacten** van [Azure Resource Manager – Best Practices Guide](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)voor meer informatie over deze twee tokens.

## <a name="user-experience"></a>Gebruikerservaring

## <a name="developer"></a>Developer
Ontwikkelaars gebruiken dezelfde workflow voor het maken van een VM om een specifieke omgeving te maken. Ze selecteren de omgeving versus de machineafbeelding en voeren de benodigde informatie in die de sjabloon vereist. Elke ontwikkelaar met een omgeving zorgt voor de implementatie van wijzigingen en verbeterde inner loop debugging. De omgeving kan op elk gewenst moment worden gemaakt met behulp van de nieuwste sjabloon.  Met deze functie kunnen de omgevingen worden vernietigd en opnieuw worden gemaakt om de downtime te verminderen van het handmatig maken van het systeem of het herstellen van fouttesten.  

### <a name="testing"></a>Testen
DevTest Labs-omgevingen maken het mogelijk om specifieke code en configuraties asynchroon te testen. De gangbare praktijk is het opzetten van de omgeving met de code van de individuele pull aanvraag en start een geautomatiseerde testen. Zodra de geautomatiseerde tests zijn voltooid, kunnen handmatige tests worden uitgevoerd tegen de specifieke omgeving. Meestal wordt dit proces gedaan als onderdeel van de CI / CD-pijplijn. 

## <a name="management-experience"></a>Management ervaring

### <a name="cost-tracking"></a>Kostenbijhouden
De functie kostenbijhouden bevat Azure-resources binnen de verschillende omgevingen als onderdeel van de algemene kostentrend. De kosten per resources breken niet uit de verschillende bronnen binnen de omgeving, maar geeft de omgeving weer als een enkele kostenpost.

### <a name="security"></a>Beveiliging
Een goed geconfigureerd Azure-abonnement met DevTest Labs kan [de toegang tot Azure-bronnen alleen via het lab beperken.](devtest-lab-add-devtest-user.md) Met omgevingen kan een labeigenaar gebruikers toegang geven tot PaaS-bronnen met goedgekeurde configuraties zonder toegang te verlenen tot andere Azure-bronnen. In het scenario waarin labgebruikers omgevingen aanpassen, kan de eigenaar van het lab toegang tot de inzender toestaan. Met de toegang tot de bijdrager kan de labgebruiker azure-bron alleen toevoegen of verwijderen binnen de beheerde brongroep. Het maakt het eenvoudiger bijhouden en beheren ten opzichte van de gebruiker inzender toegang tot het abonnement.

### <a name="automation"></a>Automation
Automatisering is een belangrijk onderdeel voor een grootschalig, effectief ecosysteem. Automatisering is nodig voor het beheren of bijhouden van meerdere omgevingen in abonnementen en laboratoria.

### <a name="cicd-pipeline"></a>CI/CD-pijplijn
PaaS-services in DevTest Labs kunnen helpen bij het verbeteren van de CI/CD-pijplijn door gerichte implementaties te hebben waarbij de toegang wordt gecontroleerd door het lab.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor meer informatie over omgevingen: 

- 
- [Multi-VM-omgevingen en PaaS-resources maken met Azure Resource Manager-sjablonen](devtest-lab-create-environment-from-arm.md)
- [Openbare omgevingen configureren en gebruiken in Azure DevTest Labs](devtest-lab-configure-use-public-environments.md)
- [Een omgeving maken met een op zichzelf staand ServiceFabric-cluster in Azure DevTest Labs](create-environment-service-fabric-cluster.md)
- [Een omgeving verbinden met het virtuele netwerk van uw lab in Azure DevTest Labs](connect-environment-lab-virtual-network.md)
- [Omgevingen integreren in uw Azure DevOps CI/CD-pijplijnen](integrate-environments-devops-pipeline.md)
 






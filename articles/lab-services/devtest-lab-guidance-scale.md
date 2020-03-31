---
title: Uw Azure DevTest Labs-infrastructuur opschalen
description: In dit artikel vindt u richtlijnen voor het opschalen van uw Azure DevTest Labs-infrastructuur.
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
ms.openlocfilehash: 3a48cef2210721bf7116b1c4ad1169779288f47d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644831"
---
# <a name="scale-up-your-azure-devtest-labs-infrastructure"></a>Uw Azure DevTest Labs-infrastructuur opschalen
Voorafgaand aan de implementatie van DevTest Labs op bedrijfsschaal, zijn er verschillende belangrijke beslissingspunten. Inzicht in deze beslissingspunten op een hoog niveau helpt een organisatie met ontwerpbeslissingen in de toekomst. Deze punten mogen echter niet voorkomen dat een organisatie een proof of concept start. De top drie gebieden voor de eerste scale-up planning zijn:

- Netwerken en beveiliging
- Abonnementstopologie
- Rollen en verantwoordelijkheden

## <a name="networking-and-security"></a>Netwerken en beveiliging
Netwerken en beveiliging zijn hoekstenen voor alle organisaties. Hoewel een bedrijfsbrede implementatie een veel diepere analyse vereist, zijn er een beperkt aantal vereisten om een proof of concept succesvol te realiseren. Een paar belangrijke aandachtsgebieden zijn:

- **Azure-abonnement** : als u DevTest Labs wilt implementeren, moet u toegang hebben tot een Azure-abonnement met de juiste rechten om resources te maken. Er zijn een aantal manieren om toegang te krijgen tot Azure-abonnementen, waaronder een Enterprise Agreement en Pay As You Go. Zie Azure voor het bedrijf [licenties voor meer](https://azure.microsoft.com/pricing/enterprise-agreement/)informatie over het verkrijgen van toegang tot een Azure-abonnement.
- **Toegang tot on-premises resources** : sommige organisaties hebben hun resources in DevTest Labs nodig om toegang te krijgen tot on-premises resources. Er is een beveiligde verbinding nodig van uw on-premises omgeving met Azure. Daarom is het belangrijk dat u een VPN- of Express Route-verbinding instelt/configureert voordat u aan de slag gaat. Zie [Overzicht virtuele netwerken](../virtual-network/virtual-networks-overview.md)voor meer informatie.
- **Aanvullende beveiligingsvereisten** - Andere beveiligingsvereisten, zoals machinebeleid, toegang tot openbare IP-adressen, verbinding maken met internet zijn scenario's die mogelijk moeten worden herzien voordat een proof of concept wordt geïmplementeerd. 

## <a name="subscription-topology"></a>Abonnementstopologie
Subscription Topology is een kritische ontwerpoverweging bij het implementeren van DevTest Labs in de Enterprise. Het is echter niet nodig om alle beslissingen te stollen tot nadat een proof of concept is voltooid. Bij de evaluatie van het aantal abonnementen dat nodig is voor een bedrijfsimplementatie, zijn er twee uitersten: 

- Eén abonnement voor de hele organisatie
- Abonnement per gebruiker

Vervolgens belichten we de pluspunten van elke aanpak.

### <a name="one-subscription"></a>Eén abonnement
Vaak is de aanpak van één abonnement niet beheersbaar in een grote onderneming. Het beperken van het aantal abonnementen biedt echter de volgende voordelen:

- **Kosten voor ondernemingen voorspellen.**  Budgettering wordt veel gemakkelijker in één abonnement omdat alle resources zich in één groep bevinden. Deze aanpak maakt eenvoudigere besluitvorming mogelijk over wanneer kostenbeheersingsmaatregelen op een bepaald moment in een factureringscyclus moeten worden genomen.
- **Beheerbaarheid** van VM's, artefacten, formules, netwerkconfiguratie, machtigingen, beleid, enz.
- **Netwerkinspanningen** worden sterk vereenvoudigd in één abonnement voor ondernemingen waar on-premises connectiviteit een vereiste is. Het aansluiten van virtuele netwerken tussen abonnementen (hub-spoke model) is vereist met extra abonnementen, waarvoor extra configuratie, beheer, IP-adresruimten, enz.
- **Teamsamenwerking** is eenvoudiger wanneer iedereen in hetzelfde abonnement werkt , bijvoorbeeld om een VM opnieuw toe te wijzen aan een collega, teamresources te delen, enz.

### <a name="subscription-per-user"></a>Abonnement per gebruiker
Een apart abonnement per gebruiker biedt gelijke kansen voor het alternatieve spectrum. De voordelen van het hebben van veel abonnementen zijn:

- **Azure-schaalquota** vormen geen belemmering voor acceptatie. Vanaf dit schrijven staat Azure bijvoorbeeld 200 opslagaccounts per abonnement toe. Er zijn operationele quota voor de meeste services in Azure (veel kunnen worden aangepast, sommige niet). In dit model van een abonnement per gebruiker is het hoogst onwaarschijnlijk dat de meeste quota worden bereikt. Zie [Azure-abonnements- en servicelimieten, quota en beperkingen](../azure-resource-manager/management/azure-subscription-service-limits.md)voor meer informatie over de huidige Azure-schalingsquota.
- **Terugboekingen** naar groepen of individuele ontwikkelaars worden veel gemakkelijker, waardoor organisaties de kosten kunnen verantwoorden met behulp van hun huidige model.
- **Eigendom & machtigingen** van de DevTest Labs-omgevingen zijn eenvoudig. U geeft ontwikkelaars toegang op abonnementsniveau en zij zijn 100% verantwoordelijk voor alles, inclusief de netwerkconfiguratie, het labbeleid en vm-beheer.

In de Enterprise kunnen er voldoende beperkingen zijn aan de uitersten van het spectrum. Daarom moet u mogelijk abonnementen instellen op een manier die in het midden van deze uitersten valt. Als een best practice, het doel van een organisatie moet zijn om het minimale aantal abonnementen te gebruiken als mogelijk met het oog op de dwingende functies die het totale aantal abonnementen te verhogen. Om te herhalen, abonnement topologie is van cruciaal belang voor een onderneming inzet van DevTest Labs, maar mag niet vertragen een proof of concept. Er zijn aanvullende details in het [artikel Governance](devtest-lab-guidance-governance-policy-compliance.md) over hoe te beslissen over abonnement en lab granulariteit in de organisatie.

## <a name="roles-and-responsibilities"></a>Rollen en verantwoordelijkheden
Een DevTest Labs proof of concept heeft drie primaire rollen met gedefinieerde verantwoordelijkheden - Eigenaar van een abonnement, Eigenaar Van DevTest Labs, DevTest Labs gebruiker, en optioneel een Bijdrager.

- **Eigenaar van een abonnement** : de eigenaar van het abonnement heeft rechten om een Azure-abonnement toe te dienen, waaronder het toewijzen van gebruikers, het beheren van beleid, het maken van & het beheren van netwerktopologie, het aanvragen van quotaverhogingen, enz. Zie voor meer informatie [dit artikel.](../role-based-access-control/rbac-and-directory-admin-roles.md)
- **DevTest Labs eigenaar** - De DevTest Labs eigenaar heeft volledige administratieve toegang tot het lab. Deze persoon is verantwoordelijk voor het toevoegen/verwijderen van gebruikers, het beheren van kosteninstellingen, algemene labinstellingen en andere vm/artefact-gebaseerde taken. Een lab eigenaar heeft ook alle rechten van een DevTest Labs Gebruiker.
- **DevTest Labs gebruiker** - De DevTest Labs gebruiker kan maken en consumeren van de virtuele machines in het lab. Deze personen hebben een aantal minimale administratieve mogelijkheden op VM's die ze maken (start/stop/delete/configureer hun VM's). De gebruikers kunnen vm's van andere gebruikers niet beheren.

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel in deze serie: [Orkestreren van de implementatie van Azure DevTest Labs](devtest-lab-guidance-orchestrate-implementation.md)
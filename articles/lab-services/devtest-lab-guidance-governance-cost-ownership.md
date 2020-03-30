---
title: Kosten en eigendom beheren in Azure DevTest Labs
description: In dit artikel vindt u informatie die u helpt te optimaliseren voor kosten en het eigendom in uw omgeving op één lijn te brengen.
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
ms.openlocfilehash: b82d338f85f1b43712296ac7f27bdad55f8f1919
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74561664"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---manage-cost-and-ownership"></a>Beheer van Azure DevTest Labs-infrastructuur - Kosten en eigendom beheren
Kosten en eigendom zijn primaire aandachtspunten wanneer u overweegt uw ontwikkel- en testomgevingen te bouwen. In deze sectie vindt u informatie die u helpt te optimaliseren voor kosten en het eigendom in uw omgeving op elkaar af te stemmen.

## <a name="optimize-for-cost"></a>Optimaliseren voor kosten

### <a name="question"></a>Vraag
Hoe kan ik optimaliseren voor kosten binnen mijn DevTest Labs-omgeving?

### <a name="answer"></a>Antwoord
Er zijn een aantal ingebouwde functies van DevTest Labs die u helpen te optimaliseren voor de kosten. Zie [kostenbeheer, drempelwaarden](devtest-lab-configure-cost-management.md) [en beleidsartikelen](devtest-lab-set-lab-policy.md) om activiteiten van uw gebruikers te beperken. 

Als u DevTest Labs gebruikt voor een ontwikkelings- en testworkload, u overwegen gebruik te maken van het [Enterprise Dev/Test Subscription Benefit](https://azure.microsoft.com/offers/ms-azr-0148p/), als onderdeel van uw Enterprise Agreement. Als u een Pay as you Go-klant bent, u de [Pay-as-you-go-DevTest-aanbieding](https://azure.microsoft.com/offers/ms-azr-0023p/)overwegen.

Deze aanpak biedt u tal van voordelen:

- Speciale lagere Dev/Test-tarieven voor virtuele Windows-machines, cloudservices, HDInsight, App Service en Logic Apps
- EA-tarieven (Great Enterprise Agreement) voor andere Azure-services
- Toegang tot exclusieve Dev/Test-afbeeldingen in de galerij, waaronder Windows 8.1 en Windows 10
 
Alleen actieve Visual Studio-abonnees (standaardabonnementen, jaarcloudabonnementen en maandelijkse cloudabonnementen) kunnen Azure-resources gebruiken die worden uitgevoerd binnen een zakelijk Dev/Test-abonnement. Eindgebruikers hebben echter toegang tot de toepassing om feedback te geven of acceptatietests uit te voeren. Het gebruik van resources binnen dit abonnement is beperkt tot het ontwikkelen en testen van toepassingen en er wordt geen uptime-garantie aangeboden.

Als u besluit om de DevTest-aanbieding te gebruiken, moet u er rekening mee houden dat dit voordeel uitsluitend is voor het ontwikkelen en testen van uw toepassingen. Het gebruik binnen het abonnement heeft geen sla met financiële ondersteuning, behalve voor het gebruik van Azure DevOps en HockeyApp.

## <a name="define-a-role-based-access-across-your-organization"></a>Een op rollen gebaseerde toegang definiëren voor uw hele organisatie
### <a name="question"></a>Vraag
Hoe definieer ik op rollen gebaseerdtoegangscontrole voor mijn DevTest Labs-omgevingen om ervoor te zorgen dat IT kan bepalen terwijl ontwikkelaars/tests hun werk kunnen doen? 

### <a name="answer"></a>Antwoord
Er is een breed patroon, maar de details zijn afhankelijk van uw organisatie.

Centrale IT moet alleen bezitten wat nodig is, en het project en de toepassingsteams in staat stellen om het vereiste niveau van controle te hebben. Meestal betekent dit dat centrale IT eigenaar is van het abonnement en de belangrijkste IT-functies zoals netwerkconfiguraties afhandelt. De set **eigenaren** voor een abonnement moet klein zijn. Deze eigenaren kunnen extra eigenaren nomineren wanneer dat nodig is, of beleid op abonnementsniveau toepassen, bijvoorbeeld "Geen openbaar IP".

Er kan een subset van gebruikers zijn die toegang nodig hebben voor een abonnement, zoals Tier1- of Tier 2-ondersteuning. In dit geval raden we u aan deze gebruikers de toegang tot de **bijdrager** te geven, zodat ze de bronnen kunnen beheren, maar geen gebruikerstoegang kunnen bieden of beleid kunnen aanpassen.

De DevTest Labs-bron moet eigendom zijn van eigenaren die dicht bij het project/toepassingsteam staan. Het is omdat ze begrijpen hun eisen in termen van machines, en de vereiste software. In de meeste organisaties, de eigenaar van deze DevTest Labs bron is meestal het project / ontwikkeling leiden. Deze eigenaar kan gebruikers en beleid binnen de labomgeving beheren en kan alle VM's beheren in de DevTest Labs-omgeving.

De leden van het project/applicatieteam moeten worden toegevoegd aan de rol DevTest Labs-gebruikers. Deze gebruikers kunnen virtuele machines maken (in lijn met het beleid op lab- en abonnementsniveau). Ze kunnen ook hun eigen virtuele machines beheren. Ze kunnen geen virtuele machines beheren die van andere gebruikers zijn.

Zie azure [enterprise scaffold - prescriptieve documentatie over abonnementsbeheer](/azure/architecture/cloud-adoption/appendix/azure-scaffold) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen
Zie [Bedrijfsbeleid en compliance](devtest-lab-guidance-governance-policy-compliance.md).

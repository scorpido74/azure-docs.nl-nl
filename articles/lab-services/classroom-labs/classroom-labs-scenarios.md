---
title: Klassikale Labs gebruiken voor trainingen-Azure Lab Services
description: In dit artikel wordt beschreven hoe u Azure DevTest Labs gebruikt voor het maken van Labs op Azure voor trainings scenario's.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76717981"
---
# <a name="use-classroom-labs-for-trainings"></a>Klassikale Labs gebruiken voor trainingen
U kunt een Lab instellen voor trainingen. Met de klassikale Labs van Azure Lab Services kunt u een lab maken voor uw training, waar elke getrainde gebruikmaakt van identieke en geïsoleerde omgevingen voor training. U kunt beleids regels Toep assen om ervoor te zorgen dat de trainings omgevingen alleen beschikbaar zijn voor elke getrainde wanneer ze deze nodig hebben en voldoende resources bevatten, zoals virtuele machines, die vereist zijn voor de training. 

![Klas Lab](../media/classroom-labs-scenarios/classroom.png)

Klassikale Labs voldoet aan de volgende vereisten die nodig zijn voor het uitvoeren van training in elke virtuele omgeving: 

- Trainees kunnen snel hun trainings omgevingen inrichten
- Elke trainings machine moet identiek zijn
- Trainees kan geen Vm's zien die zijn gemaakt door andere trainees
- Beheer kosten door ervoor te zorgen dat trainees niet meer Vm's kan ophalen dan nodig is voor de training en om ook Vm's te kunnen afsluiten wanneer ze deze niet gebruiken
- Deel het trainings Lab eenvoudig met elke getrainde
- Het trainings Lab opnieuw en opnieuw gebruiken

In dit artikel vindt u informatie over diverse Azure Lab Services functies die kunnen worden gebruikt om te voldoen aan de eerder beschreven trainings vereisten en gedetailleerde stappen die u kunt volgen om een Lab voor training in te stellen.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Het lab-account maken als beheerder van een Lab-account
De eerste stap bij het gebruik van Azure Lab Services is het maken van een Lab-account in de Azure Portal. Nadat een Lab-account beheerder het lab-account heeft gemaakt, voegt de beheerder gebruikers toe die Labs willen maken met de rol **Lab Creator** . De trainers maken Labs met virtuele machines voor studenten om oefeningen te doen voor de cursus die ze bespreken. Zie [Lab-account maken en beheren](how-to-manage-lab-accounts.md)voor meer informatie.

## <a name="create-and-manage-classroom-labs"></a>Leslokaallabs maken en beheren
Een trainer, die lid is van de rol Lab Creator in een Lab-account, kan een of meer Labs maken in het lab-account. U maakt en configureert een sjabloon-VM met alle vereiste software voor het uitvoeren van oefeningen in uw cursus. U kiest een kant-en-klare installatie kopie van de beschik bare installatie kopieën voor het maken van een leslokaal Lab en past deze vervolgens aan door de software te installeren die vereist is voor het lab. Zie voor meer informatie [klassikale Labs maken en beheren](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Instellingen en beleids regels voor het gebruik configureren
De maker van het Lab kan gebruikers toevoegen aan of verwijderen uit het lab, registratie koppeling ophalen om te verzenden naar Lab-gebruikers, beleids regels instellen, zoals het instellen van afzonderlijke quota's per gebruiker, het aantal Vm's dat beschikbaar is in het lab bijwerken, en meer. Zie [gebruiks instellingen en-beleid configureren](how-to-configure-student-usage.md)voor meer informatie.

## <a name="create-and-manage-schedules"></a>Schema's maken en beheren
Met planningen kunt u een leslokaal Lab zodanig configureren dat de virtuele machines in het lab automatisch worden gestart en op een opgegeven tijdstip worden afgesloten. U kunt een eenmalig schema of een terugkerende planning definiëren. Zie [schema's voor klassikale Labs maken en beheren](how-to-create-schedules.md)voor meer informatie.

## <a name="set-up-and-publish-a-template-vm"></a>Een sjabloon-VM instellen en publiceren
Een sjabloon in een lab is een basiskopie van de virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Stel de VM van de sjabloon zo in dat deze is geconfigureerd met precies wat u wilt bieden aan de deel nemers aan de training. U kunt een naam en een beschrijving opgeven voor de sjabloon die de gebruikers van het lab kunnen zien. Vervolgens publiceert u de sjabloon om exemplaren van de sjabloon-VM beschikbaar te maken voor uw test gebruikers. Wanneer u een sjabloon publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Het aantal virtuele machines dat in dit proces wordt gemaakt, is hetzelfde als het maximum aantal gebruikers dat in het lab is toegestaan. Dit kunt u instellen in het gebruiksbeleid van het lab. Alle virtuele machines hebben dezelfde configuratie als de sjabloon. Zie [virtuele machines van de sjabloon instellen en publiceren](how-to-create-manage-template.md)voor meer informatie. 

## <a name="use-vms-in-the-classroom-lab"></a>Vm's gebruiken in het klas lokaal Lab
Een student of trainingen meldt zich bij het lab en maakt verbinding met de virtuele machine voor de cursus. Zie [toegang tot een leslokaal Lab](how-to-use-classroom-lab.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Begin met het maken van een Lab-account in klassikale Labs door de volgende instructies te volgen in het artikel: [zelf studie: een Lab-account instellen met Azure Lab Services](tutorial-setup-lab-account.md).
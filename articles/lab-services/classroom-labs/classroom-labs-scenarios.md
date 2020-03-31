---
title: Classroom Labs gebruiken voor trainingen - Azure Lab Services
description: In dit artikel wordt beschreven hoe u Azure DevTest Labs gebruiken voor het maken van labs op Azure voor trainingsscenario's.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717981"
---
# <a name="use-classroom-labs-for-trainings"></a>Classroom Labs gebruiken voor trainingen
Je een lab opzetten voor trainingen. Met Classroom Labs van Azure Lab Services u een lab maken voor uw training waarin elke cursist identieke en geïsoleerde omgevingen gebruikt voor training. U beleid toepassen om ervoor te zorgen dat trainingsomgevingen alleen beschikbaar zijn voor elke cursist wanneer deze nodig zijn en voldoende resources bevatten - zoals virtuele machines - die nodig zijn voor de training. 

![Klaslokaal lab](../media/classroom-labs-scenarios/classroom.png)

Classroom Labs voldoet aan de volgende vereisten die nodig zijn om trainingen uit te voeren in elke virtuele omgeving: 

- Stagiairs kunnen snel hun trainingsomgeving en
- Elke trainingsmachine moet identiek zijn
- Stagiairs kunnen vm's die door andere stagiairs zijn gemaakt niet zien
- Beheers de kosten door ervoor te zorgen dat stagiairs niet meer VM's kunnen krijgen dan ze nodig hebben voor de training en ook vm's kunnen afsluiten wanneer ze ze niet gebruiken
- Deel het trainingslab eenvoudig met elke cursist
- Hergebruik van het trainingslab steeds weer

In dit artikel vindt u meer informatie over verschillende Azure Lab Services-functies die kunnen worden gebruikt om te voldoen aan de eerder beschreven trainingsvereisten en gedetailleerde stappen die u volgen om een lab voor training in te stellen.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Het labaccount maken als beheerder van een labaccount
De eerste stap in het gebruik van Azure Lab Services is het maken van een labaccount in de Azure-portal. Nadat een labaccountbeheerder het labaccount heeft gemaakt, voegt de beheerder gebruikers toe die labs willen maken aan de **Lab Creator-rol.** De trainers maken labs met virtuele machines voor studenten om oefeningen te doen voor de cursus die ze onderwijzen. Zie [Labaccount maken en beheren](how-to-manage-lab-accounts.md)voor meer informatie.

## <a name="create-and-manage-classroom-labs"></a>Leslokaallabs maken en beheren
Een trainer, die lid is van de Lab Creator-rol in een labaccount, kan een of meer labs in het labaccount aanmaken. U maakt en configureert een sjabloon-VM met alle benodigde software voor het uitvoeren van oefeningen in uw cursus. U kiest een kant-en-klare afbeelding uit de beschikbare afbeeldingen voor het maken van een klaslokaallab en past deze vervolgens aan door de software te installeren die nodig is voor het lab. Zie [Klaslokalen maken en beheren](how-to-manage-classroom-labs.md)voor meer informatie.

## <a name="configure-usage-settings-and-policies"></a>Gebruiksinstellingen en -beleid configureren
De maker van het lab kan gebruikers toevoegen of verwijderen naar het lab, registratiekoppeling ophalen om naar labgebruikers te sturen, beleid instellen, zoals het instellen van individuele quota per gebruiker, het aantal VM's bijwerken dat beschikbaar is in het lab en meer. Zie [Gebruiksinstellingen en -beleid configureren](how-to-configure-student-usage.md)voor meer informatie.

## <a name="create-and-manage-schedules"></a>Schema's maken en beheren
Met schema's u een klaslokaallab zo configureren dat VM's in het lab automatisch op een bepaald tijdstip worden gestart en afgesloten. U een eenmalig schema of een terugkerend schema definiëren. Zie [Schema's maken en beheren voor klaslokalen voor](how-to-create-schedules.md)meer informatie.

## <a name="set-up-and-publish-a-template-vm"></a>Een sjabloon-vm instellen en publiceren
Een sjabloon in een lab is een basiskopie van de virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Stel de sjabloon-VM zo in dat deze is geconfigureerd met precies wat u aan de deelnemers aan de training wilt bieden. U kunt een naam en een beschrijving opgeven voor de sjabloon die de gebruikers van het lab kunnen zien. Vervolgens publiceert u de sjabloon om exemplaren van de sjabloon-VM beschikbaar te maken voor uw labgebruikers. Wanneer u een sjabloon publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Het aantal virtuele machines dat in dit proces wordt gemaakt, is hetzelfde als het maximum aantal gebruikers dat in het lab is toegestaan. Dit kunt u instellen in het gebruiksbeleid van het lab. Alle virtuele machines hebben dezelfde configuratie als de sjabloon. Zie Virtuele [machines voor](how-to-create-manage-template.md)sjablonen instellen en publiceren voor meer informatie. 

## <a name="use-vms-in-the-classroom-lab"></a>VM's gebruiken in het laboratorium in de klas
Een student of opleidingsdeelnemer meldt zich bij het lab en maakt verbinding met de VM om oefeningen voor de cursus te doen. Zie Hoe u [toegang krijgt tot een klaslokaallab](how-to-use-classroom-lab.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
Begin met het maken van een labaccount in Classroom Labs door instructies in het artikel te volgen: [Zelfstudie: Een labaccount instellen met Azure Lab Services.](tutorial-setup-lab-account.md)
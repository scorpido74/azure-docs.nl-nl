---
title: Instellingen voor automatisch starten configureren voor een vm in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het configureren van automatische start-instellingen voor VM's in een lab. Met deze instelling kunnen VM's in het lab automatisch worden gestart volgens een schema.
services: devtest-lab,lab-services
documentationcenter: na
author: spelluru
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: 95f810ba16f358c5aabc35e26294cdb3f8c3cca0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74807912"
---
# <a name="auto-startup-lab-virtual-machines"></a>Automatisch opstarten lab virtuele machines  
Met Azure DevTest Labs u virtuele machines in uw lab configureren om automatisch te worden gestart en af te sluiten op basis van een planning. Zie Beleid voor automatisch afsluiten [beheren voor een lab in Azure DevTest Labs voor](devtest-lab-auto-shutdown.md)informatie over het configureren van instellingen voor automatisch afsluiten. 

In tegenstelling tot autoshutdown, waarbij alle VM's zijn opgenomen wanneer het beleid is ingeschakeld, vereist het autostartbeleid dat een labgebruiker expliciet een VM selecteert en zich aandit schema aangeeft. Op die manier loopt u niet gemakkelijk tegen de situatie aan waarin ongewenste VM's per ongeluk automatisch worden gestart en onverwachte uitgaven veroorzaken.

In dit artikel ziet u hoe u het beleid voor automatisch starten voor een lab configureert.

## <a name="configure-autostart-settings-for-a-lab"></a>Instellingen voor automatisch starten configureren voor een lab 
1. Navigeer naar de startpagina voor uw lab. 
2. Selecteer **Configuratie en beleid** in het linkermenu. 

    ![Menu Configuratie en beleid](./media/devtest-lab-auto-startup-vm/configuration-policies-menu.png)
3. Ga op de pagina **Configuratie en beleid** de volgende stappen uit:
    
    1. Selecteer **Aan** voor **Toestaan dat virtuele machines automatisch worden gepland om** de functie automatisch starten voor dit lab in te schakelen. 
    2. Selecteer een begintijd (bijvoorbeeld: 08:00:00 uur) voor het **startveld Planning.** 
    3. Selecteer een **tijdzone** die moet worden gebruikt. 
    4. Selecteer **dagen van de week** waarop VM's automatisch moeten worden gestart. 
    5. Selecteer vervolgens **Opslaan** op de werkbalk om de instellingen op te slaan. 

        ![Instellingen voor automatisch starten](./media/devtest-lab-auto-startup-vm/auto-start-configuration.png)

        > [!IMPORTANT]
        > Dit beleid is niet automatisch automatisch van toepassing op virtuele machines in het lab. Als **u zich wilt aanmelden voor** afzonderlijke virtuele machines, gaat u naar de pagina met virtuele machines en schakelt u automatisch **starten** in voor die vm.

## <a name="enable-autostart-for-a-vm-in-the-lab"></a>Automatisch starten inschakelen voor een vm in het lab
De volgende procedure biedt u stappen voor het kiezen in een VM in het autostartbeleid van het lab. 

1. Selecteer op de startpagina van uw lab de **VM** in de lijst **Mijn virtuele machines.** 

    ![Menu Configuratie en beleid](./media/devtest-lab-auto-startup-vm/select-vm.png)
2. Selecteer automatisch **starten** op de pagina **Virtuele machine** in het linkermenu of in de lijst **Schema's.** 

    ![Menu Automatisch start menu selecteren](./media/devtest-lab-auto-startup-vm/select-auto-start.png)
3. Selecteer **op** de pagina Automatisch starten **de** optie Aan voor de **optie Toestaan dat deze virtuele machine wordt gepland voor automatisch starten.**

    ![Automatisch starten voor de VM inschakelen](./media/devtest-lab-auto-startup-vm/auto-start-vm.png)
4. Selecteer vervolgens **Opslaan** op de werkbalk om de instelling op te slaan. 


## <a name="next-steps"></a>Volgende stappen
Zie Beleid voor automatisch afsluiten van [een lab beheren voor een lab in Azure DevTest Labs](devtest-lab-auto-shutdown.md) voor meer informatie over het beleid voor het afsluiten van configuratieauto's voor een lab

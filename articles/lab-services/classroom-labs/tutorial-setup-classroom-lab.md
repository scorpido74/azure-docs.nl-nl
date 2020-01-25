---
title: Opzetten van een leslokaallab met Azure Lab Services | Microsoft Docs
description: In deze zelf studie gebruikt u Azure Lab Services voor het instellen van een leslokaal Lab met virtuele machines die door studenten in uw klasse worden gebruikt.
services: devtest-lab, lab-services, virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 2c28375ce7252e93340f395b97224d292940ce65
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76719174"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Zelfstudie: Een leslokaallab instellen 
In deze zelfstudie stelt u een leslokaallab in met virtuele machines die worden gebruikt door studenten in het leslokaal.  

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een leslokaallab maken
> * Gebruikers toevoegen aan het lab
> * Schema instellen voor het lab
> * Uitnodiging e-mail naar studenten verzenden

## <a name="prerequisites"></a>Vereisten
Als u een leslokaal Lab in een Lab-account wilt instellen, moet u lid zijn van een van deze rollen in het lab-account: eigenaar, Lab-maker of Inzender. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan de eigenaarsrol.

Een labeigenaar kan andere gebruikers toevoegen aan de rol **Labmaker**. Zo kan een labeigenaar bijvoorbeeld professoren toevoegen aan de rol Labmaker. Vervolgens maken de professoren labs met VM’s voor hun lessen. Studenten gebruiken de registratiekoppeling die ze ontvangen van professoren om zich te registreren bij het lab. Wanneer ze zijn geregistreerd, kunnen ze VM’s in de labs gebruiken voor het werk in de les en voor hun huiswerk. Zie voor gedetailleerde stappen voor het toevoegen van gebruikers aan de rol Labmaker [Een gebruiker toevoegen aan de rol Labmaker](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).


## <a name="create-a-classroom-lab"></a>Een leslokaallab maken

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Houd er rekening mee dat Internet Explorer 11 nog niet wordt ondersteund. 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Selecteer **Nieuw Lab**. 
    
    ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** op voor uw Lab en selecteer **volgende**.  

        ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-window.png)

        Als u een Linux-installatie kopie selecteert, ziet u een optie om verbinding met extern bureau blad in te scha kelen. Zie [verbinding met extern bureau blad inschakelen voor Linux](how-to-enable-remote-desktop-linux.md)voor meer informatie.
    2. Geef op de pagina **referenties voor virtuele machine** standaard referenties op voor alle virtuele machines in het lab. Geef de **naam** en het **wacht woord** voor de gebruiker op en selecteer **volgende**.  

        ![Nieuw Lab-venster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Voer op de pagina **Lab-beleid** het aantal uren in dat is toegewezen aan elke gebruiker (**quotum voor elke gebruiker**) buiten de geplande tijd voor het lab en selecteer vervolgens **volt ooien**. 

        ![Quotum voor elke gebruiker](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Het volgende scherm wordt weer gegeven met de status van het maken van een VM-sjabloon. Het maken van de sjabloon in het lab duurt maximaal 20 minuten. 

    ![Status van het maken van de sjabloon-VM](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Voer de volgende stappen uit op de pagina **sjabloon** : deze stappen zijn **optioneel** voor de zelf studie.

    2. De sjabloon-VM verbinden door **Verbinding maken** te selecteren. Als het een Linux-sjabloon-VM is, kiest u of u verbinding wilt maken via SSH of RDP (als RDP is ingeschakeld).
    1. Selecteer **wacht woord opnieuw instellen** om het wacht woord voor de virtuele machine opnieuw in te stellen. 
    1. Software installeren en configureren op uw sjabloon-VM. 
    1. **Stop** de virtuele machine.  
    1. Een **Beschrijving** voor de sjabloon invoeren
10. Selecteer op de pagina **sjabloon** de optie **publiceren** op de werk balk. 

    ![De knop sjabloon publiceren](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
8. Op de pagina **publicatie sjabloon** voert u het aantal virtuele machines in dat u wilt maken in het lab en selecteert u vervolgens **publiceren**. 

    ![Publicatie sjabloon-aantal Vm's](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. U ziet de **status van het publiceren van** de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Ga naar de pagina **pool van virtuele machines** door virtuele machines te selecteren in het linkermenu of door de tegel virtuele machines te selecteren. Controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

    U voert de volgende taken uit op deze pagina (Voer deze stappen niet uit voor de zelf studie. Deze stappen zijn alleen ter informatie.): 
    
    1. Als u de lab-capaciteit (aantal Vm's in het lab) wilt wijzigen, selecteert u **lab-capaciteit** op de werk balk.
    2. Als u alle Vm's tegelijk wilt starten, selecteert u **Alles starten** op de werk balk. 
    3. Als u een specifieke virtuele machine wilt starten, selecteert u de pijl-omlaag in de **status**en selecteert u vervolgens **starten**. U kunt ook een virtuele machine starten door een virtuele machine in de eerste kolom te selecteren en vervolgens te klikken op **Start** op de werk balk.

    Raadpleeg de volgende artikelen voor meer informatie over het maken en beheren van sjablonen en het instellen en beheren van virtuele machines van studenten: 
    
    - [Klassikale Lab-sjablonen maken en beheren](how-to-create-manage-template.md)
    - [Virtuele-machine groep instellen en beheren](how-to-set-virtual-machine-passwords.md)

## <a name="add-users-to-the-lab"></a>Gebruikers toevoegen aan het lab

1. Selecteer **Gebruikers** in het menu links. De optie **Toegang beperken** is standaard ingeschakeld. Wanneer deze instelling is ingeschakeld, kan een gebruiker zich niet registreren bij het lab, zelfs niet als deze de registratiekoppeling heeft, tenzij de gebruiker in de lijst met gebruikers staat. Alleen gebruikers in de lijst kunnen zich registreren bij het lab door de registratiekoppeling te gebruiken die u verzendt. In deze procedure kunt u gebruikers toevoegen aan de lijst. U kunt ook **Toegang beperken** uitschakelen, zodat gebruikers zich bij het lab kunnen registreren zolang ze de registratiekoppeling hebben. 
2. Selecteer **gebruikers toevoegen** op de werk balk en selecteer vervolgens **toevoegen per e-mail adres**. 

    ![Knop Gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-button.png)
1. Op de pagina **Gebruikers toevoegen** voert u e-mailadressen van gebruikers in op afzonderlijke regels of op één regel gescheiden door puntkomma's. 

    ![E-mailadressen van gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecteer **Opslaan**. U ziet de e-mailadressen van gebruikers en hun status (al dan niet geregistreerd) in de lijst. 

    ![Lijst met gebruikers](../media/how-to-configure-student-usage/users-list-new.png)

    U ziet de namen van de gebruikers in de lijst nadat deze zijn geregistreerd bij het lab. 
    
## <a name="set-a-schedule-for-the-lab"></a>Een planning voor het lab instellen
Maak een geplande gebeurtenis voor het Lab zodat Vm's in het lab automatisch worden gestart/gestopt op specifieke tijdstippen. Het gebruikers quotum dat u eerder hebt opgegeven, is de extra tijd die wordt toegewezen aan elke gebruiker buiten dit geplande tijdstip. 

1. Ga naar de pagina **schema's** en selecteer een **geplande gebeurtenis toevoegen** op de werk balk. 

    ![Knop schema toevoegen op de pagina planningen](../media/how-to-create-schedules/add-schedule-button.png)
2. Voer op de pagina **geplande gebeurtenis toevoegen** de volgende stappen uit:
    1. Bevestig dat de **standaard** is geselecteerd het **gebeurtenis type**.  
    2. Geef de **begin datum** voor de klasse op. 
    4. Geef de **begin tijd** op waarop u de vm's wilt starten.
    5. Geef de **eind tijd** op waarop de vm's moeten worden afgesloten. 
    6. Geef de **tijd zone** op voor de start-en stop tijden die u hebt opgegeven. 
3. Selecteer op dezelfde pagina **geplande gebeurtenis toevoegen** het huidige schema in de sectie **herhalen** .  

    ![Knop schema toevoegen op de pagina planningen](../media/how-to-create-schedules/select-current-schedule.png)
5. In het dialoog venster **herhalen** voert u de volgende stappen uit:
    1. Bevestig dat **elke week** is ingesteld voor het veld **repeat** . 
    2. Selecteer de dagen waarop u de planning wilt laten ingaan. In het volgende voor beeld wordt maandag t/m vrijdag geselecteerd. 
    3. Selecteer een **eind datum** voor de planning.
    8. Selecteer **Opslaan**. 

        ![Herhalings schema instellen](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Voer nu op de pagina **geplande gebeurtenis toevoegen** voor **notities (optioneel)** een beschrijving of notities in voor de planning. 
4. Selecteer op de pagina **geplande gebeurtenis toevoegen** de optie **Opslaan**. 

    ![Wekelijks schema](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Ga naar de begin datum in de agenda om te controleren of het schema is ingesteld.
    
    ![Plannen in de agenda](../media/how-to-create-schedules/schedule-calendar.png)

    Zie voor meer informatie over het maken en beheren van planningen voor een klasse [schema maken en beheren voor klassikale Labs](how-to-create-schedules.md).

## <a name="send-invitation-emails-to-students"></a>E-mail berichten verzenden naar studenten

1. Schakel over naar de weer gave **gebruikers** als u zich nog niet op de pagina bevindt en selecteer **Alles uitnodigen** op de werk balk. 

    ![Studenten selecteren](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Voer op de pagina **uitnodiging per E-mail verzenden** een optioneel bericht in en selecteer vervolgens **verzenden**. Het e-mail bericht bevat automatisch de registratie koppeling. U kunt deze registratie koppeling ophalen door te selecteren **... (weglatings tekens)** op de werk balk en **registratie koppeling**. 

    ![Registratie koppeling via e-mail verzenden](../media/tutorial-setup-classroom-lab/send-email.png)
4. U ziet de status van de **uitnodiging** in de lijst met **gebruikers** . De status wordt gewijzigd in **verzenden** en vervolgens **verzonden op &lt;datum&gt;** . 

    Zie [How to configure student Usage](how-to-configure-student-usage.md)(Engelstalig) voor meer informatie over het toevoegen van studenten aan een klasse en het beheren van hun gebruik van het lab.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een leslokaallab gemaakt en het lab geconfigureerd. Ga voor meer informatie over hoe een student toegang kan krijgen tot een virtuele machine in het lab met behulp van de registratiekoppeling naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Verbinding maken met een virtuele machine in het leslokaallab](tutorial-connect-virtual-machine-classroom-lab.md)


---
title: Opzetten van een leslokaallab met Azure Lab Services | Microsoft Docs
description: In deze zelfstudie gebruikt u Azure Lab Services om een klaslokaallab in te stellen met virtuele machines die worden gebruikt door studenten in uw klas.
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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: 166ec4db2a2891d25a1e80526f8c1bd9770f9eef
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77592217"
---
# <a name="tutorial-set-up-a-classroom-lab"></a>Zelfstudie: Een leslokaallab instellen 
In deze zelfstudie stelt u een leslokaallab in met virtuele machines die worden gebruikt door studenten in het leslokaal.  

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Een leslokaallab maken
> * Gebruikers toevoegen aan het lab
> * Schema voor het lab instellen
> * Uitnodigingse-mail verzenden naar studenten

## <a name="prerequisites"></a>Vereisten
In deze zelfstudie zet u een lab op met virtuele machines voor uw klas. Als u een klaslokaallab wilt instellen in een labaccount, moet u lid zijn van een van deze rollen in het labaccount: eigenaar, labmaker of bijdrager. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan de eigenaarsrol. U dus het gebruikersaccount gebruiken waarmee u een labaccount hebt gemaakt om een klaslokaallab te maken. 

Hier volgt de typische werkstroom bij het gebruik van Azure Lab Services:

1. Een maker van een labaccount voegt andere gebruikers toe aan de **Lab Creator-rol.** De maker/beheerder van het labaccount voegt bijvoorbeeld professoren toe aan de **Lab Creator-rol,** zodat ze labs voor hun klassen kunnen maken. 
2. Vervolgens maken de professoren labs met VM's voor hun lessen en sturen registratielinks naar studenten in de klas. 
3. Studenten gebruiken de registratiekoppeling die ze ontvangen van professoren om zich te registreren bij het lab. Wanneer ze zijn geregistreerd, kunnen ze VM’s in de labs gebruiken voor het werk in de les en voor hun huiswerk. 

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken
In deze stap maakt u een lab voor uw klas in Azure. 

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Internet Explorer 11 wordt nog niet ondersteund. 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Selecteer **Nieuw lab**. 
    
    ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** op voor uw lab en selecteer **Volgende**.  

        ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-window.png)
    2. Geef op de pagina **Standaardreferenties** voor virtuele machine op voor alle VM's in het lab. Geef de **naam** en het **wachtwoord** voor de gebruiker op en selecteer **Volgende**.  

        ![Nieuw laboratoriumvenster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Selecteer Op de pagina **Lab-beleid** de optie **Voltooien**. 

        ![Quotum voor elke gebruiker](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. U ziet het volgende scherm met de status van het maken van de sjabloon-vm. Deze bewerking duurt maximaal 20 minuten. 

    ![Status van het maken van de sjabloon-vm](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
8. Voer op de pagina **Sjabloon** de volgende stappen uit: de volgende stappen zijn **optioneel** voor de zelfstudie.

    1. De sjabloon-VM verbinden door **Verbinding maken** te selecteren. Als het een VM voor Linux-sjablonen is, kiest u of u verbinding wilt maken met SSH of RDP (als RDP is ingeschakeld).
    3. Installeer en configureer software die nodig is voor uw klas op de sjabloon-VM. 
    4. **De** sjabloon-VM stoppen.  

## <a name="publish-the-template-vm"></a>De sjabloon-VM publiceren
In deze stap publiceert u de sjabloon-VM. Wanneer u de sjabloon-VM publiceert, maakt Azure Lab Services VM's in het lab met behulp van de sjabloon. Alle virtuele machines hebben dezelfde configuratie als de sjabloon.

1. Selecteer **publiceren** op de werkbalk op de pagina **Sjabloon.** 

    ![Knop Sjabloon publiceren](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
2. Voer **op** de pagina Sjabloon publiceren het aantal virtuele machines in dat u in het lab wilt maken en selecteer **Vervolgens Publiceren**. 

    ![Sjabloon publiceren - aantal VM's](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
3. U ziet de **status van het publiceren van** de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
4. Wacht tot de publicatie is voltooid en schakel vervolgens over naar de **poolpagina virtuele machines** door **Virtuele machines** in het linkermenu te selecteren of door de tegel Virtuele **machines te** selecteren. Controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)   

    > [!NOTE]
    > Wanneer een docent een student-vm inschakelt, wordt het quotum voor de student niet beïnvloed. Quota voor een gebruiker geeft het aantal laburen op dat beschikbaar is voor de gebruiker buiten de geplande lestijd. Zie [Quota voor gebruikers instellen voor](how-to-configure-student-usage.md?#set-quotas-for-users)meer informatie over quota.

## <a name="set-a-schedule-for-the-lab"></a>Stel een schema voor het lab
Maak een geplande gebeurtenis voor het lab, zodat VM's in het lab automatisch worden gestart/gestopt op specifieke tijdstippen. Het gebruikersquotum (standaard: 10 uur) dat u eerder hebt opgegeven, is de extra tijd die buiten deze geplande tijd aan elke gebruiker is toegewezen. 

1. Ga naar de pagina **Schema's** en selecteer **Geplande gebeurtenis toevoegen** op de werkbalk. 

    ![Knop Planning toevoegen op de pagina Schema's](../media/how-to-create-schedules/add-schedule-button.png)
2. Ga op de pagina **Geplande gebeurtenis toevoegen** de volgende stappen uit:
    1. Controleer of **Standaard** is geselecteerd op **het gebeurtenistype**.  
    2. Selecteer de **begindatum** voor de klasse. 
    4. Selecteer de **begintijd** waarop u de VM's wilt starten.
    5. Selecteer de **stoptijd** waarop de VM's moeten worden afgesloten. 
    6. Selecteer de **tijdzone** voor de opgegeven begin- en stoptijden. 
3. Selecteer op dezelfde pagina **Geplande gebeurtenis toevoegen** het huidige schema in de sectie **Herhalen.**  

    ![Knop Planning toevoegen op de pagina Schema's](../media/how-to-create-schedules/select-current-schedule.png)
5. Ga in het dialoogvenster **Herhalen** de volgende stappen uit:
    1. Controleer of **elke week** is ingesteld voor het veld **Herhalen.** 
    2. Selecteer de dagen waarop u het schema wilt laten werken. In het volgende voorbeeld wordt maandag-vrijdag geselecteerd. 
    3. Selecteer een **einddatum** voor de planning.
    8. Selecteer **Opslaan**. 

        ![Herhaalschema instellen](../media/how-to-create-schedules/set-repeat-schedule.png)

3. Voer nu op de pagina **Geplande gebeurtenis toevoegen** voor Notities **(optioneel)** een beschrijving of notities voor het schema in. 
4. Selecteer **Op** de pagina Geplande gebeurtenis toevoegen de optie **Opslaan**. 

    ![Weekschema](../media/how-to-create-schedules/add-schedule-page-weekly.png)
5. Navigeer naar de begindatum in de agenda om te controleren of de planning is ingesteld.
    
    ![Planning in de agenda](../media/how-to-create-schedules/schedule-calendar.png)

    Zie [Planning maken en beheren voor klaslokalen voor](how-to-create-schedules.md)meer informatie over het maken en beheren van schema's voor een klas.


## <a name="add-users-to-the-lab"></a>Gebruikers toevoegen aan het lab

1. Selecteer **Gebruikers** in het menu links. De optie **Toegang beperken** is standaard ingeschakeld. Wanneer deze instelling is ingeschakeld, kan een gebruiker zich niet registreren bij het lab, zelfs niet als deze de registratiekoppeling heeft, tenzij de gebruiker in de lijst met gebruikers staat. Alleen gebruikers in de lijst kunnen zich registreren bij het lab door de registratiekoppeling te gebruiken die u verzendt. In deze procedure kunt u gebruikers toevoegen aan de lijst. U kunt ook **Toegang beperken** uitschakelen, zodat gebruikers zich bij het lab kunnen registreren zolang ze de registratiekoppeling hebben. 
2. Selecteer **Gebruikers toevoegen** op de werkbalk en selecteer Toevoegen op **e-mailadres**. 

    ![Knop Gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-button.png)
1. Op de pagina **Gebruikers toevoegen** voert u e-mailadressen van gebruikers in op afzonderlijke regels of op één regel gescheiden door puntkomma's. 

    ![E-mailadressen van gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecteer **Opslaan**. U ziet de e-mailadressen van gebruikers en hun status (al dan niet geregistreerd) in de lijst. 

    ![Lijst met gebruikers](../media/how-to-configure-student-usage/users-list-new.png)

    U ziet namen van gebruikers in de lijst nadat ze zijn geregistreerd bij het lab. 
    

## <a name="send-invitation-emails-to-users"></a>E-mails van uitnodigingen verzenden naar gebruikers

1. Schakel over naar de weergave **Gebruikers** als u nog niet op de pagina bent en selecteer **Alles uitnodigen** op de werkbalk. 

    ![Studenten selecteren](../media/tutorial-setup-classroom-lab/invite-all-button.png)
1. Voer op de pagina **Uitnodiging verzenden per e-mail** een optioneel bericht in en selecteer **Verzenden**. De e-mail bevat automatisch de registratielink. U deze registratie link door het selecteren van **... (ellips)** op de werkbalk en **de koppeling Registratie**. 

    ![Registratiekoppeling verzenden via e-mail](../media/tutorial-setup-classroom-lab/send-email.png)
4. U ziet de status van **de uitnodiging** in de lijst **Gebruikers.** De status moet worden gewijzigd in **Verzenden** en vervolgens **naar Verzonden op &lt;datum&gt;**. 

    Zie [Hoe u het gebruik](how-to-configure-student-usage.md)van studenten configureren voor meer informatie over het toevoegen van studenten aan een klas en het beheren van hun gebruik van het lab.

## <a name="next-steps"></a>Volgende stappen
In deze zelfstudie hebt u een lab voor uw klas gemaakt in Azure. Ga voor meer informatie over hoe een student toegang kan krijgen tot een virtuele machine in het lab met behulp van de registratiekoppeling naar de volgende zelfstudie:

> [!div class="nextstepaction"]
> [Verbinding maken met een virtuele machine in het leslokaallab](tutorial-connect-virtual-machine-classroom-lab.md)


---
title: Gebruiks instellingen configureren in de klassikale Labs van Azure Lab Services | Microsoft Docs
description: Meer informatie over het configureren van het aantal gebruikers voor het lab, het ontvangen van de items die zijn geregistreerd bij het lab, het aantal uren bepalen dat ze de virtuele machine mogen gebruiken en nog veel meer.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 691907d1c221283f99ba59f0937cfbaf673f427a
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72324421"
---
# <a name="add-and-manage-lab-users"></a>Lab-gebruikers toevoegen en beheren
In dit artikel wordt beschreven hoe u gebruikers toevoegt aan het lab, ze aan het lab kunt toevoegen, het aantal uren bepaalt dat ze de virtuele machine kunnen gebruiken, en nog veel meer. 


## <a name="add-users-to-the-lab"></a>Gebruikers toevoegen aan het lab

1. Selecteer **Gebruikers** in het menu links. De optie **Toegang beperken** is standaard ingeschakeld. Wanneer deze instelling is ingeschakeld, kan een gebruiker zich niet registreren bij het lab, zelfs niet als deze de registratiekoppeling heeft, tenzij de gebruiker in de lijst met gebruikers staat. Alleen gebruikers in de lijst kunnen zich registreren bij het lab door de registratiekoppeling te gebruiken die u verzendt. In deze procedure kunt u gebruikers toevoegen aan de lijst. U kunt ook **Toegang beperken** uitschakelen, zodat gebruikers zich bij het lab kunnen registreren zolang ze de registratiekoppeling hebben. 
2. Selecteer **gebruikers toevoegen** op de werk balk en selecteer vervolgens **toevoegen per e-mail adres**. 

    ![Knop Gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-button.png)
1. Op de pagina **Gebruikers toevoegen** voert u e-mailadressen van gebruikers in op afzonderlijke regels of op één regel gescheiden door puntkomma's. 

    ![E-mailadressen van gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecteer **Opslaan**. U ziet de e-mailadressen van gebruikers en hun status (al dan niet geregistreerd) in de lijst. 

    ![Lijst met gebruikers](../media/how-to-configure-student-usage/users-list-new.png)

### <a name="add-users-by-uploading-a-csv-file"></a>Gebruikers toevoegen door een CSV-bestand te uploaden
U kunt ook gebruikers toevoegen door een CSV-bestand te uploaden met e-mail adressen van gebruikers.

1. Maak een CSV-bestand met e-mail adressen van gebruikers in één kolom.

    ![CSV-bestand met gebruikers](../media/how-to-configure-student-usage/csv-file-with-users.png)
2. Selecteer op de pagina **gebruikers** van het lab de optie **gebruikers toevoegen** op de werk balk en selecteer **CSV**-bestand uploaden.

    ![CSV-knop uploaden](../media/how-to-configure-student-usage/upload-csv-button.png)
3. Selecteer het CSV-bestand met gebruikers-e-mail adressen. Wanneer u **openen** selecteert nadat u het CSV-bestand hebt geselecteerd, ziet u het volgende **gebruikers venster toevoegen** . De e-mailadres lijst is gevuld met e-mail adressen uit het CSV-bestand. 

    ![Venster gebruikers toevoegen ingevuld met e-mail adressen uit CSV-bestand](../media/how-to-configure-student-usage/add-users-window.png)
4. Selecteer **Opslaan** in het venster **gebruikers toevoegen** . 
5. Bevestig dat u gebruikers in de lijst met gebruikers ziet. 

    ![Lijst met toegevoegde gebruikers](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Uitnodigingen voor gebruikers verzenden
Als u de registratie koppeling naar studenten wilt verzenden, gebruikt u een van de volgende methoden. De eerste methode laat zien hoe u e-mail berichten naar studenten verzendt met de registratie koppeling en een optioneel bericht. In de tweede methode ziet u hoe u de registratie koppeling kunt ophalen die u op een wille keurige manier met anderen wilt delen. 

Als de **toegang beperken** voor het lab is ingeschakeld, kunnen alleen gebruikers in de lijst met gebruikers de registratie koppeling gebruiken om zich bij het lab te registreren. Deze optie is standaard ingeschakeld. 

### <a name="invite-all-users"></a>Alle gebruikers uitnodigen

1. Ga naar de pagina **gebruikers** van het lab. 
2. Selecteer **Alles uitnodigen** in de werk balk. 
3. Voer een **bericht** in voor de gebruikers. Dit is een optionele stap.
4. Selecteer vervolgens **verzenden**.

    ![Alle gebruikers uitnodigen](../media/how-to-configure-student-usage/invite-all.png)

    U ziet de status van deze bewerking in de kolom **uitnodigingen** van de lijst **gebruikers** . De uitnodigings-e-mail bevat de registratie koppeling die gebruikers kunnen gebruiken om zich bij het lab te registreren. 

### <a name="invite-selected-users"></a>Geselecteerde gebruikers uitnodigen

1. Selecteer een gebruiker of meerdere gebruikers in de lijst. 
2. Selecteer vervolgens het pictogram **envelop** dat u ziet in de geselecteerde rij (of) Selecteer **uitnodigen** op de werk balk. 

    ![Geselecteerde gebruikers uitnodigen](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Voer in het venster **uitnodiging per E-mail verzenden** een optioneel **bericht**in en selecteer vervolgens **verzenden**. 

    ![E-mail naar geselecteerde gebruikers verzenden](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    U ziet de status van deze bewerking in de kolom **uitnodigingen** van de lijst **gebruikers** . De uitnodigings-e-mail bevat de registratie koppeling die gebruikers kunnen gebruiken om zich bij het lab te registreren.

1. Schakel over naar de weergave **Gebruikers** als u nog niet op die pagina bent. 

## <a name="get-registration-link"></a>Registratie koppeling ophalen
U kunt ook de registratie koppeling vanuit de portal ophalen en deze verzenden met uw eigen e-mailclient toepassing. 

1. Schakel over naar de weer gave **gebruikers** door **gebruikers** te selecteren in het menu links. 
2. Selecteren **... (weglatings tekens)** op de werk balk en selecteer vervolgens **registratie koppeling**.

    ![Registratiekoppeling voor studenten](../media/how-to-configure-student-usage/registration-link-button.png)
1. Selecteer in het dialoog venster **gebruikers registratie** de knop **kopiëren** . De koppeling wordt naar het klembord gekopieerd. Plak deze in een e-mailprogramma en verstuur een e-mail naar de student. 

    ![Registratiekoppeling voor studenten](../media/how-to-configure-student-usage/registration-link.png)
2. Selecteer **gereed**in het dialoog venster **gebruikers registratie** . 
4. De **registratie koppeling** naar een student verzenden, zodat de student kan worden geregistreerd voor de klasse. 

## <a name="view-users-registered-with-the-lab"></a>Gebruikers weergeven die zijn geregistreerd bij het lab

Selecteer **gebruikers** in het linkermenu om de lijst met gebruikers weer te geven die zijn geregistreerd bij het lab. 

![Lijst met gebruikers die zijn geregistreerd bij het lab](../media/how-to-configure-student-usage/students-registered.png)

## <a name="set-quotas-for-users"></a>Quota instellen voor gebruikers
U kunt quota per gebruiker instellen met behulp van de volgende stappen: 

1. Selecteer **gebruikers** in het menu links als de pagina nog niet actief is. 
2. Selecteer **quotum per gebruiker: &lt;number @ no__t-2 uur** op de werk balk. 
3. Geef op de pagina **quotum per gebruiker** het aantal uren op dat u wilt toewijzen aan elke gebruiker (student) buiten de geplande klasse tijd en selecteer vervolgens **Opslaan**.

    ![Quotum per gebruiker](../media/how-to-configure-student-usage/quota-per-user.png)    
5. U ziet nu de gewijzigde waarden op de werk balk: **quotum per gebruiker: @no__t 1number uur @ no__t-2**. 

    ![Quotum per gebruiker-na](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > De [geplande uitvoerings tijd van vm's](how-to-create-schedules.md) telt niet op het quotum dat aan een gebruiker is toegewezen. Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt. 

## <a name="set-additional-quota-for-a-specific-user"></a>Extra quota instellen voor een specifieke gebruiker
U kunt een afzonderlijke quota voor een gebruiker instellen. Voer hiertoe de volgende stappen uit:

1. Selecteer een gebruiker (student) in de lijst met gebruikers op de pagina **gebruikers** .
2. Selecteer vervolgens **quota aanpassen** op de werk balk. 

    ![Knop quotum aanpassen](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Voer het aantal **extra uren** in voor de geselecteerde gebruiker of gebruikers en selecteer vervolgens **Toep assen**. 

    ![Aanvullend quotum voor een gebruiker](../media/how-to-configure-student-usage/additional-quota.png)
4. Het bijgewerkte gebruik van de gebruiker wordt weer geven in de kolom **gebruik** . 

    ![Nieuw gebruik voor de gebruiker](../media/how-to-configure-student-usage/new-usage-hours.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)

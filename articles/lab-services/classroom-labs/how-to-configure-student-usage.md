---
title: Gebruiks instellingen configureren in de klassikale Labs van Azure Lab Services
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
ms.date: 02/20/2020
ms.author: spelluru
ms.openlocfilehash: 9fda1295bcdcd44b8a92c101c2aa445197ce6d4a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78358640"
---
# <a name="add-and-manage-lab-users"></a>Labgebruikers toevoegen en beheren
In dit artikel wordt beschreven hoe u gebruikers toevoegt aan het lab, ze aan het lab kunt toevoegen, het aantal uren bepaalt dat ze de virtuele machine kunnen gebruiken, en nog veel meer. 


## <a name="add-users-to-the-lab"></a>Gebruikers toevoegen aan het lab

1. Selecteer **Gebruikers** in het menu links. De optie **Toegang beperken** is standaard ingeschakeld. Wanneer deze instelling is ingeschakeld, kan een gebruiker zich niet registreren bij het lab, zelfs niet als deze de registratiekoppeling heeft, tenzij de gebruiker in de lijst met gebruikers staat. Alleen gebruikers in de lijst kunnen zich registreren bij het lab door de registratiekoppeling te gebruiken die u verzendt. In deze procedure kunt u gebruikers toevoegen aan de lijst. U kunt ook **Toegang beperken** uitschakelen, zodat gebruikers zich bij het lab kunnen registreren zolang ze de registratiekoppeling hebben. 
2. Selecteer **gebruikers toevoegen** op de werk balk en selecteer vervolgens **toevoegen per e-mail adres**. 

    ![Knop Gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-button.png)
1. Op de pagina **Gebruikers toevoegen** voert u e-mailadressen van gebruikers in op afzonderlijke regels of op één regel gescheiden door puntkomma's. 

    ![E-mailadressen van gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-email-addresses.png)
4. Selecteer **Opslaan**. U ziet de e-mailadressen van gebruikers en hun status (al dan niet geregistreerd) in de lijst. 

    ![Lijst met gebruikers](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > U ziet de namen van de gebruikers in de lijst nadat deze zijn geregistreerd bij het lab. De naam die in de lijst wordt weer gegeven, wordt samengesteld op basis van de voor-en achternaam van de gebruiker in de Azure Active Directory. 

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
1. Schakel over naar de weer gave **gebruikers** als u zich nog niet op de pagina bevindt en selecteer **Alles uitnodigen** op de werk balk. 

    ![Studenten selecteren](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Voer op de pagina **uitnodiging per E-mail verzenden** een optioneel bericht in en selecteer vervolgens **verzenden**. Het e-mail bericht bevat automatisch de registratie koppeling. U kunt deze registratie koppeling ophalen door te selecteren **... (weglatings tekens)** op de werk balk en **registratie koppeling**. 

    ![Registratie koppeling via e-mail verzenden](../media/tutorial-setup-classroom-lab/send-email.png)
4. U ziet de status van de **uitnodiging** in de lijst met **gebruikers** . De status wordt gewijzigd in **verzenden** en vervolgens **verzonden op \<datum >** . 

    Zie [How to configure student Usage](how-to-configure-student-usage.md)(Engelstalig) voor meer informatie over het toevoegen van studenten aan een klasse en het beheren van hun gebruik van het lab.

### <a name="invite-selected-users"></a>Geselecteerde gebruikers uitnodigen

1. Selecteer een gebruiker of meerdere gebruikers in de lijst. 
2. Selecteer vervolgens het pictogram **envelop** dat u ziet in de geselecteerde rij (of) Selecteer **uitnodigen** op de werk balk. 

    ![Geselecteerde gebruikers uitnodigen](../media/how-to-configure-student-usage/invite-selected-users.png)
3. Voer in het venster **uitnodiging per E-mail verzenden** een optioneel **bericht**in en selecteer vervolgens **verzenden**. 

    ![E-mail naar geselecteerde gebruikers verzenden](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    U ziet de status van deze bewerking in de kolom **uitnodigingen** van de lijst **gebruikers** . De uitnodigings-e-mail bevat de registratie koppeling die gebruikers kunnen gebruiken om zich bij het lab te registreren.

1. Schakel over naar de weer gave **gebruikers** als de pagina al niet aanwezig is. 

## <a name="get-registration-link"></a>Registratie koppeling ophalen
U kunt ook de registratie koppeling vanuit de portal ophalen en deze verzenden met uw eigen e-mailclient toepassing. 

1. Schakel over naar de weer gave **gebruikers** door **gebruikers** te selecteren in het menu links. 
2. Selecteren **... (weglatings tekens)** op de werk balk en selecteer vervolgens **registratie koppeling**.

    ![Registratiekoppeling voor studenten](../media/how-to-configure-student-usage/registration-link-button.png)
1. Selecteer in het dialoog venster **gebruikers registratie** de knop **kopiëren** . De koppeling wordt naar het klembord gekopieerd. Plak deze in een e-mailprogramma en verstuur een e-mail naar de student. 

    ![Registratiekoppeling voor studenten](../media/how-to-configure-student-usage/registration-link.png)
2. Selecteer **gereed**in het dialoog venster **gebruikers registratie** . 
4. De **registratie koppeling** naar een student verzenden, zodat de student kan worden geregistreerd voor de klasse. 

## <a name="view-registered-users"></a>Geregistreerde gebruikers weer geven

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts.
3. Op de pagina **Mijn labs** selecteert u het lab waarvoor u het gebruik wilt bijhouden. 
4. Selecteer **Gebruikers** in het menu links of in de tegel **Gebruikers**. U ziet studenten die zijn geregistreerd bij uw lab.  

    ![Geregistreerde gebruikers](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Quota instellen voor gebruikers
U kunt quota per gebruiker instellen met behulp van de volgende stappen: 

1. Selecteer **gebruikers** in het menu links als de pagina nog niet actief is. 
2. Selecteer **quotum per gebruiker: \<nummer > uur** op de werk balk. 
3. Geef op de pagina **quotum per gebruiker** het aantal uren op dat u wilt toewijzen aan elke gebruiker (student) buiten de geplande klasse tijd en selecteer vervolgens **Opslaan**.

    ![Quotum per gebruiker](../media/how-to-configure-student-usage/quota-per-user.png)    
5. U ziet nu de gewijzigde waarden op de werk balk: **quotum per gebruiker: \<aantal uur >** en ook in de lijst gebruikers.

    ![Quotum per gebruiker: na](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > De [geplande uitvoerings tijd van vm's](how-to-create-schedules.md) telt niet op het quotum dat aan een gebruiker is toegewezen. Het quotum geldt voor de tijd buiten de plannings uren die een student op Vm's doorbrengt. 

## <a name="set-additional-quota-for-a-specific-user"></a>Extra quota instellen voor een specifieke gebruiker
U kunt een extra quotum opgeven voor een gebruiker. Dit quotum is een aanvulling op de gemeen schappelijke quotaset voor alle gebruikers in de vorige sectie. Als u bijvoorbeeld (als een docent) het quotum voor alle gebruikers instelt op 10 uur en een extra quotum van 5 uur instelt voor een specifieke gebruiker, wordt het quotum van 15 (10 + 5) uur overschreden. Als u het algemene quotum later wijzigt naar, zegt 15, dan krijgt de gebruiker 20 (15 + 5) uur aan quota. Houd er rekening mee dat dit totale quotum zich buiten het geplande tijdstip bevindt. De tijd die student op een Lab-VM besteedt tijdens de plannings tijd, telt niet op dit quotum. 

Voer hiertoe de volgende stappen uit:

1. Selecteer een gebruiker (student) in de lijst met gebruikers op de pagina **gebruikers** .
2. Selecteer vervolgens **quota aanpassen** op de werk balk. 

    ![Knop quotum aanpassen](../media/how-to-configure-student-usage/adjust-quota-button.png)
3. Voer het aantal **extra uren** in voor de geselecteerde gebruiker of gebruikers en selecteer vervolgens **Toep assen**. 

    ![Aanvullend quotum voor een gebruiker](../media/how-to-configure-student-usage/additional-quota.png)
4. U ziet het bijgewerkte gebruik van de gebruiker in de kolom **gebruik** . 

    ![Nieuw gebruik voor de gebruiker](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Studenten accounts
Als u studenten wilt toevoegen aan een leslokaal Lab, gebruikt u hun e-mail accounts. De volgende typen e-mail accounts kunnen worden gebruikt:

- Een student-e-mail account dat wordt verschaft door de Office 365-Azure Active Directory (AAD) van uw universiteit. 
- Een micro soft-e-mail account, zoals `@outlook.com`, `@hotmail.com`, `@msn.com`of `@live.com`.
- Een niet-micro soft-e-mail account, zoals het abonnement van Yahoo of Google. Deze typen accounts moeten echter worden gekoppeld aan een Microsoft-account.
- Een GitHub-account. Dit account moet worden gekoppeld aan een Microsoft-account.

### <a name="using-a-non-microsoft-email-account"></a>Een niet-micro soft-e-mail account gebruiken
Studenten kunnen niet-micro soft-e-mail accounts gebruiken om zich te registreren bij een leslokaal Lab en zich aan te melden.  De registratie vereist echter dat studenten eerst een Microsoft-account maken dat is gekoppeld aan een niet-micro soft-e-mail adres.

Veel studenten hebben mogelijk al een Microsoft-account gekoppeld aan hun e-mail adressen die niet van micro soft zijn. Studenten hebben bijvoorbeeld al een Microsoft-account als ze hun e-mail adres hebben gebruikt met andere producten of services van micro soft, zoals Office, Skype, OneDrive of Windows.  

Wanneer een student op de registratie-URL klikt om zich aan te melden bij een leslokaal, wordt de gebruiker gevraagd om hun e-mail adres en wacht woord. Als de student probeert zich aan te melden met een niet-Microsoft-account waaraan geen Microsoft-account is gekoppeld, wordt het volgende fout bericht weer gegeven: 

![Foutbericht](../media/how-to-configure-student-usage/cant-find-account.png)

Studenten moeten naar [http://signup.live.com](http://signup.live.com)gaan om zich aan te melden voor een Microsoft-account.  

> [!IMPORTANT]
> Wanneer studenten zich aanmelden bij een leslokaal Lab, krijgen ze niet de optie om een Microsoft-account te maken. Daarom wordt u aangeraden deze aanmeldings koppeling op te nemen in de inschrijvings-e-mail van het leslokaal die u verzendt naar studenten die niet-micro soft-accounts gebruiken.

### <a name="using-a-github-account"></a>Een GitHub-account gebruiken
Studenten kunnen ook een bestaand GitHub-account gebruiken om zich te registreren bij een leslokaal Lab en zich aan te melden. Als aan de student al een Microsoft-account is gekoppeld aan hun GitHub-account, kunnen ze zich aanmelden en hun wacht woord opgeven, zoals in de vorige sectie wordt weer gegeven. Als ze hun GitHub-account nog niet aan een Microsoft-account hebben gekoppeld, moeten ze **aanmeldings opties**selecteren:

![Koppeling voor aanmeldings opties](../media/how-to-configure-student-usage/signin-options.png)

Selecteer **Aanmelden met github**op de pagina **aanmeldings opties** .

![Aanmelden met GitHub-koppeling](../media/how-to-configure-student-usage/signin-github.png)

Ten slotte wordt er gevraagd om een Microsoft-account te maken dat is gekoppeld aan het GitHub-account. Dit gebeurt automatisch wanneer de student **volgende**selecteert.  De student wordt vervolgens onmiddellijk aangemeld en is verbonden met het leslokaal Lab.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)

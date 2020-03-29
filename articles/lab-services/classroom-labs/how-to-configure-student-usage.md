---
title: Gebruiksinstellingen configureren in klaslabs van Azure Lab Services
description: Leer hoe u het aantal studenten voor een lab configureert, laat ze registreren bij het lab, bepaal het aantal uren dat ze de VM kunnen gebruiken en meer.
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
ms.openlocfilehash: 2697bed77263bb5b8349898765851a9b87992279
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159452"
---
# <a name="add-and-manage-lab-users"></a>Labgebruikers toevoegen en beheren

In dit artikel wordt beschreven hoe u studentengebruikers aan een lab toevoegen, ze registreren bij het lab, het aantal extra uren bepalen dat ze de virtuele machine (VM) kunnen gebruiken en meer. 

## <a name="add-users-to-a-lab"></a>Gebruikers toevoegen aan een lab

In deze sectie voegt u studenten handmatig toe aan een lab of door een CSV-bestand te uploaden. Ga als volgt te werk:

1. Selecteer **Gebruikers**in het linkerdeelvenster . 

    Standaard is de optie **Toegang beperken** ingeschakeld en tenzij ze in de lijst met gebruikers staan, kunnen studenten zich niet registreren bij het lab, zelfs niet als ze een registratiekoppeling hebben. Alleen beursgenoteerde gebruikers kunnen zich bij het lab registreren via de registratielink die u verzendt. In deze procedure kunt u gebruikers toevoegen aan de lijst. U ook **Toegang beperken**uitschakelen, zodat studenten zich kunnen registreren bij het lab zolang ze de registratielink hebben. 

1. Selecteer boven aan het deelvenster **Gebruikers** de optie **Gebruikers toevoegen**en selecteer vervolgens Toevoegen **op e-mailadres**. 

    ![De knop 'Gebruikers toevoegen'](../media/how-to-configure-student-usage/add-users-button.png)

1. Voer **in** het deelvenster Gebruikers toevoegen de e-mailadressen van de studenten in op afzonderlijke regels of op één regel die wordt gescheiden door puntkomma's. 

    ![E-mailadressen van gebruikers toevoegen](../media/how-to-configure-student-usage/add-users-email-addresses.png)

1. Selecteer **Opslaan**. 

    De lijst toont de e-mailadressen en statussen van de huidige gebruikers, ongeacht of ze zijn geregistreerd bij het lab of niet. 

    ![Lijst met gebruikers](../media/how-to-configure-student-usage/list-of-added-users.png)

    > [!NOTE]
    > Nadat de studenten zijn geregistreerd bij het lab, worden in de lijst hun namen weergegeven. De naam die in de lijst wordt weergegeven, wordt gemaakt met behulp van de voor- en achternamen van de studenten in Azure Active Directory. 

### <a name="add-users-by-uploading-a-csv-file"></a>Gebruikers toevoegen door een CSV-bestand te uploaden

U gebruikers ook toevoegen door een CSV-bestand te uploaden dat hun e-mailadressen bevat.

1. Maak in Microsoft Excel een CSV-bestand met e-mailadressen van studenten in één kolom.

    ![Lijst met gebruikers in een CSV-bestand](../media/how-to-configure-student-usage/csv-file-with-users.png)

1. Selecteer boven aan het deelvenster **Gebruikers** de optie **Gebruikers toevoegen**en selecteer **CSV uploaden**.

    ![De knop CSV uploaden](../media/how-to-configure-student-usage/upload-csv-button.png)

1. Selecteer het CSV-bestand met de e-mailadressen van de studenten en selecteer **Open**.

    In het venster **Gebruikers toevoegen** wordt de lijst met e-mailadressen weergegeven in het CSV-bestand. 

    ![Het venster 'Gebruikers toevoegen' met e-mailadressen uit CSV-bestand](../media/how-to-configure-student-usage/add-users-window.png)

1. Selecteer **Opslaan**. 

1. Bekijk in het deelvenster **Gebruikers** de lijst met toegevoegde studenten. 

    ![Lijst met toegevoegde gebruikers in het deelvenster 'Gebruikers'](../media/how-to-configure-student-usage/list-of-added-users.png)

## <a name="send-invitations-to-users"></a>Uitnodigingen verzenden naar gebruikers

Als u een registratiekoppeling naar nieuwe gebruikers wilt verzenden, gebruikt u een van de volgende methoden. 

Als de optie **Toegang beperken** is ingeschakeld voor het lab, kunnen alleen vermelde gebruikers de registratiekoppeling gebruiken om zich te registreren naar het lab. Deze optie is standaard ingeschakeld. 

### <a name="invite-all-users"></a>Alle gebruikers uitnodigen

Deze methode laat u zien hoe u e-mail verzendt met een registratielink en een optioneel bericht naar alle vermelde studenten.

1. Selecteer **in** het deelvenster Gebruikers alle **optie Alle uitnodigen**. 

    ![De knop 'Alles uitnodigen'](../media/tutorial-setup-classroom-lab/invite-all-button.png)

1. Voer in het **venster Uitnodiging verzenden per e-mail** een optioneel bericht in en selecteer **Verzenden**. 

    De e-mail bevat automatisch de registratielink. Als u de registratiekoppeling afzonderlijk wilt oppakken en opslaan, selecteert u de ellips (**...**) boven aan het deelvenster **Gebruikers** en selecteert u vervolgens De **koppeling Registratie**. 

    ![Het venster Registratiekoppeling verzenden via e-mail](../media/tutorial-setup-classroom-lab/send-email.png)

    In de kolom **Uitnodiging** van de lijst **Gebruikers** wordt de uitnodigingsstatus voor elke toegevoegde gebruiker weergegeven. De status moet worden gewijzigd in **Verzenden** en vervolgens naar **Verzonden op \<datum>**. 

### <a name="invite-selected-users"></a>Geselecteerde gebruikers uitnodigen

Deze methode laat zien hoe je alleen bepaalde studenten uitnodigt en een registratielink krijgt die je met andere mensen delen.

1. Selecteer **in** het deelvenster Gebruikers een student of meerdere studenten in de lijst. 

1. Selecteer in de rij voor de student die u hebt geselecteerd het **enveloppictogram** of selecteer **uitnodigen**op de werkbalk . 

    ![Geselecteerde gebruikers uitnodigen](../media/how-to-configure-student-usage/invite-selected-users.png)

1. Voer in het **venster Uitnodiging verzenden per e-mail** een optioneel **bericht**in en selecteer **Verzenden**. 

    ![E-mail verzenden naar geselecteerde gebruikers](../media/how-to-configure-student-usage/send-invitation-to-selected-users.png)

    In het deelvenster **Gebruikers** wordt de status van deze bewerking weergegeven in de kolom **Uitnodiging** van de tabel. De uitnodiginge-mail bevat de registratielink die studenten kunnen gebruiken om zich te registreren bij het lab.

## <a name="get-the-registration-link"></a>Download de registratielink

In deze sectie u de registratielink van de portal ophalen en verzenden met uw eigen e-mailtoepassing. 

1. Selecteer koppeling **Registratie**in het deelvenster **Gebruikers** .

    ![Registratiekoppeling voor studenten](../media/how-to-configure-student-usage/registration-link-button.png)

1. Selecteer **Kopiëren**en selecteer Gereed in het venster **Gebruikersregistratie** en selecteer **Gereed**. 

    ![Het venster 'Gebruikersregistratie'](../media/how-to-configure-student-usage/registration-link.png)

    De koppeling wordt naar het klembord gekopieerd. 
    
1. Plak in je e-mailaanvraag de registratielink en stuur de e-mail naar een student, zodat de student zich kan inschrijven voor de klas. 

## <a name="view-registered-users"></a>Geregistreerde gebruikers weergeven

1. Ga naar de azure [lab services-website.](https://labs.azure.com) 
1. Selecteer **Aanmelden**en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts.
1. Selecteer op de pagina **Mijn labs** het lab waarvan u het gebruik wilt bijhouden. 
1. Selecteer in het linkerdeelvenster **gebruikers**of selecteer de tegel **Gebruikers.** 

    In het deelvenster **Gebruikers** wordt een lijst weergegeven met studenten die zich bij uw lab hebben geregistreerd.  

    ![Lijst van geregistreerde gebruikers](../media/tutorial-track-usage/registered-users.png)

## <a name="set-quotas-for-users"></a>Quota instellen voor gebruikers

U een uurquotum instellen voor elke student door het volgende te doen: 

1. Selecteer **in** het deelvenster Gebruikers **Quota per \<gebruiker: getal> uur(s)** op de werkbalk. 
1. Geef in het **venster Quotum per gebruiker** het aantal uren op dat u aan elke student buiten de geplande lestijd wilt geven en selecteer **Opslaan**.

    ![Het venster 'Quotum per gebruiker'](../media/how-to-configure-student-usage/quota-per-user.png)    

    De gewijzigde waarden worden nu weergegeven op het **quotum per gebruiker: \<aantal uren>** knop op de werkbalk en in de gebruikerslijst, zoals hier wordt weergegeven:

    ![Quota-uren per gebruiker](../media/how-to-configure-student-usage/quot-per-user-after.png)

    > [!IMPORTANT]
    > De [geplande looptijd van VM's](how-to-create-schedules.md) telt niet mee voor het quotum dat aan een student is toegewezen. Het quotum is voor de tijd buiten de geplande uren die een student besteedt aan VM's. 

## <a name="set-additional-quotas-for-specific-users"></a>Extra quota instellen voor specifieke gebruikers

U quota voor bepaalde studenten opgeven die verder gaan dan de algemene quota die zijn ingesteld voor alle gebruikers in de vorige sectie. Als u bijvoorbeeld als cursusleider het quotum voor alle studenten instelt op 10 uur en een extra quotum van 5 uur instelt voor een specifieke student, krijgt die student 15 (10 + 5) uur quotum. Als u het algemene quotum later wijzigt in bijvoorbeeld 15, krijgt de student 20 (15 + 5) uur quotum. Houd er rekening mee dat dit totale quotum buiten de geplande tijd valt. De tijd die een student tijdens de geplande tijd aan een lab-vm doorbrengt, telt niet mee voor dit quotum. 

Ga als volgt te werk om extra quota in te stellen:

1. Selecteer **in** het deelvenster Gebruikers een student in de lijst en selecteer **Vervolgens Quotum aanpassen** op de werkbalk. 

    ![De knop Quotum aanpassen](../media/how-to-configure-student-usage/adjust-quota-button.png)

1. Voer in het **quotum aanpassen voor \<geselecteerde gebruikers of gebruikers>** in, voer het aantal extra laburen in dat u wilt toekennen aan de geselecteerde student of studenten en selecteer Vervolgens **Toepassen**. 

    ![Het "Quotum aanpassen ..." Venster](../media/how-to-configure-student-usage/additional-quota.png)

    In de kolom **Gebruik** wordt het bijgewerkte quotum voor de geselecteerde studenten weergegeven. 

    ![Nieuw gebruik voor de gebruiker](../media/how-to-configure-student-usage/new-usage-hours.png)

## <a name="student-accounts"></a>Studentenaccounts

Als je leerlingen wilt toevoegen aan een leslokaallab, gebruik je hun e-mailaccounts. Studenten hebben mogelijk de volgende typen e-mailaccounts:

- Een e-mailaccount voor studenten dat wordt geleverd door het Azure Active Directory-exemplaar van uw universiteit voor Office 365. 
- Een e-mailaccount van Microsoft-domein, zoals *outlook.com,* *hotmail.com,* *msn.com*of *live.com*.
- Een niet-Microsoft e-mailaccount, zoals een die door Yahoo! of Google. Dit soort accounts moet echter worden gekoppeld aan een Microsoft-account.
- Een GitHub-account. Dit account moet gekoppeld zijn aan een Microsoft-account.

### <a name="use-a-non-microsoft-email-account"></a>Een e-mailaccount van niet-Microsoft gebruiken
Studenten kunnen niet-Microsoft-e-mailaccounts gebruiken om zich te registreren en zich aan te melden bij een klaslokaallab.  De registratie vereist echter dat ze eerst een Microsoft-account maken dat is gekoppeld aan hun niet-Microsoft-e-mailadres.

Veel studenten hebben mogelijk al een Microsoft-account dat is gekoppeld aan hun niet-Microsoft-e-mailadres. Studenten hebben bijvoorbeeld al een Microsoft-account als ze hun e-mailadres hebben gebruikt met andere Microsoft-producten of -services, zoals Office, Skype, OneDrive of Windows.  

Wanneer studenten de registratielink gebruiken om zich aan te melden bij een klaslokaal, wordt hen gevraagd om hun e-mailadres en wachtwoord. Studenten die proberen zich aan te melden met een niet-Microsoft-account dat niet is gekoppeld aan een Microsoft-account, ontvangen het volgende foutbericht: 

![Foutbericht bij aanmelding](../media/how-to-configure-student-usage/cant-find-account.png)

Hier is een link voor studenten om zich aan te [melden voor een Microsoft-account.](http://signup.live.com)  

> [!IMPORTANT]
> Wanneer studenten zich aanmelden bij een leslab, krijgen ze niet de optie om een Microsoft-account aan te maken. Daarom raden we u aan deze aanmeldingskoppeling http://signup.live.comop te nemen in de e-mail voor de registratie van het klaslokaal lab dat u verzendt naar studenten die niet-Microsoft-accounts gebruiken.

### <a name="use-a-github-account"></a>Een GitHub-account gebruiken
Studenten kunnen ook een bestaand GitHub-account gebruiken om zich te registreren en zich aan te melden bij een klaslokaallab. Als ze al een Microsoft-account hebben gekoppeld aan hun GitHub-account, kunnen studenten zich aanmelden en hun wachtwoord opgeven, zoals in de vorige sectie wordt weergegeven. 

Als ze hun GitHub-account nog niet aan een Microsoft-account hebben gekoppeld, kunnen ze het volgende doen:

1. Selecteer de koppeling **Aanmeldingsopties,** zoals hier wordt weergegeven:

    ![De koppeling Aanmeldingsopties](../media/how-to-configure-student-usage/signin-options.png)

1. Selecteer **Aanmelden met GitHub**in het venster **Aanmeldingsopties** .

    ![De link 'Aanmelden met GitHub'](../media/how-to-configure-student-usage/signin-github.png)

    Aan de prompt maken studenten vervolgens een Microsoft-account aan dat is gekoppeld aan hun GitHub-account. De koppeling gebeurt automatisch wanneer ze **Volgende**selecteren . Ze worden dan meteen aangemeld en verbonden met het klaslab.

## <a name="export-a-list-of-users-to-a-csv-file"></a>Een lijst met gebruikers exporteren naar een CSV-bestand

1. Ga naar het deelvenster **Gebruikers.**
1. Selecteer op de werkbalk de ellips (**...**) en selecteer **CSV exporteren**. 

    ![De knop CSV exporteren](../media/how-to-export-users-virtual-machines-csv/users-export-csv.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- Voor beheerders: [Lab-accounts maken en beheren](how-to-manage-lab-accounts.md)
- Voor labeigenaren: [labs maken en beheren](how-to-manage-classroom-labs.md) en sjablonen instellen en [publiceren](how-to-create-manage-template.md)
- Voor labgebruikers: [toegang tot klaslokalen](how-to-use-classroom-lab.md)

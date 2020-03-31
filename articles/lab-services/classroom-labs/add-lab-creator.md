---
title: Een gebruiker toevoegen als maker van een lab in Azure Lab Services
description: In dit artikel ziet u hoe u een gebruiker toevoegt aan de Lab Creator-rol voor een labaccount in Azure Lab Services. De makers van het lab kunnen labs maken binnen dit labaccount.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: 0538747ec639b3fab1a7b38193796d80a7736170
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444769"
---
# <a name="add-lab-creators-to-a-lab-account-in-azure-lab-services"></a>Labmakers toevoegen aan een labaccount in Azure Lab Services
In dit artikel ziet u hoe u gebruikers als labmakers toevoegt aan een labaccount in Azure Lab Services. Deze toepassingen kunnen vervolgens klaslokalen in het labaccount maken. 

## <a name="add-microsoft-user-account-to-lab-creator-role"></a>Microsoft-gebruikersaccount toevoegen aan de rol Lab Creator
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent een leslokaallab te maken met hetzelfde gebruikersaccount, kunt u deze stap overslaan. Als u een ander gebruikersaccount wilt gebruiken om een leslokaallab te maken, voert u de volgende stappen uit: 

Als u docenten de toestemming wilt geven om labs voor hun klassen te maken, voegt u ze toe aan de rol **Lab Creator:**

1. Selecteer op de pagina **Lab-account** de optie **Toegangsbeheer (IAM)** en klik op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Zie de sectie [Een niet-Microsoft-accounttoevoegen als een labmaker als een niet-Microsoft-accountmaker.](#add-a-non-microsoft-account-user-as-a-lab-creator) 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Een niet-Microsoft-accountgebruiker toevoegen als maker van een lab
Als u een gebruiker als maker van een lab wilt toevoegen, gebruikt u hun e-mailaccounts. De volgende typen e-mailaccounts kunnen worden gebruikt:

- Een e-mailaccount dat wordt geleverd door de Office 365 Azure Active Directory (AAD) van uw universiteit. 
- Een Microsoft-e-mailaccount, `@hotmail.com` `@msn.com`zoals `@outlook.com` `@live.com`, , of .
- Een niet-Microsoft-e-mailaccount, zoals een account van Yahoo of Google. Dit soort accounts moet echter worden gekoppeld aan een Microsoft-account.
- Een GitHub-account. Dit account moet gekoppeld zijn aan een Microsoft-account.

### <a name="using-a-non-microsoft-email-account"></a>Een e-mailaccount van een niet-Microsoft-e-mailaccount gebruiken
Labmakers/cursusleiders kunnen niet-Microsoft-e-mailaccounts gebruiken om zich te registreren en zich aan te melden bij een klaslokaallab.  De aanmelding bij de Lab Services-portal vereist echter dat cursusleiders eerst een Microsoft-account maken dat is gekoppeld aan hun niet-Microsoft-e-mailadres.

Veel cursusleiders hebben mogelijk al een Microsoft-account dat is gekoppeld aan hun niet-Microsoft-e-mailadressen. Cursusleiders hebben bijvoorbeeld al een Microsoft-account als ze hun e-mailadres hebben gebruikt met andere producten of services van Microsoft, zoals Office, Skype, OneDrive of Windows.  

Wanneer cursusleiders zich aanmelden bij de Lab Services-portal, wordt hen gevraagd om hun e-mailadres en wachtwoord. Als de cursusleider probeert in te loggen met een niet-Microsoft-account zonder Microsoft-account dat niet is gekoppeld aan een Microsoft-account, ontvangt de cursusleider het volgende foutbericht: 

![Foutbericht](../media/how-to-configure-student-usage/cant-find-account.png)

Als u zich wilt aanmelden voor een [http://signup.live.com](http://signup.live.com)Microsoft-account, moeten cursusleiders naar .  


### <a name="using-a-github-account"></a>Een GitHub-account gebruiken
Cursusleiders kunnen ook een bestaand GitHub-account gebruiken om zich te registreren en zich aan te melden bij een klaslokaallab. Als de cursusleider al een Microsoft-account heeft gekoppeld aan zijn GitHub-account, kunnen ze zich aanmelden en hun wachtwoord opgeven, zoals in de vorige sectie is weergegeven. Als ze hun GitHub-account nog niet aan een Microsoft-account hebben gekoppeld, moeten ze **aanmeldingsopties**selecteren:

![Koppeling aanmeldingsopties](../media/how-to-configure-student-usage/signin-options.png)

Selecteer **Aanmelden met GitHub**op de pagina **Aanmeldingsopties** .

![Aanmelden met GitHub-koppeling](../media/how-to-configure-student-usage/signin-github.png)

Ten slotte wordt hen gevraagd een Microsoft-account aan te maken dat is gekoppeld aan hun GitHub-account. Dit gebeurt automatisch wanneer de cursusleider **Volgende**selecteert.  De instructeur wordt dan onmiddellijk aangemeld en verbonden met het klaslab.


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als labgebruiker toegang tot klaslokalen](how-to-use-classroom-lab.md)

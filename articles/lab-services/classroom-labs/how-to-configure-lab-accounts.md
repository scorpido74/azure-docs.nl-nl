---
title: Lab-accounts configureren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het configureren van een Lab-account nadat het is gemaakt.
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
ms.date: 05/07/2019
ms.author: spelluru
ms.openlocfilehash: 1a1b1e662a2e9adedfc68f1818f868c0a5318652
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75428962"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Lab-accounts configureren in Azure Lab Services 
In Azure Lab Services is een Lab-account een container voor beheerde Lab-typen zoals klassikale Labs. Een beheerder stelt een Lab-account in met Azure Lab Services en biedt toegang tot Lab-eigen aars die in het account Labs kunnen maken. In dit artikel wordt beschreven hoe u een Lab-account maakt, alle Lab-accounts weergeeft of een Lab-account verwijdert.

## <a name="connect-with-a-peer-virtual-network"></a>Verbinding maken met een virtueel netwerk op hetzelfde niveau
Als u een virtueel netwerk als peer netwerk wilt verbinden met het virtuele netwerk van het lab, voert u de volgende stappen uit:

1. Selecteer op de pagina **Lab-account** de optie **Labs-configuratie** in het menu links.

    ![Configuratie pagina voor Labs](../media/how-to-manage-lab-accounts/labs-configuration-page.png) 
1. Selecteer **ingeschakeld** of **uitgeschakeld**voor het **virtuele peer netwerk**. De standaard waarde is **uitgeschakeld**. Voer de volgende stappen uit om het virtuele peer netwerk in te scha kelen: 
    1. Selecteer **Ingeschakeld**.
    2. Selecteer het **VNet** in de vervolg keuzelijst. 
3. Selecteer **Opslaan** op de werkbalk. 

Labs die in dit account is gemaakt, is verbonden met het geselecteerde virtuele netwerk. Ze hebben toegang tot de resources in het geselecteerde virtuele netwerk. Zie [verbinding maken met het netwerk van uw Lab met een virtueel netwerk van een peer in Azure Lab Services](how-to-connect-peer-virtual-network.md)voor meer informatie.

Wanneer u een virtueel netwerk voor het veld **virtuele peer netwerk** selecteert, is de optie **Lab maken voor het kiezen van Lab-locatie** is uitgeschakeld. De reden hiervoor is dat Labs in het lab-account zich in dezelfde regio bevinden als het lab-account om verbinding te maken met resources in het virtuele peer netwerk. 

## <a name="allow-lab-creator-to-pick-location-for-the-lab"></a>Lab-Maker toestaan de locatie voor het lab te kiezen
U kunt met behulp van de volgende stappen een Lab-Maker toestaan om Labs te maken op een andere locatie dan de locatie van het lab-account: 

1. Selecteer op de pagina **Lab-account** de optie **Labs-configuratie** in het menu links.
2. Selecteer **ingeschakeld** als u wilt dat de Lab-Maker een locatie voor het Lab kan selecteren, voor de locatie van Lab- **Maker toestaan**. Als deze is uitgeschakeld, worden de Labs automatisch gemaakt op dezelfde locatie als het lab-account. 
    
    Dit veld wordt uitgeschakeld wanneer u een virtueel netwerk selecteert voor het veld **virtuele peer netwerk** . De reden hiervoor is dat Labs in het lab-account zich in dezelfde regio bevinden als het lab-account waarmee ze toegang hebben tot resources in het virtuele netwerk van de peer. 
1. Selecteer **Opslaan** op de werkbalk. 

    ![Instelling voor Lab-locatie configureren](../media/how-to-manage-lab-accounts/labs-configuration-page-lab-location.png)


## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Geef een adres bereik voor Vm's op in het lab
De volgende procedure bevat stappen voor het opgeven van een adres bereik voor Vm's in het lab. Als u het bereik bijwerkt dat u eerder hebt opgegeven, is het gewijzigde adres bereik alleen van toepassing op virtuele machines die zijn gemaakt nadat de wijziging is aangebracht. 

Hier volgen enkele beperkingen bij het opgeven van het adres bereik dat u moet onthouden. 

- Het voor voegsel moet kleiner zijn dan of gelijk zijn aan 23. 
- Als een virtueel netwerk is gekoppeld aan het lab-account, kan het gegeven adres bereik niet overlappen met het adres bereik van een gekoppeld virtueel netwerk.

1. Selecteer op de pagina **Lab-account** de optie **Labs-configuratie** in het menu links.
2. Geef in het veld **adres bereik** het adres bereik op voor virtuele machines die in het lab worden gemaakt. Het adres bereik moet in de CIDR-notatie (Classless Inter-Domain Routing) staan (voor beeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adres bereik.
3. Selecteer **Opslaan** op de werkbalk. 

    ![Adres bereik configureren](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)

## <a name="add-a-user-to-the-lab-creator-role"></a>Een gebruiker toevoegen aan de rol Labmaker
Om een leslokaallab in een labaccount in te kunnen stellen, moet de gebruiker lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van het lab-account wordt automatisch toegevoegd aan deze rol. Als u van plan bent een leslokaallab te maken met hetzelfde gebruikersaccount, kunt u deze stap overslaan. Als u een ander gebruikersaccount wilt gebruiken om een leslokaallab te maken, voert u de volgende stappen uit: 

Als u machtigingen wilt toekennen aan docenten om labs te maken voor hun lessen, voegt u hen toe aan de rol **Labmaker**:

1. Selecteer op de pagina **Lab-account** de optie **Toegangsbeheer (IAM)** en klik op **+ Roltoewijzing toevoegen** in de werkbalk. 

    ![Access Control -> Knop Roltoewijzing toevoegen](../media/tutorial-setup-lab-account/add-role-assignment-button.png)
1. Selecteer op de pagina **Roltoewijzing toevoegen** de optie **Labmaker** als **Rol**. Selecteer de gebruiker die u wilt toevoegen aan de rol Labmaker en selecteer **Opslaan**. 

    ![Labmaker toevoegen](../media/tutorial-setup-lab-account/add-lab-creator.png)

    > [!NOTE]
    > Als u een niet-Microsoft-account gebruiker toevoegt als een Lab-Maker, raadpleegt u de sectie [een niet-Microsoft-account gebruiker toevoegen als een Lab-Maker](#add-a-non-microsoft-account-user-as-a-lab-creator) . 

## <a name="specify-marketplace-images-available-to-lab-creators"></a>Microsoft Azure Marketplace-installatiekopieën opgeven die beschikbaar zijn voor labmakers
Als eigenaar van een labaccount kunt u de Marketplace-installatiekopieën opgeven die labmakers kunnen gebruiken in het labaccount. 

1. Selecteer **Marketplace-installatiekopieën** in het menu aan de linkerkant. Standaard ziet u de volledige lijst met installatiekopieën (zowel ingeschakelde als uitgeschakelde). U kunt de lijst filteren om alleen ingeschakelde/uitgeschakelde installatiekopieën te bekijken door de optie **Alleen ingeschakeld**/**Alleen uitgeschakeld** in de vervolgkeuzelijst bovenaan te selecteren. 
    
    ![Pagina Microsoft Azure Marketplace-installatiekopieën](../media/tutorial-setup-lab-account/marketplace-images-page.png)

    De Marketplace-installatiekopieën die worden weergegeven in de lijst, zijn de enige die voldoen aan de volgende voorwaarden:
        
    - Hiermee wordt een enkele VM gemaakt.
    - Maakt gebruik van Azure Resource Manager om VM’s in te richten
    - Hiervoor hoeft u geen extra licentieabonnement aan te schaffen
2. Als u een Microsoft Azure Marketplace-installatiekopie die is ingeschakeld wilt **uitschakelen**, voert u een van de volgende acties uit: 
    1. Selecteer **... (beletselteken)**  in de laatste kolom en selecteer **Installatiekopie uitschakelen**. 

        ![Een installatiekopie uitschakelen](../media/tutorial-setup-lab-account/disable-one-image.png) 
    2. Selecteer een of meer installatiekopieën in de lijst door de selectievakjes bij de namen van de installatiekopieën in de lijst te selecteren en **Geselecteerde installatiekopieën uitschakelen** te selecteren. 

        ![Meerdere installatiekopieën uitschakelen](../media/tutorial-setup-lab-account/disable-multiple-images.png) 
1. Als u een Microsoft Azure Marketplace-installatiekopie wilt **inschakelen**, voert u een van de volgende acties uit: 
    1. Selecteer **... (beletselteken)**  in de laatste kolom en selecteer **Installatiekopie inschakelen**. 
    2. Selecteer een of meer installatiekopieën in de lijst door de selectievakjes bij de namen van de installatiekopieën in de lijst te selecteren en **Geselecteerde installatiekopieën inschakelen** te selecteren. 

## <a name="add-a-non-microsoft-account-user-as-a-lab-creator"></a>Een niet-Microsoft-account gebruiker toevoegen als een Lab-Maker
Als u een gebruiker wilt toevoegen als een Lab-Maker, gebruikt u hun e-mail accounts. De volgende typen e-mail accounts kunnen worden gebruikt:

- Een e-mail account dat wordt verschaft door de Office 365-Azure Active Directory (AAD) van uw universiteit. 
- Een micro soft-e-mail account, zoals `@outlook.com`, `@hotmail.com`, `@msn.com`of `@live.com`.
- Een niet-micro soft-e-mail account, zoals het abonnement van Yahoo of Google. Deze typen accounts moeten echter worden gekoppeld aan een Microsoft-account.
- Een GitHub-account. Dit account moet worden gekoppeld aan een Microsoft-account.

### <a name="using-a-non-microsoft-email-account"></a>Een niet-micro soft-e-mail account gebruiken
Lab-makers/docenten kunnen niet-micro soft-e-mail accounts gebruiken om zich te registreren bij een leslokaal Lab en zich aan te melden.  Voor de aanmelding bij de Lab Services-portal moeten docenten eerst een Microsoft-account maken dat is gekoppeld aan een niet-micro soft-e-mail adres.

Veel docenten hebben mogelijk al een Microsoft-account gekoppeld aan hun e-mail adressen van andere leveranciers dan micro soft. Docenten hebben bijvoorbeeld al een Microsoft-account als ze hun e-mail adres hebben gebruikt met andere producten of services van micro soft, zoals Office, Skype, OneDrive of Windows.  

Wanneer docenten zich aanmelden bij de Lab Services-portal, wordt ze gevraagd hun e-mail adres en wacht woord op te sturen. Als de docent probeert zich aan te melden met een niet-Microsoft-account waaraan geen Microsoft-account is gekoppeld, wordt het volgende fout bericht weer gegeven: 

![Foutbericht](../media/how-to-configure-student-usage/cant-find-account.png)

Docenten moeten naar [http://signup.live.com](http://signup.live.com)gaan om zich aan te melden voor een Microsoft-account.  


### <a name="using-a-github-account"></a>Een GitHub-account gebruiken
Docenten kunnen ook een bestaand GitHub-account gebruiken om zich te registreren bij een leslokaal Lab en zich aan te melden. Als aan de docent al een Microsoft-account is gekoppeld aan hun GitHub-account, kunnen ze zich aanmelden en hun wacht woord opgeven, zoals in de vorige sectie wordt weer gegeven. Als ze hun GitHub-account nog niet aan een Microsoft-account hebben gekoppeld, moeten ze **aanmeldings opties**selecteren:

![Koppeling voor aanmeldings opties](../media/how-to-configure-student-usage/signin-options.png)

Selecteer **Aanmelden met github**op de pagina **aanmeldings opties** .

![Aanmelden met GitHub-koppeling](../media/how-to-configure-student-usage/signin-github.png)

Ten slotte wordt er gevraagd om een Microsoft-account te maken dat is gekoppeld aan het GitHub-account. Dit gebeurt automatisch wanneer de docent **volgende**selecteert.  De docent wordt dan onmiddellijk aangemeld en is verbonden met het leslokaal Lab.

## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatisch afsluiten van Vm's bij het verbreken van de verbinding
U kunt automatisch afsluiten van Windows Lab-Vm's (sjabloon of student) in-of uitschakelen nadat de verbinding met een extern bureau blad is verbroken. U kunt ook opgeven hoe lang de Vm's moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten.

![Instelling voor automatisch afsluiten bij Lab-account](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Deze instelling is van toepassing op alle Labs die in het lab-account is gemaakt. Een Lab-eigenaar kan deze instelling overschrijven op het niveau van de test omgeving. De wijziging van deze instelling op het lab-account is alleen van invloed op de Labs die zijn gemaakt nadat de wijziging is aangebracht.

Zie [dit artikel](how-to-enable-shutdown-disconnect.md) voor meer informatie over hoe een Lab-eigenaar deze instelling kan configureren op het niveau van de test omgeving

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)

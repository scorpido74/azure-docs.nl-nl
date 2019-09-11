---
title: Klassikale Labs beheren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken en configureren van een leslokaal Lab, het weer geven van alle klassikale Labs, het delen van de registratie koppeling met een test gebruiker of het verwijderen van een lab.
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
ms.date: 06/07/2019
ms.author: spelluru
ms.openlocfilehash: 1f9cb82abd5bc0823f5e7bc23fe437007bccc8e0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2019
ms.locfileid: "70873576"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Klassikale Labs in Azure Lab Services beheren 
In dit artikel wordt beschreven hoe u een leslokaal Lab maakt en verwijdert. U ziet ook hoe u alle klassikale Labs in een Lab-account kunt weer geven. 

## <a name="prerequisites"></a>Vereisten
Als u een leslokaallab in een labaccount instelt, moet u lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan deze rol. Een labeigenaar kan andere gebruikers toevoegen aan de rol Labmaker met behulp van de stappen in het volgende artikel: [Een gebruiker toevoegen aan de rol Labmaker](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Houd er rekening mee dat Internet Explorer 11 nog niet wordt ondersteund. 
2. Selecteer **Aanmelden**. Selecteer of typ een **gebruikers-id** die lid is van de rol **Lab Creator** in het lab-account en voer het wacht woord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** voor uw lab op. 
    2. Geef het maximale **aantal virtuele machines** in het lab op. U kunt het aantal virtuele machines in het lab later verhogen of verlagen. 
    6. Selecteer **Opslaan**.

        ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Voer op de pagina **Specificaties van virtuele machines selecteren** de volgende stappen uit:
    1. Selecteer een **grootte** voor virtuele machines (VM's) die in het lab worden gemaakt. Momenteel zijn **kleine**, **gemiddelde**, middel grote **(virtualisatie)** , **grote**en **GPU** -grootten toegestaan. Zie de sectie VM- [grootten](#vm-sizes) voor meer informatie.
    1. Selecteer de **regio** waarin de virtuele machines moeten worden gemaakt. 
    1. Selecteer de **VM-installatiekopie** die moet worden gebruikt voor het maken van virtuele machines in het lab. Als u een Linux-installatie kopie selecteert, ziet u een optie om verbinding met extern bureau blad in te scha kelen. Zie [verbinding met extern bureau blad inschakelen voor Linux](how-to-enable-remote-desktop-linux.md)voor meer informatie.
    1. Selecteer **Volgende**.

        ![VM-specificaties opgeven](../media/tutorial-setup-classroom-lab/select-vm-specifications.png)    
5. Geef op de pagina **Referenties instellen** de standaardreferenties voor alle virtuele machines in het lab op. 
    1. Geef de **naam van de gebruiker** op voor alle virtuele machines in het lab.
    2. Geef het **wachtwoord** voor de gebruiker op. 

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Schakel **hetzelfde wacht woord voor alle virtuele machines gebruiken** uit als u wilt dat studenten hun eigen wacht woord instellen. Deze stap is **optioneel**. 

        Een docent kan ervoor kiezen om hetzelfde wacht woord te gebruiken voor alle virtuele machines in de test omgeving of studenten toestaan wacht woorden in te stellen voor hun virtuele machines. Deze instelling is standaard ingeschakeld voor alle Windows-en Linux-installatie kopieën, met uitzonde ring van Ubuntu. Wanneer u **Ubuntu** VM selecteert, wordt deze instelling uitgeschakeld, zodat de studenten wordt gevraagd een wacht woord in te stellen wanneer ze zich voor de eerste keer aanmelden.
    1. Selecteer **Maken**. 

        ![Referenties instellen](../media/tutorial-setup-classroom-lab/set-credentials.png)
6. Op de pagina **Sjabloon configureren** wordt de status van het maken van het lab weergegeven. Het maken van de sjabloon in het lab duurt maximaal 20 minuten. Een sjabloon in een lab is een basiskopie van de virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Stel de sjabloon voor de virtuele machine zo in dat deze is geconfigureerd met precies wat u de labgebruikers wilt bieden.  

    ![Sjabloon configureren](../media/tutorial-setup-classroom-lab/configure-template.png)
7. Nadat de configuratie van de sjabloon is voltooid, wordt de volgende pagina weergegeven: 

    ![Sjabloonpagina configureren nadat deze is voltooid](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)
8. In deze zelfstudie zijn de volgende stappen optioneel: 
    2. De sjabloon-VM verbinden door **Verbinding maken** te selecteren. Als het een Linux-sjabloon-VM is, kiest u of u verbinding wilt maken via SSH of RDP (als RDP is ingeschakeld).
    1. Selecteer **wacht woord opnieuw instellen** om het wacht woord voor de virtuele machine opnieuw in te stellen. 
    1. Software installeren en configureren op uw sjabloon-VM. 
    1. **Stop** de virtuele machine.  
    1. Een **Beschrijving** voor de sjabloon invoeren
9. Selecteer **Volgende** op de sjabloonpagina. 
10. Voer op de pagina **De sjabloon publiceren** de volgende acties uit. 
    1. Als u de sjabloon onmiddellijk wilt publiceren, schakelt u het selectievakje in voor *Ik begrijp dat ik de sjabloon niet kan wijzigen nadat deze is gepubliceerd. Dit proces kan slechts één keer worden uitgevoerd en kan tot een uur duren*. Selecteer vervolgens **publiceren**.  Publiceer de sjabloon om exemplaren van de sjabloon-VM beschikbaar te maken voor de labgebruikers.

        > [!WARNING]
        > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
    2. Als u later wilt publiceren, selecteert u **Opslaan voor later**. Nadat de wizard is voltooid, kunt u de VM-sjabloon publiceren. Zie voor meer informatie over het configureren en publiceren nadat de wizard is voltooid voor meer informatie over het configureren en publiceren van het volt ooien van de wizard, zie de sectie sjabloon publiceren in het artikel [klas leren beheren](how-to-manage-classroom-labs.md) .

        ![Sjabloon publiceren](../media/tutorial-setup-classroom-lab/publish-template.png)
11. U kunt de **voortgang van het publiceren** van de sjabloon bekijken. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](../media/tutorial-setup-classroom-lab/publish-template-progress.png)
12. De volgende pagina wordt weergegeven wanneer de sjabloon is gepubliceerd. Selecteer **Done**.

    ![Sjabloon publiceren - gelukt](../media/tutorial-setup-classroom-lab/publish-success.png)
1. U ziet het **dashboard** voor het lab. 
    
    ![Dashboard Leslokaallab](../media/tutorial-setup-classroom-lab/classroom-lab-home-page.png)
4. Ga naar de pagina **Virtuele machines** en controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt de virtuele machines zelf starten vanaf deze pagina of ze laten starten door de studenten. 

    ![Virtuele machines met de status Gestopt](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)

### <a name="vm-sizes"></a>Formaten van virtuele machines  

| Size | Kerngeheugens | RAM | Description | 
| ---- | ----- | --- | ----------- | 
| Klein | 2 | 3,5 GB | Deze grootte is het meest geschikt voor de opdracht regel, de webbrowser openen, webservers met weinig verkeer, kleine tot middel grote data bases. |
| Gemiddeld | 4 | 7 GB | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse | 
| Gemiddeld (geneste virtualisatie) | 4 | 16 GB | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse. Deze grootte biedt ook ondersteuning voor geneste virtualisatie. <p>Deze omvang kan worden gebruikt in scenario's waarbij elke student meerdere Vm's nodig heeft. Docenten kunnen geneste virtualisatie gebruiken om een klein aantal geneste virtuele machines in de virtuele machine in te stellen. </p> |
| Groot | 8 | 32 GB | Deze grootte is het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches. Deze grootte biedt ook ondersteuning voor geneste virtualisatie |  
| Kleine GPU (visualisatie) | 6 | 56 GB | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. | 
| Kleine GPU (Compute) | 6 | 56 GB | Deze grootte is het meest geschikt voor computerintensieve en netwerkintensieve toepassingen, zoals kunst matige intelligentie en toepassingen voor diepe trainingen. | 
| Gemiddelde GPU (visualisatie) | 12 | 112 GB | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. | 

## <a name="view-all-classroom-labs"></a>Alle klassikale Labs weer geven
1. Navigeer naar [Azure Lab Services portal](https://labs.azure.com).
2. Selecteer **Aanmelden**. Selecteer of typ een **gebruikers-id** die lid is van de rol **Lab Creator** in het lab-account en voer het wacht woord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Controleer of alle Labs in het geselecteerde Lab-account worden weer geven. 

    ![Alle Labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Gebruik de vervolg keuzelijst aan de bovenkant om een ander lab-account te selecteren. U ziet Labs in het geselecteerde Lab-account. 

## <a name="delete-a-classroom-lab"></a>Een leslokaal Lab verwijderen
1. Selecteer op de tegel voor het lab drie puntjes (...) in de hoek. 

    ![Het lab selecteren](../media/how-to-manage-classroom-labs/select-three-dots.png)
2. Selecteer **Verwijderen**. 

    ![Knop verwijderen](../media/how-to-manage-classroom-labs/delete-button.png)
3. Selecteer **verwijderen**in het dialoog venster **Lab verwijderen** . 

    ![Dialoog venster verwijderen](../media/how-to-manage-classroom-labs/delete-lab-dialog-box.png)

## <a name="switch-to-another-classroom-lab"></a>Overschakelen naar een ander leslokaal Lab
Als u wilt overschakelen naar een ander leslokaal Lab vanuit de huidige, selecteert u de vervolg keuzelijst met Labs in het lab-account bovenaan.

![Selecteer de vervolg keuzelijst Lab uit bovenaan](../media/how-to-manage-classroom-labs/switch-lab.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)


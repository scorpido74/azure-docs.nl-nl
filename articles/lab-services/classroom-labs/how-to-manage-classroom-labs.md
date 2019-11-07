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
ms.date: 10/12/2019
ms.author: spelluru
ms.openlocfilehash: 91469f27e1ca86650cf94fde5cff5d1864300183
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73606351"
---
# <a name="manage-classroom-labs-in-azure-lab-services"></a>Klassikale Labs in Azure Lab Services beheren 
In dit artikel wordt beschreven hoe u een leslokaal Lab maakt en verwijdert. U ziet ook hoe u alle klassikale Labs in een Lab-account kunt weer geven. 

## <a name="prerequisites"></a>Vereisten
Als u een leslokaallab in een labaccount instelt, moet u lid zijn van de rol **Labmaker** in het labaccount. Het account dat u hebt gebruikt voor het maken van een labaccount wordt automatisch toegevoegd aan deze rol. Een labeigenaar kan andere gebruikers toevoegen aan de rol Labmaker met behulp van de stappen in het volgende artikel: [Een gebruiker toevoegen aan de rol Labmaker](tutorial-setup-lab-account.md#add-a-user-to-the-lab-creator-role).

## <a name="create-a-classroom-lab"></a>Een leslokaallab maken

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Internet Explorer 11 wordt nog niet ondersteund. 
2. Selecteer **Aanmelden** en voer uw referenties in. Selecteer of typ een **gebruikers-id** die lid is van de rol **Lab Creator** in het lab-account en voer het wacht woord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Selecteer **Nieuw Lab**. 
    
    ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-button.png)
3. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** voor uw lab op. 
    2. Selecteer de **grootte van de virtuele machines** die u nodig hebt voor de-klasse. Zie de sectie [VM-grootten](#vm-sizes) voor een lijst met beschik bare grootten. 
    3. Selecteer de **installatie kopie van de virtuele machine** die u wilt gebruiken voor het leslokaal Lab. Als u een Linux-installatie kopie selecteert, ziet u een optie om verbinding met extern bureau blad in te scha kelen. Zie [verbinding met extern bureau blad inschakelen voor Linux](how-to-enable-remote-desktop-linux.md)voor meer informatie.
    4. Bekijk de **totale prijs per uur** die op de pagina wordt weer gegeven. 
    6. Selecteer **Opslaan**.

        ![Nieuw Lab-venster](../media/tutorial-setup-classroom-lab/new-lab-window.png)
4. Geef op de pagina **referenties voor virtuele machine** standaard referenties op voor alle virtuele machines in het lab.
    1. Geef de **naam van de gebruiker** op voor alle virtuele machines in het lab.
    2. Geef het **wachtwoord** voor de gebruiker op. 

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Schakel **hetzelfde wacht woord voor alle virtuele machines gebruiken** uit als u wilt dat studenten hun eigen wacht woord instellen. Deze stap is **optioneel**. 

        Een docent kan ervoor kiezen om hetzelfde wacht woord te gebruiken voor alle virtuele machines in de test omgeving of studenten toestaan wacht woorden in te stellen voor hun virtuele machines. Deze instelling is standaard ingeschakeld voor alle Windows-en Linux-installatie kopieën, met uitzonde ring van Ubuntu. Wanneer u **Ubuntu** VM selecteert, wordt deze instelling uitgeschakeld, zodat de studenten wordt gevraagd een wacht woord in te stellen wanneer ze zich voor de eerste keer aanmelden.  

        ![Nieuw Lab-venster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)
        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.    
    4. Selecteer vervolgens **volgende** op de pagina **referenties voor de virtuele machine** . 
5. Voer op de pagina **Lab-beleid** het aantal uren in dat is toegewezen aan elke gebruiker (**quotum voor elke gebruiker**) buiten de geplande tijd voor het lab en selecteer vervolgens **volt ooien**. 

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

### <a name="vm-sizes"></a>Formaten van virtuele machines  

| Grootte | Cores | RAM | Beschrijving | 
| ---- | ----- | --- | ----------- | 
| Klein | 2 | 3,5 GB | Deze grootte is het meest geschikt voor de opdracht regel, de webbrowser openen, webservers met weinig verkeer, kleine tot middel grote data bases. |
| Middelgroot | 4 | 7 GB | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse | 
| Gemiddeld (geneste virtualisatie) | 4 | 16 GB | Deze grootte is het meest geschikt voor relationele data bases, caching in het geheugen en analyse. Deze grootte biedt ook ondersteuning voor geneste virtualisatie. <p>Deze omvang kan worden gebruikt in scenario's waarbij elke student meerdere Vm's nodig heeft. Docenten kunnen geneste virtualisatie gebruiken om een klein aantal geneste virtuele machines in de virtuele machine in te stellen. </p> |
| Groot | 8 | 32 GB | Deze grootte is het meest geschikt voor toepassingen die snellere Cpu's nodig hebben, betere prestaties van de lokale schijf, grote data bases, grote geheugen caches. Deze grootte biedt ook ondersteuning voor geneste virtualisatie |  
| Kleine GPU (visualisatie) | 6 | 56 GB | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. | 
| Kleine GPU (Compute) | 6 | 56 GB | Deze grootte is het meest geschikt voor computerintensieve en netwerkintensieve toepassingen, zoals kunst matige intelligentie en toepassingen voor diepe trainingen. | 
| Gemiddelde GPU (visualisatie) | 12 | 112 GB | Deze grootte is het meest geschikt voor externe visualisatie, streaming, games, code ring met behulp van frameworks zoals OpenGL en DirectX. | 

## <a name="view-all-classroom-labs"></a>Alle klassikale Labs weer geven
1. Navigeer naar [Azure Lab Services portal](https://labs.azure.com).
2. Selecteer **Aanmelden**. Selecteer of typ een **gebruikers-id** die lid is van de rol **Lab Creator** in het lab-account en voer het wacht woord in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Controleer of alle Labs in het geselecteerde Lab-account worden weer geven. Op de tegel van het lab ziet u het aantal virtuele machines in het lab en het quotum voor elke gebruiker (buiten de geplande tijd).

    ![Alle Labs](../media/how-to-manage-classroom-labs/all-labs.png)
3. Gebruik de vervolg keuzelijst aan de bovenkant om een ander lab-account te selecteren. U ziet Labs in het geselecteerde Lab-account. 

## <a name="delete-a-classroom-lab"></a>Een leslokaal Lab verwijderen
1. Selecteer op de tegel voor het lab drie puntjes (...) in de hoek en selecteer vervolgens **verwijderen**. 

    ![De knop Verwijderen](../media/how-to-manage-classroom-labs/delete-button.png)
3. Selecteer **verwijderen** in het dialoog venster **Lab verwijderen** om door te gaan met het verwijderen. 

## <a name="switch-to-another-classroom-lab"></a>Overschakelen naar een ander leslokaal Lab
Als u wilt overschakelen naar een ander leslokaal Lab vanuit de huidige, selecteert u de vervolg keuzelijst met Labs in het lab-account bovenaan.

![Selecteer de vervolg keuzelijst Lab uit bovenaan](../media/how-to-manage-classroom-labs/switch-lab.png)

U kunt ook een nieuw lab maken met behulp van het **nieuwe lab** in deze vervolg keuzelijst. 

> [!NOTE]
> U kunt ook de Power shell-module AZ. LabServices (preview) gebruiken voor het beheren van Labs. Zie de [Introductie pagina AZ. LabServices op github](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Modules/Library)voor meer informatie.

Als u wilt overschakelen naar een ander lab-account, selecteert u de vervolg keuzelijst naast het lab-account en selecteert u het andere Lab-account. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Sjablonen instellen en publiceren als labeigenaar](how-to-create-manage-template.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)


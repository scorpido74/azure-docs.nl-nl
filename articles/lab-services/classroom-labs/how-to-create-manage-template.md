---
title: Een sjabloon van een leslokaal Lab beheren in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken en beheren van een sjabloon voor een leslokaal Lab in Azure Lab Services.
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
ms.openlocfilehash: f0fc1e143ce7d271d5faaa8dda0eb40cdfc9e006
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332761"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Een leslokaal sjabloon maken en beheren in Azure Lab Services
Een sjabloon in een lab is een basiskopie van de virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Stel de sjabloon voor de virtuele machine zo in dat deze is geconfigureerd met precies wat u de labgebruikers wilt bieden. U kunt een naam en een beschrijving opgeven voor de sjabloon die de gebruikers van het lab kunnen zien. Vervolgens publiceert u de sjabloon om exemplaren van de sjabloon-VM beschikbaar te maken voor uw test gebruikers. Wanneer u een sjabloon publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Het aantal virtuele machines dat in dit proces wordt gemaakt, is hetzelfde als het maximum aantal gebruikers dat in het lab is toegestaan. Dit kunt u instellen in het gebruiksbeleid van het lab. Alle virtuele machines hebben dezelfde configuratie als de sjabloon.

In dit artikel wordt beschreven hoe u een virtuele-machine sjabloon maakt en beheert in een leslokaal Lab van Azure Lab Services. 

## <a name="publish-a-template-while-creating-a-classroom-lab"></a>Een sjabloon publiceren tijdens het maken van een leslokaal
Zie voor meer informatie over het publiceren van een sjabloon tijdens het maken van een leslokaal Lab [een leslokaal Lab maken](how-to-manage-classroom-labs.md#create-a-classroom-lab)
 
## <a name="set-or-update-template-title-and-description"></a>Titel en beschrijving van de sjabloon instellen of bijwerken
Gebruik de volgende stappen om de titel en beschrijving voor de eerste keer in te stellen en deze later bij te werken. 

1. Voer op de pagina **sjabloon** de nieuwe **titel** in voor het lab.  
2. Voer de nieuwe **Beschrijving** in voor de sjabloon. Wanneer u de focus uit het tekstvak verplaatst, wordt deze automatisch opgeslagen. 

    ![Naam en beschrijving van de sjabloon](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Een sjabloon-VM bijwerken
Gebruik de volgende stappen om een sjabloon-VM bij te werken.  

1. Selecteer op de pagina **sjabloon** de optie **sjabloon aanpassen** op de werk balk. 

    ![Knop sjabloon aanpassen](../media/how-to-create-manage-template/customize-template-button.png)
2. Selecteer **door gaan**in het dialoog venster **sjabloon aanpassen** . Zodra u de sjabloon hebt gestart en wijzigingen hebt aangebracht, heeft deze niet langer dezelfde instellingen als de virtuele machines die voor het laatst zijn gepubliceerd naar uw gebruikers. Sjabloon wijzigingen worden pas weer gegeven op de bestaande virtuele machines van uw gebruikers nadat u de publicatie opnieuw hebt gepubliceerd.

    ![Het dialoog venster aanpassen](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selecteer de knop **verbinding maken met sjabloon** op de werk balk om verbinding te maken met de sjabloon-VM en volg de instructies. Als het een Windows-computer is, ziet u een optie voor het downloaden van het RDP-bestand. 

    ![Verbinding maken met de sjabloon-VM](../media/how-to-create-manage-template/connect-template-vm.png)
1. Installeer alle software die de studenten nodig hebben om het lab te doorlopen (bijvoorbeeld Visual Studio, Azure Storage Explorer, enzovoort). 
2. Verbreek de verbinding met de sjabloon-VM (sluit de Extern bureaublad-sessie). 
3. **Stop** de sjabloon-VM door **sjabloon stoppen**te selecteren. 
4. Volg de stappen in de volgende sectie om de bijgewerkte sjabloon-VM te **publiceren** . 

## <a name="publish-the-template-vm"></a>De sjabloon-VM publiceren  
Als u de sjabloon niet publiceert tijdens het maken van het lab, kunt u deze later publiceren. Voordat u publiceert, kunt u verbinding maken met de VM van de sjabloon en deze bijwerken met alle software. Wanneer u een sjabloon publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Het aantal Vm's dat in dit proces is gemaakt, is het aantal Vm's dat u hebt opgegeven voor de eerste keer of wat u hebt opgegeven op de pagina van de virtuele-machine groep. Alle virtuele machines hebben dezelfde configuratie als de sjabloon. 

1. Selecteer op de pagina **sjabloon** de optie **publiceren** op de werk balk. 
1. Bekijk het bericht in het bericht **sjabloon publiceren** en selecteer **publiceren**. Dit proces kan enige tijd duren, afhankelijk van het aantal virtuele machines dat wordt gemaakt.

    ![De knop Publiceren](../media/how-to-create-manage-template/publish-button.png)

    > [!IMPORTANT]
    > Zodra een sjabloon is gepubliceerd, kan dit niet meer ongedaan worden gemaakt. U kunt de sjabloon echter opnieuw publiceren. 
4. U kunt de status van het publicatie proces zien op de pagina sjabloon. Wacht tot de status van de sjabloon is gewijzigd in **gepubliceerd**. 

    ![Publicatie status](../media/how-to-create-manage-template/publish-status.png)
1. Ga naar de pagina **Virtuele machines** en controleer of u virtuele machines ziet met de status **Niet-toegewezen**. Deze virtuele machines zijn nog niet toegewezen aan studenten. Wacht totdat de virtuele machines zijn gemaakt. Deze horen de status **Gestopt** te hebben. Op deze pagina kunt u een student-VM starten, verbinding maken met de virtuele machine, de virtuele machine stoppen en de virtuele machine verwijderen. U kunt ze op deze pagina starten of uw studenten de Vm's laten starten. 

    ![Virtuele machines met de status Gestopt](../media/tutorial-setup-classroom-lab/virtual-machines-stopped.png)


## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als test gebruiker toegang krijgen tot klassikale Labs](how-to-use-classroom-lab.md)

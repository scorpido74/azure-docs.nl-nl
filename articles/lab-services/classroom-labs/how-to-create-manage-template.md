---
title: Een sjabloon van een klaslokaallab beheren in Azure Lab Services | Microsoft Documenten
description: Meer informatie over het maken en beheren van een laboratoriumsjabloon in het klaslokaal in Azure Lab Services.
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
ms.openlocfilehash: fcf31fcc266358911612c25e0b73a0a9de696b1d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77539013"
---
# <a name="create-and-manage-a-classroom-template-in-azure-lab-services"></a>Een klassjabloon maken en beheren in Azure Lab Services
Een sjabloon in een lab is een basiskopie van de virtuele machine waarvan de virtuele machines van alle gebruikers worden gemaakt. Stel de sjabloon voor de virtuele machine zo in dat deze is geconfigureerd met precies wat u de labgebruikers wilt bieden. U kunt een naam en een beschrijving opgeven voor de sjabloon die de gebruikers van het lab kunnen zien. Vervolgens publiceert u de sjabloon om exemplaren van de sjabloon-VM beschikbaar te maken voor uw labgebruikers. Wanneer u een sjabloon publiceert, maakt Azure Lab Services virtuele machines in het lab met behulp van de sjabloon. Het aantal virtuele machines dat in dit proces wordt gemaakt, is hetzelfde als het maximum aantal gebruikers dat in het lab is toegestaan. Dit kunt u instellen in het gebruiksbeleid van het lab. Alle virtuele machines hebben dezelfde configuratie als de sjabloon.

In dit artikel wordt beschreven hoe u een virtuele sjabloonmachine maakt en beheert in een klaslokaallab van Azure Lab Services. 

## <a name="set-or-update-template-title-and-description"></a>De titel en beschrijving van sjabloonsets instellen of bijwerken
Gebruik de volgende stappen om titel en beschrijving voor de eerste keer in te stellen en deze later bij te werken. 

1. Voer **op** de pagina Sjabloon de nieuwe **titel** voor het lab in.  
2. Voer de nieuwe **beschrijving** voor de sjabloon in. Wanneer u de focus uit het tekstvak verplaatst, wordt deze automatisch opgeslagen. 

    ![Naam en beschrijving van sjabloon](../media/how-to-create-manage-template/template-name-description.png)

## <a name="update-a-template-vm"></a>Een sjabloon-VM bijwerken
Gebruik de volgende stappen om een sjabloon-vm bij te werken.  

1. Wacht tot de sjabloon-vm is gestart en selecteer **Vervolgens Verbinding maken met sjabloon** op de werkbalk om verbinding te maken met de sjabloon-VM en volg instructies. Als het een Windows-machine is, ziet u een optie om het RDP-bestand te downloaden. 
1. Zodra u verbinding maakt met de sjabloon en wijzigingen aanbrengt, heeft deze niet langer dezelfde instelling als de virtuele machines die voor het laatst aan uw gebruikers zijn gepubliceerd. Sjabloonwijzigingen worden pas na publicatie weergeven op de bestaande virtuele machines van uw gebruikers.

    ![Verbinding maken met de sjabloon-VM](../media/how-to-create-manage-template/connect-template-vm.png)
    
1. Installeer alle software die de studenten nodig hebben om het lab te doorlopen (bijvoorbeeld Visual Studio, Azure Storage Explorer, enzovoort). 
1. Verbreek de verbinding met de sjabloon-VM (sluit de Extern bureaublad-sessie). 
1. **Stop** de sjabloon-VM door **sjabloon stoppen te**selecteren . 
1. Voer stappen uit in de volgende sectie om de bijgewerkte sjabloon-VM te **publiceren.** 

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
## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Labaccounts maken en beheren als beheerder](how-to-manage-lab-accounts.md)
- [Labs maken en beheren als labeigenaar](how-to-manage-classroom-labs.md)
- [Het gebruik van een lab configureren en beheren als labeigenaar](how-to-configure-student-usage.md)
- [Als labgebruiker toegang tot klaslokalen](how-to-use-classroom-lab.md)

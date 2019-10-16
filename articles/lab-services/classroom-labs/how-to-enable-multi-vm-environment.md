---
title: Multi-VM-omgevingen inschakelen in Azure Lab Services | Microsoft Docs
description: Meer informatie over het maken van een omgeving met meerdere virtuele machines in een sjabloon van een virtuele machine in een leslokaal Lab van Azure Lab Services.
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
ms.date: 10/13/2019
ms.author: spelluru
ms.openlocfilehash: 9a86ba803f899e78b2ba9640e6cc317966969e64
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72332343"
---
# <a name="create-an-environment-with-multiple-vms-inside-a-template-vm-of-a-classroom-lab"></a>Een omgeving met meerdere virtuele machines maken in een sjabloon-VM van een leslokaal Lab
Op dit moment Azure Lab Services kunt u één virtuele machine in een Lab instellen en één exemplaar beschikbaar maken voor elk van uw gebruikers. Maar als u een docent een IT-klasse wilt laten weten over het instellen van firewalls of servers, moet u mogelijk elk van uw studenten voorzien van een omgeving waarin meerdere virtuele machines met elkaar kunnen communiceren via een netwerk.

Met geneste virtualisatie kunt u een multi-VM-omgeving maken in de virtuele machine van een lab. Als u de sjabloon publiceert, wordt elke gebruiker in het lab voorzien van een virtuele machine die is ingesteld met meerdere virtuele machines in de test omgeving.

## <a name="what-is-nested-virtualization"></a>Wat is geneste virtualisatie?
Met geneste virtualisatie kunt u virtuele machines maken binnen een virtuele machine. Geneste virtualisatie wordt uitgevoerd via Hyper-V en is alleen beschikbaar op Windows-Vm's.

Raadpleeg de volgende artikelen voor meer informatie over geneste virtualisatie:

- [Geneste virtualisatie in azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Geneste virtualisatie inschakelen in een Azure VM](../../virtual-machines/windows/nested-virtualization.md)

## <a name="use-nested-virtualization-in-azure-lab-services"></a>Geneste virtualisatie in Azure Lab Services gebruiken
De belangrijkste stappen zijn:

1. Maak een **grote** **Windows** -sjabloon machine voor het lab. 
2. Maak verbinding met de IT-server en [Schakel geneste virtualisatie in](../../virtual-machines/windows/nested-virtualization.md).


De volgende procedure geeft u de gedetailleerde stappen: 

1. Maak een Lab-account als u er nog geen hebt. Zie [zelf studie: een Lab-account instellen met Azure Lab Services](tutorial-setup-lab-account.md)voor instructies.
1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). Houd er rekening mee dat Internet Explorer 11 nog niet wordt ondersteund. 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts. 
3. Selecteer **Nieuw Lab**. 
    
    ![Een leslokaallab maken](../media/tutorial-setup-classroom-lab/new-lab-button.png)
4. Voer in het venster **Nieuw lab** de volgende acties uit: 
    1. Geef een **naam** voor uw lab op. 
    2. Selecteer **groot (geneste virtualisatie)** of **Gemiddeld (geneste virtualisatie)** voor de grootte van de **virtuele machine**.
    6. Selecteer een Windows- **installatie kopie** die u wilt gebruiken. Geneste virtualisatie is alleen beschikbaar op Windows-computers. 
    4. Selecteer vervolgens **Volgende**. 

        ![Een leslokaallab maken](../media/how-to-enable-multi-vm-environment/new-lab-window.png)
    1. Geef op de pagina **referenties voor virtuele machine** standaard referenties op voor alle virtuele machines in het lab. Geef de **naam** en het **wacht woord** voor de gebruiker op en selecteer **volgende**.  

        ![Nieuw Lab-venster](../media/tutorial-setup-classroom-lab/virtual-machine-credentials.png)

        > [!IMPORTANT]
        > Noteer de gebruikersnaam en het wachtwoord. Deze worden niet opnieuw weergegeven.
    3. Voer op de pagina **Lab-beleid** het aantal uren in dat is toegewezen aan elke gebruiker (**quotum voor elke gebruiker**) buiten de geplande tijd voor het lab en selecteer vervolgens **volt ooien**. 

        ![Quotum voor elke gebruiker](../media/tutorial-setup-classroom-lab/quota-for-each-user.png)
5. Het volgende scherm wordt weer gegeven met de status van het maken van een VM-sjabloon. Het maken van de sjabloon in het lab duurt maximaal 20 minuten. 

    ![Status van het maken van de sjabloon-VM](../media/tutorial-setup-classroom-lab/create-template-vm-progress.png)
1. Selecteer op de pagina **sjabloon** de optie **sjabloon aanpassen** op de werk balk. 

    ![Knop sjabloon aanpassen](../media/how-to-create-manage-template/customize-template-button.png)
2. Selecteer **door gaan**in het dialoog venster **sjabloon aanpassen** . Zodra u de sjabloon hebt gestart en wijzigingen hebt aangebracht, heeft deze niet langer dezelfde instellingen als de virtuele machines die voor het laatst zijn gepubliceerd naar uw gebruikers. Sjabloon wijzigingen worden pas weer gegeven op de bestaande virtuele machines van uw gebruikers nadat u de publicatie opnieuw hebt gepubliceerd.

    ![Het dialoog venster aanpassen](../media/how-to-create-manage-template/customize-template-dialog.png)
1. Selecteer de knop **verbinding maken met sjabloon** op de werk balk om verbinding te maken met de VM van de sjabloon om geneste virtualisatie te configureren. en volg de instructies. Als het een Windows-computer is, ziet u een optie voor het downloaden van het RDP-bestand. 

    ![Verbinding maken met de sjabloon-VM](../media/how-to-create-manage-template/connect-template-vm.png) 
9. In de virtuele machine van de sjabloon stelt u geneste virtualisatie in en configureert u een virtueel netwerk met meerdere virtuele machines. Zie [geneste virtualisatie inschakelen in een Azure VM](../../virtual-machines/windows/nested-virtualization.md)voor gedetailleerde stapsgewijze instructies. Hier volgt een korte samen vatting van de stappen: 
    1. Schakel de Hyper-V-functie in op de virtuele machine van de sjabloon.
    2. Een intern virtueel netwerk met Internet connectiviteit instellen voor de geneste virtuele machines
    3. Virtuele machines maken via Hyper-V-beheer
    4. Een IP-adres aan de virtuele machines toewijzen
10. Selecteer op de pagina **sjabloon** de optie **publiceren** op de werk balk. 

    ![De knop sjabloon publiceren](../media/tutorial-setup-classroom-lab/template-page-publish-button.png)

    > [!WARNING]
    > Zodra u de sjabloon hebt gepubliceerd, kan dit niet ongedaan worden gemaakt. 
8. Op de pagina **publicatie sjabloon** voert u het aantal virtuele machines in dat u wilt maken in het lab en selecteert u vervolgens **publiceren**. 

    ![Publicatie sjabloon-aantal Vm's](../media/tutorial-setup-classroom-lab/publish-template-number-vms.png)
11. U ziet de **status van het publiceren van** de sjabloon op de pagina. Dit proces duurt maximaal een uur. 

    ![Sjabloon publiceren - voortgang](../media/tutorial-setup-classroom-lab/publish-template-progress.png)


## <a name="next-steps"></a>Volgende stappen

Nu haalt elke gebruiker één virtuele machine op die een multi-VM-omgeving bevat. Zie het volgende artikel: [gebruikers toevoegen aan het lab](tutorial-setup-classroom-lab.md#add-users-to-the-lab)voor meer informatie over het toevoegen van gebruikers aan de test omgeving en het verzenden van een registratie koppeling.
---
title: Een gegevensschijf koppelen aan een virtuele machine in Azure DevTest Labs
description: Meer informatie over het koppelen of loskoppelen van een gegevensschijf aan een virtuele machine in Azure DevTest Labs
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 9616bf38-7db8-4915-a32a-e4f40a7a56ad
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/28/2020
ms.author: spelluru
ms.openlocfilehash: 3f18425408e6526904db85eae1c3a4db41d11a58
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78198778"
---
# <a name="attach-or-detach-a-data-disk-to-a-virtual-machine-in-azure-devtest-labs"></a>Een gegevensschijf koppelen aan een virtuele machine in Azure DevTest Labs
[Azure Managed Disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview) beheert de opslagaccounts die zijn gekoppeld aan virtuele machinegegevensschijven. Een gebruiker koppelt een nieuwe gegevensschijf aan een vm, geeft het type en de grootte van de schijf op die nodig is en Azure maakt en beheert de schijf automatisch. De gegevensschijf kan vervolgens worden losgekoppeld van de VM en later opnieuw worden gekoppeld aan dezelfde VM, of worden gekoppeld aan een andere VM die van dezelfde gebruiker is.

Deze functionaliteit is handig voor het beheren van opslag of software buiten elke afzonderlijke virtuele machine. Als de opslag of software al bestaat in een gegevensschijf, kan deze eenvoudig worden gekoppeld, losgemaakt en opnieuw worden gekoppeld aan een vm die eigendom is van de gebruiker die eigenaar is van die gegevensschijf.

## <a name="attach-a-data-disk"></a>Een gegevensschijf koppelen
Voordat u een gegevensschijf aan een vm koppelt, controleert u de volgende tips:

- De grootte van de VM bepaalt hoeveel gegevensschijven u koppelen. Zie Grootte [voor virtuele machines voor](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)meer informatie.
- U een gegevensschijf alleen koppelen aan een vm die wordt uitgevoerd. Controleer of de vm wordt uitgevoerd voordat u een gegevensschijf probeert te koppelen.

### <a name="attach-a-new-disk"></a>Een nieuwe schijf koppelen
Volg deze stappen om een nieuwe beheerde gegevensschijf te maken en toe te voegen aan een vm in Azure DevTest Labs.

1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met laboratoria het gewenste lab. 
1. Selecteer in de lijst **met Mijn virtuele machines**een draaiende VM.
1. Selecteer **Schijven**in het menu aan de linkerkant .
1. Kies **Nieuw koppelen** om een nieuwe gegevensschijf te maken en deze aan de virtuele machine te koppelen.

    ![Nieuwe gegevensschijf aan een virtuele machine koppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new.png)
1. Vul het **deelvenster Nieuwe schijf koppelen in** door de naam, het type en de grootte van de gegevensschijf in te voeren.

    ![Het formulier 'nieuwe schijf koppelen' invullen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-new-form.png)
1. Selecteer **OK**.

Na enkele ogenblikken wordt de nieuwe gegevensschijf gemaakt en gekoppeld aan de VM en wordt deze weergegeven in de lijst **met gegevensschijven** voor die VM.

### <a name="attach-an-existing-disk"></a>Een bestaande schijf koppelen
Volg deze stappen om een bestaande beschikbare gegevensschijf opnieuw te koppelen aan een bestaande vm. 

1. Selecteer een draaiende VM waarvoor u een gegevensschijf opnieuw wilt koppelen.
1. Selecteer **Schijven**in het menu aan de linkerkant .
1. Selecteer **Bestaand koppelen** om een beschikbare gegevensschijf aan de VM toe te voegen.

    ![Bestaande gegevensschijf aan een virtuele machine koppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing-button.png)

1. Selecteer OK in het **deelvenster Bestaande schijf bijvoegen.**

    ![Bestaande gegevensschijf aan een virtuele machine koppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-attach-existing.png)

Na enkele ogenblikken wordt de gegevensschijf aan de VM gekoppeld en wordt deze weergegeven in de lijst **met gegevensschijven** voor die VM.

## <a name="detach-a-data-disk"></a>Een gegevensschijf ontkoppelen
Wanneer u geen gegevensschijf meer nodig hebt die aan een vm is gekoppeld, u deze eenvoudig loskoppelen. Het loskoppelen verwijdert de schijf van de VM, maar bewaart deze in opslag voor later gebruik.

Als u de bestaande gegevens op de schijf opnieuw wilt gebruiken, u deze opnieuw koppelen aan dezelfde virtuele machine of aan een andere.

### <a name="detach-from-the-vms-management-pane"></a>Los maken van het beheervenster van de VM
1. Selecteer in uw lijst met virtuele machines een VM waarop een gegevensschijf is gekoppeld.
1. Selecteer **Schijven**in het menu aan de linkerkant .
1. Selecteer in de lijst **met gegevensschijven**de gegevensschijf die u wilt loskoppelen.

    ![Gegevensschijven selecteren voor een virtuele machine](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-button.png) 
1. Selecteer **Losmaken** vanaf de bovenkant van het detailvenster van de schijf.

    ![Een gegevensschijf ontkoppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk2.png)
1. Selecteer **Ja** om te bevestigen dat u de gegevensschijf wilt loskoppelen.

De schijf is losgekoppeld en is beschikbaar om aan een andere VM te koppelen. 
### <a name="detach-from-the-labs-main-pane"></a>Los maken van het hoofdvenster van het lab
1. Selecteer **Mijn gegevensschijven**in het hoofdvenster van uw lab.
1. Klik met de rechtermuisknop op de gegevensschijf die u wilt loskoppelen – of selecteer de ellips (**...**) – en kies **Losmaken**.

    ![Een gegevensschijf ontkoppelen](./media/devtest-lab-attach-detach-data-disk/devtest-lab-detach-data-disk.png)
1. Selecteer **Ja** om te bevestigen dat u deze wilt loskoppelen.

   > [!NOTE]
   > Als een gegevensschijf al is losgekoppeld, u ervoor kiezen deze uit uw lijst met beschikbare gegevensschijven te verwijderen door **Verwijderen te**selecteren.
   >
   >

## <a name="upgrade-an-unmanaged-data-disk"></a>Een onbeheerde gegevensschijf upgraden
Als u een bestaande virtuele machine hebt die onbeheerde gegevensschijven gebruikt, u de vm eenvoudig converteren naar beheerde schijven. Hiermee wordt zowel de osschijf als eventuele gekoppelde gegevensschijven geconverteerd.

Als u een niet-beheerde gegevensschijf wilt upgraden, voert u de stappen uit die in dit artikel worden beschreven om [de gegevensschijf los](#detach-a-data-disk) te koppelen van een niet-beheerde virtuele machine. Koppel [de schijf vervolgens opnieuw aan](#attach-an-existing-disk) een beheerde vm om de gegevensschijf automatisch te upgraden van onbeheerd naar beheerd.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het beheren van gegevensschijven voor [claimbare virtuele machines.](devtest-lab-add-claimable-vm.md#unclaim-a-vm)


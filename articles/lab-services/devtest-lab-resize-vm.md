---
title: Het formaat van een vm wijzigen in een lab in Azure DevTest Labs
description: Meer informatie over het wijzigen van de grootte van een virtuele machine (VM) in Azure DevTest Labs op basis van uw behoeften aan CPU,netwerk of schijfprestaties.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76756174"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Het formaat van een vm wijzigen in een lab in Azure DevTest Labs
Een van de belangrijkste functies van Azure virtuele machines is dat u de grootte van een virtuele machine (VM) wijzigen op basis van uw behoeften aan CPU, netwerk of schijfprestaties. Azure DevTest Labs ondersteunt deze functie nu voor VM's in een lab. De functie formaat wijzigen houdt zich aan het labbeleid voor toegestane VM-formaten in het lab. Dat wil zeggen, u de grootte van een VM wijzigen in alleen toegestane formaten in het lab. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>Stappen om het formaat van een virtuele machine in een lab te wijzigen 
Ga als volgt te werk om het formaat van een vm in een lab in Azure DevTest Labs te wijzigen: 

> [!NOTE]
> Als u via een Extern bureaublad-sessie (RDP) met de VM bent verbonden, slaat u uw werk op en koppelt u de verbinding met de virtuele machine voordat u de grootte ervan wijzigt.

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met labs het lab met de vm die u wilt wijzigen.  
4. Selecteer in het linkerdeelvenster de optie **Mijn virtuele machines**. 
5. Selecteer een VM in de lijst met VM's.
6. Selecteer **Stoppen** op de werkbalk als de vm wordt uitgevoerd. Controleer de status van de bewerking in het venster **Meldingen.** Wacht tot de vm is gestopt en sluit het venster **Meldingen.** 

    ![De virtuele machine stoppen](media/devtest-lab-resize-vm/stop-vm.png)
1. Selecteer op de pagina Virtuele machine voor uw vm de optie **Grootte** onder **INSTELLINGEN** in het linkermenu.

    ![Menu Grootte](media/devtest-lab-resize-vm/size-menu.png)
1. Blader **in het** venster Een grootte kiezen door een maat te zoeken en selecteer een maat voor uw virtuele machine en klik op **Selecteren**.     
1. Controleer de status van de bewerking wijzigen in het venster **Meldingen.**

    ![Grootte van de status wijzigen](media/devtest-lab-resize-vm/resize-status.png)
10. Nadat de bewerking het formaat is voltooid, sluit u het venster **Meldingen.** 
11. Selecteer **Overzicht** in het linkermenu en selecteer **Opnieuw starten** op de werkbalk om de vm opnieuw te starten. 

## <a name="next-steps"></a>Volgende stappen
Zie Virtuele [machines wijzigen](https://azure.microsoft.com/blog/resize-virtual-machines/)voor gedetailleerde informatie over de functie voor het formaat van Azure.



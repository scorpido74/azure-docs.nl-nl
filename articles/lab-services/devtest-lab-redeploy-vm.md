---
title: Een VM opnieuw implementeren in een lab in Azure DevTest Labs | Microsoft Documenten
description: Meer informatie over het opnieuw implementeren van een virtuele machine (van het ene Azure-knooppunt naar het andere) in Azure DevTest Labs.
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
ms.date: 06/11/2018
ms.author: spelluru
ms.openlocfilehash: 4500fcfa6fbfb346a6e5c7fd045ba0046a901b91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "60561598"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Een VM opnieuw implementeren in een lab in Azure DevTest Labs
Als u geen verbinding maken met een virtuele machine (VM) in een lab via een verbinding met extern bureaublad, implementeert u de VM opnieuw en probeert u opnieuw verbinding te maken. Wanneer u een VM opnieuw implementeert, verplaatst DevTest Labs de VM van het knooppunt waarop deze wordt uitgevoerd naar een nieuw knooppunt binnen de Azure-infrastructuur. Vervolgens wordt de VM gestart met behoud van al uw configuratieopties en bijbehorende resources. Met deze functie bespaart u de tijd die u hebt besteed aan het oplossen van problemen met uw externe bureaubladverbinding of toegang tot v.s.-v.S.-v.S. in het lab. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Stappen om een VM opnieuw te implementeren in een lab 
Als u een VM opnieuw wilt implementeren in een lab in Azure DevTest Labs, neemt u de volgende stappen: 

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met labs het lab met de VM die u opnieuw wilt implementeren.  
4. Selecteer in het linkerdeelvenster de optie **Mijn virtuele machines**. 
5. Selecteer een VM in de lijst met VM's.
6. Selecteer op de pagina Virtuele machine voor uw vm de optie **Opnieuw implementeren** onder **BEWERKINGEN** in het linkermenu.

    ![Opnieuw implementeren](media/devtest-lab-redeploy-vm/redeploy.png)
7. Lees de informatie op de pagina en selecteer **Knop Opnieuw implementeren.** 9. Controleer de status van de bewerking opnieuw implementeren in het venster **Meldingen.**

    ![Status opnieuw implementeren](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het wijzigen van het formaat van een virtuele machine in Azure DevTest Labs, zie [Het formaat van een virtuele machine wijzigen.](devtest-lab-resize-vm.md)



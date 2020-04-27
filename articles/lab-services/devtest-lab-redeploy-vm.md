---
title: Een virtuele machine opnieuw implementeren in een lab in Azure DevTest Labs | Microsoft Docs
description: Meer informatie over hoe u een virtuele machine opnieuw implementeert (van het ene naar het andere Azure-knoop punt naar het andere) in Azure DevTest Labs.
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
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/26/2020
ms.locfileid: "60561598"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Een virtuele machine opnieuw implementeren in een lab in Azure DevTest Labs
Als u geen verbinding kunt maken met een virtuele machine (VM) in een lab via een verbinding met een extern bureau blad, implementeert u de VM opnieuw en probeert u opnieuw verbinding te maken. Wanneer u een virtuele machine opnieuw implementeert, verplaatst DevTest Labs de VM van het knoop punt waarop deze wordt uitgevoerd naar een nieuw knoop punt in de Azure-infra structuur. Vervolgens wordt de virtuele machine gestart en blijven alle configuratie opties en bijbehorende resources behouden. Met deze functie bespaart u de tijd die wordt besteed aan het oplossen van problemen met verbinding met extern bureau blad of toepassing op Windows-Vm's in het lab. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>Stappen voor het opnieuw implementeren van een virtuele machine in een Lab 
Voer de volgende stappen uit om een virtuele machine in een lab in Azure DevTest Labs opnieuw te implementeren: 

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
2. Selecteer **alle services**en selecteer vervolgens **DevTest Labs** in de lijst.
3. Selecteer in de lijst met Labs het lab dat de VM bevat die u opnieuw wilt implementeren.  
4. Selecteer in het linkerdeel venster **mijn virtual machines**. 
5. Selecteer een virtuele machine in de lijst met Vm's.
6. Selecteer op de pagina virtuele machine voor uw VM opnieuw **implementeren** onder **bewerkingen** in het menu links.

    ![Opnieuw implementeren](media/devtest-lab-redeploy-vm/redeploy.png)
7. Lees de informatie op de pagina en selecteer vervolgens de knop opnieuw **implementeren** . 9. Controleer de status van de bewerking voor opnieuw implementeren in het venster **meldingen** .

    ![Status opnieuw implementeren](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>Volgende stappen
Zie het [formaat van een virtuele machine wijzigen](devtest-lab-resize-vm.md)voor meer informatie over het wijzigen van het formaat van een virtuele machine in azure DevTest Labs.



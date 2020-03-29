---
title: Een VM opnieuw starten in een lab in Azure DevTest Labs | Microsoft Documenten
description: In dit artikel vindt u stappen om virtuele machines (VM) snel en eenvoudig opnieuw op te starten in Azure DevTest Labs.
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
ms.openlocfilehash: 52d3b92909483a99eb82c86b727261bbeb5f8d46
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76759991"
---
# <a name="restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Een VM opnieuw starten in een lab in Azure DevTest Labs
U snel en eenvoudig een virtuele machine opnieuw opstarten in DevTest Labs door de stappen in dit artikel te volgen. Houd rekening met het volgende voordat u een vm opnieuw start:

- De VM moet worden uitgevoerd om de functie opnieuw opnieuw op te starten.
- Als een gebruiker is verbonden met een draaiende VM wanneer deze een herstart uitvoert, moet deze opnieuw verbinding maken met de virtuele machine nadat deze opnieuw is opgestart.
- Als een artefact wordt toegepast wanneer u de VM opnieuw start, ontvangt u een waarschuwing dat het artefact mogelijk niet wordt toegepast.

    ![Waarschuwing bij het opnieuw opstarten tijdens het toepassen van artefacten](./media/devtest-lab-restart-vm/devtest-lab-restart-vm-apply-artifacts.png)


   > [!NOTE]
   > Als de VM is vastgelopen tijdens het toepassen van een artefact, u de VM-functie opnieuw starten gebruiken als een mogelijke manier om het probleem op te lossen.
   >
   >

## <a name="steps-to-restart-a-vm-in-a-lab-in-azure-devtest-labs"></a>Stappen om een VM opnieuw op te starten in een lab in Azure DevTest Labs
1. Meld u aan bij [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).
1. Selecteer **Alle services**en selecteer Vervolgens **DevTest Labs** in de lijst.
1. Selecteer in de lijst met labs het lab met de VM die u opnieuw wilt starten.
1. Selecteer in het linkerdeelvenster de optie **Mijn virtuele machines**.
1. Selecteer in de lijst met VM's een uitvoerende VM.
1. Selecteer Boven aan het VM-beheervenster de optie **Opnieuw starten**.

    ![Knop VM opnieuw starten](./media/devtest-lab-restart-vm/devtest-lab-restart-vm.png)

1. Controleer de status van de herstart door het pictogram **Meldingen** rechtsboven in het venster te selecteren.

    ![De status van de vm opnieuw opstarten weergeven](./media/devtest-lab-restart-vm/devtest-lab-restart-notification.png)

U een draaiende VM ook opnieuw starten door de ellips (...) te selecteren in de lijst van **Mijn virtuele machines**.

![VM opnieuw starten via ellipsen](./media/devtest-lab-restart-vm/devtest-lab-restart-elipses.png)

## <a name="next-steps"></a>Volgende stappen
* Zodra deze opnieuw is gestart, u opnieuw verbinding maken met de virtuele machine door **Verbinding maken** in het beheervenster te selecteren.
* De [sjabloongalerie Voor DevTest Labs Azure Resource Manager snel beginnen](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates)

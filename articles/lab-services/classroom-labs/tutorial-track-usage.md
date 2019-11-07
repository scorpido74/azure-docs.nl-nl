---
title: Gebruik van een lab bijhouden in Azure Lab Services | Microsoft Docs
description: In deze zelfstudie houdt u, als maker/eigenaar van een lab, het gebruik van uw lab bij.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 10/31/2019
ms.author: spelluru
ms.openlocfilehash: 315ebfa1460f9d9bc041925cec2451f63ac5be16
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73580238"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Zelfstudie: Gebruik van een lab bijhouden in Azure Lab Services
In deze zelfstudie leert u hoe een maker of eigenaar van een lab het gebruik van een lab kan bijhouden.

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Gebruikers weergeven die zijn geregistreerd bij uw lab
> * Het gebruik van virtuele machines in het lab weergeven
> * Virtuele machines van studenten beheren 


## <a name="view-users-registered-with-the-lab"></a>Gebruikers weergeven die zijn geregistreerd bij het lab

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts.
3. Op de pagina **Mijn labs** selecteert u het lab waarvoor u het gebruik wilt bijhouden. 
4. Selecteer **Gebruikers** in het menu links of in de tegel **Gebruikers**. U ziet studenten die zijn geregistreerd bij uw lab. Selecteer **Registratiekoppeling**, kopieer de koppeling en stuur deze naar elke nieuwe student die nog niet is geregistreerd bij uw lab. 

    ![Geregistreerde gebruikers](../media/tutorial-track-usage/registered-users.png)

    Zie [Lab-gebruikers toevoegen en beheren](how-to-configure-student-usage.md)voor meer informatie over het toevoegen en beheren van gebruikers voor het lab.

## <a name="view-the-usage-of-vms-in-the-lab"></a>Het gebruik van virtuele machines in het lab weergeven 

1. Selecteer **Virtuele machines** in het menu aan de linkerkant. 
2. Controleer of u de status van virtuele machines kunt zien en het aantal uur dat de virtuele machines actief zijn geweest. De tijd die een labeigenaar besteedt op de VM van een student, wordt niet meegeteld in de gebruikstijd die wordt weergegeven in de laatste kolom. 

    ![VM-gebruik](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Virtuele machines van studenten beheren 
Op deze pagina kunt u student-Vm's starten, stoppen of opnieuw instellen met behulp van besturings elementen in de kolom **status** of op de werk balk.

![Besturingselementen van virtuele machines](../media/tutorial-track-usage/vm-controls.png)

Zie [virtuele-machine groep instellen en beheren](how-to-set-virtual-machine-passwords.md)voor meer informatie over het beheren van de virtuele-machine groep voor het lab.

## <a name="next-steps"></a>Volgende stappen
Zie de artikelen onder [Instructiegidsen](how-to-manage-lab-accounts.md) voor meer informatie over leslokaallabs.

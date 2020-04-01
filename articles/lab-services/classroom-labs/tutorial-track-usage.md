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
ms.date: 02/10/2020
ms.author: spelluru
ms.openlocfilehash: a1a3e62646fedd468a02eac7b1a48d0b2d00fd74
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77591979"
---
# <a name="tutorial-track-usage-of-a-lab-in-azure-lab-service"></a>Zelfstudie: Gebruik van een lab bijhouden in Azure Lab Services
In deze zelfstudie leert u hoe een maker of eigenaar van een lab het gebruik van een lab kan bijhouden.

In deze zelfstudie voert u de volgende acties uit:

> [!div class="checklist"]
> * Gebruikers weergeven die zijn geregistreerd bij uw lab
> * Het gebruik van virtuele machines in het lab weergeven
> * Virtuele machines van studenten beheren 


## <a name="view-registered-users"></a>Geregistreerde gebruikers weergeven

1. Navigeer naar de [Azure Lab Services-website](https://labs.azure.com). 
2. Selecteer **Aanmelden** en voer uw referenties in. Azure Lab Services ondersteunt organisatieaccounts en Microsoft-accounts.
3. Op de pagina **Mijn labs** selecteert u het lab waarvoor u het gebruik wilt bijhouden. 
4. Selecteer **Gebruikers** in het menu links of in de tegel **Gebruikers**. U ziet studenten die zijn geregistreerd bij uw lab.  

    ![Geregistreerde gebruikers](../media/tutorial-track-usage/registered-users.png)

    Zie [Labgebruikers toevoegen en beheren](how-to-configure-student-usage.md)voor meer informatie over het toevoegen en beheren van gebruikers voor het lab.

## <a name="view-the-usage-of-vms"></a>Het gebruik van VM's weergeven

1. Selecteer **Virtuele machines** in het menu aan de linkerkant. 
2. Controleer of u de status van virtuele machines kunt zien en het aantal uur dat de virtuele machines actief zijn geweest. De tijd die een labeigenaar besteedt op de VM van een student, wordt niet meegeteld in de gebruikstijd die wordt weergegeven in de laatste kolom. 

    ![VM-gebruik](../media/tutorial-track-usage/vm-usage.png)

## <a name="manage-student-vms"></a>Virtuele machines van studenten beheren 
Op deze pagina u vm's voor studenten starten, stoppen of opnieuw instellen met besturingselementen in de **kolom Status** of op de werkbalk.

![VM-acties](../media/tutorial-track-usage/vm-controls.png)

Zie [Virtuele machinepool instellen en beheren](how-to-set-virtual-machine-passwords.md)voor meer informatie over het beheren van virtuele machinepool voor het lab.

> [!NOTE]
> Wanneer een docent een student-vm inschakelt, wordt het quotum voor de student niet beïnvloed. Quota voor een gebruiker geeft het aantal laburen op dat beschikbaar is voor de gebruiker buiten de geplande lestijd. Zie [Quota voor gebruikers instellen voor](how-to-configure-student-usage.md?#set-quotas-for-users)meer informatie over quota.

## <a name="next-steps"></a>Volgende stappen
Zie de artikelen onder [Instructiegidsen](how-to-manage-lab-accounts.md) voor meer informatie over leslokaallabs.

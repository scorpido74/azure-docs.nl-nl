---
title: Wacht woorden instellen voor virtuele machines in Azure Lab Services | Microsoft Docs
description: Meer informatie over het instellen en opnieuw instellen van wacht woorden voor virtuele machines (Vm's) in klassikale Labs van Azure Lab Services.
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
ms.date: 08/20/2019
ms.author: spelluru
ms.openlocfilehash: a4cb2abec429a790f493f95d3d16b2ff7b3eb445
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69645017"
---
# <a name="set-or-reset-password-for-virtual-machines-in-classroom-labs-instructor"></a>Wacht woord instellen of opnieuw instellen voor virtuele machines in klassikale Labs (docent)
Een Lab-eigenaar (docent) kan het wacht woord voor Vm's instellen of opnieuw instellen op het moment van het maken van de Lab (wizard Lab maken) of na het maken van het lab (op het dash board). 

## <a name="set-password-at-the-time-of-lab-creation"></a>Wacht woord instellen op het moment dat het lab wordt gemaakt
Een Lab-eigenaar (docent) kan een wacht woord voor virtuele machines in het lab instellen op de pagina **Referenties instellen** van de wizard Lab maken.

![Referenties instellen](../media/tutorial-setup-classroom-lab/set-credentials.png)

Door het **gebruik van hetzelfde wacht woord voor alle virtuele machines** op deze pagina in-of uit te scha kelen, kan een docent ervoor kiezen hetzelfde wacht woord te gebruiken voor alle vm's in het lab of dat studenten wacht woorden kunnen instellen voor hun vm's. Deze instelling is standaard ingeschakeld voor alle installatie kopieën van het Windows-en Linux-besturings systeem, met uitzonde ring van Ubuntu. Als deze instelling is uitgeschakeld, worden studenten gevraagd om een wacht woord in te stellen wanneer ze voor de eerste keer verbinding proberen te maken met de virtuele machine. 

De eigenaar van het Lab kan dit wacht woord opnieuw instellen (indien nodig) op de pagina **sjabloon configureren** van de wizard voor het maken van het lab. 

![Sjabloonpagina configureren nadat deze is voltooid](../media/tutorial-setup-classroom-lab/configure-template-after-complete.png)

De eigenaar van het Lab kan het wacht woord ook opnieuw instellen nadat het lab is gemaakt in het dash board. 

## <a name="reset-password-on-the-dashboard"></a>Wacht woord opnieuw instellen op het dash board

1. Selecteer het overloop menu (verticaal drie punten) op de tegel Lab en selecteer **wacht woord opnieuw instellen**. 

    ![Het menu wacht woord opnieuw instellen op de start pagina](../media/how-to-set-virtual-machine-passwords/reset-password-menu-dashboard.png)
1. Voer een wacht woord in het dialoog venster **wacht woord instellen** in en selecteer **wacht woord instellen**.
    
    ![Het dialoog venster wacht woord instellen](../media/how-to-set-virtual-machine-passwords/set-password.png)

## <a name="next-steps"></a>Volgende stappen
Zie het volgende artikel voor meer informatie over andere gebruiks opties voor studenten die u (als een Lab-eigenaar) kunt configureren: Het gebruik van de [student configureren](how-to-configure-student-usage.md).

Zie [wacht woord instellen of opnieuw instellen voor virtuele machines in klassikale Labs (studenten)](how-to-set-virtual-machine-passwords-student.md)voor meer informatie over hoe studenten wacht woorden opnieuw kunnen instellen voor hun vm's.
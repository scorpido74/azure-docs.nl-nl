---
title: Automatisch afsluiten van Vm's in Azure Lab Services configureren
description: In dit artikel wordt beschreven hoe u automatisch afsluiten van Vm's in het lab-account configureert.
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
ms.date: 05/15/2020
ms.author: spelluru
ms.openlocfilehash: 787b9aa3738335ee0261ab94253cf828c232bd44
ms.sourcegitcommit: e3c28affcee2423dc94f3f8daceb7d54f8ac36fd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84896016"
---
# <a name="configure-automatic-shutdown-of-vms-on-disconnect-setting-for-a-lab-account"></a>Automatisch afsluiten van Vm's configureren bij het verbreken van de instelling voor een Lab-account
U kunt automatisch afsluiten van Windows Lab-Vm's (sjabloon of student) in-of uitschakelen nadat de verbinding met een extern bureau blad is verbroken. U kunt ook opgeven hoe lang Lab-services moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat automatisch wordt afgesloten.

## <a name="enable-automatic-shutdown"></a>Automatisch afsluiten inschakelen

1. Selecteer op de pagina **Lab-account** de optie **Labs-instellingen** in het menu links.
2. Selecteer de optie **virtuele machines automatisch afsluiten wanneer gebruikers de verbinding verbreken** .
3. Geef op hoe lang Lab-services moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat de virtuele machines automatisch worden afgesloten.

    ![Instelling voor automatisch afsluiten bij Lab-account](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

    Deze instelling is van toepassing op alle Labs die in het lab-account is gemaakt. Een Lab Creator (docenten) kan deze instelling onderdrukken op het niveau van de test omgeving. De wijziging van deze instelling op het lab-account is alleen van invloed op de Labs die zijn gemaakt nadat de wijziging is aangebracht.

    Als u deze instelling wilt uitschakelen, schakelt u het selectie vakje voor de **virtuele machines automatisch afsluiten wanneer gebruikers de verbinding verbreken** op deze pagina uit. 

## <a name="next-steps"></a>Volgende stappen
Zie [dit artikel](how-to-enable-shutdown-disconnect.md) voor meer informatie over hoe een Lab-eigenaar deze instelling kan configureren of negeren op het niveau van de test omgeving

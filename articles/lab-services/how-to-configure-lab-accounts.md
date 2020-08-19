---
title: Automatisch afsluiten van Vm's in Azure Lab Services configureren
description: In dit artikel wordt beschreven hoe u automatisch afsluiten van Vm's in het lab-account configureert.
ms.topic: article
ms.date: 08/17/2020
ms.openlocfilehash: 8dbc54ad14530006b56bf336d9d78e7d59843485
ms.sourcegitcommit: d661149f8db075800242bef070ea30f82448981e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88603904"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab-account"></a>Automatisch afsluiten van Vm's voor een Lab-account configureren

U kunt verschillende functies voor het automatisch afsluiten van kosten beheer inschakelen om extra kosten te voor komen wanneer de virtuele machines niet actief worden gebruikt. De combi natie van de volgende drie functies voor automatisch afsluiten en verbreken van de meeste gevallen, waarbij gebruikers per ongeluk hun virtuele machines verlaten:
 
- Automatisch de verbinding van gebruikers met virtuele machines die het besturings systeem inactief acht (alleen Windows) verbreken.
- Virtuele machines automatisch uitschakelen wanneer gebruikers de verbinding verbreken (Windows & Linux).
- Virtuele machines die zijn gestart automatisch afsluiten, maar gebruikers maken geen verbinding.

Bekijk meer informatie over de functies voor automatisch afsluiten in het gedeelte [kosten beheer maximaliseren met instellingen voor automatisch afsluiten](cost-management-guide.md#maximize-cost-control-with-auto-shutdown-settings) .

## <a name="enable-automatic-shutdown"></a>Automatisch afsluiten inschakelen

1. Ga in het [Azure Portal](https://portal.azure.com/) naar de pagina **Lab-account** .
1. Selecteer **Labs-instellingen** in het menu links.
1. Selecteer de instelling (en) voor automatisch afsluiten die geschikt is voor uw scenario.  

    > [!div class="mx-imgBorder"]
    > ![Instelling voor automatisch afsluiten bij Lab-account](./media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)
    
    De instelling (en) zijn van toepassing op alle Labs die in het lab-account zijn gemaakt. Een Lab Creator (docenten) kan deze instelling onderdrukken op het niveau van de test omgeving. De wijziging van deze instelling op het lab-account is alleen van invloed op de Labs die zijn gemaakt nadat de wijziging is aangebracht.

    Schakel de selectie vakjes op deze pagina uit om de instelling (en) uit te scha kelen. 

## <a name="next-steps"></a>Volgende stappen

Zie [automatisch afsluiten van vm's voor een lab configureren](how-to-enable-shutdown-disconnect.md) voor meer informatie over hoe een Lab-eigenaar deze instelling kan configureren of negeren op het niveau van de Lab

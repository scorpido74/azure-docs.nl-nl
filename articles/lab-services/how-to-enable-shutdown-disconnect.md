---
title: Automatisch afsluiten van Vm's voor een lab in Azure Lab Services configureren
description: Meer informatie over het in-of uitschakelen van automatisch afsluiten van Vm's wanneer een verbinding met een extern bureau blad wordt verbroken.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 67b51941207fdd4913df9a92362959bbd468d336
ms.sourcegitcommit: b4f303f59bb04e3bae0739761a0eb7e974745bb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91649878"
---
# <a name="configure-automatic-shutdown-of-vms-for-a-lab"></a>Automatisch afsluiten van Vm's voor een lab configureren

Dit artikel laat u zien hoe u automatisch afsluiten van Vm's voor een lab kunt configureren.

U kunt verschillende functies voor het automatisch afsluiten van kosten beheer inschakelen om extra kosten te voor komen wanneer de virtuele machines niet actief worden gebruikt. De combi natie van de volgende drie functies voor automatisch afsluiten en verbreken van de meeste gevallen, waarbij gebruikers per ongeluk hun virtuele machines verlaten:
 
* Automatisch de verbinding van gebruikers met virtuele machines die het besturings systeem inactief bewaart.
* Virtuele machines automatisch uitschakelen wanneer gebruikers de verbinding verbreken.
* Virtuele machines die zijn gestart automatisch afsluiten, maar gebruikers maken geen verbinding.

Bekijk meer informatie over de functies voor automatisch afsluiten in het gedeelte [kosten beheer maximaliseren met instellingen voor automatisch afsluiten](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

Een Lab-account beheerder kan deze instelling configureren voor het lab-account waarin u Labs maakt. Zie [Configure Automatic Shutdown of vm's voor een Lab-account](how-to-configure-lab-accounts.md)voor meer informatie. Als eigenaar van het lab kunt u de instelling onderdrukken wanneer u een Lab maakt of nadat het lab is gemaakt. 

## <a name="configure-for-the-lab-level"></a>Configureren voor het lab-niveau

U kunt de instelling voor automatisch afsluiten configureren in het [Azure Lab Services](https://labs.azure.com/).

* bij het maken van een Lab (in een **Lab-beleid**) of
* Nadat het lab is gemaakt (in **instellingen**)

> [!div class="mx-imgBorder"]
> ![Configureren op het moment dat het lab wordt gemaakt](./media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

Controleer de informatie over automatisch afsluiten in het gedeelte [kosten beheer maximaliseren met instellingen voor automatisch afsluiten](cost-management-guide.md#automatic-shutdown-settings-for-cost-control) .

> [!WARNING]
> Als u het Linux-of Windows-besturings systeem op een virtuele machine afsluit voordat u de verbinding met een RDP-sessie met de virtuele machine verbreekt, werkt de functie automatisch afsluiten niet goed.  
## <a name="next-steps"></a>Volgende stappen

[Dash board voor klassikale Labs](use-dashboard.md)

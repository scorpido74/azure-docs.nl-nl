---
title: Labaccounts configureren in Azure Lab Services | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u een labaccount maakt, alle labaccounts bekijkt of een labaccount verwijdert in Azure Lab Services.
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
ms.date: 02/14/2020
ms.author: spelluru
ms.openlocfilehash: fa9dba62b3b58687ec6a2bfc29e8722f7016b679
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284302"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Labaccounts configureren in Azure Lab Services 
In Azure Lab Services is een labaccount een container voor beheerde labtypen, zoals klaslabs. Een beheerder stelt een labaccount in met Azure Lab Services en biedt toegang tot labeigenaren die labs in het account kunnen maken. 

In dit artikel ziet u hoe u de volgende taken uitvoert: 

- Een adresbereik opgeven voor VM's in het lab
- Automatische uitschakeling van VM's configureren bij het verbreken

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Een adresbereik opgeven voor VM's in het lab
De volgende procedure heeft stappen om een adresbereik voor VM's in het lab op te geven. Als u het bereik bijwerkt dat u eerder hebt opgegeven, is het gewijzigde adresbereik alleen van toepassing op VM's die zijn gemaakt nadat de wijziging is aangebracht. 

Hier volgen enkele beperkingen bij het opgeven van het adresbereik waarmee u rekening moet houden. 

- Het voorvoegsel moet kleiner zijn dan of gelijk zijn aan 23. 
- Als een virtueel netwerk is gekoppeld aan het labaccount, kan het opgegeven adresbereik niet overlappen met het adresbereik van peered virtueel netwerk.

1. Selecteer op de pagina **Lab-account** **de optie Labs-instellingen** in het linkermenu.
2. Geef voor het veld **Adresbereik** het adresbereik op voor VM's die in het lab worden gemaakt. Het adresbereik moet zich bevinden in de cidr-notatie (classless inter-domain routing) (voorbeeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adresbereik.
3. Selecteer **Opslaan** op de werkbalk. 

    ![Adresbereik configureren](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatisch afsluiten van VM's bij loskoppelen
U het automatisch afsluiten van Windows-lab-VM's (sjabloon of student) inschakelen of uitschakelen nadat de verbinding met een extern bureaublad is verbroken. U ook opgeven hoe lang de VM's moeten wachten tot de gebruiker opnieuw verbinding maakt voordat hij automatisch wordt afgesloten.

![Automatische instelling voor afsluiten bij labaccount](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Deze instelling is van toepassing op alle labs die in het labaccount zijn gemaakt. Een labeigenaar kan deze instelling op labniveau overschrijven. De wijziging in deze instelling op het labaccount heeft alleen invloed op labs die zijn gemaakt nadat de wijziging is aangebracht.

Zie [dit artikel](how-to-enable-shutdown-disconnect.md) voor meer informatie over hoe een labeigenaar deze instelling op labniveau kan configureren

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Toestaan dat de labmaker de lablocatie kiest](allow-lab-creator-pick-lab-location.md)
- [Verbind het netwerk van uw lab met een virtueel netwerk van collega's](how-to-connect-peer-virtual-network.md)
- [Een gedeelde afbeeldingsgalerie aan een lab koppelen](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als eigenaar van een lab](how-to-add-user-lab-owner.md)
- [Firewall-instellingen voor een lab weergeven](how-to-configure-firewall-settings.md)

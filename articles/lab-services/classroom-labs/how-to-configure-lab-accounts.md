---
title: Lab-accounts configureren in Azure Lab Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe u een Lab-account maakt, alle Lab-accounts weergeeft of een Lab-account verwijdert in Azure Lab Services.
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
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79284302"
---
# <a name="configure-lab-accounts-in-azure-lab-services"></a>Lab-accounts configureren in Azure Lab Services 
In Azure Lab Services is een Lab-account een container voor beheerde Lab-typen zoals klassikale Labs. Een beheerder stelt een Lab-account in met Azure Lab Services en biedt toegang tot Lab-eigen aars die in het account Labs kunnen maken. 

In dit artikel wordt beschreven hoe u de volgende taken uitvoert: 

- Geef een adres bereik voor Vm's op in het lab
- Automatisch afsluiten van Vm's configureren bij het verbreken van de verbinding

## <a name="specify-an-address-range-for-vms-in-the-lab"></a>Geef een adres bereik voor Vm's op in het lab
De volgende procedure bevat stappen voor het opgeven van een adres bereik voor Vm's in het lab. Als u het bereik bijwerkt dat u eerder hebt opgegeven, is het gewijzigde adres bereik alleen van toepassing op virtuele machines die zijn gemaakt nadat de wijziging is aangebracht. 

Hier volgen enkele beperkingen bij het opgeven van het adres bereik dat u moet onthouden. 

- Het voor voegsel moet kleiner zijn dan of gelijk zijn aan 23. 
- Als een virtueel netwerk is gekoppeld aan het lab-account, kan het gegeven adres bereik niet overlappen met het adres bereik van een gekoppeld virtueel netwerk.

1. Selecteer op de pagina **Lab-account** de optie **Labs-instellingen** in het menu links.
2. Geef in het veld **adres bereik** het adres bereik op voor virtuele machines die in het lab worden gemaakt. Het adres bereik moet in de CIDR-notatie (Classless Inter-Domain Routing) staan (voor beeld: 10.20.0.0/23). Virtuele machines in het lab worden gemaakt in dit adres bereik.
3. Selecteer **Opslaan** op de werkbalk. 

    ![Adres bereik configureren](../media/how-to-manage-lab-accounts/labs-configuration-page-address-range.png)


## <a name="automatic-shutdown-of-vms-on-disconnect"></a>Automatisch afsluiten van Vm's bij het verbreken van de verbinding
U kunt automatisch afsluiten van Windows Lab-Vm's (sjabloon of student) in-of uitschakelen nadat de verbinding met een extern bureau blad is verbroken. U kunt ook opgeven hoe lang de Vm's moeten wachten totdat de gebruiker opnieuw verbinding maakt voordat deze automatisch wordt afgesloten.

![Instelling voor automatisch afsluiten bij Lab-account](../media/how-to-configure-lab-accounts/automatic-shutdown-vm-disconnect.png)

Deze instelling is van toepassing op alle Labs die in het lab-account is gemaakt. Een Lab-eigenaar kan deze instelling overschrijven op het niveau van de test omgeving. De wijziging van deze instelling op het lab-account is alleen van invloed op de Labs die zijn gemaakt nadat de wijziging is aangebracht.

Zie [dit artikel](how-to-enable-shutdown-disconnect.md) voor meer informatie over hoe een Lab-eigenaar deze instelling kan configureren op het niveau van de test omgeving

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Lab-Maker toestaan locatie van test omgeving te kiezen](allow-lab-creator-pick-lab-location.md)
- [Uw Lab-netwerk verbinden met een virtueel peer netwerk](how-to-connect-peer-virtual-network.md)
- [Een galerie met gedeelde afbeeldingen koppelen aan een Lab](how-to-attach-detach-shared-image-gallery.md)
- [Een gebruiker toevoegen als een Lab-eigenaar](how-to-add-user-lab-owner.md)
- [Firewall instellingen voor een Lab weer geven](how-to-configure-firewall-settings.md)

---
title: Afsluiten van VM's inschakelen bij het loskoppelen van Azure Lab Services | Microsoft Documenten
description: Meer informatie over het in- of uitschakelen van automatische uitschakeling van VM's wanneer de verbinding met een extern bureaublad is verbroken.
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
ms.date: 12/13/2019
ms.author: spelluru
ms.openlocfilehash: 68a27a325a0ef02c6eeea9867a21ba0e24ab5321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117128"
---
# <a name="enable-automatic-shutdown-of-vms-on-disconnect"></a>Automatische uitschakeling van VM's inschakelen bij het verbreken
In dit artikel ziet u hoe u het automatisch afsluiten van Windows 10-labVM's (sjabloon of student) inschakelen of uitschakelen nadat de verbinding met een extern bureaublad is verbroken. **Windows 10** U ook opgeven hoe lang de VM's moeten wachten tot de gebruiker opnieuw verbinding maakt voordat hij automatisch wordt afgesloten.

Een labaccountbeheerder kan deze instelling configureren voor het labaccount waarin u labs maakt. Zie [Automatische uitschakeling van VM's configureren bij het verbreken van de verbinding voor een labaccount voor](how-to-configure-lab-accounts.md#automatic-shutdown-of-vms-on-disconnect)meer informatie. Als eigenaar van een lab u de instelling overschrijven bij het maken van een lab of nadat het lab is gemaakt. 

## <a name="configure-when-creating-a-lab"></a>Configureren bij het maken van een lab
Op de wizard Stap 3 van de wizard Lab maken u deze functie in- of uitschakelen en ook opgeven hoe lang de vm moet wachten tot de gebruiker opnieuw verbinding maakt voordat hij automatisch wordt afgesloten. 

![Configureren op het moment van het maken van het lab](../media/how-to-enable-shutdown-disconnect/configure-lab-creation.png)

## <a name="configure-after-the-lab-is-created"></a>Configureren nadat het lab is gemaakt
U deze instelling configureren op de pagina **Instellingen** zoals weergegeven in de volgende afbeelding: 

![Configureren nadat het lab is gemaakt](../media/how-to-enable-shutdown-disconnect/configure-lab-automatic-shutdown.png)

> [!WARNING]
> Als u het Windows-besturingssysteem (OS) op een vm afsluit voordat u een RDP-sessie loskoppelt van de VM, werkt de functie automatisch afsluiten niet goed.  

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen:

- [Dashboard voor klaslokalen](use-dashboard.md)
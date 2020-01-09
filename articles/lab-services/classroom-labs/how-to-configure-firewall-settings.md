---
title: Firewall instellingen voor Azure Lab Services
description: Informatie over het bepalen van het open bare IP-adres en het poort nummer bereik van virtuele machines in een Lab zodat informatie kan worden toegevoegd aan de firewall regels.
author: emaher
ms.author: enewman
ms.date: 12/12/2019
ms.topic: article
ms.service: lab-services
ms.openlocfilehash: da1614e4a3e02ed91ef2d3c59ac4eb3eac0dcc7c
ms.sourcegitcommit: 2f8ff235b1456ccfd527e07d55149e0c0f0647cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75692769"
---
# <a name="firewall-settings-for-azure-lab-services"></a>Firewall instellingen voor Azure Lab Services

Elke organisatie of school zal hun eigen netwerk instellen op een manier die het beste past bij hun behoeften.  Soms omvat het instellen van firewall regels die Remote Desktop Protocol (RDP) of ssh-verbindingen (Secure Shell) met computers buiten hun netwerk blok keren.  Omdat Azure Lab Services in de open bare Cloud wordt uitgevoerd, is er mogelijk een extra configuratie nodig om studenten toegang te geven tot hun virtuele machine bij het maken van verbinding via het netwerk van de campus.

Elk Lab maakt gebruik van één openbaar IP-adres en meerdere poorten.  Alle Vm's, zowel de virtuele machine als de student-Vm's, gebruiken dit open bare IP-adres.  Het open bare IP-adres wordt niet gewijzigd voor de levens duur van het lab.  Elke VM heeft echter een ander poort nummer.  De poort nummers variëren van 49152 tot 65535.  De combi natie van het open bare IP-adres en het poort nummer wordt gebruikt om docenten en studenten te verbinden met de juiste VM.  In dit artikel wordt beschreven hoe u het specifieke open bare IP-adres kunt vinden dat wordt gebruikt door een lab.  Deze informatie kan worden gebruikt om binnenkomende en uitgaande firewall regels bij te werken zodat studenten toegang hebben tot hun Vm's.

>[!IMPORTANT]
>Elk lab heeft een ander openbaar IP-adres.

## <a name="find-public-ip-for-a-lab"></a>Een openbaar IP-adres voor een Lab zoeken

De open bare IP-adressen voor elk lab worden weer gegeven op de Blade **alle Labs** van het lab-account voor Lab-Services.  Zie [Labs beheren in een Lab-account](how-to-manage-lab-accounts.md#view-and-manage-labs-in-the-lab-account)voor instructies voor het vinden van de Blade **alle Labs** .  

> [!div class="mx-imgBorder"]
> ![de Blade alle Labs](../media/how-to-configure-firewall-settings/all-labs-properties.png)

>[!NOTE]
>Het open bare IP-adres wordt niet weer geven als de sjabloon machine voor uw Lab nog niet is gepubliceerd.

## <a name="conclusion"></a>Conclusie

Nu weten we het open bare IP-adres voor het lab.  Er kunnen binnenkomende en uitgaande regels worden gemaakt voor de firewall van de organisatie voor het open bare IP-adres en het poort bereik 49152-65535.  Zodra de regels zijn bijgewerkt, hebben studenten toegang tot hun Vm's zonder dat de netwerk firewall de toegang blokkeert.

---
title: Latentie voor windows Virtual Desktop-gebruikersverbinding - Azure
description: Verbindingslatentie voor Windows Virtual Desktop-gebruikers.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: a4210947d771768943775a3e62c2558fa2883bd5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128193"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Latentie van gebruikersverbinding bepalen in Windows Virtual Desktop

Windows Virtual Desktop is wereldwijd beschikbaar. Beheerders kunnen virtuele machines (VM's) maken in elke Azure-regio die ze willen. De kliklatentie zijn afhankelijk van de locatie van de gebruikers en de virtuele machines. Windows Virtual Desktop-services worden voortdurend uitgerold naar nieuwe regio's om de latentie te verbeteren. 
 
Met het [hulpprogramma voor het schatten van virtuele bureaubladervaring](https://azure.microsoft.com/services/virtual-desktop/assessment/) van Windows u bepalen wat de beste locatie is om de latentie van uw VM's te optimaliseren. We raden u aan de tool elke twee tot drie maanden te gebruiken om ervoor te zorgen dat de optimale locatie niet is gewijzigd naarmate Windows Virtual Desktop naar nieuwe gebieden uitrolt. 

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Windows Virtual Desktop gebruikt Azure Traffic Manager, waarmee de locatie van de DNS-server van de gebruiker wordt gecontroleerd om het dichtstbijzijnde Windows Virtual Desktop-serviceexemplaar te vinden. We raden beheerders aan de locatie van de DNS-server van de gebruiker te controleren voordat ze de locatie voor de VM's kiezen.

## <a name="next-steps"></a>Volgende stappen

- Zie het [hulpprogramma voor virtuele desktopervaring van Windows](https://azure.microsoft.com/services/virtual-desktop/assessment/)om de beste locatie te controleren op optimale latentie.
- Zie [Windows Virtual Desktop-prijzen](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor prijsplannen voor prijsplannen.
- Bekijk [onze zelfstudie](tenant-setup-azure-active-directory.md)om aan de slag te gaan met uw Windows Virtual Desktop-implementatie.
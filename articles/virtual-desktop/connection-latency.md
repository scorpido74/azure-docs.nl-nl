---
title: Windows-wacht tijd voor gebruikers verbinding met virtueel bureau blad-Azure
description: Verbindings latentie voor Windows-virtuele bureau blad-gebruikers.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 403cf584c79bc0a166054ae36c9d2ea50e4b9d9e
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/07/2020
ms.locfileid: "88008726"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>De latentie van gebruikers verbindingen bepalen in het virtuele bureau blad van Windows

Virtueel bureau blad van Windows is wereld wijd beschikbaar. Beheerders kunnen virtuele machines (Vm's) in elke gewenste Azure-regio maken. De latentie van de verbinding is afhankelijk van de locatie van de gebruikers en de virtuele machines. Virtuele bureau blad-services van Windows worden voortdurend geïmplementeerd naar nieuwe geografische gebieden om latentie te verbeteren.

Met het [Windows-hulp programma virtuele bureau blad-ervaring Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/) kunt u de beste locatie bepalen om de latentie van uw vm's te optimaliseren. We raden u aan om het hulp programma om de twee tot drie maanden te gebruiken om ervoor te zorgen dat de optimale locatie niet is gewijzigd, omdat Windows virtueel bureau blad naar nieuwe gebieden rolt.

## <a name="azure-traffic-manager"></a>Azure Traffic Manager

Het virtuele bureau blad van Windows maakt gebruik van de Azure-Traffic Manager, waarmee de locatie van de DNS-server van de gebruiker wordt gecontroleerd om het dichtstbijzijnde exemplaar van het Windows Virtual Desktop-service te vinden. Beheerders wordt aangeraden de locatie van de DNS-server van de gebruiker te controleren voordat u de locatie voor de virtuele machines selecteert.

## <a name="next-steps"></a>Volgende stappen

- Als u de beste locatie voor optimale latentie wilt controleren, raadpleegt u het [Windows-hulp programma Virtual Desktop Experience Estimator](https://azure.microsoft.com/services/virtual-desktop/assessment/).
- Zie [prijzen voor Windows virtueel bureau blad](https://azure.microsoft.com/pricing/details/virtual-desktop/)voor prijs plannen.
- Bekijk [onze zelf studie](./virtual-desktop-fall-2019/tenant-setup-azure-active-directory.md)om aan de slag te gaan met de implementatie van uw virtuele Windows-bureau blad.
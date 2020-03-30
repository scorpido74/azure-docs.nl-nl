---
title: Windows Virtual Desktop-hostpool load-balancing - Azure
description: Load-balancing-methoden voor hostpool voor een Windows Virtual Desktop-omgeving.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127869"
---
# <a name="host-pool-load-balancing-methods"></a>Taakverdelingsmethoden voor hostpool

Windows Virtual Desktop ondersteunt twee methoden voor het balanceren van belastingsering. Elke methode bepaalt welke sessiehost de sessie van een gebruiker host wanneer deze verbinding maakt met een resource in een hostgroep.

De volgende taakverdelingsmethoden zijn beschikbaar in Windows Virtual Desktop:

- Met breadth-first load balancing u gebruikerssessies gelijkmatig verdelen over de sessiehosts in een hostpool.
- Met diepte-first load balancing u een sessiehost verzadigen met gebruikerssessies in een hostpool. Zodra de eerste sessie de drempelwaarde voor de sessielimiet heeft bereikt, stuurt de load balancer eventuele nieuwe gebruikersverbindingen naar de volgende sessiehost in de hostgroep totdat deze de limiet heeft bereikt, enzovoort.

Elke hostpool kan slechts één type load-balancing configureren dat specifiek is voor het type. Beide methoden voor het balanceren van de taak delen echter het volgende gedrag, ongeacht in welke hostgroep ze zich bevindt:

- Als een gebruiker al een sessie in de hostgroep heeft en opnieuw verbinding maakt met die sessie, wordt deze door de load balancer met hun bestaande sessie doorgestuurd naar de sessiehost. Dit gedrag is van toepassing, zelfs als de eigenschap AllowNewConnections van die sessiehost is ingesteld op False.
- Als een gebruiker nog geen sessie in de hostgroep heeft, wordt de load balancer niet in overweging nemen bij sessiehosts waarvan de eigenschap AllowNewConnections is ingesteld op False tijdens het balanceren van de belasting.

## <a name="breadth-first-load-balancing-method"></a>Breedte-eerste load-balancing methode

Met de methode voor het in de eerste breedte laden u gebruikersverbindingen distribueren om te optimaliseren voor dit scenario. Deze methode is ideaal voor organisaties die de beste ervaring willen bieden voor gebruikers die verbinding maken met hun gepoolde virtuele desktopomgeving.

De eerste sessiehost voor de eerste methode in de breedte wordt geopperd en nieuwe verbindingen mogelijk gemaakt. De methode selecteert vervolgens de sessiehost met het minste aantal sessies. Als er een gelijkspel is, selecteert de methode de eerste sessiehost in de query.

## <a name="depth-first-load-balancing-method"></a>Diepte-eerste load-balancing methode

Met de diepte-eerste load-balancing-methode u één sessiehost tegelijk verzadigen om te optimaliseren voor dit scenario. Deze methode is ideaal voor kostenbewuste organisaties die meer gedetailleerde controle willen over het aantal virtuele machines dat ze hebben toegewezen voor een hostpool.

De eerste sessiesessie met de eerste diepte-methode wordt geopperd die nieuwe verbindingen mogelijk maakt en niet meer dan hun maximale sessielimiet heeft bereikt. De methode selecteert vervolgens de sessiehost met het hoogste aantal sessies. Als er een gelijkspel is, selecteert de methode de eerste sessiehost in de query.
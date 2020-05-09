---
title: Taak verdeling van hostgroep voor Windows Virtual Desktop-Azure
description: Methoden voor het verdelen van de hostgroep voor een virtueel-bureaublad omgeving van Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2bfbd580d0e114cf4a135879340745107183b6a
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614809"
---
# <a name="host-pool-load-balancing-methods"></a>Taakverdelingsmethoden voor hostpool

>[!IMPORTANT]
>Deze inhoud is van toepassing op de najaar 2019-release die geen ondersteuning biedt voor Azure Resource Manager virtueel-bureaublad objecten van Windows. Raadpleeg [dit artikel](../host-pool-load-balancing.md)als u probeert Azure Resource Manager virtuele Windows-bureaublad objecten te beheren die zijn geïntroduceerd in de lente 2020-update.

Virtueel bureau blad van Windows ondersteunt twee methoden voor taak verdeling. Elke methode bepaalt welke sessie-host als host fungeert voor de sessie van een gebruiker wanneer deze verbinding maakt met een bron in een hostgroep.

De volgende taakverdelings methoden zijn beschikbaar in het virtuele bureau blad van Windows:

- Met breedte-eerste taak verdeling kunt u gebruikers sessies gelijkmatig verdelen over de sessie-hosts in een hostgroep.
- Diepte-First Load Balancing biedt u de mogelijkheid om een sessie-host te verzadigen met gebruikers sessies in een hostgroep. Zodra de eerste sessie de drempel waarde voor de sessie limiet bereikt, stuurt de load balancer eventuele nieuwe gebruikers verbindingen met de volgende sessie-host in de hostgroep door totdat de limiet is bereikt, enzovoort.

Elke hostgroep kan alleen een specifiek type taak verdeling configureren. Beide taakverdelings methoden delen echter het volgende gedrag, ongeacht in welke hostgroep ze zich bevinden:

- Als een gebruiker al een sessie in de hostgroep heeft en opnieuw verbinding maakt met die sessie, stuurt de load balancer deze door naar de sessiehost met hun bestaande sessie. Dit gedrag geldt ook als de eigenschap AllowNewConnections van de sessie-host is ingesteld op false.
- Als een gebruiker nog niet beschikt over een sessie in de hostgroep, beschouwt de load balancer geen hosts voor sessies waarvan de eigenschap AllowNewConnections is ingesteld op false tijdens taak verdeling.

## <a name="breadth-first-load-balancing-method"></a>Breedte-eerste methode voor taak verdeling

Met de gelijkmatige methode voor taak verdeling kunt u gebruikers verbindingen distribueren om te optimaliseren voor dit scenario. Deze methode is ideaal voor organisaties die de beste ervaring willen bieden voor gebruikers die verbinding willen maken met hun gegroepeerde virtueel-bureaublad omgeving.

Met de methode breedte-eerste wordt eerst een query uitgevoerd op sessie-hosts die nieuwe verbindingen toestaan. De-methode selecteert vervolgens de sessiehost met het minste aantal sessies. Als er sprake is van een das, selecteert de methode de eerste sessie-host in de query.

## <a name="depth-first-load-balancing-method"></a>Diepte: eerste methode voor taak verdeling

Met de functie voor diepte en eerste taak verdeling kunt u één sessie-host op een moment verzadigen om te optimaliseren voor dit scenario. Deze methode is ideaal voor prijs bewuste organisaties die meer nauw keuriger willen controleren op het aantal virtuele machines dat ze voor een hostgroep hebben toegewezen.

De methode depth-first voert eerst een query uit op sessie-hosts die nieuwe verbindingen toestaan en de maximale sessie limiet niet hebben overschreden. De-methode selecteert vervolgens de sessiehost met het hoogste aantal sessies. Als er sprake is van een das, selecteert de-methode de eerste sessie-host in de query.
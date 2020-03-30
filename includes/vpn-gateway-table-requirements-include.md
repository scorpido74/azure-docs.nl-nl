---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dc018b5d09c9b33c10cd2d54ac6572537e05ed25
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175922"
---
In de volgende tabel worden de vereisten voor PolicyBased en RouteBased VPN-gateways weergegeven. Deze tabel is van toepassing op de Resource Manager en de klassieke implementatiemodellen. Voor het klassieke model zijn PolicyBased VPN-gateways dezelfde als statische gateways en zijn routegebaseerde gateways dezelfde als dynamische gateways.

|  | **PolicyBased Basic VPN-gateway** | **Routebased Basic VPN-gateway** | **Routebased Standard VPN-gateway** | **Routebased High Performance VPN-gateway** |
| --- | --- | --- | --- | --- |
| **Site-naar-site-connectiviteit (S2S)** |PolicyBased VPN-configuratie |RouteGebaseerde VPN-configuratie |RouteGebaseerde VPN-configuratie |RouteGebaseerde VPN-configuratie |
| **Punt-naar-site-verbinding (P2S**) |Niet ondersteund |Ondersteund (kan tegelijk bestaan met S2S) |Ondersteund (kan tegelijk bestaan met S2S) |Ondersteund (kan tegelijk bestaan met S2S) |
| **Verificatiemethode** |Vooraf gedeelde sleutel |Vooraf gedeelde sleutel voor S2S-connectiviteit, certificaten voor P2S-connectiviteit |Vooraf gedeelde sleutel voor S2S-connectiviteit, certificaten voor P2S-connectiviteit |Vooraf gedeelde sleutel voor S2S-connectiviteit, certificaten voor P2S-connectiviteit |
| **Maximumaantal S2S-verbindingen** |1 |10 |10 |30 |
| **Maximumaantal P2S-verbindingen** |Niet ondersteund |128 |128 |128 |
| **Actieve routeringondersteuning (BGP)** (*) |Niet ondersteund |Niet ondersteund |Ondersteund |Ondersteund |

  (*) BGP wordt niet ondersteund voor het klassieke implementatiemodel.

---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0e12ad66c635632b29b70000b6e227ddcbb5357b
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80501871"
---
Door de gebruiker gedefinieerde routes met een bestemming 0.0.0/0 en NSG's op het GatewaySubnet **worden niet ondersteund.** Gateways die met deze configuratie zijn gemaakt, worden geblokkeerd voor de creatie. Gateways vereisen toegang tot de beheercontrollers om goed te kunnen functioneren. [BGP-routepropagatie](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol]) moet worden ingesteld op 'Ingeschakeld' op het GatewaySubnet om de beschikbaarheid van de gateway te garanderen. Als dit is ingesteld op uitgeschakeld, werkt de gateway niet.

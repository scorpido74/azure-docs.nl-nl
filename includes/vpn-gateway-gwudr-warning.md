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
ms.openlocfilehash: 97fde67c3ac7649418ed0239a2c7aa4f1a4b3f96
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81274893"
---
Door de gebruiker gedefinieerde routes met een bestemming 0.0.0/0 en NSG's op het GatewaySubnet **worden niet ondersteund.** Gateways die met deze configuratie zijn gemaakt, worden geblokkeerd voor de creatie. Gateways vereisen toegang tot de beheercontrollers om goed te kunnen functioneren. [BGP-routepropagatie](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) moet worden ingesteld op 'Ingeschakeld' op het GatewaySubnet om de beschikbaarheid van de gateway te garanderen. Als dit is ingesteld op uitgeschakeld, werkt de gateway niet.

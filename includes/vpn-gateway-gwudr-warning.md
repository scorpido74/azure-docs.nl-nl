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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81274893"
---
Door de gebruiker gedefinieerde routes met een doel van 0.0.0.0/0 en Nsg's op de GatewaySubnet **worden niet ondersteund**. Gateways die met deze configuratie zijn gemaakt, worden geblokkeerd en kunnen niet worden gemaakt. Gateways hebben toegang tot de beheer controllers nodig om goed te kunnen functioneren. [Doorgifte van BGP-route](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) moet worden ingesteld op ingeschakeld op de GatewaySubnet om de beschik baarheid van de gateway te garanderen. Als deze optie is ingesteld op uitgeschakeld, werkt de gateway niet.

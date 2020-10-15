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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "81274893"
---
Door gebruikers gedefinieerde routes met de bestemming 0.0.0.0.0/0 en NSG's in het GatewaySubnet **worden niet ondersteund**. Gateways die gemaakt zijn met deze configuratie, worden geblokkeerd en kunnen niet worden gemaakt. Gateways vereisen toegang tot de management controllers om correct te kunnen funcioneren. [Doorgifte van BGP-route](https://docs.microsoft.com/azure/virtual-network/virtual-networks-udr-overview#border-gateway-protocol) moet worden ingesteld op 'Ingeschakeld' in het GatewaySubnet om voor de beschikbaarheid van de gateway te zorgen. Als deze optie is uitgeschakeld, functioneert de gateway niet.

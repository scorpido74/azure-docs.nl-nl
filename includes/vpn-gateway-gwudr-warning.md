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
ms.openlocfilehash: a852807ab685e85b76d26e5b39c99a32f645bbd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "71838165"
---
Door de gebruiker gedefinieerde routes met een bestemming 0.0.0/0 en NSG's op het GatewaySubnet **worden niet ondersteund.** Gateways die met deze configuratie zijn gemaakt, worden geblokkeerd voor de creatie. Gateways vereisen toegang tot de beheercontrollers om goed te kunnen functioneren.

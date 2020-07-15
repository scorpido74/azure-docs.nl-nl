---
title: Azure PowerShell-voorbeelden voor virtueel netwerk
description: Azure PowerShell-voorbeelden voor virtueel netwerk.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: ''
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: sample
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: kumud
ms.openlocfilehash: 1e1bac855ef943bdddcc82966ff3b824bcbe2ad0
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232216"
---
# <a name="azure-powershell-samples-for-virtual-network"></a>Azure PowerShell-voorbeelden voor virtueel netwerk

De volgende tabel bevat links naar Azure PowerShell-scripts:

| Script | Beschrijving |
|----|----|
| [Een virtueel netwerk maken voor toepassingen met meerdere lagen](./scripts/virtual-network-powershell-sample-multi-tier-application.md) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Verkeer naar het front-end-subnet wordt beperkt tot HTTP, terwijl verkeer naar het back-end-subnet wordt beperkt tot SQL, poort 1433. |
| [Peering van twee virtuele netwerken](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md) | Hiermee worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden. |
| [Verkeer routeren via een virtueel netwerkapparaat](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md) | Hiermee wordt een virtueel netwerk gemaakt met front-end- en back-end-subnetten en een VM die verkeer tussen de twee subnetten kan routeren. |
| [Binnenkomend en uitgaand VM-netwerkverkeer filteren](./scripts/virtual-network-powershell-sample-filter-network-traffic.md) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Binnenkomend netwerkverkeer naar het front-end-subnet wordt beperkt tot HTTP en HTTPS. Uitgaand verkeer van het back-end-subnet naar internet is niet toegestaan. |
|[Dual-stack virtueel netwerk IPv4 + IPv6 configureren met Basic Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack.md)|Hiermee wordt een dual-stack (IPv4+IPv6) virtueel netwerk geïmplementeerd met twee virtuele machines en een Azure Basic Load Balancer met openbare IPv4- en IPv6-IP-adressen. |
|[Dual-stack virtueel netwerk IPv4 + IPv6 configureren met Standard Load Balancer](./scripts/virtual-network-powershell-sample-ipv6-dual-stack-standard-load-balancer.md)|Implementeert een dual-stack (IPv4+IPv6) virtueel netwerk met twee virtuele machines en een Azure Standard Load Balancer met openbare IPv4- en IPv6-IP-adressen. |

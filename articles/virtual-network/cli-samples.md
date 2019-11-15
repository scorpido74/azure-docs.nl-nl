---
title: Azure CLI-voorbeelden voor virtueel netwerk
description: Azure CLI-voorbeelden voor virtueel netwerk.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: twooley
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
ms.openlocfilehash: ff1017c8a6bc3c2806da6ea2a53b931aaf28644f
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092101"
---
# <a name="azure-cli-samples-for-virtual-network"></a>Azure CLI-voorbeelden voor virtueel netwerk

De volgende tabel bevat koppelingen naar bash-scripts met Azure CLI-opdrachten:

| | |
|----|----|
| [Een virtueel netwerk maken voor toepassingen met meerdere lagen](./scripts/virtual-network-cli-sample-multi-tier-application.md) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Verkeer naar het front-end-subnet wordt beperkt tot HTTP en SSH, terwijl het verkeer naar het back-end-subnet wordt beperkt tot MySQL, poort 3306. |
| [Peering van twee virtuele netwerken](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md) | Hiermee worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden. |
| [Verkeer routeren via een virtueel netwerkapparaat](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md) | Hiermee wordt een virtueel netwerk gemaakt met front-end- en back-end-subnetten en een VM die verkeer tussen de twee subnetten kan routeren. |
| [Binnenkomend en uitgaand VM-netwerkverkeer filteren](./scripts/virtual-network-cli-sample-filter-network-traffic.md) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Binnenkomend netwerkverkeer naar het front-end-subnet wordt beperkt tot HTTP, HTTPS en SSH. Uitgaand verkeer van het back-end-subnet naar internet is niet toegestaan. |
|[Het virtuele netwerk van IPv4 en IPv6 Dual stack configureren met de basis Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack.md)|Hiermee wordt een virtueel netwerk met dubbele stack (IPv4 + IPv6) geïmplementeerd met twee virtuele machines en een Azure Basic-Load Balancer met open bare IPv4-en IPv6-adressen. |
|[Virtueel netwerk van IPv4 + IPv6 met dubbele stack configureren met Standard Load Balancer](./scripts/virtual-network-cli-sample-ipv6-dual-stack-standard-load-balancer.md)|Hiermee wordt een virtueel netwerk met dubbele stack (IPv4 + IPv6) geïmplementeerd met twee virtuele machines en een Azure-Standard Load Balancer met open bare IPv4-en IPv6-adressen. |
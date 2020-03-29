---
title: Azure CLI-voorbeelden - Netwerken
description: Azure CLI-voorbeelden
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: timlt
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 04/25/2017
ms.author: kumud
ms.openlocfilehash: 21d8896e4f87b432908a2ccf6a994a5c29d4b293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840516"
---
# <a name="azure-cli-samples-for-networking"></a>Azure CLI-voorbeelden voor netwerken

De volgende tabel bevat koppelingen naar bash-scripts die zijn gebouwd met behulp van de Azure CLI.

| | |
|-|-|
|**Connectiviteit tussen Azure-resources**||
| [Een virtueel netwerk maken voor toepassingen met meerdere lagen](./scripts/virtual-network-cli-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Verkeer naar het front-end-subnet wordt beperkt tot HTTP en SSH, terwijl het verkeer naar het back-end-subnet wordt beperkt tot MySQL, poort 3306. |
| [Peering van twee virtuele netwerken](./scripts/virtual-network-cli-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden. |
| [Verkeer routeren via een virtueel netwerkapparaat](./scripts/virtual-network-cli-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk gemaakt met front-end- en back-end-subnetten en een VM die verkeer tussen de twee subnetten kan routeren. |
| [Binnenkomend en uitgaand VM-netwerkverkeer filteren](./scripts/virtual-network-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Inkomend netwerkverkeer naar het front-end subnet is beperkt tot HTTP, HTTPS en SSH. Uitgaand verkeer naar het internet vanaf het back-end subnet is niet toegestaan. |
|**Taakverdeling en verkeersrichting**||
| [Load balance meerdere websites op VM's](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee maakt u twee VM's met meerdere IP-configuraties, gekoppeld aan een Azure Availability Set, die toegankelijk is via een Azure Load Balancer. |
| [Direct verkeer in meerdere regio's voor een hoge beschikbaarheid van toepassingen](./scripts/traffic-manager-cli-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Hiermee maakt u twee app-serviceplannen, twee web-apps, een profiel van verkeersbeheer en twee eindpunten voor verkeersbeheer. |
| | |

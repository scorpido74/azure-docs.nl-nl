---
title: Azure PowerShell-voor beelden-netwerken
description: Voorbeelden van Azure PowerShell
services: virtual-network
documentationcenter: virtual-network
author: KumudD
manager: mtillman
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 05/24/2017
ms.author: gwallace
ms.openlocfilehash: fb4b63d8329f0a175252b819653e192c746489b6
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/14/2019
ms.locfileid: "74092021"
---
# <a name="azure-powershell-samples-for-networking"></a>Voor beelden Azure PowerShell voor netwerken

De volgende tabel bevat koppelingen naar scripts die zijn gebouwd met behulp van Azure PowerShell.

| | |
|-|-|
|**Connectiviteit tussen Azure-resources**||
| [Een virtueel netwerk maken voor toepassingen met meerdere lagen](./scripts/virtual-network-powershell-sample-multi-tier-application.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Verkeer naar het front-end-subnet wordt beperkt tot HTTP, terwijl verkeer naar het back-end-subnet wordt beperkt tot SQL, poort 1433. |
| [Peering van twee virtuele netwerken](./scripts/virtual-network-powershell-sample-peer-two-virtual-networks.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee worden twee virtuele netwerken in dezelfde regio gemaakt en verbonden. |
| [Verkeer routeren via een virtueel netwerkapparaat](./scripts/virtual-network-powershell-sample-route-traffic-through-nva.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk gemaakt met front-end- en back-end-subnetten en een VM die verkeer tussen de twee subnetten kan routeren. |
| [Binnenkomend en uitgaand VM-netwerkverkeer filteren](./scripts/virtual-network-powershell-filter-network-traffic.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee wordt een virtueel netwerk met front-end- en back-end-subnetten gemaakt. Binnenkomend netwerk verkeer naar het front-end-subnet is beperkt tot HTTP en HTTPS.. Uitgaand verkeer naar Internet vanuit het back-end-subnet is niet toegestaan. |
|**Taak verdeling en verkeers richting**||
| [Taakverdeling voor verkeer naar VM's voor hoge beschikbaarheid](./scripts/load-balancer-windows-powershell-sample-nlb.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee maakt u meerdere virtuele machines in een maximaal beschikbare en configuratie van taakverdeling. |
| [Taakverdeling instellen voor meerdere websites op virtuele machines](./scripts/load-balancer-windows-powershell-load-balance-multiple-websites-vm.md?toc=%2fazure%2fnetworking%2ftoc.json) | Hiermee maakt twee virtuele machines met meerdere IP-configuraties, lid is van een Azure-Beschikbaarheidsset toegankelijk zijn via een Azure Load Balancer. |
| [Direct verkeer tussen meerdere regio's voor hoge Beschik baarheid van toepassingen](./scripts/traffic-manager-powershell-websites-high-availability.md?toc=%2fazure%2fnetworking%2ftoc.json) |  Hiermee maakt u twee app service-abonnementen, twee web-apps, een Traffic Manager-profiel en twee Traffic Manager-eind punten. |
| | |

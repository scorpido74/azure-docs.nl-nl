---
title: Azure CLI-monsters voor load balancer
titleSuffix: Azure Load Balancer
description: Azure CLI-voorbeelden
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: f35007bc3c47aeec9bcdd8a418983b95f6f20ad6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225423"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Azure CLI-monsters voor load balancer

De volgende tabel bevat koppelingen naar bash-scripts die zijn gebouwd met behulp van de Azure CLI.

| | |
|-|-|
| [Verkeer verdelen naar virtuele machines voor hoge beschikbaarheid](./scripts/load-balancer-linux-cli-sample-nlb.md) | Hiermee worden verschillende virtuele machines gemaakt in een zeer beschikbare en laadgebalanceerde configuratie. |
| [Taken over VM's in meerdere beschikbaarheidszones verdelen](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md) | Hiermee maakt u drie VM's in verschillende beschikbaarheidszones binnen een regio en Standard Load Balancer met een zoneredundant ip-adres voor aan de voorzijde. Deze configuratie van de load balancer helpt uw apps en gegevens te beschermen tegen een onwaarschijnlijke storing of verlies van een heel datacenter. |
|[Taken over VM's binnen een specifieke beschikbaarheidszone verdelen](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)|Hiermee maakt u drie VM's, een StandaardLoad Balancer met zonale frontend IP waarmee gegevenspad en resources in één zone voor een bepaalde regio kunnen worden uitgelijnd.|
| [Load balance meerdere websites op VM's](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md) | Hiermee maakt u twee VM's met meerdere IP-configuraties, gekoppeld aan een Azure Availability Set, die toegankelijk is via een Azure Load Balancer. |
| | |


---
title: Azure VMware-oplossing per cloudEenvoudig - Openbaar IP-adres
description: Meer informatie over openbare IP-adressen en hun voordelen op Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 147ab6b5ace4493e1a0b303c320cb2e81d829b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024973"
---
# <a name="cloudsimple-public-ip-address-overview"></a>Overzicht van openbare IP-adres van CloudSimple

Met een openbaar IP-adres kunnen internetbronnen binnenkomen naar Private Cloud-bronnen op een privé-IP-adres. Het privé-IP-adres is een virtuele machine of een software load balancer op uw Private Cloud vCenter. Met het openbare IP-adres u services die op uw Private Cloud worden uitgevoerd, blootstellen aan het internet.

Het openbare IP-adres is toegewezen aan het privé-IP-adres totdat u het niet toewijst. Een openbaar IP-adres kan slechts aan één privé-IP-adres worden toegewezen.

Een bron die is gekoppeld aan een openbaar IP-adres gebruikt altijd het openbare IP-adres voor internettoegang. Standaard is alleen uitgaande internettoegang toegestaan op een openbaar IP-adres.  Binnenkomend verkeer op het openbare IP-adres wordt geweigerd.  Als u binnenkomend verkeer wilt toestaan, maakt u een firewallregel voor het openbare IP-adres naar de specifieke poort.

## <a name="benefits"></a>Voordelen

Het gebruik van een openbaar IP-adres om binnenkomend te communiceren biedt:

* Distributed denial of service (DDoS) aanvalspreventie. Deze beveiliging wordt automatisch ingeschakeld voor het openbare IP-adres.
* Always-on verkeersbewaking en real-time mitigatie van veelvoorkomende aanvallen op netwerkniveau. Deze verdedigingen zijn dezelfde verdedigingen gebruikt door Microsoft online services.
* De volledige schaal van het wereldwijde Azure-netwerk. Het netwerk kan worden gebruikt om aanvalsverkeer over regio's te distribueren en te beperken.  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [toewijzen van een openbaar IP-adres](public-ips.md)
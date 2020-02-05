---
title: Azure VMware-oplossingen (AVS)-openbaar IP-adres
description: Meer informatie over open bare IP-adressen en hun voor delen van Azure VMware-oplossingen (AVS)
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2cb9d0e33da4447760ae0be216c1dd9868c498bd
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024973"
---
# <a name="avs-public-ip-address-overview"></a>Overzicht van openbaar IP-adres van AVS

Met een openbaar IP-adres kunnen Internet bronnen inkomend communiceren met de persoonlijke cloud resources van een privé-IP-adres. Het privé-IP-adres is een virtuele machine of een software load balancer op uw AVS-persoonlijke Cloud-vCenter. Met het open bare IP-adres kunt u services weer geven die worden uitgevoerd op uw automatische AVS-Cloud op internet.

Het open bare IP-adres is toegewezen aan het privé-IP-adres totdat u de toewijzing ervan ongedaan maakt. Een openbaar IP-adres kan alleen worden toegewezen aan één privé-IP-adres.

Een resource die is gekoppeld aan een openbaar IP-adres maakt altijd gebruik van het open bare IP-adres voor Internet toegang. Standaard is alleen uitgaande internet toegang toegestaan op een openbaar IP-adres.  Binnenkomend verkeer op het open bare IP-adres wordt geweigerd.  Als u inkomend verkeer wilt toestaan, maakt u een firewall regel voor het open bare IP-adres voor de specifieke poort.

## <a name="benefits"></a>Voordelen

Het gebruik van een openbaar IP-adres om inkomende communicatie te geven biedt:

* Preventie van DDoS-aanvallen (Distributed Denial of service). Deze beveiliging wordt automatisch ingeschakeld voor het open bare IP-adres.
* Controle van het verkeer en real-time beperking van veelvoorkomende aanvallen op netwerk niveau. Deze verdedigingen zijn dezelfde verdedigings die worden gebruikt door micro soft onlineservices.
* De volledige schaal van het wereld wijde Azure-netwerk. Het netwerk kan worden gebruikt om het aanvals verkeer tussen regio's te distribueren en te verhelpen.  

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over het [toewijzen van een openbaar IP-adres](public-ips.md)
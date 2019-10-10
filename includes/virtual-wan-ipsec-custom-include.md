---
title: bestand opnemen
description: bestand opnemen
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 10/07/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: f7167cbb26e69941cade01ab8c0b8d9dc633f0d2
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168406"
---
Houd bij het werken met aangepaste IPsec-beleids regels rekening met de volgende vereisten:

* **IKE** -voor IKE kunt u een wille keurige para meter uit IKE-versleuteling selecteren, plus alle para meters van IKE-integriteit, plus alle para meters uit de DH-groep.
* **IPSec** : voor IPSec kunt u een wille keurige para meter uit IPSec-versleuteling selecteren, plus alle para meters van IPSec-integriteit, plus PFS. Als een van de para meters voor IPsec-versleuteling of IPsec-integriteit GCM is, moeten de para meters voor beide instellingen GCM zijn.

>[!NOTE]
> Met aangepaste IPsec-beleids regels is er geen responder en initiator (in tegens telling tot standaard IPsec-beleid). Beide zijden (on-premises en Azure VPN-gateway) gebruiken dezelfde instellingen voor IKE fase 1 en IKE fase 2. Zowel IKEv1-als IKEv2-protocollen worden ondersteund. Er is alleen ondersteuning voor Azure als een responder.
>

**Beschik bare instellingen en para meters**

| Instelling | Parameters |
|--- |--- |
| IKE-versleuteling | AES256, AES192, AES128 |
| IKE-integriteit | SHA384, SHA256, SHA1 |
| DH-groep | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPsec-versleuteling | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPsec-integriteit | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS-groep | PFS24, ECP384, ECP256, PFS2048, PFS2 |

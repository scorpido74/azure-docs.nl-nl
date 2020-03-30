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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72168406"
---
Houd bij het werken met aangepast IPsec-beleid rekening met de volgende vereisten:

* **IKE** - Voor IKE u elke parameter selecteren uit IKE Encryption, plus elke parameter van IKE Integrity, plus een parameter van DH Group.
* **IPsec** - Voor IPsec u elke parameter selecteren uit IPsec-versleuteling, plus elke parameter van IPsec Integrity, plus PFS. Als een van de parameters voor IPsec-versleuteling of IPsec-integriteit GCM is, moeten de parameters voor beide instellingen GCM zijn.

>[!NOTE]
> Met aangepast IPsec-beleid is er geen concept van responder en initiator (in tegenstelling tot standaard IPsec-beleid). Beide zijden (on-premises en Azure VPN-gateway) gebruiken dezelfde instellingen voor IKE Phase 1 en IKE Phase 2. Zowel IKEv1 als IKEv2 protocollen worden ondersteund. Er is alleen geen ondersteuning voor Azure als responder.
>

**Beschikbare instellingen en parameters**

| Instelling | Parameters |
|--- |--- |
| IKE-versleuteling | AES256, AES192, AES128 |
| IKE Integriteit | SHA384, SHA256, SHA1 |
| DH-groep | DHGroup24, ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2 |
| IPsec-versleuteling | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128 |
| IPsec-integriteit | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1 |
| PFS-groep | PFS24, ECP384, ECP256, PFS2048, PFS2 |

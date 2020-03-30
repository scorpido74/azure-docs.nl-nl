---
title: bestand opnemen
description: bestand opnemen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e4d20cd39d2a843ee1ab57a412ac668b3495fdb1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175859"
---
>[!NOTE]
>Vanaf 1 juli 2018 is ondersteuning voor TLS 1.0 en 1.1 uit Azure VPN Gateway verwijderd. VPN Gateway ondersteunt alleen TLS 1.2. Zie de updates [om ondersteuning voor TLS1.2 in te schakelen](#tls1)om ondersteuning te behouden.

Bovendien worden de volgende verouderde algoritmen ook afgeschaft voor TLS op 1 juli 2018:

* RC4 (Rivest Cipher 4)
* DES (Data Encryption Algorithm)
* 3DES (Triple Data Encryption Algorithm)
* MD5 (Message Digest 5)

### <a name="how-do-i-enable-support-for-tls-12-in-windows-7-and-windows-81"></a><a name="tls1"></a>Hoe schakel ik ondersteuning in voor TLS 1.2 in Windows 7 en Windows 8.1?

[!INCLUDE [tls 1.2](vpn-gateway-tls-include.md)]

---
title: Clientverificatie configureren voor binnenkomende oproepen - Azure Event Grid IoT Edge | Microsoft Documenten
description: API-protocollen configureren die worden weergegeven door gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 3363db4557dd19e8d72747ccd62bb535abb7b1e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841788"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Clientverificatie van binnenkomende oproepen configureren

Deze handleiding geeft voorbeelden van de mogelijke clientverificatieconfiguraties voor de Event Grid-module. De module Event Grid ondersteunt twee typen clientverificatie:

* SAS-sleutelgebaseerde handtekening (Shared Access Signature)
* Op basis van certificaten

Zie [beveiligings- en verificatiehandleiding](security-authentication.md) voor alle mogelijke configuraties.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Clientverificatie op basis van certificaten inschakelen, geen zelfondertekende certificaten

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Clientverificatie op basis van certificaten inschakelen, zelfondertekende certificaten toestaan

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Stel de eigenschap **inbound__clientAuth__clientCert__allowUnknownCA** alleen in testomgevingen **waar** in, omdat u doorgaans zelfondertekende certificaten gebruiken. Voor productieworkloads raden we u aan deze eigenschap in te stellen op **false** en certificaten van een certificeringsinstantie (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Clientverificatie op basis van certificaten en sas-sleutels inschakelen

```json
 {
  "Env": [
    "inbound__clientAuth__sasKeys__enabled=true",
    "inbound__clientAuth__sasKeys__key1=<some-secret1-here>",
    "inbound__clientAuth__sasKeys__key2=<some-secret2-here>",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Met sas-key-gebaseerde clientverificatie kan een niet-IoT-edge-module beheer- en runtime-bewerkingen uitvoeren, ervan uitgaande dat de API-poorten natuurlijk toegankelijk zijn buiten het IoT Edge-netwerk.

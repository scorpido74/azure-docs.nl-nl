---
title: Identiteit configureren - IoT-rand voor Azure-gebeurtenisraster | Microsoft Documenten
description: De identiteit van de gebeurtenisrastermodule configureren
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841762"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Identiteit configureren voor de module Gebeurtenisraster

In dit artikel ziet u hoe u identiteit configureert voor Grid on Edge. Standaard presenteert de module Event Grid het identiteitscertificaat zoals geconfigureerd door de IoT-beveiligingsdaemon. Event Grid on Edge presenteert zijn identiteitscertificaat met uitgaande oproepen wanneer het evenementen levert. Een abonnee kan vervolgens valideren dat het de eventgridmodule is die de gebeurtenis heeft verzonden voordat deze wordt geaccepteerd.

Zie [beveiligings- en verificatiehandleiding](security-authentication.md) voor alle mogelijke configuraties.

## <a name="always-present-identity-certificate"></a>Altijd een identiteitsbewijs tonen
Hier is een voorbeeldconfiguratie voor het altijd presenteren van een identiteitscertificaat voor uitgaande oproepen. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Geen identiteitsbewijs tonen
Hier is een voorbeeldconfiguratie voor het niet presenteren van een identiteitscertificaat voor uitgaande oproepen. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```

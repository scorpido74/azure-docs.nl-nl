---
title: Identiteit Azure Event Grid IoT Edge configureren | Microsoft Docs
description: De identiteit van de Event Grid-module configureren
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/05/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 0aedeea2a6ad08e1627c2d1a6ebde6c91a4d02d9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76841762"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Identiteit configureren voor de module Event Grid

In dit artikel wordt beschreven hoe u identiteit configureert voor raster aan de rand. De module Event Grid geeft standaard het identiteits certificaat weer zoals geconfigureerd door de IoT-beveiligings-daemon. Event Grid op Edge presenteert het identiteits certificaat met de uitgaande oproepen wanneer het gebeurtenissen levert. Een abonnee kan vervolgens de Event Grid-module valideren die de gebeurtenis heeft verzonden voordat deze wordt geaccepteerd.

Zie de hand leiding voor [beveiliging en verificatie](security-authentication.md) voor alle mogelijke configuraties.

## <a name="always-present-identity-certificate"></a>Altijd identiteits certificaat weer geven
Hier volgt een voorbeeld configuratie voor het altijd presen teren van een identiteits certificaat bij uitgaande oproepen. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Identiteits certificaat niet weer geven
Hier volgt een voorbeeld configuratie voor het niet presen teren van een identiteits certificaat bij uitgaande oproepen. 

```json
 {
  "Env": [
    "outbound__clientAuth__clientCert__enabled=false"
  ]
}
 ```

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
ms.openlocfilehash: 336b6157128468169264d6ffa9564da4d9338aae
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992442"
---
# <a name="configure-identity-for-the-event-grid-module"></a>Identiteit configureren voor de module Event Grid

In dit artikel vindt u voor beelden van de mogelijke identiteits configuraties voor een Event Grid-module. Standaard zal de module Event Grid het identiteits certificaat weer geven zoals geconfigureerd door de IoT-beveiligings-daemon. Er wordt een identiteits certificaat weer gegeven door de Event Grid-module op de uitgaande oproepen die zich voordoen wanneer er gebeurtenissen worden geleverd. Een abonnee van een Event Grid gebeurtenis kan er vervolgens voor kiezen om te controleren of het inderdaad de Event Grid module is die de gebeurtenis heeft verzonden voordat de gebeurtenis wordt geaccepteerd.

Zie de hand leiding voor [beveiliging en verificatie](security-authentication.md) voor alle mogelijke configuraties.

## <a name="always-present-identity-certificate"></a>Altijd identiteits certificaat weer geven
Hier volgt een voorbeeld configuratie voor het altijd presen teren van een identiteits certificaat bij uitgaande oproepen. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=true",
    "outbound:clientAuth:clientCert:source=IoTEdge"
  ]
}
 ```

## <a name="dont-present-identity-certificate"></a>Identiteits certificaat niet weer geven
Hier volgt een voorbeeld configuratie voor het niet presen teren van een identiteits certificaat bij uitgaande oproepen. 

```json
 {
  "Env": [
    "outbound:clientAuth:clientCert:enabled=false"
  ]
}
 ```

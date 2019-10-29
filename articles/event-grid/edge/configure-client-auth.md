---
title: Client verificatie van binnenkomende oproepen configureren-Azure Event Grid IoT Edge | Microsoft Docs
description: API-protocollen configureren die worden weer gegeven door Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: b5456130e89bf77e2c2ba41880323e38f6b27f4c
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992507"
---
# <a name="configure-client-authentication-of-incoming-calls"></a>Client verificatie van binnenkomende oproepen configureren

Deze hand leiding bevat voor beelden van de mogelijke configuraties voor client verificatie voor de module Event Grid. De module Event Grid ondersteunt twee typen client verificatie:-

* Shared Access Signature (SAS) op basis van sleutels
* Op basis van certificaten

Zie de hand leiding voor [beveiliging en verificatie](security-authentication.md) voor alle mogelijke configuraties.

## <a name="enable-certificate-based-client-authentication-no-self-signed-certificates"></a>Client authenticatie op basis van certificaten inschakelen, geen zelfondertekende certificaten

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=false"
  ]
}
 ```

## <a name="enable-certificate-based-client-authentication-allow-self-signed-certificates"></a>Client authenticatie op basis van certificaten inschakelen, zelfondertekende certificaten toestaan

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=false",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
```

>[!NOTE]
>Stel de eigenschap **Inkomend: clientAuth: clientCert: allowUnknownCA** in op **True** in test omgevingen, omdat u meestal zelfondertekende certificaten kunt gebruiken. Voor werk belastingen wordt u aangeraden deze eigenschap in te stellen op **False** en certificaten van een certificerings instantie (CA).

## <a name="enable-certificate-based-and-sas-key-based-client-authentication"></a>Op certificaten gebaseerde en SAS-sleutel gebaseerde client verificatie inschakelen

```json
 {
  "Env": [
    "inbound:clientAuth:sasKeys:enabled=true",
    "inbound:clientAuth:sasKeys:key1=<some-secret1-here>",
    "inbound:clientAuth:sasKeys:key2=<some-secret2-here>",
    "inbound:clientAuth:clientCert:enabled=true",
    "inbound:clientAuth:clientCert:source=IoTEdge",
    "inbound:clientAuth:clientCert:allowUnknownCA=true"
  ]
}
 ```

>[!NOTE]
>Met SAS-client authenticatie op basis van sleutels kan een niet-IoT Edge-module beheer-en runtime-bewerkingen uitvoeren, uitgaande van de API-poorten die buiten het IoT Edge netwerk toegankelijk zijn.

---
title: API-protocollen configureren-Azure Event Grid IoT Edge | Microsoft Docs
description: API-protocollen configureren die worden weer gegeven door Event Grid op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: f39968476f2fecf655e6c69bea2ff19304d2b465
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/28/2019
ms.locfileid: "72992520"
---
# <a name="configure-event-grid-api-protocols"></a>Event Grid API-protocollen configureren

Deze hand leiding bevat voor beelden van de mogelijke protocol configuraties van een Event Grid module. In de module Event Grid wordt API weer gegeven voor de beheer-en runtime-bewerkingen. In de volgende tabel worden de protocollen en poorten vastgelegd.

| Protocol | Port | Beschrijving |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Standaard uitgeschakeld. Alleen nuttig tijdens het testen. Niet geschikt voor productie werkbelastingen.
| HTTPS | 4438 | Standaard

Zie de hand leiding voor [beveiliging en verificatie](security-authentication.md) voor alle mogelijke configuraties.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>HTTPS beschikbaar maken voor IoT-modules op hetzelfde Edge-netwerk

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTPS inschakelen voor andere IoT-modules en niet-IoT-workloads

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=strict",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Met de sectie **PortBindings** kunt u interne poorten toewijzen aan poorten van de container host. Met deze functie kunt u de module Event Grid van buiten het netwerk van IoT Edge container bereiken als het IoT edge-apparaat openbaar is.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>HTTP en HTTPS beschikbaar maken voor IoT-modules op hetzelfde Edge-netwerk

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTP en HTTPS inschakelen voor andere IoT-modules en niet-IoT-workloads

```json
 {
  "Env": [
    "inbound:serverAuth:tlsPolicy=enabled",
    "inbound:serverAuth:serverCert:source=IoTEdge"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ],
      "5888/tcp": [
        {
          "HostPort": "5888"
        }
      ]
    }
  }
}
 ```

>[!NOTE]
> Elke IoT-module maakt standaard deel uit van de IoT Edge runtime die door het brug netwerk is gemaakt. Hierdoor kunnen verschillende IoT-modules op hetzelfde netwerk communiceren met elkaar. Met **PortBindings** kunt u een interne container poort toewijzen op de hostcomputer, zodat iedereen toegang heeft tot de poort van Event grid module van buiten.

>[!IMPORTANT]
> Hoewel de poorten toegankelijk kunnen worden gemaakt buiten het IoT Edge netwerk, dwingt client verificatie af wie in feite aanroepen naar de module mag worden uitgevoerd.

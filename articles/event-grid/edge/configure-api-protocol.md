---
title: API-protocollen configureren - IoT Edge voor Azure Event Grid | Microsoft Documenten
description: API-protocollen configureren die worden weergegeven door gebeurtenisraster op IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: ''
ms.date: 10/03/2019
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: 908bc941ee7379de067621e10adf5fd6ee6df559
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76841807"
---
# <a name="configure-event-grid-api-protocols"></a>Api-protocollen voor gebeurtenisraster configureren

Deze handleiding geeft voorbeelden van de mogelijke protocolconfiguraties van een Event Grid-module. De module Event Grid stelt API bloot voor de beheer- en runtime-bewerkingen. In de volgende tabel worden de protocollen en poorten vastgelegd.

| Protocol | Poort | Beschrijving |
| ---------------- | ------------ | ------------ |
| HTTP | 5888 | Standaard uitgeschakeld. Alleen nuttig tijdens het testen. Niet geschikt voor productieworkloads.
| HTTPS | 4438 | Standaard

Zie [beveiligings- en verificatiehandleiding](security-authentication.md) voor alle mogelijke configuraties.

## <a name="expose-https-to-iot-modules-on-the-same-edge-network"></a>HTTPS blootstellen aan IoT-modules op hetzelfde randnetwerk

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTPS inschakelen voor andere IoT-modules en niet-IoT-workloads

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge"
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
> Met de sectie **PortBindings** u interne poorten toewijzen aan poorten van de containerhost. Deze functie maakt het mogelijk om de Event Grid-module van buiten het IoT Edge-containernetwerk te bereiken, als het IoT-edge-apparaat openbaar bereikbaar is.

## <a name="expose-http-and-https-to-iot-modules-on-the-same-edge-network"></a>HTTP en HTTPS blootstellen aan IoT-modules op hetzelfde edge-netwerk

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
  ]
}
 ```

## <a name="enable-http-and-https-to-other-iot-modules-and-non-iot-workloads"></a>HTTP en HTTPS inschakelen voor andere IoT-modules en niet-IoT-workloads

```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=enabled",
    "inbound__serverAuth__serverCert__source=IoTEdge"
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
> Standaard maakt elke IoT-module deel uit van de IoT Edge-runtime die door het brugnetwerk is gemaakt. Hiermee kunnen verschillende IoT-modules op hetzelfde netwerk met elkaar communiceren. **Met PortBindings** u een interne containerpoort op de hostmachine toewijzen, zodat iedereen van buitenaf toegang heeft tot de poort van de Event Grid-module.

>[!IMPORTANT]
> Hoewel de poorten toegankelijk kunnen worden gemaakt buiten het IoT Edge-netwerk, dwingt clientverificatie wie daadwerkelijk mag bellen naar de module.

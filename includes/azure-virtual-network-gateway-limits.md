---
title: bestand opnemen
description: bestand opnemen
services: networking
author: anzaman
ms.service: VPN Gateway
ms.topic: include
ms.date: 08/25/2020
ms.author: alzam
ms.custom: include file
ms.openlocfilehash: 01796985a05f88b59786be7bbe0b06907cf0bc25
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854109"
---
| Resource                                | Limiet        |
|-----------------------------------------|------------------------------|
| VNet-adres voorvoegsels                   | 600 per VPN-gateway          |
| Aggregatie van BGP-routes                    | 4.000 per VPN-gateway        |
| Adres voorvoegsels van het lokale netwerk gateway  | 1000 per lokale netwerk gateway               |
| S2S-verbindingen                         | [Is afhankelijk van de gateway-SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku)|
| P2S-verbindingen                         | [Is afhankelijk van de gateway-SKU](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways#gwsku) |
| P2S-route limiet-IKEv2                 | 256 voor niet-Windows **/** 25 voor Windows           |
| P2S-route limiet-OpenVPN               | 1000                         |
| Met maximaal flows                              | 100.000 voor VpnGw1/AZ  **/**  512k voor VpnGw2-4/AZ|
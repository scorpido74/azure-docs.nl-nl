---
title: Informatie over het IP-adres van uw IoT-hub | Microsoft Docs
description: Meer informatie over het opvragen van uw IoT hub IP-adres en de bijbehorende eigenschappen. Het IP-adres van uw IoT-hub kan worden gewijzigd tijdens bepaalde scenario's, zoals herstel na nood gevallen of een regionale failover.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/29/2019
ms.openlocfilehash: 7af40404550fb78af891563d8256f23620781b24
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71841535"
---
# <a name="understanding-the-ip-address-of-your-iot-hub"></a>Informatie over het IP-adres van uw IoT-hub

Het IP-adres van uw IoT-hub is een openbaar eind punt met gelijke taak verdeling voor uw hub en niet een specifiek IP-adres. Het adres wordt bepaald door het netwerk adres bereik van de Azure-regio waar het wordt geïmplementeerd. Dit IP-adres kan zonder kennisgeving worden gewijzigd. Data Center-netwerk updates, Data Center-herstel na nood gevallen of regionale failover van een IoT-hub kunnen het IP-adres van de IoT-hub wijzigen. Zie [IOT hub hoge Beschik baarheid en herstel na nood gevallen](iot-hub-ha-dr.md) voor meer informatie over Azure IOT hub regionale failover en beschik baarheid.

Het IP-adres van uw IoT-hub wordt gewijzigd na een failover naar een andere regio. U kunt deze functionaliteit testen door de zelf studie [een hand matige failover van een IOT-hub uit te voeren](tutorial-manual-failover.md).

## <a name="discover-your-iot-hub-ip-address"></a>Het IP-adres van uw IoT-hub detecteren

Uw IoT Hub IP-adres kan worden gedetecteerd met behulp van een reverse DNS-zoek opdracht op de CNAME ([*IOT-hub-name*]. Azure-devices.net). U kunt **nslookup** gebruiken om het IP-adres van een IOT hub-instantie te controleren:

```cmd/sh
nslookup {YourIoTHubName}.azure-devices.net
```

Dit IP-adres kan zonder kennisgeving worden gewijzigd. In een scenario voor failover of nood herstel moet u het IP-adres van uw IoT-hub in de secundaire regio navragen.

## <a name="outbound-firewall-rules-for-iot-hub"></a>Uitgaande firewall regels voor IoT hub

Probeer firewall regels en filters te maken op basis van de hostnaam of het domein van de IoT-hub. Als u alleen uitgaand verkeer naar specifieke adressen kunt toestaan, moet u het IP-adres van uw IoT-hub regel matig controleren en uw firewall regels bijwerken.

## <a name="support-for-ipv6"></a>Ondersteuning voor IPv6 

IPv6 wordt momenteel niet ondersteund in IoT Hub.
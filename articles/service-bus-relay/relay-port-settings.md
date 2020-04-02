---
title: Azure Relay-poortinstellingen | Microsoft Documenten
description: Dit artikel bevat een tabel waarin de vereiste configuratie voor poortwaarden voor Azure Relay wordt beschreven.
services: service-bus-relay
documentationcenter: na
author: spelluru
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/21/2020
ms.author: spelluru
ms.openlocfilehash: 532e3c297c42ea16b1f462a01699fc2fd71c6cce
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529113"
---
# <a name="azure-relay-port-settings"></a>Azure Relay-poortinstellingen

In de volgende tabel wordt de vereiste configuratie voor poortwaarden voor Azure Relay beschreven.

## <a name="hybrid-connections"></a>Hybride verbindingen

Hybrid Connections gebruikt WebSockets op poort 443 met TLS als onderliggend transportmechanisme, dat alleen **HTTPS** gebruikt. 

## <a name="wcf-relays"></a>WCF-relays
  
|Binding|Transportbeveiliging|Poort|  
|-------------|------------------------|----------|  
|[BasishttpRelayBinding klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Ja|HTTPS| 
|" |Nee|HTTP|  
|[BasishttpRelayBinding klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (service)|Ofwel|9351/HTTP|  
|[NetEventRelayBinding Klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Ja|9351/HTTPS|  
|" |Nee|9350/HTTP|  
|[NetEventRelayBinding Klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (service)|Ofwel|9351/HTTP|  
|[NetTcpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Ofwel|5671/9352/HTTP (9352/9353 bij gebruik van hybride)|  
|[NetOnewayRelayBinding-klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Ja|9351/HTTPS|  
|" |Nee|9350/HTTP|  
|[NetOnewayRelayBinding Klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (service)|Ofwel|9351/HTTP|  
|[WebhttpRelayBinding Klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Ja|HTTPS|  
|" |Nee|HTTP|  
|[WebhttpRelayBinding Klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (service)|Ofwel|9351/HTTP|  
|[WS2007HttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Ja|HTTPS|  
|" |Nee|HTTP|  
|[WS2007HttpRelayBinding Klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (service)|Ofwel|9351/HTTP|

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over Azure Relay naar de volgende koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Veelgestelde vragen over Relay](relay-faq.md)
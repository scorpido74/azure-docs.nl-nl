---
title: Poort instellingen Azure Relay | Microsoft Docs
description: Dit artikel bevat een tabel met een beschrijving van de vereiste configuratie voor poort waarden voor Azure Relay.
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
ms.openlocfilehash: 55bb6b620c7ba9ee13068213cbc867b165acc185
ms.sourcegitcommit: 38b11501526a7997cfe1c7980d57e772b1f3169b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2020
ms.locfileid: "76514881"
---
# <a name="azure-relay-port-settings"></a>Poort instellingen Azure Relay

In de volgende tabel wordt de vereiste configuratie voor poort waarden voor Azure Relay beschreven.

## <a name="hybrid-connections"></a>Hybride verbindingen

Hybride verbindingen maakt gebruik van websockets op poort 443 met SSL als onderliggend transport mechanisme dat alleen **https** gebruikt. 

## <a name="wcf-relays"></a>WCF-relays
  
|Binding|Transport beveiliging|Port|  
|-------------|------------------------|----------|  
|[BasicHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (client)|Ja|HTTPS| 
|" |Nee|HTTP|  
|[BasicHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.basichttprelaybinding) (Service)|Een van beide|9351/HTTP|  
|[NetEventRelayBinding-klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (client)|Ja|9351/HTTPS|  
|" |Nee|9350/HTTP|  
|[NetEventRelayBinding-klasse](/dotnet/api/microsoft.servicebus.neteventrelaybinding) (Service)|Een van beide|9351/HTTP|  
|[NetTcpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.nettcprelaybinding) (client/service)|Een van beide|5671/9352/HTTP (9352/9353 als u hybride gebruikt)|  
|[NetOnewayRelayBinding-klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (client)|Ja|9351/HTTPS|  
|" |Nee|9350/HTTP|  
|[NetOnewayRelayBinding-klasse](/dotnet/api/microsoft.servicebus.netonewayrelaybinding) (Service)|Een van beide|9351/HTTP|  
|[WebHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (client)|Ja|HTTPS|  
|" |Nee|HTTP|  
|[WebHttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.webhttprelaybinding) (Service)|Een van beide|9351/HTTP|  
|[WS2007HttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (client)|Ja|HTTPS|  
|" |Nee|HTTP|  
|[WS2007HttpRelayBinding-klasse](/dotnet/api/microsoft.servicebus.ws2007httprelaybinding) (Service)|Een van beide|9351/HTTP|

## <a name="next-steps"></a>Volgende stappen
Ga voor meer informatie over Azure Relay naar deze koppelingen:
* [Wat is Azure Relay?](relay-what-is-it.md)
* [Veelgestelde vragen over Relay](relay-faq.md)
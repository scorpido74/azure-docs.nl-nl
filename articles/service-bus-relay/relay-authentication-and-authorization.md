---
title: Azure Relay-verificatie en -autorisatie | Microsoft Documenten
description: In dit artikel vindt u een overzicht van SAS-verificatie (Shared Access Signature) met de Azure Relay-service.
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
ms.openlocfilehash: aac5c973a99b13d5918a0162feb7f1ede443463b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76514575"
---
# <a name="azure-relay-authentication-and-authorization"></a>Azure Relay-verificatie en -autorisatie

Toepassingen kunnen zich verifiëren naar Azure Relay met behulp van SAS-verificatie (Shared Access Signature). Met SAS-verificatie kunnen toepassingen zich verifiëren naar de Azure Relay-service met behulp van een toegangssleutel die is geconfigureerd op de naamruimte relay. U deze sleutel vervolgens gebruiken om een geclusterd handtekeningtoken voor gedeelde toegang te genereren dat clients kunnen gebruiken om te verifiëren bij de relayservice.

## <a name="shared-access-signature-authentication"></a>Verificatie van gedeelde toegangshandtekeningen

[Met SAS-verificatie](../service-bus-messaging/service-bus-sas.md) u een gebruiker toegang verlenen tot Azure Relay-bronnen met specifieke rechten. SAS-verificatie omvat de configuratie van een cryptografische sleutel met bijbehorende rechten op een resource. Clients kunnen vervolgens toegang krijgen tot die bron door een SAS-token te presenteren, dat bestaat uit de toegang tot de bron URI en een vervaldatum die is ondertekend met de geconfigureerde sleutel.

U sleutels voor SAS configureren op een naamruimte voor relais. In tegenstelling tot Service Bus-berichten ondersteunt [Relay Hybrid Connections](relay-hybrid-connections-protocol.md) onbevoegde of anonieme afzenders. U anonieme toegang voor de entiteit inschakelen wanneer u deze maakt, zoals wordt weergegeven in de volgende schermafbeelding van de portal:

![][0]

Als u SAS wilt gebruiken, u een object [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) configureren op een naamruimte relay die bestaat uit het volgende:

* *KeyName* die de regel identificeert.
* *PrimaryKey* is een cryptografische sleutel die wordt gebruikt om SAS-tokens te ondertekenen/valideren.
* *SecondaryKey* is een cryptografische sleutel die wordt gebruikt om SAS-tokens te ondertekenen/valideren.
* *Rechten* die de verzameling van verleende rechten voor luisteren, verzenden of beheren vertegenwoordigen.

Autorisatieregels die zijn geconfigureerd op naamruimteniveau, kunnen toegang verlenen tot alle relayverbindingen in een naamruimte voor clients met tokens die zijn ondertekend met de bijbehorende sleutel. Maximaal 12 dergelijke autorisatieregels kunnen worden geconfigureerd op een relay-naamruimte. Standaard is een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) met alle rechten geconfigureerd voor elke naamruimte wanneer deze voor het eerst wordt ingericht.

Om toegang te krijgen tot een entiteit, heeft de client een SAS-token nodig dat is gegenereerd met een specifieke [SharedAccessAuthorizationRule.](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule) Het SAS-token wordt gegenereerd met behulp van de HMAC-SHA256 van een resourcetekenreeks die bestaat uit de bron URI waarvoor toegang wordt geclaimd, en een vervaldatum met een cryptografische sleutel die is gekoppeld aan de autorisatieregel.

SAS-verificatieondersteuning voor Azure Relay is opgenomen in de Azure .NET SDK-versies 2.0 en hoger. SAS bevat ondersteuning voor een [SharedAccessAuthorizationRule](/dotnet/api/microsoft.servicebus.messaging.sharedaccessauthorizationrule). Alle API's die een verbindingstekenreeks als parameter accepteren, bevatten ondersteuning voor SAS-verbindingstekenreeksen.

## <a name="next-steps"></a>Volgende stappen

- Lees verder [Service Bus-verificatie met Gedeelde toegangshandtekeningen](../service-bus-messaging/service-bus-sas.md) voor meer informatie over SAS.
- Zie de [protocolhandleiding](relay-hybrid-connections-protocol.md) azure relay hybrid connections voor gedetailleerde informatie over de mogelijkheid hybride verbindingen.
- Zie [Verificatie en autorisatie servicebus](../service-bus-messaging/service-bus-authentication-and-authorization.md)voor overeenkomstige informatie over verificatie en autorisatie van Service Bus. 

[0]: ./media/relay-authentication-and-authorization/hcanon.png
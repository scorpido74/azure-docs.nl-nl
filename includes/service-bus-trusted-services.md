---
title: bestand opnemen
description: bestand opnemen
services: service-bus-messaging
author: spelluru
ms.service: service-bus-messaging
ms.topic: include
ms.date: 11/05/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: bb6f2281cc115904db6babcf31f1af387b254d48
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2020
ms.locfileid: "94426564"
---
## <a name="trusted-microsoft-services"></a>Vertrouwde micro soft-Services
Wanneer u de instelling **vertrouwde micro soft-Services toestaan deze firewall wilt overs Laan** inschakelt, krijgen de volgende services toegang tot uw service bus-resources.

| Vertrouwde service | Ondersteunde gebruiks scenario's | 
| --------------- | ------------------------- | 
| Azure Event Grid | Hiermee kan Azure Event Grid gebeurtenissen verzenden naar wacht rijen of onderwerpen in uw Service Bus naam ruimte. U moet ook de volgende stappen uitvoeren: <ul><li>Door het systeem toegewezen identiteit voor een onderwerp of een domein inschakelen</li><li>De identiteit toevoegen aan de Azure Service Bus gegevens afzender-rol op de Service Bus naam ruimte</li><li>Configureer vervolgens het gebeurtenis abonnement dat gebruikmaakt van een Service Bus wachtrij of onderwerp als een eind punt om de door het systeem toegewezen identiteit te gebruiken.</li></ul> <p>Zie [Event Delivery with a Managed Identity](../articles/event-grid/managed-service-identity.md) (Engelstalig) voor meer informatie.</p>|

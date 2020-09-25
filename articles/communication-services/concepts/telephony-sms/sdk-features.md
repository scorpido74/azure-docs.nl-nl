---
title: Overzicht van de sms-clientbibliotheek voor Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Hierin wordt een overzicht gegeven van de sms-clientbibliotheek en de bijbehorende aanbiedingen.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2d81749e7023bdbf5353e5c8da633674ea8e8ce9
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943677"
---
# <a name="sms-client-library-overview"></a>Overzicht van de sms-clientbibliotheek

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Sms-clientbibliotheken van Azure Communication Services kunnen worden gebruikt om functionaliteit voor sms-berichten aan uw toepassingen toe te voegen.

## <a name="sms-client-library-capabilities"></a>Mogelijkheden van sms-clientbibliotheken

De volgende lijst bevat de set functies die momenteel beschikbaar zijn in onze clientbibliotheken.

| Groep functies | Mogelijkheid                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Belangrijkste mogelijkheden | Sms-berichten verzenden en ontvangen </br> *Ondersteuning voor Unicode-emoji's*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Bezorgingsrapporten ontvangen voor verzonden berichten                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Alle tekensets (taal-/Unicode-ondersteuning)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Ondersteuning voor lange berichten (maximaal 2048 tekens)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Automatische samenvoeging van lange berichten                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Gebeurtenissen            | Event Grid gebruiken om webhooks te configureren voor het ontvangen van binnenkomende berichten en bezorgingsrapporten | ✔️   | ✔️    | ✔️    | ✔️      |
| Telefoonnummer      | Gratis nummers                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regelgeving        | Afhandeling van uitschrijving                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Bewaking        | Gebruik bewaken voor verzonden en ontvangen berichten                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Bellen via PSTN      | Mogelijkheden voor bellen via PSTN toevoegen aan uw sms-telefoonnummer                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Aan de slag met het verzenden van sms-berichten](../../quickstarts/telephony-sms/send.md)

De volgende documenten zijn mogelijk interessant voor u:

- Zorg dat u vertrouwd raakt met algemene [sms-concepten](../telephony-sms/concepts.md)
- Koop een [telefoonnummer](../../quickstarts/telephony-sms/get-phone-number.md) dat geschikt is voor sms
- [Plan uw sms-oplossing](../telephony-sms/plan-solution.md)
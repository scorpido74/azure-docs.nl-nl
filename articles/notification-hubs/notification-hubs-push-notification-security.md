---
title: Notification Hubs beveiliging
description: In dit onderwerp wordt de beveiliging beschreven van Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: 6506177c-e25c-4af7-8508-a3ddca9dc07c
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 05/31/2019
ms.author: jowargo
ms.openlocfilehash: 73a6d0eaab286dec9d02bb55eb75f0781bcffcc4
ms.sourcegitcommit: a3a40ad60b8ecd8dbaf7f756091a419b1fe3208e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/21/2019
ms.locfileid: "69891577"
---
# <a name="notification-hubs-security"></a>Notification Hubs beveiliging

## <a name="overview"></a>Overzicht

In dit onderwerp wordt het beveiligings model van Azure Notification Hubs beschreven.

## <a name="shared-access-signature-security-sas"></a>Shared Access Signature Security (SAS)

Notification Hubs implementeert een beveiligings schema op entiteits niveau met de naam SAS (Shared Access Signature). Elke regel bevat een naam, een sleutel waarde (gedeeld geheim) en een set rechten, zoals wordt uitgelegd in [beveiligings claims](#security-claims). Wanneer u een notification hub maakt, worden er automatisch twee regels gemaakt: één met **Luister** rechten (die de client-app gebruikt) en één met **alle** rechten (die de app-back-end gebruikt).

Bij het uitvoeren van registratie beheer vanuit client-apps, als de gegevens die via meldingen worden verzonden, niet gevoelig zijn (bijvoorbeeld weer updates), een veelgebruikte manier om toegang te krijgen tot een notification hub is de sleutel waarde van de regel alleen-lezen toegang tot de client-app te geven. en om de sleutel waarde van de regel volledige toegang te geven tot de back-end van de app.

Apps moeten de sleutel waarde niet insluiten in Windows Store-client-apps. in plaats daarvan wordt de client-app tijdens het opstarten opgehaald uit de back-end van de app.

De sleutel met **listen** -toegang kan een client-app voor elk label registreren. Als uw app de registraties beperkt tot specifieke tags voor specifieke clients (bijvoorbeeld wanneer labels gebruikers-Id's vertegenwoordigen), moet uw app-back-end de registraties uitvoeren. Zie [registratie beheer](notification-hubs-push-notification-registration-management.md)voor meer informatie. Op deze manier is de client-app niet rechtstreeks toegang tot Notification Hubs.

## <a name="security-claims"></a>Beveiligings claims

Net als bij andere entiteiten zijn notification hub-bewerkingen toegestaan voor drie beveiligings claims: **Luis teren**, **verzenden**en **beheren**.

| Claim   | Description                                          | Toegestane bewerkingen |
| ------- | ---------------------------------------------------- | ------------------ |
| Luisteren  | Enkelvoudige registraties maken/bijwerken, lezen en verwijderen | Registratie maken/bijwerken<br><br>Registratie lezen<br><br>Alle registraties voor een ingang lezen<br><br>Registratie verwijderen |
| Verzenden    | Berichten verzenden naar de notification hub                | Bericht verzenden |
| Beheren  | RUWE gegevens op Notification Hubs (inclusief het bijwerken van PNS-referenties en beveiligings sleutels) en het lezen van registraties op basis van Tags |Notification hubs maken/bijwerken/lezen/verwijderen<br><br>Registraties per tag lezen |

Notification Hubs accepteert handtekening tokens die zijn gegenereerd met gedeelde sleutels die rechtstreeks zijn geconfigureerd op de notification hub.

Het is niet mogelijk om een melding naar meer dan één naam ruimte te verzenden. Naam ruimten zijn logische container voor notification hubs en zijn niet betrokken bij het verzenden van meldingen.
Het toegangs beleid op naam ruimte niveau (referenties) kan worden gebruikt voor bewerkingen op naam ruimte niveau, zoals het vermelden van meldings hubs, het maken of verwijderen van Notification hubs, enzovoort. Alleen met het toegangs beleid op hubniveau kunt u meldingen verzenden.

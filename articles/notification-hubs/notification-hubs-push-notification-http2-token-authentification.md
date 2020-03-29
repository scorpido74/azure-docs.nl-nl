---
title: Http/2-verificatie (Tokengebaseerd (HTTP/2) voor APNS in Azure-meldingshubs | Microsoft Documenten
description: Meer informatie over het gebruik van de nieuwe tokenverificatie voor APNS.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: dotnet
ms.topic: article
ms.date: 11/14/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: 448b5c38371024c2eae900f4f87b343ee0a3b36a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263809"
---
# <a name="token-based-http2-authentication-for-apns"></a>Http/2-verificatie op basis van token voor APNS

## <a name="overview"></a>Overzicht

In dit artikel wordt uitgelegd hoe u het nieuwe APNS HTTP/2-protocol gebruiken met tokengebaseerde verificatie.

De belangrijkste voordelen van het gebruik van het nieuwe protocol zijn:

* Tokengeneratie is relatief eenvoudig (in vergelijking met certificaten)
* Geen vervaldatums meer - u hebt de controle over uw verificatietokens en hun intrekking
* Payloads kunnen nu tot 4 KB
* Synchrone feedback
* U bent op het nieuwste protocol van Apple - certificaten nog steeds gebruik maken van de binaire protocol, die is gemarkeerd voor afschaffing

Met behulp van dit nieuwe mechanisme kan worden uitgevoerd in twee stappen:

* Verkrijg de benodigde informatie van de Apple Developer-accountportal.
* Configureer uw meldingshub met de nieuwe informatie.

Notification Hubs is nu ingesteld op het nieuwe verificatiesysteem te gebruiken met APNS.

Houd er rekening mee dat als u bent gemigreerd van het gebruik van certificaatreferenties voor APNS, de token-eigenschappen uw certificaat in ons systeem overschrijven, maar dat uw toepassing meldingen naadloos blijft ontvangen.

## <a name="obtaining-authentication-information-from-apple"></a>Verificatiegegevens van Apple verkrijgen

Als u verificatie op basis van tokens wilt inschakelen, hebt u de volgende eigenschappen van uw Apple Developer-account nodig:

### <a name="key-identifier"></a>Sleutel-id

De sleutel-id kan worden verkregen op de pagina **Sleutels** onder **Certificaten, Id's & Profielen**in uw Apple Developer-account:

![](./media/notification-hubs-push-notification-http2-token-authentification/keys.png)

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier-and-application-name"></a>Toepassings-id en toepassingsnaam

De naam en id van de toepassing zijn ook beschikbaar in de pagina **Certificaten, Id's & profielen** in het ontwikkelaarsaccount:

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

### <a name="configure-via-the-net-sdk-or-the-azure-portal"></a>Configureren via de .NET SDK of de Azure-portal

U uw hub configureren om verificatie op basis van tokens te gebruiken via onze [nieuwste client-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs)of in de Azure-portal. Als u verificatie op basis van tokens in de portal wilt inschakelen, meldt u zich aan bij de Azure-portal en gaat u naar **het deelvenster Instellingen > Apple (APNS).** Selecteer **Token** in de eigenschap **Verificatiemodus** om uw hub bij te werken met alle relevante token-eigenschappen.

![Token configureren](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Voer de eigenschappen in die u hebt opgehaald uit uw Apple Developer-account.
* Kies de toepassingsmodus **(Productie** of **Sandbox).**
* Klik **op** de knop Opslaan om uw APNS-referenties bij te werken.

Tokengebaseerde referenties bestaan uit de volgende velden:

* **Sleutel-id:** id van de privésleutel die is gegenereerd in de Apple Developer-portal; bijvoorbeeld . `2USFGKSKLT`
* **Team-ID**: Ook wel het voorvoegsel of 'App-voorvoegsel' genoemd. Dit is de id voor de organisatie in de Apple Developer-portal; bijvoorbeeld . `S4V3D7CHJR`
* **Bundel-ID**: ook wel de App ID genoemd. Dit is de bundel-id voor de toepassing; bijvoorbeeld . `com.microsoft.nhubsample2019` Houd er rekening mee dat u één sleutel voor veel apps gebruiken. Deze waarde wordt `apns-topic` toegewezen aan de HTTP-header bij het verzenden van een melding en wordt gebruikt om de specifieke toepassing te targeten.
* **Token**: Ook wel de "Sleutel" of "Privésleutel" genoemd. Dit wordt verkregen uit het .p8-bestand dat is gegenereerd op de Apple Developer-portal. De sleutel moet APNS hebben ingeschakeld (die is geselecteerd op de Apple Developer-portal bij het genereren van de sleutel). De waarde moet de PEM-header/voettekst hebben die wordt verwijderd wanneer u deze aan de NH-portal/API levert.
* **Eindpunt:** dit is een schakel in het portalblad van Notificatiehubs en een tekenreeksveld in de API. Geldige waarden `https://api.push.apple.com` `https://api.sandbox.push.apple.com`zijn of . Notification Hubs gebruikt deze waarde voor de productie- of sandbox-omgeving voor het verzenden van meldingen. Dit moet `aps-environment` overeenkomen met het recht in de app, anders komen de gegenereerde APNS-apparaattokens niet overeen met de omgeving en worden de meldingen niet verzonden.

Hier is een codevoorbeeld dat het juiste gebruik illustreert:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR PRIVATE KEY HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="next-steps"></a>Volgende stappen

* [Een Azure notification hub maken met behulp van de Azure-portal](create-notification-hub-portal.md)
* [Een meldingshub configureren in de Azure-portal](create-notification-hub-portal.md)

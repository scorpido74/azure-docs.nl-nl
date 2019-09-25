---
title: Verificatie op basis van tokens (HTTP/2) voor APNS in azure Notification Hubs | Microsoft Docs
description: In dit onderwerp wordt uitgelegd hoe u de nieuwe token verificatie gebruikt voor APNS
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
ms.date: 02/13/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 02/13/2019
ms.openlocfilehash: a7fdaae33e28bd543b44c54868324339d1269bc2
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213120"
---
# <a name="token-based-http2-authentication-for-apns"></a>Verificatie op basis van tokens (HTTP/2) voor APNS

## <a name="overview"></a>Overzicht

In dit artikel vindt u informatie over het gebruik van het nieuwe APNS HTTP/2-protocol met verificatie op basis van tokens.

De belangrijkste voor delen van het gebruik van het nieuwe protocol zijn:

* Het genereren van tokens is relatief gedoe vrij (vergeleken met certificaten)
* Geen verval datums meer: u hebt de controle over uw verificatie tokens en de intrekking ervan
* Nettoladingen kunnen nu Maxi maal 4 KB groot zijn
* Synchrone feedback
* U bent het meest recente Protocol: certificaten gebruiken nog steeds het binaire protocol, dat is gemarkeerd voor afschaffing

Dit nieuwe mechanisme kan in een paar minuten in twee stappen worden uitgevoerd:

1. De benodigde gegevens ophalen uit de Apple Developer-account Portal
2. Uw notification hub configureren met de nieuwe gegevens

Notification Hubs nu is ingesteld om het nieuwe verificatie systeem met APNS te gebruiken.

Houd er rekening mee dat als u de certificaat referenties voor APNS hebt gemigreerd:

* de token eigenschappen overschrijven uw certificaat in ons systeem,
* uw toepassing blijft echter naadloos meldingen ontvangen.

## <a name="obtaining-authentication-information-from-apple"></a>Verificatie gegevens verkrijgen van Apple

Als u verificatie op basis van tokens wilt inschakelen, hebt u de volgende eigenschappen van uw Apple Developer-account nodig:

### <a name="key-identifier"></a>Id van sleutel

De sleutel-id kan worden verkregen via de pagina sleutels in uw Apple Developer-account

![](./media/notification-hubs-push-notification-http2-token-authentification/obtaining-auth-information-from-apple.png)

### <a name="application-identifier--application-name"></a>Toepassings-id & toepassings naam

De naam van de toepassing is beschikbaar via de pagina app-Id's in het ontwikkelaars account.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-name.png)

De toepassings-id is beschikbaar via de pagina Membership Details in het ontwikkelaars account.

![](./media/notification-hubs-push-notification-http2-token-authentification/app-id.png)

### <a name="authentication-token"></a>Verificatie token

Het verificatie token kan worden gedownload nadat u een token voor uw toepassing hebt gegenereerd. Raadpleeg [de Apple-documentatie voor ontwikkel aars](https://help.apple.com/xcode/mac/current/#/devdfd3d04a1)voor meer informatie over het genereren van dit token.

## <a name="configuring-your-notification-hub-to-use-token-based-authentication"></a>Uw notification hub configureren voor het gebruik van verificatie op basis van tokens

### <a name="configure-via-the-azure-portal"></a>Configureren via de Azure Portal

Als u verificatie op basis van tokens in de portal wilt inschakelen, meldt u zich aan bij de Azure Portal en gaat u naar uw notification hub > Notification Services > APNS-paneel.

Er is een nieuwe eigenschap- *verificatie modus*. Als u token selecteert, kunt u uw hub bijwerken met alle relevante token eigenschappen.

![](./media/notification-hubs-push-notification-http2-token-authentification/azure-portal-apns-settings.png)

* Voer de eigenschappen in die u hebt opgehaald van uw Apple Developer-account
* Kies de toepassings modus (productie of sandbox)
* Klik op de knop **Opslaan** om uw APNS-referenties bij te werken

### <a name="configure-via-management-api-rest"></a>Configureren via beheer-API (REST)

U kunt onze [beheer-api's](https://msdn.microsoft.com/library/azure/dn495827.aspx) gebruiken om uw notification hub bij te werken voor het gebruik van verificatie op basis van tokens.
Afhankelijk van of de toepassing die u wilt configureren een sandbox of productie-app is (opgegeven in uw Apple Developer-account), gebruikt u een van de volgende eind punten:

* Sandbox-eind punt:[https://api.development.push.apple.com:443/3/device](https://api.development.push.apple.com:443/3/device)
* Productie-eind punt:[https://api.push.apple.com:443/3/device](https://api.push.apple.com:443/3/device)

> [!IMPORTANT]
> Voor verificatie op basis van tokens is een API-versie van vereist: **2017-04 of hoger**.

Hier volgt een voor beeld van een PUT-aanvraag voor het bijwerken van een hub met verificatie op basis van tokens:

    ```text
    PUT https://{namespace}.servicebus.windows.net/{Notification Hub}?api-version=2017-04
      "Properties": {
        "ApnsCredential": {
          "Properties": {
            "KeyId": "<Your Key Id>",
            "Token": "<Your Authentication Token>",
            "AppName": "<Your Application Name>",
            "AppId": "<Your Application Id>",
            "Endpoint":"<Sandbox/Production Endpoint>"
          }
        }
      }
    ```

### <a name="configure-via-the-net-sdk"></a>Configureren via de .NET SDK

U kunt uw hub configureren voor het gebruik van verificatie op basis van tokens met behulp van onze [nieuwste client-SDK](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/1.0.8).

Hier volgt een code voorbeeld dat het juiste gebruik illustreert:

```csharp
NamespaceManager nm = NamespaceManager.CreateFromConnectionString(_endpoint);
string token = "YOUR TOKEN HERE";
string keyId = "YOUR KEY ID HERE";
string appName = "YOUR APP NAME HERE";
string appId = "YOUR APP ID HERE";
NotificationHubDescription desc = new NotificationHubDescription("PATH TO YOUR HUB");
desc.ApnsCredential = new ApnsCredential(token, keyId, appId, appName);
desc.ApnsCredential.Endpoint = @"https://api.development.push.apple.com:443/3/device";
nm.UpdateNotificationHubAsync(desc);
```

## <a name="reverting-to-using-certificate-based-authentication"></a>Herstellen met verificatie op basis van een certificaat

U kunt op elk gewenst moment terugkeren naar het gebruik van verificatie op basis van certificaten door een van de voor gaande methoden te gebruiken en het certificaat door te geven in plaats van de eigenschappen van het token. Deze actie overschrijft de eerder opgeslagen referenties.

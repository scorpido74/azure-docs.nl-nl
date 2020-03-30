---
title: Hoe Apache Cordova Plugin gebruiken
description: Apache Cordova-plug-in gebruiken voor Azure Mobile Apps
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: cafeea8afe571fc81548833952eee72a695fed41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77459358"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Apache Cordova-clientbibliotheek gebruiken voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Overzicht
In deze handleiding leert u veelvoorkomende scenario's uit te voeren met behulp van de nieuwste [Apache Cordova-plug-in voor Azure Mobile Apps.] Als u nieuw bent in Azure Mobile Apps, voltooit u Azure [Mobile Apps Snel aan de slag] om een backend te maken, een tabel te maken en een vooraf gebouwd Apache Cordova-project te downloaden. In deze gids richten we ons op de client-side Apache Cordova Plugin.

## <a name="supported-platforms"></a>Ondersteunde platforms
Deze SDK ondersteunt Apache Cordova v6.0.0 en hoger op iOS-, Android- en Windows-apparaten.  De platformondersteuning is als volgt:

* Android API 19-24 (KitKat via Nougat).
* iOS-versies 8.0 en hoger.
* Windows Phone 8.1.
* Universeel Windows-platform.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Instellingen en vereisten
In deze handleiding wordt ervan uitgegaan dat u een back-end met een tabel hebt gemaakt. In deze handleiding wordt ervan uitgegaan dat de tabel hetzelfde schema heeft als de tabellen in die zelfstudies. Deze handleiding gaat er ook van uit dat u de Apache Cordova Plugin aan uw code hebt toegevoegd.  Als u dit niet hebt gedaan, u de Plug-in Apache Cordova toevoegen aan uw project op de opdrachtregel:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Voor meer informatie over het maken van [uw eerste Apache Cordova app,]zie hun documentatie.

## <a name="setting-up-an-ionic-v2-app"></a><a name="ionic"></a>Een Ionic v2-app instellen

Als u een Ionic v2-project goed wilt configureren, maakt u eerst een basis-app en voegt u de Cordova-plug-in toe:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Voeg de volgende `app.component.ts` regels toe om het clientobject te maken:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

U het project nu bouwen en uitvoeren in de browser:

```
ionic platform add browser
ionic run browser
```

De Azure Mobile Apps Cordova-plug-in ondersteunt zowel Ionische v1- als v2-apps.  Alleen de Ionische v2-apps `WindowsAzure` vereisen de extra verklaring voor het object.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Hoe: Gebruikers verifiëren
Azure App Service ondersteunt het authenticeren en autoriseren van app-gebruikers met behulp van verschillende externe identiteitsproviders: Facebook, Google, Microsoft-account en Twitter. U machtigingen voor tabellen instellen om de toegang voor specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U ook de identiteit van geverifieerde gebruikers gebruiken om autorisatieregels in serverscripts te implementeren. Zie de [zelfstudie Aan de slag met verificatie] voor meer informatie.

Bij het gebruik van verificatie in een Apache Cordova-app moeten de volgende Cordova-plug-ins beschikbaar zijn:

* [cordova-plugin-apparaat]
* [cordova-plugin-inappbrowser]

Twee verificatiestromen worden ondersteund: een serverstroom en een clientstroom.  De serverstroom biedt de eenvoudigste verificatie-ervaring, omdat deze is gebaseerd op de webverificatie-interface van de provider. De clientflow zorgt voor een diepere integratie met apparaatspecifieke mogelijkheden, zoals single-sign-on, omdat deze afhankelijk is van providerspecifieke apparaatspecifieke SDK's.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>How to: Configureer uw mobiele app-service voor externe omleidings-URL's.
Verschillende soorten Apache Cordova-toepassingen gebruiken een loopback-mogelijkheid om OAuth UI-stromen te verwerken.  OAuth UI-stromen op localhost veroorzaken problemen, omdat de verificatieservice alleen weet hoe u uw service standaard gebruiken.  Voorbeelden van problematische OAuth UI-stromen zijn:

* De Ripple emulator.
* Live Reload met Ionic.
* De mobiele backend lokaal uitvoeren
* De mobiele backend uitvoeren in een andere Azure App-service dan de back-end die verificatie biedt.

Volg deze instructies om uw lokale instellingen toe te voegen aan de configuratie:

1. Inloggen bij de [Azure-portal]
2. Selecteer **Alle bronnen** of **App-services** klik vervolgens op de naam van uw mobiele app.
3. Klik **op Extra**
4. Klik op **Resourceexplorer** in het menu OBSERVEREN en klik vervolgens op **Start**.  Er wordt een nieuw venster of tabblad geopend.
5. Vouw de **config,** **authsettings-knooppunten** voor uw site uit in de linkernavigatie.
6. Klik **op Bewerken**
7. Zoek naar het element 'toegestaanExternalRedirectUrls'.  Het kan worden ingesteld op null of een array van waarden.  Wijzig de waarde in de volgende waarde:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Vervang de URL's door de URL's van uw service.  Voorbeelden hiervan `http://localhost:3000` zijn (voor de voorbeeldservice `http://localhost:4400` Node.js) of (voor de Ripple-service).  Deze URL's zijn echter voorbeelden - uw situatie, ook voor de services die in de voorbeelden worden genoemd, kan verschillen.
8. Klik op de knop **Lezen/schrijven** in de rechterbovenhoek van het scherm.
9. Klik op **PUT** de groene PUT-knop.

De instellingen worden op dit moment opgeslagen.  Sluit het browservenster niet totdat de instellingen zijn opgeslagen.
Voeg deze loopback-URL's ook toe aan de CORS-instellingen voor uw app-service:

1. Inloggen bij de [Azure-portal]
2. Selecteer **Alle bronnen** of **App-services** klik vervolgens op de naam van uw mobiele app.
3. Het mes Instellingen wordt automatisch geopend.  Als dit niet het zo is, klikt u op **Alle instellingen**.
4. Klik op **CORS** onder het API-menu.
5. Voer de URL in die u wilt toevoegen in het opgegeven vak en druk op Enter.
6. Voer indien nodig extra URL's in.
7. Klik op **Opslaan** om de instellingen op te slaan.

Het duurt ongeveer 10-15 seconden voordat de nieuwe instellingen van kracht worden.

## <a name="how-to-register-for-push-notifications"></a><a name="register-for-push"></a>Hoe: Registreren voor pushmeldingen
Installeer de [phonegap-plugin-push] om pushmeldingen te verwerken.  Deze plugin kan eenvoudig `cordova plugin add` worden toegevoegd met behulp van de opdracht op de command line, of via de Git plugin installer binnen Visual Studio.  De volgende code in uw Apache Cordova-app registreert uw apparaat op pushmeldingen:

```javascript
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Gebruik de Notification Hubs SDK om pushmeldingen vanaf de server te verzenden.  Verstuur nooit pushmeldingen rechtstreeks van clients. Dit kan worden gebruikt om een denial of service-aanval op Notification Hubs of de PNS te activeren.  De PNS kan uw verkeer te verbieden als gevolg van dergelijke aanvallen.

## <a name="more-information"></a>Meer informatie

U vindt gedetailleerde API-details in onze [API-documentatie.](https://azure.github.io/azure-mobile-apps-js-client/)

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com
[Snel aan de slag met Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Aan de slag met verificatie]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-plug-in voor Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[uw eerste Apache Cordova app]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-apparaat]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

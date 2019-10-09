---
title: Apache Cordova-invoeg toepassing voor Azure Mobile Apps gebruiken
description: Apache Cordova-invoeg toepassing voor Azure Mobile Apps gebruiken
services: app-service\mobile
documentationcenter: javascript
author: elamalani
manager: crdun
editor: ''
ms.assetid: a56a1ce4-de0c-4f3c-8763-66252c52aa59
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.author: emalani
ms.openlocfilehash: 3cf18e6da56b25e453d52dc58020961f672da27d
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027430"
---
# <a name="how-to-use-apache-cordova-client-library-for-azure-mobile-apps"></a>Apache Cordova-client bibliotheek gebruiken voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end en geïntegreerde services in de ontwikkeling van mobiele apps. Ontwikkel aars kunnen services **bouwen**, **testen** en **distribueren** om een continue integratie-en leverings pijplijn in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkel aars de status en het gebruik van hun app bewaken met behulp van de **analyse** -en **diagnose** Services en gebruikers benaderen met behulp van de **Push** service. Ontwikkel aars kunnen ook gebruikmaken van **auth** voor het verifiëren van hun gebruikers en **gegevens** service om app-gegevens in de Cloud op te slaan en te synchroniseren.
> Als u Cloud Services wilt integreren in uw mobiele toepassing, meldt u zich aan met App Center [app Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc) vandaag.

## <a name="overview"></a>Overzicht
Deze hand leiding leert u veelvoorkomende scenario's uit te voeren met behulp van de nieuwste [Apache Cordova-invoeg toepassing voor Azure Mobile apps]. Als u nog geen ervaring hebt met Azure Mobile Apps, voltooit u eerst [azure Mobile Apps Quick Start] om een back-end te maken, een tabel te maken en een vooraf gebouwd Apache Cordova-project te downloaden. In deze hand leiding richten we ons op de client-side Apache Cordova-invoeg toepassing.

## <a name="supported-platforms"></a>Ondersteunde platforms
Deze SDK ondersteunt Apache Cordova v 6.0.0 en hoger op iOS-, Android-en Windows-apparaten.  De ondersteuning voor het platform is als volgt:

* Android API 19-24 (KitKat via Nougat).
* iOS-versies 8,0 en hoger.
* Windows Phone 8,1.
* Universeel Windows-platform.

## <a name="Setup"></a>Installatie en vereisten
In deze hand leiding wordt ervan uitgegaan dat u een back-end hebt gemaakt met een tabel. In deze hand leiding wordt ervan uitgegaan dat de tabel hetzelfde schema heeft als de tabellen in deze zelf studies. In deze hand leiding wordt ook ervan uitgegaan dat u de Apache Cordova-invoeg toepassing aan uw code hebt toegevoegd.  Als u dit nog niet hebt gedaan, kunt u de Apache Cordova-invoeg toepassing toevoegen aan uw project op de opdracht regel:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Raadpleeg de documentatie voor meer informatie over het maken van [uw eerste Apache Cordova-app].

## <a name="ionic"></a>Een ionogene v2-app instellen

Als u een ionogene v2-project correct wilt configureren, maakt u eerst een eenvoudige app en voegt u de Cordova-invoeg toepassing toe:

```
ionic start projectName --v2
cd projectName
ionic plugin add cordova-plugin-ms-azure-mobile-apps
```

Voeg de volgende regels toe aan `app.component.ts` om het client object te maken:

```typescript
declare var WindowsAzure: any;
var client = new WindowsAzure.MobileServiceClient("https://yoursite.azurewebsites.net");
```

U kunt nu het project maken en uitvoeren in de browser:

```
ionic platform add browser
ionic run browser
```

De Azure Mobile Apps Cordova-invoeg toepassing ondersteunt zowel ionogene v1-als v2-apps.  Alleen voor de ionogene v2-apps is de extra declaratie vereist voor het `WindowsAzure`-object.

[!INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Procedures: Gebruikers verifiëren
Azure App Service ondersteunt het verifiëren en autoriseren van app-gebruikers met behulp van verschillende externe ID-providers: Facebook, Google, micro soft-account en Twitter. U kunt machtigingen voor tabellen instellen om de toegang tot specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U kunt ook de identiteit van geverifieerde gebruikers gebruiken voor het implementeren van autorisatie regels in Server scripts. Zie voor meer informatie de zelf studie [aan de slag met verificatie] .

Wanneer u verificatie gebruikt in een Apache Cordova-app, moeten de volgende Cordova-invoeg toepassingen beschikbaar zijn:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Twee verificatie stromen worden ondersteund: een server stroom en een client stroom.  De server stroom biedt de eenvoudigste verificatie-ervaring, omdat deze afhankelijk is van de webauthenticatie interface van de provider. De client stroom maakt een diep gaande integratie mogelijk met apparaatspecifieke mogelijkheden, zoals eenmalige aanmelding, afhankelijk van specifieke Sdk's voor een apparaat.

[!INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Procedures: Configureer uw mobiele App Service voor externe omleidings-Url's.
Verschillende typen Apache Cordova-toepassingen gebruiken een loop back-mogelijkheid voor het verwerken van OAuth-gebruikers interface stromen.  OAuth-UI-stromen op localhost veroorzaken problemen omdat de verificatie service alleen weet hoe u uw service standaard moet gebruiken.  Voor beelden van problematische OAuth-UI-stromen zijn:

* De rimpel-emulator.
* Live opnieuw laden met ionogene.
* De mobiele back-end lokaal uitvoeren
* De mobiele back-end wordt uitgevoerd in een andere Azure App Service dan die waarvoor een verificatie wordt geboden.

Volg deze instructies om uw lokale instellingen toe te voegen aan de configuratie:

1. Meld u aan bij [Azure-portal].
2. Selecteer **alle resources** of **app Services** Klik op de naam van uw mobiele app.
3. Klik op **extra**
4. Klik op **resource Verkenner** in het menu observeren en klik vervolgens op **Go**.  Er wordt een nieuw venster of tabblad geopend.
5. Vouw de **configuratie**-, **authsettings** -knoop punten voor uw site uit in de linkernavigatiebalk.
6. Klik op **bewerken**
7. Zoek het element ' allowedExternalRedirectUrls '.  Deze kan worden ingesteld op null of op een matrix met waarden.  Wijzig de waarde in de volgende waarde:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Vervang de Url's door de Url's van uw service.  Voor beelden zijn `http://localhost:3000` (voor de voor beeld-service van node. js) of `http://localhost:4400` (voor de rimpel-service).  Deze Url's zijn echter voor beelden: uw situatie, met inbegrip van de services die in de voor beelden worden genoemd, kunnen afwijken.
8. Klik op de knop **lezen/schrijven** in de rechter bovenhoek van het scherm.
9. Klik op de knop groene **plaats** .

De instellingen worden op dit moment opgeslagen.  Sluit het browser venster pas nadat de instellingen zijn opgeslagen.
Voeg deze loop back-Url's ook toe aan de CORS-instellingen voor uw App Service:

1. Meld u aan bij [Azure-portal].
2. Selecteer **alle resources** of **app Services** Klik op de naam van uw mobiele app.
3. De Blade instellingen wordt automatisch geopend.  Als dat niet het geval is, klikt u op **alle instellingen**.
4. Klik op **CORS** onder het API-menu.
5. Voer de URL in die u wilt toevoegen in het meegeleverde vak en druk op ENTER.
6. Voer indien nodig aanvullende Url's in.
7. Klik op **Opslaan** om de instellingen op te slaan.

Het duurt ongeveer 10-15 seconden voordat de nieuwe instellingen van kracht worden.

## <a name="register-for-push"></a>Procedures: Registreren voor push meldingen
Installeer de [PhoneGap-plugin-push] om Push meldingen te verwerken.  Deze invoeg toepassing kan eenvoudig worden toegevoegd met behulp van de `cordova plugin add`-opdracht op de opdracht regel of via het installatie programma voor de Git-invoeg toepassing in Visual Studio.  Met de volgende code in uw Apache Cordova-app wordt uw apparaat voor push meldingen geregistreerd:

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

Gebruik de Notification Hubs SDK om Push meldingen van de server te verzenden.  Nooit push meldingen rechtstreeks vanuit clients verzenden. Dit kan worden gebruikt om een DOS-aanval (Denial of service) te activeren voor Notification Hubs of de PNS.  De PNS kan uw verkeer als gevolg van dergelijke aanvallen verleiden.

## <a name="more-information"></a>Meer informatie

In onze [API-documentatie](https://azure.github.io/azure-mobile-apps-js-client/)vindt u gedetailleerde informatie over de API.

<!-- URLs. -->
[Azure-portal]: https://portal.azure.com
[Azure Mobile Apps Quick Start]: app-service-mobile-cordova-get-started.md
[Aan de slag met verificatie]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-invoeg toepassing voor Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[uw eerste Apache Cordova-app]: https://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plugin-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

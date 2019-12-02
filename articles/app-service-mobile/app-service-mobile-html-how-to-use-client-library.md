---
title: De Java script-SDK gebruiken
description: V voor Azure Mobile Apps gebruiken
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 0f70523b5b27a4f3db86bacf39469e233e43f807
ms.sourcegitcommit: 3d4917ed58603ab59d1902c5d8388b954147fe50
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74668833"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>De Java script-client bibliotheek voor Azure-Mobile Apps gebruiken
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

> [!NOTE]
> Visual Studio App Center ondersteunt end-to-end-services en geïntegreerde services die een centrale rol spelen bij het ontwikkelen van mobiele apps. Ontwikkelaars kunnen services **bouwen**, **testen** en **distribueren** om een CI/CD-pijplijn (continue integratie en continue levering) in te stellen. Zodra de app is geïmplementeerd, kunnen ontwikkelaars de status en het gebruik van hun app controleren met behulp van de **analyseservice** en de **diagnoseservice** en communiceren met gebruikers met behulp van de **pushservice**. Ontwikkelaars kunnen ook gebruikmaken van **Auth** voor het verifiëren van gebruikers en van **Data** Service voor het persistent maken en synchroniseren van app-gegevens in de cloud.
>
> Als u cloudservices wilt integreren in uw mobiele toepassing, meldt u zich aan bij [App Center](https://appcenter.ms/?utm_source=zumo&utm_medium=Azure&utm_campaign=zumo%20doc).

## <a name="overview"></a>Overzicht
Deze hand leiding leert u veelvoorkomende scenario's uit te voeren met behulp van de nieuwste [Java script-SDK voor Azure Mobile Apps]. Als u nog geen ervaring hebt met Azure Mobile Apps, voltooit u eerst [azure Mobile Apps Quick Start] om een back-end te maken en een tabel te maken. In deze hand leiding richten we ons op het gebruik van de mobiele back-end in HTML/Java script-webtoepassingen.

## <a name="supported-platforms"></a>Ondersteunde platforms
De browser ondersteuning wordt beperkt tot de huidige en laatste versies van de belangrijkste browsers: Google Chrome, micro soft Edge, micro soft Internet Explorer en Mozilla Firefox.  We verwachten dat de SDK werkt met een relatief moderne browser.

Het pakket wordt gedistribueerd als een universele java script-module, zodat het globale, AMD-en CommonJS-indelingen ondersteunt.

## <a name="Setup"></a>Installatie en vereisten
In deze hand leiding wordt ervan uitgegaan dat u een back-end hebt gemaakt met een tabel. In deze hand leiding wordt ervan uitgegaan dat de tabel hetzelfde schema heeft als de tabellen in deze zelf studies.

U kunt de Azure Mobile Apps java script SDK installeren met behulp van de `npm` opdracht:

```
npm install azure-mobile-apps-client --save
```

De bibliotheek kan ook worden gebruikt als een ES2015-module in CommonJS-omgevingen zoals Browserify en webpack en als een AMD-bibliotheek.  Bijvoorbeeld:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

U kunt ook een vooraf ontwikkelde versie van de SDK gebruiken door rechtstreeks vanuit onze CDN te downloaden:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="auth"></a>Procedure: gebruikers verifiëren
Azure App Service ondersteunt het verifiëren en autoriseren van app-gebruikers met behulp van verschillende externe ID-providers: Facebook, Google, micro soft-account en Twitter. U kunt machtigingen voor tabellen instellen om de toegang tot specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U kunt ook de identiteit van geverifieerde gebruikers gebruiken voor het implementeren van autorisatie regels in Server scripts. Zie voor meer informatie de zelf studie [aan de slag met verificatie] .

Twee verificatie stromen worden ondersteund: een server stroom en een client stroom.  De server stroom biedt de eenvoudigste verificatie-ervaring, omdat deze afhankelijk is van de webauthenticatie interface van de provider. De client stroom biedt een diep gaande integratie met apparaatspecifieke mogelijkheden, zoals eenmalige aanmelding, afhankelijk van providerspecifieke Sdk's.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="configure-external-redirect-urls"></a>Procedure: uw mobiele App Service configureren voor externe omleidings-Url's.
Verschillende typen java script-toepassingen gebruiken een loop back-mogelijkheid voor het verwerken van OAuth-gebruikers interface stromen.  Deze mogelijkheden zijn onder andere:

* Uw service lokaal uitvoeren
* Live opnieuw laden met het ionogene-Framework
* Omleiden naar App Service voor authenticatie.

Lokaal uitvoeren kan problemen veroorzaken omdat App Service verificatie standaard alleen is geconfigureerd om toegang via de back-end van uw mobiele app toe te staan. Gebruik de volgende stappen om de App Service-instellingen te wijzigen om verificatie in te scha kelen wanneer de server lokaal wordt uitgevoerd:

1. Meld u aan bij [Azure-portal].
2. Navigeer naar de back-end van uw mobiele app.
3. Selecteer **resource Verkenner** in het menu **Hulpprogram Ma's voor ontwikkel aars** .
4. Klik op **Start** om de resource Explorer te openen voor de back-end van uw mobiele app in een nieuw tabblad of venster.
5. Vouw het knoop punt **config** > **authsettings** uit voor uw app.
6. Klik op de knop **bewerken** om het bewerken van de resource in te scha kelen.
7. Zoek het **allowedExternalRedirectUrls** -element, dat null moet zijn. Voeg uw Url's toe aan een matrix:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Vervang de Url's in de matrix door de Url's van uw service, die in dit voor beeld `http://localhost:3000` voor de lokale node. js-voorbeeld service. U kunt ook `http://localhost:4400` gebruiken voor de rimpel-service of een andere URL, afhankelijk van de configuratie van uw app.
8. Klik boven aan de pagina op **lezen/schrijven**en klik vervolgens op **put** om uw updates op te slaan.

U moet ook dezelfde loop back-Url's toevoegen aan de CORS white list-instellingen:

1. Ga terug naar de [Azure-portal].
2. Navigeer naar de back-end van uw mobiele app.
3. Klik op **CORS** in het **API** -menu.
4. Voer elke URL in het tekstvak lege **toegestane oorsprong** in.  Er wordt een nieuw tekstvak gemaakt.
5. Klik op **Opslaan**

Nadat de back-end is bijgewerkt, kunt u de nieuwe loop back-Url's in uw app gebruiken.

<!-- URLs. -->
[Azure Mobile Apps Quick Start]: app-service-mobile-cordova-get-started.md
[Aan de slag met verificatie]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-portal]: https://portal.azure.com/
[Java script-SDK voor Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

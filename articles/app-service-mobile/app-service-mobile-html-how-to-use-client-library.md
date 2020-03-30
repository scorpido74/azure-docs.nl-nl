---
title: De JavaScript SDK gebruiken
description: V gebruiken voor Azure Mobile Apps
ms.assetid: 53b78965-caa3-4b22-bb67-5bd5c19d03c4
ms.tgt_pltfrm: html
ms.devlang: javascript
ms.topic: article
ms.date: 06/25/2019
ms.openlocfilehash: 029b01f3aacc928ebdae0e8fe90871437afccea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77461518"
---
# <a name="how-to-use-the-javascript-client-library-for-azure-mobile-apps"></a>De JavaScript-clientbibliotheek gebruiken voor Azure Mobile Apps
[!INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

## <a name="overview"></a>Overzicht
In deze handleiding leert u veelvoorkomende scenario's uit te voeren met behulp van de nieuwste [JavaScript SDK voor Azure Mobile Apps.] Als u nieuw bent in Azure Mobile Apps, voltooit u [Azure Mobile Apps Snel aan de slag] om een backend te maken en een tabel te maken. In deze handleiding richten we ons op het gebruik van de mobiele backend in HTML/JavaScript-webtoepassingen.

## <a name="supported-platforms"></a>Ondersteunde platforms
We beperken de browserondersteuning tot de huidige en laatste versies van de grote browsers: Google Chrome, Microsoft Edge, Microsoft Internet Explorer en Mozilla Firefox.  We verwachten dat de SDK met een relatief moderne browser functioneert.

Het pakket wordt gedistribueerd als een universele JavaScript-module, dus het ondersteunt globalen, AMD- en CommonJS-indelingen.

## <a name="setup-and-prerequisites"></a><a name="Setup"></a>Instellingen en vereisten
In deze handleiding wordt ervan uitgegaan dat u een back-end met een tabel hebt gemaakt. In deze handleiding wordt ervan uitgegaan dat de tabel hetzelfde schema heeft als de tabellen in die zelfstudies.

Het installeren van de Azure Mobile Apps `npm` JavaScript SDK kan worden gedaan via de opdracht:

```
npm install azure-mobile-apps-client --save
```

De bibliotheek kan ook worden gebruikt als ES2015-module, binnen CommonJS-omgevingen zoals Browserify en Webpack en als EEN AMD-bibliotheek.  Bijvoorbeeld:

```javascript
// For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
// For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

U ook een vooraf gebouwde versie van de SDK gebruiken door rechtstreeks van onze CDN te downloaden:

```html
<script src="https://zumo.blob.core.windows.net/sdk/azure-mobile-apps-client.min.js"></script>
```

[!INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

## <a name="how-to-authenticate-users"></a><a name="auth"></a>Hoe: Gebruikers verifiëren
Azure App Service ondersteunt het authenticeren en autoriseren van app-gebruikers met behulp van verschillende externe identiteitsproviders: Facebook, Google, Microsoft-account en Twitter. U machtigingen voor tabellen instellen om de toegang voor specifieke bewerkingen te beperken tot alleen geverifieerde gebruikers. U ook de identiteit van geverifieerde gebruikers gebruiken om autorisatieregels in serverscripts te implementeren. Zie de [zelfstudie Aan de slag met verificatie] voor meer informatie.

Twee verificatiestromen worden ondersteund: een serverstroom en een clientstroom.  De serverstroom biedt de eenvoudigste verificatie-ervaring, omdat deze is gebaseerd op de webverificatie-interface van de provider. De clientflow zorgt voor een diepere integratie met apparaatspecifieke mogelijkheden, zoals single-sign-on, omdat deze afhankelijk is van providerspecifieke SDK's.

[!INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

### <a name="how-to-configure-your-mobile-app-service-for-external-redirect-urls"></a><a name="configure-external-redirect-urls"></a>How to: Configureer uw mobiele app-service voor externe omleidings-URL's.
Verschillende typen JavaScript-toepassingen gebruiken een loopback-mogelijkheid om OAuth UI-stromen te verwerken.  Deze mogelijkheden omvatten:

* Uw service lokaal uitvoeren
* Live laden gebruiken met het Ionische Framework
* Doorverwijzen naar App Service voor verificatie.

Lokaal worden uitgevoerd, kan problemen veroorzaken omdat app-serviceverificatie standaard alleen is geconfigureerd om toegang te verlenen via de back-end van uw mobiele app. Gebruik de volgende stappen om de instellingen van de App-service te wijzigen om verificatie in te schakelen wanneer de server lokaal wordt uitgevoerd:

1. Inloggen bij de [Azure-portal]
2. Navigeer naar de back-end van uw mobiele app.
3. Selecteer **Resourceexplorer** in het menu **TOOLS VOOR ONTWIKKELING.**
4. Klik **op Ga** om de bronverkenner voor de back-end van uw mobiele app te openen in een nieuw tabblad of venster.
5. Vouw het knooppunt **config** > **authsettings** voor uw app uit.
6. Klik **op** de knop Bewerken om het bewerken van de resource in te schakelen.
7. Zoek het element **toegestaneExternalRedirectUrls,** dat null moet zijn. Voeg uw URL's toe aan een array:

         "allowedExternalRedirectUrls": [
             "http://localhost:3000",
             "https://localhost:3000"
         ],

    Vervang de URL's in de array door de URL's van uw service, die in dit voorbeeld voor de lokale voorbeeldservice Node.js is. `http://localhost:3000` U `http://localhost:4400` ook gebruik maken voor de Ripple-service of een andere URL, afhankelijk van hoe uw app is geconfigureerd.
8. Klik boven aan de pagina op **Lezen/schrijven**en klik vervolgens op **PUT** om uw updates op te slaan.

U moet ook dezelfde lusback-URL's toevoegen aan de cors-whitelist-instellingen:

1. Navigeer terug naar de [Azure-portal.]
2. Navigeer naar de back-end van uw mobiele app.
3. Klik **op CORS** in het **API-menu.**
4. Voer elke URL in het lege **tekstvak Toegestane oorsprong** in.  Er wordt een nieuw tekstvak gemaakt.
5. Klik **op OPSLAAN**

Na de backend-updates u de nieuwe loopback-URL's in uw app gebruiken.

<!-- URLs. -->
[Snel aan de slag met Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Aan de slag met verificatie]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Azure-portal]: https://portal.azure.com/
[JavaScript SDK voor Azure Mobile Apps]: https://www.npmjs.com/package/azure-mobile-apps-client
[Query object documentation]: https://msdn.microsoft.com/library/azure/jj613353.aspx

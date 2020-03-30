---
title: Vergunningverwekkers en hoe deze in staat te worden gesteld | Azure
description: Meer informatie over de verschillende autorisatieagents waarmee de Microsoft Authentication Library (MSAL) uw Android-app kan gebruiken en hoe u deze inschakelen.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: marsma
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: 4f1b3fc5b60069cfa47d437e4341ded141204418
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77085331"
---
# <a name="authorization-agents-android"></a>Machtigingsagenten (Android)

In dit artikel worden de verschillende autorisatieagents beschreven die de Microsoft Authentication Library (MSAL) uw app kan gebruiken en hoe u deze inschakelen.

Het kiezen van een specifieke strategie voor autorisatieagents is optioneel en vertegenwoordigt extra functionaliteit die apps kunnen aanpassen. De meeste apps gebruiken de MSAL-standaardinstellingen (zie [Het Configuratiebestand van Android MSAL begrijpen](msal-configuration.md) om de verschillende standaardinstellingen te bekijken).

MSAL ondersteunt autorisatie `WebView`met behulp van een , of de systeembrowser.  De afbeelding hieronder laat zien `WebView`hoe het eruit ziet met behulp van de , of het systeem browser met CustomTabs of zonder CustomTabs:

![MSAL login voorbeelden](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicaties voor één aanmelding

Standaard gebruiken toepassingen die zijn geïntegreerd met MSAL de aangepaste tabbladen van de systeembrowser om te autoriseren. In tegenstelling tot WebViews delen aangepaste tabbladen een cookiepot met de standaardsysteembrowser, waardoor minder aanmeldingen mogelijk zijn met web- of andere native apps die zijn geïntegreerd met aangepaste tabbladen.

Als de toepassing `WebView` een strategie gebruikt zonder microsoft authenticator of bedrijfsportalondersteuning in hun app te integreren, hebben gebruikers geen Enkele SSO-ervaring (Sign On) op het apparaat of tussen native apps en web-apps.

Als de toepassing MSAL gebruikt met Microsoft Authenticator of Company Portal-ondersteuning, kunnen gebruikers een SSO-ervaring hebben in verschillende toepassingen als de gebruiker een actieve aanmelding heeft met een van de apps.

## <a name="webview"></a>Webview

Als u de in-app WebView wilt gebruiken, plaatst u de volgende regel in de app-configuratie JSON die wordt doorgegeven aan MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Bij gebruik van `WebView`de in-app meldt de gebruiker zich rechtstreeks bij de app. De tokens worden bewaard in de sandbox van de app en zijn niet beschikbaar buiten de cookiepot van de app. Als gevolg hiervan kan de gebruiker geen SSO-ervaring hebben in verschillende toepassingen, tenzij de apps worden geïntegreerd met de Authenticator of Company Portal.

Biedt `WebView` echter wel de mogelijkheid om de look en feel voor aanmeldingsgebruikersinterface aan te passen. Zie [Android WebViews](https://developer.android.com/reference/android/webkit/WebView) voor meer informatie over hoe u deze aanpassing doen.

## <a name="default-browser-plus-custom-tabs"></a>Standaardbrowser plus aangepaste tabbladen

Standaard gebruikt MSAL de browser en een [aangepaste tabbladenstrategie.](https://developer.chrome.com/multidevice/android/customtabs) U deze strategie expliciet aangeven om `DEFAULT` wijzigingen in toekomstige releases te voorkomen door de volgende JSON-configuratie in het aangepaste configuratiebestand te gebruiken:

```json
"authorization_user_agent" : "BROWSER"
```

Gebruik deze aanpak om een SSO-ervaring te bieden via de browser van het apparaat. MSAL maakt gebruik van een gedeelde cookiepot, waarmee andere native apps of webapps SSO op het apparaat kunnen bereiken met behulp van de persist-sessiecookie die door MSAL is ingesteld.

## <a name="browser-selection-heuristic"></a>Browser selectie heuristisch

Omdat het voor MSAL onmogelijk is om het exacte browserpakket te specificeren dat op elk van de brede reeks Android-telefoons moet worden gebruikt, implementeert MSAL een browserselectie heuristisch die probeert de beste sso met meerdere apparaten te bieden.

MSAL haalt de volledige lijst op met browsers die op het apparaat zijn geïnstalleerd om te selecteren welke browser u wilt gebruiken. De lijst is in de volgorde geretourneerd door de package manager, die indirect de voorkeuren van de gebruiker weerspiegelt. De standaardbrowser is bijvoorbeeld de eerste vermelding in de lijst als deze is ingesteld. De _eerste_ browser in de lijst wordt gekozen, ongeacht of deze aangepaste tabbladen ondersteunt. Als de browser aangepaste tabbladen ondersteunt, start MSAL het aangepaste tabblad. Aangepaste tabbladen `WebView` hebben een blik en gevoel dichter bij een in-app en staan eenvoudige aanpassing van de gebruikersinterface toe. Zie [Aangepaste tabbladen in Android](https://developer.chrome.com/multidevice/android/customtabs) voor meer informatie.

Als er geen browserpakketten op het apparaat zijn, `WebView`gebruikt MSAL de in-app.

De volgorde van browsers in de browserlijst wordt bepaald door het besturingssysteem. Het is in orde van de meeste voorkeur aan de minste. Als de standaardinstelling van het apparaat niet wordt gewijzigd, moet dezelfde browser worden gestart voor elke aanmelding om een SSO-ervaring te garanderen.

> [!NOTE]
> MSAL geeft niet langer altijd de voorkeur aan Chrome als een andere browser als standaard is ingesteld. Op een apparaat dat zowel Chrome als een andere browser vooraf heeft geïnstalleerd, gebruikt MSAL bijvoorbeeld de browser die de gebruiker als standaard heeft ingesteld.

### <a name="tested-browsers"></a>Geteste browsers

De volgende browsers zijn getest om te zien `"redirect_uri"` of ze correct worden omgeleid naar het opgegeven in het configuratiebestand:

| | Ingebouwde browser | Chrome | Opera  | Microsoft Edge | UC-browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Pass | Pass |niet van toepassing |niet van toepassing |niet van toepassing |niet van toepassing |
| Samsung S7 (API 25) | pass* | Pass | Pass | Pass | Niet |Pass |
| Huawei (API 26) |pass** | Pass | Niet | Pass | Pass |Pass |
| Vivo (API 26) |Pass|Pass|Pass|Pass|Pass|Niet|
| Pixel 2 (API 26) |Pass | Pass | Pass | Pass | Niet |Pass |
| Oppo (Oppo) | Pass | niet van toepassing*** |niet van toepassing  |niet van toepassing |niet van toepassing | niet van toepassing|
| OnePlus (API 25) |Pass | Pass | Pass | Pass | Niet |Pass |
| Nexus (API 28) |Pass | Pass | Pass | Pass | Niet |Pass |
|MI | Pass | Pass | Pass | Pass | Niet |Pass |

* Samsung's ingebouwde browser is Samsung Internet.  
**Huawei's ingebouwde browser is Huawei Browser.  
De standaardbrowser kan niet worden gewijzigd in de instelling van het Oppo-apparaat.

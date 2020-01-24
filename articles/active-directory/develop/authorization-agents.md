---
title: Autorisatie agenten en hoe ze kunnen worden ingeschakeld | Azure
description: Meer informatie over de verschillende autorisatie agenten in de micro soft Authentication Library (MSAL) kan uw Android-app gebruiken en hoe deze kunnen worden ingeschakeld.
services: active-directory
author: tylermsft
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: shoatman, brianmel, hahamil
ms.custom: aaddev
ms.openlocfilehash: c2d0c8a877ba856aa7a93bfc960c44b63cbe8f9c
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76697996"
---
# <a name="authorization-agents-android"></a>Machtigingsagenten (Android)

In dit artikel worden de verschillende autorisatie agenten beschreven die de micro soft Authentication Library (MSAL) kan gebruiken en hoe deze kunnen worden ingeschakeld.

Het kiezen van een specifieke strategie voor autorisatie agenten is optioneel en vertegenwoordigt extra functionaliteits-apps die kunnen worden aangepast. De meeste apps gebruiken de standaard instellingen voor MSAL (Zie [het configuratie bestand van de ANDROID MSAL begrijpen](msal-configuration.md) voor een overzicht van de verschillende standaard waarden).

MSAL ondersteunt autorisatie met behulp van een `WebView`of de systeem browser.  In de onderstaande afbeelding ziet u hoe deze wordt weer gegeven met behulp van de `WebView`, of de systeem browser met CustomTabs of zonder CustomTabs:

![Voor beelden van MSAL-aanmelding](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicaties van eenmalige aanmelding

Toepassingen die zijn geïntegreerd met MSAL gebruiken standaard de aangepaste tabbladen van de systeem browser om te autoriseren. In tegens telling tot webweergaven delen aangepaste tabbladen een cookie jar met de standaard systeem browser om minder aanmeldingen mogelijk te maken met web of andere systeem eigen apps die zijn geïntegreerd met aangepaste tabbladen.

Als de toepassing gebruikmaakt van een `WebView` strategie zonder Microsoft Authenticator of Bedrijfsportal ondersteuning te integreren in de app, hebben gebruikers geen SSO-ervaring (eenmalige aanmelding) op het apparaat of tussen systeem eigen apps en web-apps.

Als de toepassing gebruikmaakt van MSAL met Microsoft Authenticator-of Bedrijfsportal-ondersteuning, kunnen gebruikers een SSO-ervaring hebben voor toepassingen als de gebruiker zich heeft aangemeld met een van de apps.

## <a name="webview"></a>WebView

Als u de webweergave in-app wilt gebruiken, plaatst u de volgende regel in de JSON van de app-configuratie die wordt door gegeven aan MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Wanneer u de in-app-`WebView`gebruikt, wordt de gebruiker rechtstreeks aangemeld bij de app. De tokens worden bewaard in de sandbox van de app en zijn niet beschikbaar buiten het cookie jar van de app. Als gevolg hiervan kan de gebruiker geen SSO-ervaring hebben voor toepassingen, tenzij de apps worden geïntegreerd met de verificator of Bedrijfsportal.

`WebView` biedt echter de mogelijkheid om het uiterlijk aan te passen aan de gebruikers interface voor aanmelden. Zie [Android-webviewers](https://developer.android.com/reference/android/webkit/WebView) voor meer informatie over hoe u deze aanpassing kunt uitvoeren.

## <a name="default-browser-plus-custom-tabs"></a>Standaard browser plus aangepaste tabbladen

MSAL maakt standaard gebruik van de browser en een strategie voor [aangepaste tabbladen](https://developer.chrome.com/multidevice/android/customtabs) . U kunt deze strategie expliciet aanduiden om te voor komen dat wijzigingen in toekomstige releases worden `DEFAULT` door gebruik te maken van de volgende JSON-configuratie in het aangepaste configuratie bestand:

```json
"authorization_user_agent" : "BROWSER"
```

Gebruik deze methode om een SSO-ervaring te bieden via de browser van het apparaat. MSAL maakt gebruik van een gedeeld cookie jar, waarmee andere systeem eigen apps of web-apps SSO kunnen bezorgen op het apparaat met behulp van de permanente sessie cookie die door MSAL is ingesteld.

## <a name="browser-selection-heuristic"></a>Selectie heuristiek van browser

Omdat het niet mogelijk is om het exacte browser pakket op te geven dat moet worden gebruikt voor elk van de brede matrix van Android-telefoons, wordt door MSAL een MSAL geïmplementeerd die de beste SSO van meerdere apparaten probeert te bieden.

MSAL haalt de volledige lijst met browsers die op het apparaat zijn geïnstalleerd om te selecteren welke browser moet worden gebruikt. De lijst bevindt zich in de volg orde die wordt geretourneerd door package manager, die indirect overeenkomt met de voor keuren van de gebruiker. Zo is de standaard browser, indien ingesteld, het eerste item in de lijst. De _eerste_ browser in de lijst wordt gekozen, ongeacht of deze aangepaste tabbladen ondersteunt. Als de browser aangepaste tabbladen ondersteunt, wordt het aangepaste tabblad in MSAL geopend. aangepaste tabbladen hebben een uiterlijk en een in-app-`WebView` en bieden eenvoudige aanpassing van de gebruikers interface. Zie [aangepaste tabbladen in Android](https://developer.chrome.com/multidevice/android/customtabs) voor meer informatie.

Als er geen browser pakketten aanwezig zijn op het apparaat, maakt MSAL gebruik van de in-app-`WebView`.

De volg orde van de browsers in de browser lijst wordt bepaald door het besturings systeem. Dit is de juiste volg orde van de voor keur ten minste. Als de standaard instelling van het apparaat niet wordt gewijzigd, moet dezelfde browser worden gestart voor elke aanmelding om te zorgen voor een SSO-ervaring.

> [!NOTE]
> MSAL heeft niet langer de voor keur aan Chrome als een andere browser is ingesteld als standaard. Op een apparaat met zowel Chrome als een andere browser die vooraf is geïnstalleerd, gebruikt MSAL bijvoorbeeld de browser die de gebruiker heeft ingesteld als de standaard instelling.

### <a name="tested-browsers"></a>Geteste browsers

De volgende browsers zijn getest om te zien of ze correct worden omgeleid naar de `"redirect_uri"` die is opgegeven in het configuratie bestand:

| | Ingebouwde browser | Chrome | Opera  | Microsoft Edge | UC-browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | Door | Door |niet van toepassing |niet van toepassing |niet van toepassing |niet van toepassing |
| Samsung S7 (API 25) | door | Door | Door | Door | mislukt |Door |
| Huawei (API 26) |Pass-* * | Door | mislukt | Door | Door |Door |
| Vivo (API 26) |Door|Door|Door|Door|Door|mislukt|
| Pixel 2 (API 26) |Door | Door | Door | Door | mislukt |Door |
| Oppo | Door | niet van toepassing * * * |niet van toepassing  |niet van toepassing |niet van toepassing | niet van toepassing|
| OnePlus (API 25) |Door | Door | Door | Door | mislukt |Door |
| Nexus (API 28) |Door | Door | Door | Door | mislukt |Door |
|MI | Door | Door | Door | Door | mislukt |Door |

\* De ingebouwde browser van Samsung is Samsung internet.  
\* * De ingebouwde browser van Huawei is de Huawei-browser.  
De standaard browser kan niet worden gewijzigd in de oppo.

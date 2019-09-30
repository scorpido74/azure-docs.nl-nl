---
title: Autorisatie agenten en hoe ze kunnen worden ingeschakeld | Azure
description: Meer informatie over de verschillende autorisatie agenten in de micro soft Authentication Library (MSAL) kan uw Android-app gebruiken en hoe deze kunnen worden ingeschakeld.
services: active-directory
documentationcenter: dev-center-name
author: tylermsft
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/05/2019
ms.author: twhitney
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: f7daf40d6c6e552d6b76e424359f57f031641039
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679813"
---
# <a name="authorization-agents-android"></a>Autorisatie agenten (Android)

In dit artikel worden de verschillende autorisatie agenten beschreven die de micro soft Authentication Library (MSAL) kan gebruiken en hoe deze kunnen worden ingeschakeld.

Het kiezen van een specifieke strategie voor autorisatie agenten is optioneel en vertegenwoordigt extra functionaliteits-apps die kunnen worden aangepast. De meeste apps gebruiken de standaard instellingen voor MSAL (Zie [het configuratie bestand van de ANDROID MSAL begrijpen](msal-configuration.md) voor een overzicht van de verschillende standaard waarden).

MSAL ondersteunt autorisatie met behulp van een `WebView` of de systeem browser.  In de onderstaande afbeelding ziet u hoe u de `WebView` of de systeem browser met CustomTabs of zonder CustomTabs kunt zien:

![Voor beelden van MSAL-aanmelding](./media/authorization-agents/sign-in-ui.jpg)

## <a name="single-sign-in-implications"></a>Implicaties van eenmalige aanmelding

Toepassingen die zijn geïntegreerd met MSAL gebruiken standaard de aangepaste tabbladen van de systeem browser om te autoriseren. In tegens telling tot webweergaven delen aangepaste tabbladen een cookie jar met de standaard systeem browser om minder aanmeldingen mogelijk te maken met web of andere systeem eigen apps die zijn geïntegreerd met aangepaste tabbladen.

Als de toepassing gebruikmaakt van een `WebView`-strategie zonder de Microsoft Authenticator of Bedrijfsportal ondersteuning in de app te integreren, hebben gebruikers geen SSO-ervaring (eenmalige aanmelding) op het apparaat of tussen systeem eigen apps en web-apps.

Als de toepassing gebruikmaakt van MSAL met Microsoft Authenticator-of Bedrijfsportal-ondersteuning, kunnen gebruikers een SSO-ervaring hebben voor toepassingen als de gebruiker zich heeft aangemeld met een van de apps.

## <a name="webview"></a>WebView

Als u de webweergave in-app wilt gebruiken, plaatst u de volgende regel in de JSON van de app-configuratie die wordt door gegeven aan MSAL:

```json
"authorization_user_agent" : "WEBVIEW"
```

Wanneer u de in-app `WebView` gebruikt, meldt de gebruiker zich rechtstreeks aan bij de app. De tokens worden bewaard in de sandbox van de app en zijn niet beschikbaar buiten het cookie jar van de app. Als gevolg hiervan kan de gebruiker geen SSO-ervaring hebben voor toepassingen, tenzij de apps worden geïntegreerd met de verificator of Bedrijfsportal.

@No__t-0 biedt echter de mogelijkheid om het uiterlijk aan te passen aan de gebruikers interface voor aanmelden. Zie [Android-webviewers](https://developer.android.com/reference/android/webkit/WebView) voor meer informatie over hoe u deze aanpassing kunt uitvoeren.

## <a name="default-browser-plus-custom-tabs"></a>Standaard browser plus aangepaste tabbladen

MSAL maakt standaard gebruik van de browser en een strategie voor [aangepaste tabbladen](https://developer.chrome.com/multidevice/android/customtabs) . U kunt deze strategie expliciet aanduiden om te voor komen dat wijzigingen in toekomstige releases worden `DEFAULT` met behulp van de volgende JSON-configuratie in het aangepaste configuratie bestand:

```json
"authorization_user_agent" : "BROWSER"
```

Gebruik deze methode om een SSO-ervaring te bieden via de browser van het apparaat. MSAL maakt gebruik van een gedeeld cookie jar, waarmee andere systeem eigen apps of web-apps SSO kunnen bezorgen op het apparaat met behulp van de permanente sessie cookie die door MSAL is ingesteld.

## <a name="browser-selection-heuristic"></a>Selectie heuristiek van browser

Omdat het niet mogelijk is om het exacte browser pakket op te geven dat moet worden gebruikt voor elk van de brede matrix van Android-telefoons, wordt door MSAL een MSAL geïmplementeerd die de beste SSO van meerdere apparaten probeert te bieden.

MSAL haalt de volledige lijst met browsers die op het apparaat zijn geïnstalleerd om te selecteren welke browser moet worden gebruikt. De lijst bevindt zich in de volg orde die wordt geretourneerd door package manager, die indirect overeenkomt met de voor keuren van de gebruiker. Zo is de standaard browser, indien ingesteld, het eerste item in de lijst. De _eerste_ browser in de lijst wordt gekozen, ongeacht of deze aangepaste tabbladen ondersteunt. Als de browser aangepaste tabbladen ondersteunt, wordt het aangepaste tabblad MSAL geopend. Aangepaste tabbladen hebben een uiterlijk van een in-app `WebView` en geven de basis aanpassing van de gebruikers interface toe. Zie [aangepaste tabbladen in Android](https://developer.chrome.com/multidevice/android/customtabs) voor meer informatie.

Als er geen browser pakketten aanwezig zijn op het apparaat, maakt MSAL gebruik van de in-app `WebView`.

De volg orde van de browsers in de browser lijst wordt bepaald door het besturings systeem. Dit is de juiste volg orde van de voor keur ten minste. Als de standaard instelling van het apparaat niet wordt gewijzigd, moet dezelfde browser worden gestart voor elke aanmelding om te zorgen voor een SSO-ervaring.

> [!NOTE]
> MSAL heeft niet langer de voor keur aan Chrome als een andere browser is ingesteld als standaard. Op een apparaat met zowel Chrome als een andere browser die vooraf is geïnstalleerd, gebruikt MSAL bijvoorbeeld de browser die de gebruiker heeft ingesteld als de standaard instelling.

### <a name="tested-browsers"></a>Geteste browsers

De volgende browsers zijn getest om te zien of ze correct worden omgeleid naar het `"redirect_uri"` dat is opgegeven in het configuratie bestand:

| | Ingebouwde browser | Chrome | Opera  | Microsoft Edge | UC-browser | Firefox |
| -- |:-------------:| -----:|-----:|-----:|-----:|-----:|
| Nexus 4 (API 17) | door | door |Niet van toepassing |Niet van toepassing |Niet van toepassing |Niet van toepassing |
| Samsung S7 (API 25) | door | door | door | door | Daarvan |door |
| Huawei (API 26) |Pass-* * | door | Daarvan | door | door |door |
| Vivo (API 26) |door|door|door|door|door|Daarvan|
| Pixel 2 (API 26) |door | door | door | door | Daarvan |door |
| Oppo | door | niet van toepassing * * * |Niet van toepassing  |Niet van toepassing |Niet van toepassing | Niet van toepassing|
| OnePlus (API 25) |door | door | door | door | Daarvan |door |
| Nexus (API 28) |door | door | door | door | Daarvan |door |
|MI | door | door | door | door | Daarvan |door |

\* De ingebouwde browser van Samsung is Samsung internet.  
\* * De ingebouwde browser van Huawei is de Huawei-browser.  
De standaard browser kan niet worden gewijzigd in de oppo.

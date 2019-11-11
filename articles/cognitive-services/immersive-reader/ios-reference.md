---
title: Referentie voor insluitende lezer iOS SDK
titleSuffix: Azure Cognitive Services
description: De insluitende lezer iOS SDK is een Swift-CocoaPod waarmee u de insluitende lezer kunt integreren in uw iOS-toepassing.
services: cognitive-services
author: metanMSFT
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: reference
ms.date: 08/01/2019
ms.author: metan
ms.openlocfilehash: 67d6b8c22c5635bd789078a7f91b02f8b07e5e70
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73903124"
---
# <a name="immersive-reader-sdk-reference-for-ios"></a>Naslag Gids voor insluitende lezers SDK voor iOS

De insluitende lezer iOS SDK is een Swift-CocoaPod waarmee u de insluitende lezer kunt integreren in uw iOS-toepassing.

## <a name="functions"></a>Functions

De SDK biedt één functie `launchImmersiveReader(navController, token, subdomain, content, options, onSuccess, onFailure)`.

### <a name="launchimmersivereader"></a>launchImmersiveReader

Hiermee opent u de insluitende lezer door een weergave controller in uw iOS-toepassing te starten.

```swift
public func launchImmersiveReader(navController: UINavigationController, token: String, subdomain: String, content: Content, options: Options?, onSuccess: @escaping () -> Void, onFailure: @escaping (_ error: Error) -> Void)
```

#### <a name="parameters"></a>Parameters

| Naam | Type | Beschrijving |
| ---- | ---- |------------ |
| `navController` | UINavigationController | De navigatie controller voor de iOS-toepassing waarvan de functie wordt aangeroepen. |
| `token` | Tekenreeks | Het Azure AD-verificatie token. Zie de [instructies voor Azure AD-verificatie](./azure-active-directory-authentication.md). |
| `subdomain` | Tekenreeks | Het aangepaste subdomein van uw insluitende lezer-resource in Azure. Zie de [instructies voor Azure AD-verificatie](./azure-active-directory-authentication.md). |
| `content` | [Inhoud](#content) | Een object met de inhoud die in de insluitende lezer moet worden weer gegeven. |
| `options` | [Opties](#options) | Opties voor het configureren van bepaald gedrag van de insluitende lezer. Optioneel. |
| `onSuccess` | ()-> void | Een sluiting die wordt aangeroepen wanneer de insluitende lezer wordt gestart. |
| `onFailure` | (_ fout: [fout](#error))-> ongeldig | Een sluiting die wordt aangeroepen wanneer de insluitende lezer niet kan worden geladen. Deze closure retourneert een [`Error`](#error) -object dat een fout code en fout bericht vertegenwoordigt dat is gekoppeld aan de fout. Zie de [fout codes](#error-codes)voor meer informatie. |

## <a name="types"></a>Typen

### <a name="content"></a>Inhoud

Bevat de inhoud die in de insluitende lezer moet worden weer gegeven.

```swift
struct Content: Encodable {
    var title: String
    var chunks: [Chunk]
}
```

#### <a name="supported-mime-types"></a>Ondersteunde MIME-typen

| MIME-type | Beschrijving |
| --------- | ----------- |
| tekst/zonder opmaak | Tekst zonder opmaak. |
| Application/MathML + XML | MathML (wiskundige Markup Language). [Meer informatie](https://developer.mozilla.org/en-US/docs/Web/MathML).

### <a name="options"></a>Opties

Bevat eigenschappen die bepaald gedrag van de insluitende lezer configureren.

```swift
struct Options {
    var uiLang: String?
    var timeout: TimeInterval?
}
```

### <a name="error"></a>Fout

Bevat informatie over de fout.

```swift
struct Error {
    var code: String
    var message: String
}
```

#### <a name="error-codes"></a>Foutcodes

| Code | Beschrijving |
| ---- | ----------- |
| BadArgument | Het opgegeven argument is ongeldig. Zie `message` voor meer informatie. |
| Out | De insluitende lezer kan niet worden geladen binnen de opgegeven time-out. |
| TokenExpired | Het opgegeven token is verlopen. |
| Beperkt | De limiet voor de aanroep frequentie is overschreden. |
| InternalError | Er is een interne fout opgetreden in de controller van de insluitende lezer weergave. Zie `message` voor meer informatie.|

## <a name="os-version-support"></a>Ondersteuning voor besturingssysteem versies

De insluitende lezer iOS SDK wordt ondersteund voor iOS 9,0 of hoger, op iPad en iPhone.

## <a name="next-steps"></a>Volgende stappen

* De [insluitende lezer IOS SDK op github](https://github.com/microsoft/immersive-reader-sdk/tree/master/iOS) verkennen
* [Snelstartgids: een iOS-app maken waarmee de insluitende lezer wordt gestart (SWIFT)](./ios-quickstart.md)
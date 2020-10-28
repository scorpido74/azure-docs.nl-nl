---
title: 'Azure Active Directory B2C: codevoorbeelden | Microsoft Docs'
description: Codevoorbeelden voor mobiele toepassingen, desktop- en webtoepassingen, en toepassingen met één pagina van Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.author: mimart
ms.date: 10/02/2020
ms.custom: mvc
ms.topic: sample
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 2db64a950569e4a96a9a87bb86a5397a17b4f2ba
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/20/2020
ms.locfileid: "92214937"
---
# <a name="azure-active-directory-b2c-code-samples"></a>Azure Active Directory B2C: codevoorbeelden

In de volgende tabellen ziet u koppelingen naar voorbeelden voor toepassingen, waaronder iOS, Android, .NET en Node.js.

## <a name="mobile-and-desktop-apps"></a>Mobiele apps en desktop-apps

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [ios-swift-native-msal](https://github.com/Azure-Samples/active-directory-b2c-ios-swift-native-msal) | Een iOS-voorbeeld in Swift waarmee Azure AD B2C-gebruikers worden geverifieerd en een API wordt aangeroepen met behulp van OAuth 2.0 |
| [android-native-msal](https://github.com/Azure-Samples/ms-identity-android-java#b2cmodefragment-class) | Een eenvoudige Android-app die laat zien hoe u MSAL kunt gebruiken om gebruikers te verifiëren via Active Directory B2C en toegang kunt krijgen tot een Web-API met behulp van de resulterende tokens. |
| [ios-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-ios-native-appauth) | Een voorbeeld van hoe u een bibliotheek van derden kunt gebruiken om een iOS-toepassing te bouwen in Objective-C waarmee Microsoft-id-gebruikers kunnen worden geverifieerd bij de Azure AD B2C-id-service. |
| [android-native-appauth](https://github.com/Azure-Samples/active-directory-b2c-android-native-appauth) | Een voorbeeld van hoe u een bibliotheek van derden kunt gebruiken om een Android-toepassing te bouwen waarmee Microsoft-id-gebruikers kunnen worden geverifieerd bij de B2C-id-service, en waarmee een Web-API wordt aangeroepen met behulp van OAuth 2.0-toegangstokens. |
| [dotnet-desktop](https://github.com/Azure-Samples/active-directory-b2c-dotnet-desktop) | Een voorbeeld van hoe een gebruiker wordt aangemeld bij een WPF-toepassing (Windows Desktop .NET) via Azure AD B2C, een toegangstoken wordt verkregen met behulp van MSAL.NET en een API wordt aangeroepen. |
| [xamarin-native](https://github.com/Azure-Samples/active-directory-b2c-xamarin-native) | Een eenvoudige Xamarin Forms-app die laat zien hoe u MSAL kunt gebruiken om gebruikers te verifiëren via Active Directory B2C en toegang kunt krijgen tot een Web-API met behulp van de resulterende tokens. |

## <a name="web-apps-and-apis"></a>Web-apps en API's

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [dotnet-webapp-and-webapi](https://github.com/Azure-Samples/active-directory-b2c-dotnet-webapp-and-webapi) | Een gecombineerd voorbeeld voor een .NET-webtoepassing waarmee een .NET Web-API wordt aangeroepen, waarbij beide zijn beveiligd via Azure AD B2C. |
| [dotnetcore-webapp-openidconnect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-5-B2C) | Een ASP.NET Core-webtoepassing die gebruikmaakt van OpenID Connect voor het aanmelden van gebruikers bij Azure AD B2C. |
| [dotnetcore-webapp-msal-api](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C) | Een ASP.NET Core-webtoepassing waarmee een gebruiker wordt aangemeld via Azure AD B2C, een toegangstoken wordt verkregen met behulp van MSAL.NET en een API wordt aangeroepen. |
| [openidconnect-nodejs](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) | Een Node.js-app die een snelle en eenvoudige manier biedt om een webtoepassing in te stellen met Express met behulp van OpenID Connect. |
| [javascript-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) | Een kleine Node.js Web-API voor Azure AD B2C die laat zien hoe u uw Web-API kunt beveiligen en een B2C-toegangstoken kunt accepteren met behulp van passport.js. |
| [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/README_B2C.md) | Laat zien hoe u B2C van Microsoft Identity Platform integreert met een Python-webtoepassing.  |

## <a name="single-page-apps"></a>Apps met één pagina

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [ms-identity-b2c-javascript-spa](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) | Een toepassing met één pagina (SPA) waarmee een Web-API wordt aangeroepen. Verificatie wordt uitgevoerd met Azure AD B2C door gebruik te maken van MSAL.js. In dit voorbeeld wordt de autorisatiecodestroom met PKCE gebruikt. |
| [javascript-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) | Een toepassing met één pagina (SPA) waarmee een Web-API wordt aangeroepen. Verificatie wordt uitgevoerd met Azure AD B2C door gebruik te maken van MSAL.js. In dit voorbeeld wordt de impliciete stroom gebruikt.|

## <a name="saml-test-application"></a>SAML-testtoepassing

| Voorbeeld | Beschrijving |
|--------| ----------- |
| [saml-sp-tester](https://github.com/azure-ad-b2c/saml-sp-tester/tree/master/source-code) | De SAML-testtoepassing voor het testen van Azure AD B2C is geconfigureerd om te fungeren als SAML-id-provider. |

## <a name="api-connectors"></a>API-connectors

De volgende tabellen bieden koppelingen naar codevoorbeelden voor het gebruik van web-API's in uw gebruikersstroom met behulp van [API-connectors](api-connectors-overview.md).

## <a name="api-connector-azure-function-quickstarts"></a>Quickstarts voor API-connectors in Azure Function

| Voorbeeld                                                                                                                          | Beschrijving                                                                                                                                               |
| ------------------------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [.NET Core](https://github.com/Azure-Samples/active-directory-dotnet-external-identities-api-connector-azure-function-validate) | Dit .NET Core Azure Function-voorbeeld laat zien hoe u aanmeldingen kunt beperken tot specifieke e-maildomeinen en door de gebruiker verstrekte informatie kunt valideren. |
| [Node.js](https://github.com/Azure-Samples/active-directory-nodejs-external-identities-api-connector-azure-function-validate)   | Dit Azure Function-voorbeeld voor Node.js laat zien hoe u aanmeldingen kunt beperken tot specifieke e-maildomeinen en door de gebruiker verstrekte informatie kunt valideren.  |
| [Python](https://github.com/Azure-Samples/active-directory-python-external-identities-api-connector-azure-function-validate)    | Dit Azure Function-voorbeeld voor Python laat zien hoe u aanmeldingen kunt beperken tot specifieke e-maildomeinen en door de gebruiker verstrekte informatie kunt valideren.    |
---
title: Hoe om te gaan met SameSite-cookiewijzigingen in de Chrome-browser | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over het omgaan met SameSite-cookiewijzigingen in de Chrome-browser.
services: active-directory
documentationcenter: ''
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 01/27/2020
ms.author: jmprieur
ms.reviewer: kkrishna
ms.custom: aaddev
ms.openlocfilehash: 056b787bbbcde6ba7f9510043deabdcf85ac7467
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050529"
---
# <a name="handle-samesite-cookie-changes-in-chrome-browser"></a>SameSite-cookiewijzigingen in Chrome-browser verwerken

## <a name="what-is-samesite"></a>Wat is SameSite?

`SameSite`is een eigenschap die kan worden ingesteld in HTTP-cookies om aanvallen op cross site request forgery (CSRF) in webapplicaties te voorkomen:

- Wanneer `SameSite` is ingesteld op **Lax,** wordt de cookie verzonden in verzoeken binnen dezelfde site en in GET-verzoeken van andere sites. Het wordt niet verzonden in GET-verzoeken die cross-domein zijn.
- Een waarde van **Strict** zorgt ervoor dat de cookie alleen binnen dezelfde site in aanvragen wordt verzonden.

Standaard is `SameSite` de waarde NIET ingesteld in browsers en daarom zijn er geen beperkingen voor cookies die in verzoeken worden verzonden. Een aanvraag zou moeten opt-in voor de CSRF bescherming door het instellen **van Lax** of **Strict** volgens hun eisen.

## <a name="samesite-changes-and-impact-on-authentication"></a>SameSite-wijzigingen en impact op verificatie

Recente [updates van de standaarden op SameSite](https://tools.ietf.org/html/draft-west-cookie-incrementalism-00) stellen `SameSite` voor om apps te beschermen door het standaardgedrag te maken van wanneer er geen waarde is ingesteld op Lax. Deze beperking betekent dat cookies worden beperkt op HTTP-verzoeken, behalve GET gemaakt van andere sites. Daarnaast wordt een waarde van **Geen** ingevoerd om beperkingen te verwijderen voor het verzonden van cookies. Deze updates zullen binnenkort worden uitgebracht in een aankomende versie van de Chrome-browser.

Wanneer web-apps zich verifiëren met het Microsoft Identity-platform met behulp van de reactiemodus "form_post", reageert de inlogserver op de toepassing met behulp van een HTTP-POST om de tokens of auth-code te verzenden. Omdat dit verzoek een cross-domain `login.microsoftonline.com` request is (van naar uw domein - bijvoorbeeld `https://contoso.com/auth`), vallen cookies die door uw app zijn ingesteld nu onder de nieuwe regels in Chrome. De cookies die moeten worden gebruikt in cross-site scenario's zijn cookies die de *status* en *nonce* waarden bevatten, die ook worden verzonden in het aanmeldingsverzoek. Er zijn andere cookies die door Azure AD zijn verwijderd om de sessie te houden.

Als u uw web-apps niet bijwerkt, leidt dit nieuwe gedrag tot verificatiefouten.

## <a name="mitigation-and-samples"></a>Mitigatie en monsters

Om de verificatiefouten te overwinnen, kunnen web-apps die `SameSite` authenticeren met het Microsoft-identiteitsplatform de eigenschap instellen `None` op cookies die worden gebruikt in cross-domeinscenario's wanneer ze worden uitgevoerd in de Chrome-browser.
Andere browsers (zie [hier](https://www.chromium.org/updates/same-site/incompatible-clients) voor een volledige `SameSite` lijst) volgen het vorige `SameSite=None` gedrag van en zullen de cookies niet opnemen als deze is ingesteld.
Daarom moeten webbrowsers, om verificatie op meerdere browsers `SameSite` te `None` ondersteunen, de waarde alleen in Chrome instellen en de waarde leeg laten in andere browsers.

Deze aanpak wordt gedemonstreerd in onze code samples hieronder.

# <a name="net"></a>[.NET](#tab/dotnet)

De onderstaande tabel presenteert de pull-verzoeken die rond de SameSite-wijzigingen in onze ASP.NET en ASP.NET Core-samples werkten.

| Voorbeeld | Pull-aanvraag |
| ------ | ------------ |
|  [ASP.NET basiszelfstudie van Core Web App](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2)  |  [Dezelfde site cookie fix #261](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/pull/261)  |
|  [ASP.NET MVC-voorbeeld van webapp](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect)  |  [Dezelfde site cookie fix #35](https://github.com/Azure-Samples/ms-identity-aspnet-webapp-openidconnect/pull/35)  |
|  [active-directory-dotnet-admin-restricted-scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2)  |  [Dezelfde site cookie fix #28](https://github.com/Azure-Samples/active-directory-dotnet-admin-restricted-scopes-v2/pull/28)  |

Zie ook: ASP.NET ASP.NET

- [Werken met SameSite-cookies in ASP.NET Kern.](https://docs.microsoft.com/aspnet/core/security/samesite)
- [ASP.NET blog op samesite probleem](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)

# <a name="python"></a>[Python](#tab/python)

| Voorbeeld |
| ------ |
|  [ms-identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp)  |

# <a name="java"></a>[Java](#tab/java)

| Voorbeeld | Pull-aanvraag |
| ------ | ------------ |
|  [ms-identity-java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp)  | [Dezelfde site cookie fix #24](https://github.com/Azure-Samples/ms-identity-java-webapp/pull/24)
|  [ms-identity-java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi)  | [Dezelfde site cookie fix #4](https://github.com/Azure-Samples/ms-identity-java-webapi/pull/4)

---

## <a name="next-steps"></a>Volgende stappen

Meer informatie over SameSite en het scenario voor webapps:

> [!div class="nextstepaction"]
> [Veelgestelde vragen van Google Chrome op dezelfde site](https://www.chromium.org/updates/same-site/faq)

> [!div class="nextstepaction"]
> [Pagina SameSite van Chromium](https://www.chromium.org/updates/same-site)

> [!div class="nextstepaction"]
> [Scenario: web-app die zich aanmeldt bij gebruikers](scenario-web-app-sign-user-overview.md)
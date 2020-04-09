---
title: Web-app verplaatsen die gebruikers aanmeldt bij productie - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een web-app die gebruikers aantekent (overstappen naar productie)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 9c5fd444c55a20441325088912a07eb051219b84
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881465"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Web-app die gebruikers aantekent: Overstappen naar productie

Nu u weet hoe u een token krijgen om web-API's te bellen, leert u hoe u deze naar productie verplaatsen.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Volgende stappen

### <a name="same-site"></a>Dezelfde site

Zorg ervoor dat u mogelijke problemen met nieuwe versies van de Chrome-browser begrijpt

> [!div class="nextstepaction"]
> [Hoe om te gaan met SameSite-cookiewijzigingen in de Chrome-browser](howto-handle-samesite-cookie-changes-chrome-browser.md)

### <a name="scenario-for-calling-web-apis"></a>Scenario voor het aanroepen van web-API's

Nadat uw webapp zich aanmeldt bij gebruikers, kan deze namens de aangemelde gebruikers web-API's aanroepen. Het aanroepen van web-API's vanuit de web-app is het doelwit van het volgende scenario:

> [!div class="nextstepaction"]
> [Web-app die web-API's aanroept](scenario-web-app-call-api-overview.md)

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Deep dive: ASP.NET Core web app tutorial

Meer informatie over andere manieren om gebruikers aan te melden met deze ASP.NET Core-zelfstudie: 

> [!div class="nextstepaction"]
> [Uw web-apps inschakelen om gebruikers aan te melden en API's te bellen met het Microsoft-identiteitsplatform voor ontwikkelaars](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial) 

Deze progressieve zelfstudie heeft code die klaar is voor de productie voor een web-app, inclusief het toevoegen van aanmelding met accounts in:

- Uw organisatie
- Meerdere organisaties
- Werk- of schoolaccounts of persoonlijke Microsoft-accounts
- [Azure AD B2C](https://aka.ms/aadb2c)
- Nationale wolken

## <a name="sample-code-java-web-app"></a>Voorbeeldcode: Java-web-app

Meer informatie over de Java-webapp vindt u in dit voorbeeld op GitHub: 

> [!div class="nextstepaction"]
> [Een Java-webtoepassing die gebruikers inlogt met het Microsoft-identiteitsplatform en Microsoft Graph aanroept](https://github.com/Azure-Samples/ms-identity-java-webapp)

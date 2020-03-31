---
title: Daemon-apps registreren die web-API's aanroepen - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een daemon-app die web-API's aanroept - app-registratie
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 922a484d111746e5073c08a64d7c92e2b6b4a7c4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773369"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon-app die web-API's aanroept - app-registratie

Voor een daemon-toepassing moet u het weten wanneer u de app registreert.

## <a name="supported-account-types"></a>Ondersteunde accounttypen

Daemon-toepassingen hebben alleen zin in Azure AD-tenants. Dus wanneer u de toepassing maakt, moet u een van de volgende opties kiezen:

- **Alleen accounts in deze organisatiemap**. Deze keuze is de meest voorkomende omdat daemon-toepassingen meestal worden geschreven door lob-ontwikkelaars (line-of-business).
- **Accounts in een organisatiemap**. U maakt deze keuze als u een ISV bent die een hulpprogramma biedt aan uw klanten. U hebt de tenantbeheerders van uw klanten nodig om het goed te keuren.

## <a name="authentication---no-reply-uri-needed"></a>Authenticatie - geen antwoord URI nodig

In het geval dat uw vertrouwelijke clienttoepassing *alleen* de clientreferentiesstroom gebruikt, hoeft het antwoord URI niet te worden geregistreerd. Het is niet nodig voor de configuratie of constructie van de toepassing. De stroom van clientreferenties gebruikt deze niet.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-machtigingen - app-machtigingen en beheerderstoestemming

Een daemon-toepassing kan alleen toepassingsmachtigingen aanvragen voor API's (geen gedelegeerde machtigingen). Kies op de pagina **API-machtigingen** voor de toepassingsregistratie nadat u **Een machtiging toevoegen** hebt geselecteerd en de API-familie hebt gekozen, **Toepassingsmachtigingen**en selecteer vervolgens uw machtigingen.

![App-machtigingen en beheerderstoestemming](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> De web-API die u wilt aanroepen, moet *toepassingsmachtigingen (app-rollen)* definiëren, niet gedelegeerde machtigingen. Zie [Protected web API: App registration - wanneer uw web-API wordt aangeroepen door een daemon-app](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app)voor meer informatie over het blootleggen van een dergelijke API.

Daemon-toepassingen vereisen dat een tenantbeheerder vooraf instemt met de toepassing die de web-API aanroept. Tenantbeheerders geven deze toestemming op dezelfde **API-machtigingspagina** door **toestemming voor beheerders van subsidie voor onze *organisatie* te** selecteren

Als u een ISV bouwt een multitenant-toepassing, moet u de sectie Implementatie lezen [- geval van multitenant daemon-apps](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Daemon app - app code configuratie](./scenario-daemon-app-configuration.md)

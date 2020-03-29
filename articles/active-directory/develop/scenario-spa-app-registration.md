---
title: Apps voor één pagina registreren - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een toepassing met één pagina (app-registratie)
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 5b18b10748e0587920c6965f1d235376da928469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701889"
---
# <a name="single-page-application-app-registration"></a>Toepassing met één pagina: app-registratie

Op deze pagina worden de specificaties van de app-registratie voor een enkele pagina-toepassing (SPA) uitgelegd.

Volg de stappen om [een nieuwe toepassing te registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)en selecteer de ondersteunde accounts voor uw toepassing. Het SPA-scenario kan verificatie ondersteunen met accounts in uw organisatie of een organisatie en persoonlijke Microsoft-accounts.

Leer vervolgens de specifieke aspecten van toepassingsregistratie die van toepassing zijn op toepassingen met één pagina.

## <a name="register-a-redirect-uri"></a>Een omleidingsURI registreren

De impliciete stroom stuurt de tokens in een omleiding naar de toepassing met één pagina die op een webbrowser wordt uitgevoerd. Het is dus belangrijk om een omleiding URI te registreren waar uw toepassing de tokens kan ontvangen. Zorg ervoor dat de omleiding URI precies overeenkomt met de URI voor uw toepassing.

Ga in de [Azure-portal](https://go.microsoft.com/fwlink/?linkid=2083908)naar uw geregistreerde toepassing. Selecteer **op** de pagina Verificatie van de toepassing het **webplatform.** Voer de waarde in van de omleidingsURI voor uw toepassing in het veld **URI omleiden.**

## <a name="enable-the-implicit-flow"></a>De impliciete stroom inschakelen

Op dezelfde **verificatiepagina,** onder **Geavanceerde instellingen,** moet u ook **Impliciete subsidie**inschakelen. Als uw toepassing alleen gebruikers aanmeldt en ID-tokens krijgt, is het voldoende om het selectievakje **ID-tokens** in te schakelen.

Als uw toepassing ook toegangstokens nodig heeft om API's te bellen, moet u ook het selectievakje **Toegangstokens** inschakelen. Zie [ID-tokens](./id-tokens.md) en [Access-tokens voor](./access-tokens.md)meer informatie.

## <a name="api-permissions"></a>API-machtigingen

Toepassingen op één pagina kunnen API's bellen namens de aangemelde gebruiker. Ze moeten gedelegeerde machtigingen aanvragen. Zie Machtigingen [toevoegen om toegang te krijgen tot web-API's](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [De codeconfiguratie van de app](scenario-spa-app-configuration.md)

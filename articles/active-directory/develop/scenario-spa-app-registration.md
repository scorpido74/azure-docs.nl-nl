---
title: Toepassingen met één pagina registreren-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een toepassing met één pagina (app-registratie)
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03115db0ad286c7a5c24590906d8e3715e43bac7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962437"
---
# <a name="single-page-application-app-registration"></a>Toepassing met één pagina: app-registratie

Op deze pagina worden de specifieke app-registraties voor een toepassing met één pagina (SPA) uitgelegd.

Volg de stappen om [een nieuwe toepassing te registreren bij het micro soft-identiteits platform](quickstart-register-app.md)en selecteer de ondersteunde accounts voor uw toepassing. Het beveiligd-wachtwoord verificatie scenario kan authenticatie ondersteunen met accounts in uw organisatie of organisatie-en persoonlijke micro soft-accounts.

Vervolgens leert u de specifieke aspecten van de registratie van toepassingen die van toepassing zijn op toepassingen met één pagina.

## <a name="register-a-redirect-uri"></a>Een omleidings-URI registreren

De impliciete stroom verzendt de tokens in een omleiding naar de toepassing met één pagina die wordt uitgevoerd op een webbrowser. Het is dus belang rijk om een omleidings-URI te registreren waarbij uw toepassing de tokens kan ontvangen. Zorg ervoor dat de omleidings-URI exact overeenkomt met de URI voor uw toepassing.

Ga in het [Azure Portal](https://go.microsoft.com/fwlink/?linkid=2083908)naar de geregistreerde toepassing. Selecteer op de pagina **verificatie** van de toepassing het **webplatform.** Voer de waarde van de omleidings-URI voor uw toepassing in het veld **omleidings-URI** in.

## <a name="enable-the-implicit-flow"></a>De impliciete stroom inschakelen

Op dezelfde **verificatie** pagina, onder **Geavanceerde instellingen**, moet u ook **impliciete toekenning**inschakelen. Als uw toepassing alleen gebruikers aanmeldt en ID-tokens ophaalt, is het voldoende om het selectie vakje **id-tokens** te selecteren.

Als uw toepassing ook toegangs tokens moet ophalen om Api's aan te roepen, moet u ervoor zorgen dat u ook het selectie vakje **toegangs tokens** inschakelt. Zie [id-tokens](./id-tokens.md) en [toegangs tokens](./access-tokens.md)voor meer informatie.

## <a name="api-permissions"></a>API-machtigingen

Toepassingen met één pagina kunnen Api's aanroepen namens de aangemelde gebruiker. Ze moeten gedelegeerde machtigingen aanvragen. Zie [machtigingen voor toegang tot Web-Api's toevoegen](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie van de app-code](scenario-spa-app-configuration.md)

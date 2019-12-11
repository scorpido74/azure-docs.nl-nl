---
title: Een web-app registreren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het registreren van een web-app die web-Api's aanroept
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 784de823e94aace6f91222c19c1ff8130c3f995f
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962930"
---
# <a name="web-app-that-calls-web-apis---app-registration"></a>Web-app die web-Api's aanroept-app-registratie

Een web-app die web-Api's aanroept, heeft dezelfde registratie als gebruikers voor het aanmelden via een web-app. Daarom moet u de instructies in de [Web-app volgen waarmee gebruikers zich kunnen aanmelden-app-registratie](scenario-web-app-sign-user-app-registration.md)

Omdat de web-app nu web-Api's aanroept, wordt het een vertrouwelijke client toepassing. Daarom is er een stukje extra registratie vereist: het moet geheimen (client referenties) delen met het micro soft Identity-platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Webtoepassingen roepen Api's namens de aangemelde gebruiker aan. Ze moeten gedelegeerde machtigingen aanvragen. Zie machtigingen voor [toegang tot Web-Api's toevoegen](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Configuratie van de app-code](scenario-web-app-call-api-app-configuration.md)

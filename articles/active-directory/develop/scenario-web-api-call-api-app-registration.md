---
title: Een web-API registreren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-API die downstream Web-Api's (app-registratie) aanroept.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 048f7d41bd9d106121859e6b1fc013258067af9c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885120"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Een web-API die web-Api's aanroept: app-registratie

Een web-API die downstream Web-Api's aanroept, heeft dezelfde registratie als een beveiligde web-API. Daarom moet u de instructies in de [beveiligde web-API volgen: app-registratie](scenario-protected-web-api-app-registration.md).

Omdat de web-app nu web-Api's aanroept, wordt het een vertrouwelijke client toepassing. Daarom is extra registratie-informatie vereist: de app moet geheimen (client referenties) delen met het micro soft Identity-platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web Apps bellen Api's namens gebruikers voor wie het Bearer-token is ontvangen. De web-apps moeten gedelegeerde machtigingen aanvragen. Zie [machtigingen voor toegang tot Web-Api's toevoegen](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: code configuratie](scenario-web-api-call-api-app-configuration.md)

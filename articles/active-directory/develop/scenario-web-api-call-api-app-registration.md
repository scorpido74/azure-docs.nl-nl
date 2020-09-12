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
ms.openlocfilehash: c0b009d6d53a117aa11d0629fb649b2dd55559af
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89438191"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Een web-API die web-Api's aanroept: app-registratie

Een web-API die downstream Web-Api's aanroept, heeft dezelfde registratie als een beveiligde web-API. Daarom moet u de instructies in de [beveiligde web-API volgen: app-registratie](scenario-protected-web-api-app-registration.md).

Omdat de web-app nu web-Api's aanroept, wordt het een vertrouwelijke client toepassing. Daarom is extra registratie-informatie vereist: de app moet geheimen (client referenties) delen met het micro soft Identity-platform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web Apps bellen Api's namens gebruikers voor wie het Bearer-token is ontvangen. De web-apps moeten gedelegeerde machtigingen aanvragen. Zie [machtigingen toevoegen voor toegang tot uw web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-Api's aanroept: code configuratie](scenario-web-api-call-api-app-configuration.md)

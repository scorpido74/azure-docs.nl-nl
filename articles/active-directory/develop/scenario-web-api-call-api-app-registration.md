---
title: Een web-API registreren die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het maken van een web-API die downstream web API's aanroept (app-registratie).
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
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885120"
---
# <a name="a-web-api-that-calls-web-apis-app-registration"></a>Een web-API die web-API's aanroept: app-registratie

Een web-API die downstream web API's aanroept, heeft dezelfde registratie als een beveiligde web-API. Daarom moet u de instructies in [Protected Web API: App registration](scenario-protected-web-api-app-registration.md)volgen.

Omdat de web-app nu web-API's aanroept, wordt het een vertrouwelijke clienttoepassing. Daarom is extra registratiegegevens vereist: de app moet geheimen (clientreferenties) delen met het Microsoft-identiteitsplatform.

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web-apps bellen API's namens gebruikers voor wie het token aan toonder is ontvangen. De web-apps moeten gedelegeerde machtigingen aanvragen. Zie [Machtigingen toevoegen om toegang te krijgen tot webAPI's voor](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-web-apis)meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-API die web-API's aanroept: codeconfiguratie](scenario-web-api-call-api-app-configuration.md)

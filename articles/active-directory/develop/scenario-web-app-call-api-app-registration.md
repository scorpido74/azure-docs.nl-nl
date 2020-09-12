---
title: Een web-app registreren die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het registreren van een web-app die web-Api's aanroept
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
ms.openlocfilehash: f94a3da96243e30faa90277ce86efec037f54672
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/03/2020
ms.locfileid: "89436460"
---
# <a name="a-web-app-that-calls-web-apis-app-registration"></a>Een web-app die web-Api's aanroept: app-registratie

Een web-app die web-Api's aanroept, heeft dezelfde registratie als een web-app die gebruikers ondertekent in. Volg de instructies in [een web-app die zich in gebruikers aanmeldt: app-registratie](scenario-web-app-sign-user-app-registration.md).

Echter, omdat de web-app nu ook Web-Api's aanroept, wordt het een vertrouwelijke client toepassing. Daarom is een extra registratie vereist. De app moet client referenties of *geheimen*delen met het micro soft Identity-platform.

[!INCLUDE [Registration of client secrets](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="api-permissions"></a>API-machtigingen

Web apps roepen namens de aangemelde gebruiker Api's aan. Hiervoor moeten ze *gedelegeerde machtigingen*aanvragen. Zie [machtigingen toevoegen voor toegang tot uw web-API](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app die web-Api's aanroept: code configuratie](scenario-web-app-call-api-app-configuration.md)

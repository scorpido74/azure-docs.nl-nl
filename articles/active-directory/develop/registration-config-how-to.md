---
title: De eind punten voor een Azure AD-App-registratie ophalen
titleSuffix: Microsoft identity platform
description: De verificatie-eind punten zoeken voor een aangepaste toepassing die u ontwikkelt of registreert met Azure AD.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ms.openlocfilehash: dc37ce474ad3226f5dbf7e6efd687ddf55f7da03
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82926677"
---
# <a name="how-to-discover-endpoints"></a>Eind punten detecteren

U kunt de verificatie-eind punten voor uw toepassing vinden in de [Azure Portal](https://portal.azure.com).

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer **Azure Active Directory**.
1. Selecteer onder **beheren**de optie **app-registraties**en selecteer **eind punten** in het bovenste menu.

    De pagina **eind punten** wordt weer gegeven, met de verificatie-eind punten voor uw Tenant.
    
    Gebruik het eind punt dat overeenkomt met het verificatie protocol dat u gebruikt in combi natie met de **client-id** van de toepassing om de verificatie aanvraag te maken die specifiek is voor uw toepassing.

**Nationale Clouds** (bijvoorbeeld Azure AD China, Duitsland en de Amerikaanse overheid) hebben hun eigen app-registratie Portal en Azure AD-verificatie-eind punten. Meer informatie vindt u in het [overzicht van nationale Clouds](authentication-national-cloud.md).

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over eind punten in de verschillende Azure-omgevingen raadpleegt u het [overzicht van nationale Clouds](authentication-national-cloud.md).

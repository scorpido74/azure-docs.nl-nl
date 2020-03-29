---
title: Protected Web API - overzicht
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een beveiligde web-API (overzicht).
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
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: abe4ecf77e7a65251830ff822b15f79291471202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773340"
---
# <a name="scenario-protected-web-api"></a>Scenario: Beveiligde web-API

In dit scenario leert u hoe u een web-API blootleggen. U leert ook hoe u de web-API beschermen, zodat alleen geverifieerde gebruikers er toegang toe hebben.

Als u uw web-API wilt gebruiken, moet u geverifieerde gebruikers met zowel werk- als schoolaccounts inschakelen of persoonlijke microsoft-accounts inschakelen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Details

Hier vindt u specifieke informatie die u moet weten om web-API's te beschermen:

- Uw app-registratie moet ten minste één bereik blootleggen. De tokenversie die door uw web-API wordt geaccepteerd, is afhankelijk van de aanmeldingsdoelgroep.
- De codeconfiguratie voor de web-API moet het token valideren dat wordt gebruikt wanneer de web-API wordt aangeroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-protected-web-api-app-registration.md)

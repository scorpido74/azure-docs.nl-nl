---
title: Beveiligde web-API - overzicht
titleSuffix: Microsoft identity platform
description: Meer informatie over het maken van een beveiligde web-API (overzicht).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: cf66757d28a3883664aaacd85baad9cc0dea6956
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537199"
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

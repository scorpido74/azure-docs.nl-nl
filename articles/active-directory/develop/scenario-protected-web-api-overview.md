---
title: Beveiligde web-API-overzicht
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API (overzicht).
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "81537199"
---
# <a name="scenario-protected-web-api"></a>Scenario: beveiligde web-API

In dit scenario leert u hoe u een web-API beschikbaar maakt. U leert ook hoe u de Web-API kunt beveiligen, zodat alleen geverifieerde gebruikers er toegang toe hebben.

Als u uw web-API wilt gebruiken, moet u geverifieerde gebruikers met zowel werk-als school accounts inschakelen of persoonlijke micro soft-accounts inschakelen.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Opsporingsgegevens

Hier vindt u specifieke informatie die u moet kennen om Web-Api's te beveiligen:

- De registratie van uw app moet ten minste één bereik weer geven. De token versie die door de Web-API wordt geaccepteerd, is afhankelijk van de aanmeldings doel groep.
- De code configuratie voor de Web-API moet het token valideren dat wordt gebruikt wanneer de Web-API wordt aangeroepen.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-protected-web-api-app-registration.md)

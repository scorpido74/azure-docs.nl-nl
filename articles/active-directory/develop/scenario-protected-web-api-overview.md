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
ms.date: 07/14/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: c9ff9ae811a29685937b922f04a277e663e26f1f
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91257361"
---
# <a name="scenario-protected-web-api"></a>Scenario: beveiligde web-API

In dit scenario leert u hoe u een web-API beschikbaar maakt. U leert ook hoe u de Web-API kunt beveiligen, zodat alleen geverifieerde gebruikers er toegang toe hebben.

Als u uw web-API wilt gebruiken, moet u geverifieerde gebruikers met zowel werk-als school accounts inschakelen of persoonlijke micro soft-accounts inschakelen.

## <a name="specifics"></a>Opsporingsgegevens

Hier vindt u specifieke informatie die u moet kennen om Web-Api's te beveiligen:

- De registratie van uw app moet ten minste één *bereik* of een *toepassingsrol weer geven.*
  - Bereiken worden weer gegeven door Web-Api's die namens een gebruiker worden genoemd.
  - Toepassings rollen worden weer gegeven door Web-Api's die worden aangeroepen door daemon-toepassingen (die uw web-API op eigen naam aanroepen).
- Als u een nieuwe web API-app-registratie maakt, kiest u de versie van het [toegangs token](reference-app-manifest.md#accesstokenacceptedversion-attribute) die door uw web-API is geaccepteerd `2` . Voor verouderde web-Api's kan de geaccepteerde token versie zijn `null` , maar deze waarde beperkt de aanmeld doel groep tot alleen organisaties en persoonlijke micro soft-accounts (MSA) worden niet ondersteund.
- De code configuratie voor de Web-API moet het token valideren dat wordt gebruikt wanneer de Web-API wordt aangeroepen.
- De code in de controller acties moet de rollen of bereiken in het token valideren.

## <a name="recommended-reading"></a>Aanbevolen Lees bewerkingen

[!INCLUDE [recommended-topics](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [App-registratie](scenario-protected-web-api-app-registration.md)

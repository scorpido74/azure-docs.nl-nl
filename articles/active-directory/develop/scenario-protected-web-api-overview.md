---
title: Beveiligde web-API-overzicht
titleSuffix: Microsoft identity platform
description: Meer informatie over het bouwen van een beveiligde web-API (overzicht).
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
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773340"
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
> [Appregistratie](scenario-protected-web-api-app-registration.md)

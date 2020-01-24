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
ms.openlocfilehash: f96393adf0eaed8a28bc1a2ec0def6b0386bd7ac
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701974"
---
# <a name="scenario-protected-web-api"></a>Scenario: beveiligde web-API

In dit scenario laten we u zien hoe u een web-API beschikbaar maakt en hoe u deze kunt beveiligen zodat alleen geverifieerde gebruikers toegang hebben tot de API. U wilt geverifieerde gebruikers met zowel werk-als school accounts of persoonlijke micro soft-accounts inschakelen om uw web-API te gebruiken.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="specifics"></a>Opsporingsgegevens

Hier volgen enkele specifieke informatie die u moet kennen om Web-Api's te beveiligen:

- De registratie van uw app moet ten minste één bereik weer geven. De token versie die door uw web-API wordt geaccepteerd, is afhankelijk van de doel groep voor aanmelden.
- De configuratie van de code voor de Web-API moet het token valideren dat wordt gebruikt bij het aanroepen van de Web-API.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Appregistratie](scenario-protected-web-api-app-registration.md)

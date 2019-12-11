---
title: Een web-app bouwen die web-Api's aanroept-micro soft Identity-platform | Azure
description: Meer informatie over het bouwen van een web-app die web-Api's aanroept (overzicht)
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
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3888c7f838d6009382f849bc7d3e34c49b3b70a4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74962131"
---
# <a name="scenario-web-app-that-calls-web-apis"></a>Scenario: Web-app die web-Api's aanroept

Informatie over het bouwen van een web-app die zich aanmeldt bij gebruikers op het micro soft-identiteits platform en vervolgens Web-Api's aanroept namens de aangemelde gebruiker.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

In dit scenario wordt ervan uitgekomen dat u het volgende scenario hebt door lopen:

> [!div class="nextstepaction"]
> [Een web-app die gebruikers aanmeldt](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Overzicht

U kunt verificatie toevoegen aan uw web-app, waarmee gebruikers zich kunnen aanmelden en een web-API aanroepen namens de aangemelde gebruiker.

![Web-app die web-API's aanroept](./media/scenario-webapp/web-app.svg)

Web Apps die web-Api's aanroept:

- Zijn vertrouwelijke client toepassingen.
- Daarom hebben ze een geheim (toepassings wachtwoord of-certificaat) geregistreerd bij Azure AD. Dit geheim wordt door gegeven tijdens het aanroepen van Azure AD om een token op te halen

## <a name="specifics"></a>Opsporingsgegevens

> [!NOTE]
> Het toevoegen van een aanmelding aan een web-app maakt geen gebruik van de MSAL-bibliotheken omdat dit de web-app beveiligt. Het beveiligen van bibliotheken wordt bereikt door bibliotheken met de naam middleware. Dit was het object van het vorige scenario [waarmee gebruikers zich aanmelden bij een web-app](scenario-web-app-sign-user-overview.md)
>
> Wanneer u Web-Api's aanroept vanuit een web-app, moet u toegangs tokens voor deze web-Api's ophalen. U kunt MSAL-bibliotheken gebruiken om deze tokens te verkrijgen.

De end-to-end-ervaring van ontwikkel aars voor dit scenario heeft daarom specifieke aspecten als:

- Tijdens de [registratie](scenario-web-app-call-api-app-registration.md)van de toepassing moet u een of meerdere opgeven (als u uw app op meerdere locaties implementeert) antwoord-uri's, geheimen of certificaten moeten worden gedeeld met Azure AD.
- De [configuratie](scenario-web-app-call-api-app-configuration.md) van de toepassing moet client referenties opgeven die worden gedeeld met Azure ad tijdens de registratie van de toepassing

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Appregistratie](scenario-web-app-call-api-app-registration.md)

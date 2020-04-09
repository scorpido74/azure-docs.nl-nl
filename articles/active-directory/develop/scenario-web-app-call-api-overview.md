---
title: Een web-app bouwen die web-API's aanroept - Microsoft-identiteitsplatform | Azure
description: Meer informatie over het bouwen van een web-app die web-API's aanroept (overzicht)
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
ms.openlocfilehash: 5af9e34baf6115e801fbfe35e6e3895e48b360e7
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80881720"
---
# <a name="scenario-a-web-app-that-calls-web-apis"></a>Scenario: een web-app die web-API's aanroept

Meer informatie over het bouwen van een web-app die gebruikers aanmeldt bij het Microsoft-identiteitsplatform en vervolgens web-API's roept namens de aangemelde gebruiker.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

In dit scenario wordt ervan uitgegaan dat u het volgende scenario al hebt doorlopen:

> [!div class="nextstepaction"]
> [Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md)

## <a name="overview"></a>Overzicht

U voegt verificatie toe aan uw web-app, zodat gebruikers zich kunnen aanmelden en namens de aangemelde gebruiker een web-API kunnen aanroepen.

![Web-app die web-API's aanroept](./media/scenario-webapp/web-app.svg)

Webapps die web-API's aanroepen, zijn vertrouwelijke clienttoepassingen.
Daarom registreren ze een geheim (een toepassingswachtwoord of -certificaat) bij Azure Active Directory (Azure AD). Dit geheim wordt doorgegeven tijdens het gesprek met Azure AD om een token te krijgen.

## <a name="specifics"></a>Details

> [!NOTE]
> Het toevoegen van aanmelding aan een web-app gaat over het beschermen van de web-app zelf. Die bescherming wordt bereikt door *middleware-bibliotheken* te gebruiken, niet met de Microsoft Authentication Library (MSAL). Het vorige scenario, [Web app die zich aanmeldt bij gebruikers,](scenario-web-app-sign-user-overview.md)behandelde dat onderwerp.
>
> Dit scenario heeft betrekking op het aanroepen van web-API's vanuit een web-app. U moet toegangstokens voor die web-API's krijgen. U gebruikt MSAL-bibliotheken om deze tokens te verkrijgen.

Ontwikkeling voor dit scenario omvat deze specifieke taken:

- Tijdens [toepassingsregistratie](scenario-web-app-call-api-app-registration.md)moet u een uri-, geheim- of certificaat opgeven dat moet worden gedeeld met Azure AD. Als u uw app op verschillende locaties implementeert, geeft u deze informatie voor elke locatie.
- De [toepassingsconfiguratie](scenario-web-app-call-api-app-configuration.md) moet de clientreferenties bieden die tijdens de toepassingsregistratie met Azure AD zijn gedeeld.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een web-app die web-API's aanroept: app-registratie](scenario-web-app-call-api-app-registration.md)

---
title: API-verificatie begrijpen - Azure Digital Twins | Microsoft Documenten
description: Meer informatie over hoe u verbinding maken met API's en deze verifiëren met API's met Azure Digital Twins.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: d950d41186d578702343645875dd7c565002d5a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512995"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Verbinding maken met en verifiëren met API's

Azure Digital Twins gebruikt Azure Active Directory (Azure AD) om gebruikers te verifiëren en toepassingen te beschermen. Azure AD ondersteunt verificatie voor verschillende moderne architecturen. Ze zijn allemaal gebaseerd op de industriestandaard protocollen OAuth 2.0 of OpenID Connect. Bovendien kunnen ontwikkelaars Azure AD gebruiken om LOB-toepassingen (single-tenant en line-of-business) te bouwen. Ontwikkelaars kunnen Azure AD ook gebruiken om [multitenant-toepassingen](how-to-multitenant-applications.md)te ontwikkelen.

Ga voor een overzicht van Azure AD naar de [pagina Basisfundamenten](https://docs.microsoft.com/azure/active-directory/fundamentals/) voor stapsgewijze handleidingen, concepten en snelstarts.

> [!TIP]
> Volg de [zelfstudie](tutorial-facilities-setup.md) om een voorbeeld-app van Azure Digital Twins in te stellen en uit te voeren.

Om een toepassing of service met Azure Active Directory te integreren, moet een ontwikkelaar de toepassing eerst bij Azure Active Directory registreren. Voor gedetailleerde instructies en screenshots, lees [deze quickstart](../active-directory/develop/quickstart-register-app.md).

[Vijf primaire toepassingsscenario's](../active-directory/develop/v2-app-types.md) worden ondersteund door Azure AD:

* Spa(SPA) een enkele pagina: een gebruiker moet zich aanmelden bij een toepassing van één pagina die is beveiligd door Azure AD.
* Webbrowser naar webtoepassing: een gebruiker moet zich aanmelden bij een webtoepassing die is beveiligd door Azure AD.
* Native application to web API: A native application that runs on a phone, tablet, or PC needs to authenticate a user to get resources from a web API that's secured by Azure AD.
* Webtoepassing naar web-API: een webtoepassing moet resources krijgen uit een web-API die is beveiligd door Azure AD.
* Daemon of servertoepassing naar web-API: een daemon-toepassing of een servertoepassing zonder webgebruikersinterface moet resources krijgen van een web-API die is beveiligd door Azure AD.

> [!IMPORTANT]
> Azure Digital Twins ondersteunt beide verificatiebibliotheken:
> * De meer recente [Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * De [Azure Active Directory Authentication Library (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Bel Digital Twins vanuit een internet-API op het middensegment

Wanneer ontwikkelaars Digital Twins-oplossingen ontwerpen, maken ze meestal een toepassing of API op het middenniveau. De app of API roept vervolgens de Digital Twins API stroomafwaarts aan. Als u deze standaardarchitectuur voor weboplossingen wilt ondersteunen, moet u ervoor zorgen dat gebruikers eerst:

1. Verifiëren met de toepassing op de middelste laag

1. Een OAuth 2.0 On-Behalf-Of token wordt aangeschaft tijdens verificatie

1. Het verkregen token wordt vervolgens gebruikt om API's te verifiëren of te bellen die verder stroomafwaarts zijn met behulp van de on-behalf-of-flow

Voor instructies over het orkestreren van de stroom namens de stroom, leest u [OAuth 2.0 On-Behalf-Of flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow). U ook codevoorbeelden bekijken in [Een downstream web API aanroepen.](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof)

## <a name="next-steps"></a>Volgende stappen

Als u Azure Digital Twins wilt configureren en testen met de impliciete subsidiestroom van OAuth 2.0, leest u [Postman configureren](./how-to-configure-postman.md).

Lees [Roltoewijzingen maken en beheren](./security-create-manage-role-assignments.md)voor meer informatie over azure digital twins-beveiliging.

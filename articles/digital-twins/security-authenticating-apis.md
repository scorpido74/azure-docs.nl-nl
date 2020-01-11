---
title: API-verificatie begrijpen-Azure Digital Apparaatdubbels | Microsoft Docs
description: Meer informatie over hoe u verbinding kunt maken met Api's en hoe u deze kunt verifiëren met behulp van Azure Digital Apparaatdubbels.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 0aed84dc4d78f8e83f568ff3e73d1300aaedbcaa
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75860959"
---
# <a name="connect-to-and-authenticate-with-apis"></a>Verbinding maken met en verifiëren met Api's

Azure Digital Apparaatdubbels maakt gebruik van Azure Active Directory (Azure AD) om gebruikers te verifiëren en toepassingen te beveiligen. Azure AD biedt ondersteuning voor verificatie voor verschillende moderne architecturen. Ze zijn allemaal gebaseerd op de industrie standaard protocollen OAuth 2,0 of OpenID Connect Connect. Daarnaast kunnen ontwikkel aars Azure AD gebruiken om single tenant-en LOB-toepassingen (line-of-Business) te bouwen. Ontwikkel aars kunnen ook Azure AD gebruiken voor het ontwikkelen van [multi tenant-toepassingen](how-to-multitenant-applications.md).

Ga voor een overzicht van Azure AD naar de [pagina fundamentals](https://docs.microsoft.com/azure/active-directory/fundamentals/) voor stapsgewijze hand leidingen, concepten en Quick starts.

> [!TIP]
> Volg de [zelf studie](tutorial-facilities-setup.md) om een Azure Digital apparaatdubbels-voor beeld-app in te stellen en uit te voeren.

Om een toepassing of service met Azure Active Directory te integreren, moet een ontwikkelaar de toepassing eerst bij Azure Active Directory registreren. Lees [deze Snelstartgids](../active-directory/develop/quickstart-register-app.md)voor gedetailleerde instructies en scherm afbeeldingen.

Er worden [vijf primaire toepassings scenario's](../active-directory/develop/v2-app-types.md) ondersteund door Azure AD:

* Toepassing met één pagina (SPA): een gebruiker moet zich aanmelden bij een toepassing met één pagina die wordt beveiligd door Azure AD.
* Webbrowser naar Web Application: een gebruiker moet zich aanmelden bij een webtoepassing die wordt beveiligd door Azure AD.
* Systeem eigen toepassing op Web-API: een systeem eigen toepassing die wordt uitgevoerd op een telefoon, tablet of PC moet een gebruiker verifiëren om bronnen te verkrijgen van een web-API die wordt beveiligd door Azure AD.
* Web Application to Web API: een webtoepassing moet resources ophalen van een web-API die wordt beveiligd door Azure AD.
* Daemon of server toepassing op Web-API: een daemon-toepassing of een server toepassing zonder webgebruikersinterface moet resources ophalen van een web-API die wordt beveiligd door Azure AD.

> [!IMPORTANT]
> Azure Digital Apparaatdubbels ondersteunt de volgende verificatie bibliotheken:
> * De recentere [micro soft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-overview)
> * De [Azure Active Directory-verificatie bibliotheek (ADAL)](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries)

## <a name="call-digital-twins-from-a-middle-tier-web-api"></a>Digital Apparaatdubbels aanroepen vanuit een web-API in de middelste laag

Wanneer ontwikkel aars oplossingen voor digitale Apparaatdubbels ontwikkelen, maken ze doorgaans een toepassing op middelste laag of API. De app of API roept vervolgens de Digital Apparaatdubbels API downstream aan. Als u deze standaard architectuur voor weboplossing wilt ondersteunen, moet u ervoor zorgen dat gebruikers eerst het volgende doen:

1. Verifiëren met de toepassing voor middelste laag

1. Er is een OAuth 2,0--of-token voor het verkrijgen van validatie tijdens verificatie

1. Het aangeschafte token wordt vervolgens gebruikt voor verificatie met of aanroepen van Api's die verder downstream worden gebruikt met behulp van de namens-stroom

Lees [OAuth 2,0 voor-of-flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-on-behalf-of-flow)voor instructies over het indelen van de namens-stroom. U kunt ook code voorbeelden bekijken in [het aanroepen van een stroomafwaartse Web-API](https://github.com/Azure-Samples/active-directory-dotnet-webapi-onbehalfof).

## <a name="next-steps"></a>Volgende stappen

Lees [Configure postman](./how-to-configure-postman.md)om Azure Digital apparaatdubbels te configureren en te testen met behulp van de OAuth 2,0 impliciete toekennings stroom.

Voor meer informatie over de beveiliging van Azure Digital Apparaatdubbels leest u [roltoewijzingen maken en beheren](./security-create-manage-role-assignments.md).
---
title: Azure AD gebruiken voor toepassingen die niet worden vermeld in de app-galerie
description: Meer informatie over het integreren van apps die niet worden vermeld in de Azure AD-galerie.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 07/27/2020
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.openlocfilehash: 300c6e268e3d2f639bf697237253d2b98e5daa15
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87352319"
---
# <a name="using-azure-ad-for-applications-not-listed-in-the-app-gallery"></a>Azure AD gebruiken voor toepassingen die niet worden vermeld in de app-galerie

In het Snelstartgids voor het [toevoegen van een app](add-application-portal.md) leert u hoe u een app kunt toevoegen aan uw Azure AD-Tenant.

Naast de opties in de [Azure AD-toepassings galerie](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/), hebt u de mogelijkheid om een **niet-galerie toepassing**toe te voegen. 

## <a name="capabilities-for-apps-not-listed-in-the-azure-ad-gallery"></a>Mogelijkheden voor apps die niet worden vermeld in de Azure AD-galerie

U kunt elke toepassing toevoegen die al bestaat in uw organisatie of een toepassing van derden van een leverancier die nog geen deel uitmaakt van de Azure AD-galerie. Afhankelijk van uw [gebruiksrecht overeenkomst](https://azure.microsoft.com/pricing/details/active-directory/)zijn de volgende mogelijkheden beschikbaar:

- Selfservice-integratie van elke toepassing die ondersteuning biedt voor services van [Security Assertion Markup Language (SAML) 2,0](https://wikipedia.org/wiki/SAML_2.0) (door SP geïnitieerd of IDP)
- Self-Service-integratie van elke webtoepassing met een op een HTML gebaseerde aanmeldings pagina met [SSO op basis van wacht woorden](what-is-single-sign-on.md#password-based-sso)
- Selfservice verbinding van toepassingen die gebruikmaken van het [systeem voor het scim-Protocol (Cross-Domain Identity Management) voor het inrichten van gebruikers](../app-provisioning/use-scim-to-provision-users-and-groups.md)
- Mogelijkheid om koppelingen toe te voegen aan een toepassing in het [Start programma voor Office 365-apps](https://www.microsoft.com/microsoft-365/blog/2014/10/16/organize-office-365-new-app-launcher-2/) of het [Azure AD-toegangs venster](what-is-single-sign-on.md#linked-sign-on)

Zie [verificatie scenario's voor Azure AD](../develop/authentication-scenarios.md)als u hulp nodig hebt bij het integreren van aangepaste apps met Azure AD. Wanneer u een App ontwikkelt die gebruikmaakt van een modern protocol zoals [OpenID Connect Connect/OAuth](../develop/active-directory-v2-protocols.md) om gebruikers te verifiëren, kunt u dit registreren bij het micro soft Identity-platform met behulp van de [app-registraties](../develop/quickstart-register-app.md) -ervaring in de Azure Portal.

## <a name="next-steps"></a>Volgende stappen

- [Quick Start Series op het beheer van apps](view-applications-portal.md)


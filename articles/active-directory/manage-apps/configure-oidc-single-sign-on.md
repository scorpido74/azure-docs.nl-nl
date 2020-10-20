---
title: Informatie over eenmalige aanmelding op basis van OIDC (SSO) voor apps in Azure Active Directory
description: Informatie over eenmalige aanmelding op basis van OIDC voor apps in Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.openlocfilehash: 825f79b0a1c132fb7a15d643c3487dfb7d6a9abd
ms.sourcegitcommit: 957c916118f87ea3d67a60e1d72a30f48bad0db6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92209872"
---
# <a name="understand-oidc-based-single-sign-on"></a>Informatie over eenmalige aanmelding op basis van OIDC
In de [Quick](view-applications-portal.md) start-serie op toepassings beheer hebt u geleerd hoe u Azure AD als id-provider (IDP) gebruikt voor een toepassing. In dit artikel vindt u meer informatie over apps die gebruikmaken van de OpenID Connect Connect-standaard voor het implementeren van eenmalige aanmelding. 

## <a name="before-you-begin"></a>Voordat u begint
Het proces voor het toevoegen van een app aan uw Azure Active Directory-Tenant is afhankelijk van het type eenmalige aanmelding van de toepassing die is geïmplementeerd. Zie [Opties voor eenmalige aanmelding](sso-options.md)voor meer informatie over de opties voor eenmalige aanmelding die beschikbaar zijn voor apps waarvoor Azure AD kan worden gebruikt voor identiteits beheer. Dit artikel heeft betrekking op OIDC-apps.


## <a name="basic-oidc-configuration"></a>Basis configuratie van OIDC
In de [Quick Start serie](add-application-portal-setup-oidc-sso.md)is er een artikel over het configureren van eenmalige aanmelding. Hierin leert u hoe u een op OIDC gebaseerde app kunt toevoegen aan uw Azure-Tenant.

Het mooie van het toevoegen van een app die gebruikmaakt van de OIDC-standaard voor eenmalige aanmelding is dat de configuratie mini maal is. Hier volgt een korte video waarin wordt weer gegeven hoe u een OIDC-app toevoegt aan uw Tenant.

Een op OIDC gebaseerde app toevoegen in Azure Active Directory

> [!VIDEO https://www.youtube.com/embed/4kv-upsZCI0]

## <a name="next-steps"></a>Volgende stappen

- [Quickstartreeks over toepassingsbeheer](add-application-portal-setup-oidc-sso.md)
- [Opties voor eenmalige aanmelding](sso-options.md)
- [Procedure: Een Azure Active Directory-gebruiker aanmelden met behulp van het patroon voor multitenant-toepassingen](../develop/howto-convert-app-to-be-multi-tenant.md)
---
title: Gekoppelde aanmelding voor Azure AD-apps - Microsoft-identiteitsplatform
description: Gekoppelde single sign-on (SSO) configureren voor uw Azure AD enterprise-toepassingen in Microsoft Identity Platform (Azure AD)
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: article
ms.workload: identity
ms.date: 05/08/2019
ms.author: mimart
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfe4aeb17f482cc9d4126efc6d65d3f7d173536b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77063540"
---
# <a name="configure-linked-sign-on"></a>Gekoppelde aanmelding configureren

Wanneer u een galerie- of niet-galeriewebtoepassing toevoegt, is een van de opties voor één aanmelding die voor u beschikbaar [is, gekoppeld aan sign-on](what-is-single-sign-on.md). Selecteer deze optie om een koppeling toe te voegen aan de toepassing in het Azure AD Access-paneel van uw organisatie of office 365-portal. U deze methode gebruiken om koppelingen toe te voegen aan aangepaste webtoepassingen die momenteel Active Directory Federation Services (of andere federatieservice) gebruiken in plaats van Azure AD voor verificatie. U ook diepe koppelingen toevoegen aan specifieke SharePoint-pagina's of andere webpagina's die u gewoon op de toegangspanelen van uw gebruiker wilt weergeven.

## <a name="before-you-begin"></a>Voordat u begint

Zie [Een galerie-app toevoegen](add-gallery-app.md) of [Een niet-galerie-app toevoegen](add-non-gallery-app.md)als de toepassing niet is toegevoegd aan uw Azure AD-tenant.

### <a name="open-the-app-and-select-linked-sign-on"></a>De app openen en gekoppeldaanmelden selecteren

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

1. Navigeer naar **Azure Active Directory** > **Enterprise-toepassingen**. Er verschijnt een willekeurig voorbeeld van de toepassingen in uw Azure AD-tenant. 

1. Selecteer in het menu **Toepassingstype** **alle toepassingen**en selecteer **Vervolgens Toepassen**.

1. Voer de naam van de toepassing in het zoekvak in en selecteer de toepassing in de resultaten.

1. Selecteer onder de sectie **Beheren** de optie **Eén aanmelding**. 

1. Selecteer **Gekoppeld**.

1. Voer de URL van de toepassing in om naar te linken. Typ de URL en selecteer **Opslaan**. 
 
1. U gebruikers en groepen toewijzen aan de toepassing, waardoor de toepassing wordt weergegeven in het [startprogramma voor Office 365-apps](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) of in het [Azure AD-toegangspaneel](end-user-experiences.md) voor die gebruikers.

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatische gebruikersaccountinrichting configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---
title: Gekoppelde aanmelding voor Azure AD-apps-micro soft Identity-platform
description: Gekoppelde eenmalige aanmelding (SSO) configureren voor uw Azure AD-zakelijke toepassingen in micro soft Identity platform (Azure AD)
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 05/08/2019
ms.author: kenwith
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cfcece43ae1b7d7bcf0c38feba14f1e82b29f18
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84763479"
---
# <a name="configure-linked-sign-on"></a>Gekoppelde aanmelding configureren

Wanneer u een webtoepassing voor een galerie of niet-galerie toevoegt, wordt een van de opties voor eenmalige aanmelding aan u [gekoppeld](what-is-single-sign-on.md). Selecteer deze optie om een koppeling toe te voegen aan de toepassing in het Azure AD-toegangs venster of de Office 365-portal van uw organisatie. U kunt deze methode gebruiken om koppelingen toe te voegen aan aangepaste webtoepassingen die momenteel gebruikmaken van Active Directory Federation Services (of een andere Federation service) in plaats van Azure AD voor verificatie. U kunt ook diep gaande koppelingen toevoegen aan specifieke share point-pagina's of andere webpagina's die u alleen op de toegangs Vensters van uw gebruikers wilt weer geven.

## <a name="before-you-begin"></a>Voordat u begint

Als de toepassing niet is toegevoegd aan uw Azure AD-Tenant, raadpleegt u [een galerie-app toevoegen](add-gallery-app.md) of [een niet-galerij-app toevoegen](add-non-gallery-app.md).

### <a name="open-the-app-and-select-linked-sign-on"></a>Open de app en selecteer gekoppelde aanmelding

1. Meld u bij [Azure Portal](https://portal.azure.com) aan als beheerder van de cloudtoepassing of als toepassingsbeheerder voor uw Azure Active Directory-tenant.

1. Ga naar **Azure Active Directory**  >  **bedrijfs toepassingen**. Een wille keurig voor beeld van de toepassingen in uw Azure AD-Tenant wordt weer gegeven. 

1. Selecteer in het menu **toepassings type** **alle toepassingen**en selecteer vervolgens **Toep assen**.

1. Voer de naam van de toepassing in het zoekvak in en selecteer vervolgens de toepassing in de resultaten.

1. Selecteer onder de sectie **beheren** de optie **eenmalige aanmelding**. 

1. Selecteer **gekoppeld**.

1. Voer de URL in van de toepassing waarnaar u een koppeling wilt maken. Typ de URL en selecteer **Opslaan**. 
 
1. U kunt gebruikers en groepen toewijzen aan de toepassing, waardoor de toepassing wordt weer gegeven in het [Start programma voor Office 365-apps](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) of in het [Azure AD-toegangs venster](end-user-experiences.md) voor die gebruikers.

1. Selecteer **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

- [Gebruikers of groepen toewijzen aan de toepassing](methods-for-assigning-users-and-groups.md)
- [Automatisch inrichten van gebruikers accounts configureren](../app-provisioning/configure-automatic-user-provisioning-portal.md)

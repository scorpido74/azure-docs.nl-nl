---
title: Beheerderstoestemming voor een toepassing voor tenantbrede beheerders verlenen - Azure AD
description: Meer informatie over het verlenen van toestemming voor een toepassing voor een tenant, zodat eindgebruikers niet om toestemming worden gevraagd wanneer ze zich aanmelden bij een toepassing.
services: active-directory
author: psignoret
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: mimart
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: c515fef4997720435c64bd5f3ae7b18f8921fc5d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75480916"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Toestemming voor een tenantbeheerder verlenen voor een toepassing

Meer informatie over hoe u de gebruikerservaring vereenvoudigen door toestemming voor beheerders voor een tenant te verlenen voor een toepassing. Dit artikel geeft de verschillende manieren om dit te bereiken. De methoden zijn van toepassing op alle eindgebruikers in uw Azure AD-tenant (Azure Directory).

Zie Azure Active Directory Consent [Framework](../develop/consent-framework.md)voor meer informatie over het toestaan van toepassingen.

## <a name="prerequisites"></a>Vereisten

Voor het verlenen van toestemming voor beheerders voor een tenant moet u zich aanmelden als [globale beheerder,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [applicatiebeheerder](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)of [cloudtoepassingsbeheerder.](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator)

> [!IMPORTANT]
> Wanneer een toepassing toestemming heeft gekregen voor beheerders voor de hele tenant, kunnen alle gebruikers zich aanmelden bij de app, tenzij deze is geconfigureerd om gebruikerstoewijzing te vereisen. Als u wilt beperken welke gebruikers zich bij een toepassing kunnen aanmelden, moet u gebruikerstoewijzing vereisen en vervolgens gebruikers of groepen aan de toepassing toewijzen. Zie [Methoden voor het toewijzen van gebruikers en groepen voor](methods-for-assigning-users-and-groups.md)meer informatie.

> [!WARNING]
> Als u toestemming verleent voor een tenantbrede beheerder voor een toepassing, krijgt de app en de uitgever van de app toegang tot de gegevens van uw organisatie. Controleer zorgvuldig de machtigingen die de aanvraag aanvraagt voordat u toestemming verleent.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Beheerderstoestemming verlenen vanuit de Azure-portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Beheerderstoestemming verlenen in Enterprise-apps

U toestemming voor beheerders voor tenant verlenen via *Enterprise-toepassingen* als de toepassing al in uw tenant is ingericht. Een app kan bijvoorbeeld in uw tenant worden ingericht als ten minste één gebruiker al heeft ingestemd met de toepassing. Zie [Hoe en waarom toepassingen worden toegevoegd aan Azure Active Directory](../develop/active-directory-how-applications-are-added.md)voor meer informatie.

Ga als bedoeld als u toestemming verleent voor een tenantbrede beheerder voor een app die wordt vermeld in **Enterprise-toepassingen:**

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als [globale beheerder,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [een toepassingsbeheerder](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)of [een cloudtoepassingsbeheerder](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecteer **Azure Active Directory** en vervolgens **Enterprise-toepassingen**.
3. Selecteer de toepassing waarvoor u toestemming voor beheerders voor de hele tenant wilt verlenen.
4. Selecteer **Machtigingen** en klik op **Toestemming verlenen van beheerders**.
5. Controleer zorgvuldig de machtigingen die de toepassing vereist.
6. Als u akkoord gaat met de machtigingen die de aanvraag vereist, verleent u toestemming. Als dit niet het is, klikt u op **Annuleren** of sluit u het venster.

### <a name="grant-admin-consent-in-app-registrations"></a>Beheerderstoestemming verlenen in app-registraties

Voor toepassingen die uw organisatie heeft ontwikkeld of die rechtstreeks zijn geregistreerd in uw Azure AD-tenant, u ook beheerderstoestemming verlenen voor tenanten van **app-registraties** in de Azure-portal.

Als u toestemming voor beheerders voor de hele tenant wilt verlenen van **app-registraties:**

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) als [globale beheerder,](../users-groups-roles/directory-assign-admin-roles.md#global-administrator--company-administrator) [een toepassingsbeheerder](../users-groups-roles/directory-assign-admin-roles.md#application-administrator)of [een cloudtoepassingsbeheerder](../users-groups-roles/directory-assign-admin-roles.md#cloud-application-administrator).
2. Selecteer **Azure Active Directory** en vervolgens **App-registraties**.
3. Selecteer de toepassing waarvoor u toestemming voor beheerders voor de hele tenant wilt verlenen.
4. Selecteer **API-machtigingen** en klik op **Toestemming verlenen van beheerders**.
5. Controleer zorgvuldig de machtigingen die de toepassing vereist.
6. Als u akkoord gaat met de machtigingen die de aanvraag vereist, verleent u toestemming. Als dit niet het is, klikt u op **Annuleren** of sluit u het venster.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>De URL construeren voor het verlenen van beheerderstoestemming voor tenant

Wanneer u toestemming voor beheerders voor de hele tenant verleent met behulp van een van de hierboven beschreven methoden, wordt vanuit de Azure-portal een venster geopend om toestemming te vragen voor beheerderstoestemming voor de hele tenant. Als u de client-ID (ook wel de toepassings-ID genoemd) van de toepassing kent, u dezelfde URL bouwen om beheerderstoestemming voor de tenant te verlenen.

De URL voor beheerderstoestemming voor beheerders volgt de volgende indeling:

    https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}

Hierbij

* `{client-id}`is de client-id van de toepassing (ook bekend als app-id).
* `{tenant-id}`is de tenant-id van uw organisatie of een geverifieerde domeinnaam.

Controleer zoals altijd zorgvuldig de machtigingen die een toepassing aanvraagt voordat u toestemming verleent.

## <a name="next-steps"></a>Volgende stappen

[Configureren hoe eindgebruikers toestemming geven voor toepassingen](configure-user-consent.md)

[De werkstroom voor beheerderstoestemming configureren](configure-admin-consent-workflow.md)

[Machtigingen en toestemming in het Microsoft-identiteitsplatform](../develop/active-directory-v2-scopes.md)

[Azure AD op StackOverflow](https://stackoverflow.com/questions/tagged/azure-active-directory)

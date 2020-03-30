---
title: Beschikbare aangepaste machtigingen voor beheerdersrollen - Azure AD | Microsoft Documenten
description: Machtigingen voor aangepaste beheerdersrollen voor het delegeren van identiteitsbeheer.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 11/08/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d6156857202c1cca94df6d70ec2059daf55178f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74025148"
---
# <a name="application-registration-subtypes-and-permissions-in-azure-active-directory"></a>Subtypen en machtigingen voor toepassingsregistratie in Azure Active Directory

Dit artikel bevat de momenteel beschikbare machtigingen voor app-registratie voor aangepaste roldefinities in Azure Active Directory (Azure AD).

## <a name="permissions-for-managing-single-directory-applications"></a>Machtigingen voor het beheren van toepassingen met één map

Wanneer u de machtigingen voor uw aangepaste rol kiest, hebt u de mogelijkheid om toegang te verlenen om alleen toepassingen met één map te beheren. Toepassingen met één map zijn alleen beschikbaar voor gebruikers in de Azure AD-organisatie waar de toepassing is geregistreerd. Toepassingen met één map worden gedefinieerd als het instellen **van ondersteunde accounttypen** op 'Alleen accounts in deze organisatiemap'. In de Graph API hebben toepassingen met één map de eigenschap signInAudience ingesteld op 'AzureADMyOrg'.

Als u toegang wilt verlenen voor het beheren van alleen toepassingen in één map, gebruikt u de onderstaande machtigingen met het subtype **applications.myOrganization**. Bijvoorbeeld microsoft.directory/applications.myOrganization/basic/update.

Zie het [overzicht van aangepaste rollen](roles-custom-overview.md) voor een uitleg over wat de algemene termen subtype, machtiging en eigenschapset betekenen. De volgende informatie is specifiek voor registraties van toepassingen.

### <a name="create-and-delete"></a>Maken en verwijderen

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om toepassingsregistraties te maken, elk met verschillend gedrag:

#### <a name="microsoftdirectoryapplicationscreateasowner"></a>microsoft.directory/applications/createAsOwner

Als u deze machtiging toevoegt, wordt de maker toegevoegd als de eerste eigenaar van de gemaakte app-registratie en telt de gemaakte app-registratie mee voor het quotum van 250 gemaakte objecten van de maker.

#### <a name="microsoftdirectoryapplicationscreate"></a>microsoft.directory/applications/create

Als u deze machtiging toevoegt, wordt de maker niet toegevoegd als de eerste eigenaar van de gemaakte app-registratie en telt de gemaakte app-registratie niet mee voor het quotum van de maker van 250 gemaakte objecten. Gebruik deze toestemming zorgvuldig, omdat er niets is dat de cessionaris ervan weerhoudt app-registraties te maken totdat het quotum op directoryniveau is bereikt. Als beide machtigingen zijn toegewezen, heeft deze toestemming voorrang.

Als beide machtigingen zijn toegewezen, heeft de machtiging /maken voorrang. Hoewel de /createAsOwner-toestemming de maker niet automatisch als eerste eigenaar toevoegt, kunnen eigenaren worden opgegeven tijdens het maken van de app-registratie bij het gebruik van Graph API's of PowerShell-cmdlets.

Met machtigingen maken die toegang verlenen tot de opdracht **Nieuwe registratie.**

[Deze machtigingen verlenen toegang tot de opdracht Nieuwe registratieportal](./media/roles-create-custom/new-custom-role.png)

Er zijn twee machtigingen beschikbaar voor het verlenen van de mogelijkheid om app-registraties te verwijderen:

#### <a name="microsoftdirectoryapplicationsdelete"></a>microsoft.directory/applications/delete

Verleent de mogelijkheid om app-registraties te verwijderen, ongeacht het subtype; dat wil zeggen, zowel single-tenant en multi-tenant toepassingen.

#### <a name="microsoftdirectoryapplicationsmyorganizationdelete"></a>microsoft.directory/applications.myOrganization/delete

Hiermee u app-registraties verwijderen die beperkt zijn tot registraties die alleen toegankelijk zijn voor accounts in uw organisatie of toepassingen met één tenant (subtype myOrganization).

![Met deze machtigingen wordt toegang verleend tot de opdracht App-registratie verwijderen](./media/roles-custom-available-permissions/delete-app-registration.png)

> [!NOTE]
> Bij het toewijzen van een rol die create-machtigingen bevat, moet de roltoewijzing worden uitgevoerd in het mapbereik. Een machtiging maken die is toegewezen aan een resourcebereik, geeft niet de mogelijkheid om app-registraties te maken.

### <a name="read"></a>Lezen

Alle leden in de organisatie kunnen standaard app-registratiegegevens lezen. Gastgebruikers en aangevers van de toepassingsservice kunnen dat echter niet. Als u van plan bent een rol toe te wijzen aan een gastgebruiker of -toepassing, moet u de juiste leesmachtigingen opnemen.

#### <a name="microsoftdirectoryapplicationsallpropertiesread"></a>microsoft.directory/applications/allProperties/read

Mogelijkheid om alle eigenschappen van toepassingen met één tenant en meerdere tenants buiten eigenschappen te lezen die in geen enkele situatie zoals referenties kunnen worden gelezen.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesread"></a>microsoft.directory/applications.myOrganization/allProperties/read

Verleent dezelfde machtigingen als microsoft.directory/applications/allProperties/read, maar alleen voor toepassingen met één tenant.

#### <a name="microsoftdirectoryapplicationsownersread"></a>microsoft.directory/applications/owners/read

Verleent de mogelijkheid om eigenaren te lezen op single-tenant en multi-tenant toepassingen. Verleent toegang tot alle velden op de pagina met eigenaren van toepassingsregistraties:

![Met deze machtigingen krijgt u toegang tot de pagina met eigenaren van app-registraties](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsstandardread"></a>microsoft.directory/applications/standard/read

Verleent toegang tot standaard applicatieregistratie-eigenschappen. Dit omvat eigenschappen op de registratiepagina's van toepassingen.

#### <a name="microsoftdirectoryapplicationsmyorganizationstandardread"></a>microsoft.directory/applications.myOrganization/standard/read

Verleent dezelfde machtigingen als microsoft.directory/applications/standard/read, maar alleen voor toepassingen met één tenant.

### <a name="update"></a>Update

#### <a name="microsoftdirectoryapplicationsallpropertiesupdate"></a>microsoft.directory/applications/allProperties/update

Mogelijkheid om alle eigenschappen op single-directory- en multidirectorytoepassingen bij te werken.

#### <a name="microsoftdirectoryapplicationsmyorganizationallpropertiesupdate"></a>microsoft.directory/applications.myOrganization/allProperties/update

Verleent dezelfde machtigingen als microsoft.directory/applications/allProperties/update, maar alleen voor toepassingen met één tenant.

#### <a name="microsoftdirectoryapplicationsaudienceupdate"></a>microsoft.directory/applications/audience/update

Mogelijkheid om de eigenschap van het ondersteunde accounttype (signInAudience) bij te werken op toepassingen met één map en meerdere directorytoepassingen.

![Met deze machtiging krijgt u toegang tot de eigenschap van app-registratie ondersteund accounttype op de verificatiepagina](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationaudienceupdate"></a>microsoft.directory/applications.myOrganization/audience/update

Verleent dezelfde machtigingen als microsoft.directory/applications/audience/update, maar alleen voor toepassingen met één tenant.

#### <a name="microsoftdirectoryapplicationsauthenticationupdate"></a>microsoft.directory/applications/authentication/update

Mogelijkheid om de url van het antwoord, de URL van de afmelding, impliciete stroom en domeineigenschappen van uitgevers bij te werken op toepassingen met één tenant en meerdere tenants. Verleent toegang tot alle velden op de verificatiepagina voor toepassingsregistratie, behalve ondersteunde accounttypen:

![Verleent toegang tot verificatie van app-registratie, maar niet ondersteund accounttypen](./media/roles-custom-available-permissions/supported-account-types.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationauthenticationupdate"></a>microsoft.directory/applications.myOrganization/authentication/update

Verleent dezelfde machtigingen als microsoft.directory/applications/authentication/update, maar alleen voor toepassingen met één tenant.

#### <a name="microsoftdirectoryapplicationsbasicupdate"></a>microsoft.directory/applications/basic/update

Mogelijkheid om de naam, het logo, de URL van de startpagina, url-voorwaarden en URL-eigenschappen voor privacyverklaringen bij te werken op url-toepassingen met één tenant en meerdere tenant.To update the name, logo, homepage URL, terms of service URL, and privacy statement URL properties on single-tenant and multi-tenant applications. Verleent toegang tot alle velden op de pagina met registratie van toepassingen:

![Met deze toestemming krijgt u toegang tot de merkpagina voor app-registratie](./media/roles-custom-available-permissions/app-registration-branding.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationbasicupdate"></a>microsoft.directory/applications.myOrganization/basic/update

Verleent dezelfde machtigingen als microsoft.directory/applications/basic/update, maar alleen voor toepassingen met één tenant.

#### <a name="microsoftdirectoryapplicationscredentialsupdate"></a>microsoft.directory/applications/credentials/update

Mogelijkheid om de eigenschappen van certificaten en klantgeheimen bij te werken op toepassingen met één tenant en meerdere tenants. Verleent toegang tot alle velden op de aanvraagregistratiecertificaten & secrets-pagina:

![Met deze toestemming krijgt u toegang tot de pagina app-registratiecertificaten & geheimen](./media/roles-custom-available-permissions/app-registration-secrets.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationcredentialsupdate"></a>microsoft.directory/applications.myOrganization/credentials/update

Verleent dezelfde machtigingen als microsoft.directory/applications/credentials/update, maar alleen voor toepassingen met één map.

#### <a name="microsoftdirectoryapplicationsownersupdate"></a>microsoft.directory/applications/owners/update

Mogelijkheid om de eigenaar eigendom bij te werken op single-tenant en multi-tenant. Verleent toegang tot alle velden op de pagina met eigenaren van toepassingsregistraties:

![Met deze machtigingen krijgt u toegang tot de pagina met eigenaren van app-registraties](./media/roles-custom-available-permissions/app-registration-owners.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationownersupdate"></a>microsoft.directory/applications.myOrganization/owners/update

Verleent dezelfde machtigingen als microsoft.directory/applications/owners/update, maar alleen voor toepassingen met één tenant.

#### <a name="microsoftdirectoryapplicationspermissionsupdate"></a>microsoft.directory/applications/permissions/update

Mogelijkheid om de gedelegeerde machtigingen, toepassingsmachtigingen, geautoriseerde clienttoepassingen, vereiste machtigingen en toestemmingseigenschappen voor toepassingen met één tenant en meerdere tenant's bij te werken. Verleent niet de mogelijkheid om toestemming uit te voeren. Verleent toegang tot alle velden op de API-machtigingen voor toepassingsregistratie en Stelt een API-pagina's bloot:

![Met deze machtigingen wordt toegang verleend tot de pagina API-machtigingen voor app-registratie](./media/roles-custom-available-permissions/app-registration-api-permissions.png)

![Met deze machtigingen wordt toegang verleend tot de app-registratie Een API-pagina blootleggen](./media/roles-custom-available-permissions/app-registration-expose-api.png)

#### <a name="microsoftdirectoryapplicationsmyorganizationpermissionsupdate"></a>microsoft.directory/applications.myOrganization/permissions/update

Verleent dezelfde machtigingen als microsoft.directory/applications/permissions/update, maar alleen voor toepassingen met één tenant.

## <a name="required-license-plan"></a>Vereist licentieplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Aangepaste rollen maken met [de Azure-portal, Azure AD PowerShell en Graph API](roles-create-custom.md)
- [De toewijzingen voor een aangepaste rol weergeven](roles-view-assignments.md)

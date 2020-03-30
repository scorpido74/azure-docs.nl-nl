---
title: Aangepaste beheerdersrollen in Azure Active Directory | Microsoft Documenten
description: Bekijk een voorbeeld van aangepaste Azure AD-rollen voor het delegeren van identiteitsbeheer. Azure-rollen beheren in de Azure-portal, PowerShell of Graph-API.
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
ms.openlocfilehash: e5c7919dcc89e34831cb4cae7921b60b35eb4c69
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74024965"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Aangepaste beheerdersrollen in Azure Active Directory (voorbeeld)

In dit artikel wordt beschreven hoe u de aangepaste rollen van Azure AD begrijpt in Azure Active Directory (Azure AD) met op rollen gebaseerde toegangscontrole en resourcescopes. Aangepaste Azure [AD-rollen](directory-assign-admin-roles.md)geven de onderliggende machtigingen van de ingebouwde rollen weer, zodat u uw eigen aangepaste rollen maken en ordenen. Met deze aanpak u toegang verlenen op een meer gedetailleerde manier dan ingebouwde rollen, wanneer ze nodig zijn. Deze eerste versie van aangepaste Azure AD-rollen omvat de mogelijkheid om een rol te maken om machtigingen toe te wijzen voor het beheren van app-registraties. Na verloop van tijd worden extra machtigingen voor organisatieresources zoals bedrijfstoepassingen, gebruikers en apparaten toegevoegd.  

Daarnaast ondersteunen azure AD aangepaste rollen toewijzingen per resource, naast de meer traditionele organisatiebrede toewijzingen. Deze aanpak geeft u de mogelijkheid om toegang te verlenen tot het beheren van bepaalde bronnen (bijvoorbeeld één app-registratie) zonder toegang te geven tot alle bronnen (alle app-registraties).

Azure AD-rolegebaseerdtoegangsbeheer is een openbare preview-functie van Azure AD en is beschikbaar met elk betaald Azure AD-licentieplan. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie over voorvertoningen.

## <a name="understand-azure-ad-role-based-access-control"></a>Inzicht in Azure AD-toegangsbeheer op basis van azure-rollen

Het verlenen van toestemming met aangepaste Azure AD-rollen is een proces in twee stappen waarbij een aangepaste roldefinitie wordt gemaakt en deze vervolgens wordt toegewezen met behulp van een roltoewijzing. Een aangepaste roldefinitie is een verzameling machtigingen die u toevoegt uit een vooraf ingestelde lijst. Deze machtigingen zijn dezelfde machtigingen die worden gebruikt in de ingebouwde rollen.  

Zodra u uw roldefinitie hebt gemaakt, u deze aan een gebruiker toewijzen door een roltoewijzing te maken. Een roltoewijzing geeft de gebruiker de machtigingen in een roldefinitie op een opgegeven bereik. Met dit proces in twee stappen u één roldefinitie maken en deze vele malen toewijzen op verschillende scopes. Een bereik definieert de set Azure AD-resources waartoe het rollid toegang heeft. De meest voorkomende scope is organisatiebrede (org-wide) scope. Een aangepaste rol kan worden toegewezen aan het hele kader, wat betekent dat het rollid de rolmachtigingen heeft voor alle resources in de organisatie. Een aangepaste rol kan ook worden toegewezen aan een objectbereik. Een voorbeeld van een objectbereik is één toepassing. Dezelfde rol kan worden toegewezen aan één gebruiker voor alle toepassingen in de organisatie en vervolgens aan een andere gebruiker met een bereik van alleen de Contoso Expense Reports-app.  

Azure AD-ingebouwde en aangepaste rollen werken op concepten die vergelijkbaar zijn met [Azure-toegangsbeheer op basis van azure.](../../role-based-access-control/overview.md) Het [verschil tussen deze twee op rollen gebaseerde toegangscontrolesystemen](../../role-based-access-control/rbac-and-directory-admin-roles.md) is dat Azure RBAC de toegang tot Azure-bronnen zoals virtuele machines of opslag regelt met Azure Resource Management en aangepaste azure-rollen de toegang tot Azure AD-bronnen beheren met behulp van Graph API. Beide systemen maken gebruik van het concept van roldefinities en roltoewijzingen.

### <a name="role-assignments"></a>Roltoewijzingen

Een roltoewijzing is het object dat een roldefinitie koppelt aan een gebruiker in een bepaald bereik om Azure AD-brontoegang toe te kennen. Toegang wordt verleend door een roltoewijzing te maken en toegang kan worden ingetrokken door een roltoewijzing te verwijderen. In de kern bestaat een rolopdracht uit drie elementen:

- Gebruiker (een persoon die een gebruikersprofiel heeft in Azure Active Directory)
- Roldefinitie ophalen
- Resourcebereik

U [roltoewijzingen maken](roles-create-custom.md) met de Azure-portal, Azure AD PowerShell of Graph API. U ook [de toewijzingen voor een aangepaste rol bekijken.](roles-view-assignments.md#view-the-assignments-of-a-role)

Het volgende diagram toont een voorbeeld van een roltoewijzing. In dit voorbeeld heeft Chris Green de aangepaste rol van de app-registratiebeheerder toegewezen gekregen in het kader van de registratie van de Contoso Widget Builder-app. De toewijzing verleent Chris de machtigingen van de rol van de app-registratiebeheerder voor alleen deze specifieke app-registratie.

![Roltoewijzing is hoe machtigingen worden afgedwongen en drie delen hebben](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Beveiligings-principal

Een beveiligingsprincipal vertegenwoordigt de gebruiker die toegang moet krijgen tot Azure AD-bronnen. Een *gebruiker* is een persoon die een gebruikersprofiel heeft in Azure Active Directory.

### <a name="role"></a>Rol

Een roldefinitie of rol is een verzameling machtigingen. Een roldefinitie bevat de bewerkingen die kunnen worden uitgevoerd op Azure AD-bronnen, zoals maken, lezen, bijwerken en verwijderen. Er zijn twee typen rollen in Azure AD:

- Ingebouwde rollen die door Microsoft zijn gemaakt en die niet kunnen worden gewijzigd.
- Aangepaste rollen die door uw organisatie zijn gemaakt en beheerd.

### <a name="scope"></a>Bereik

Een bereik is de beperking van toegestane acties tot een bepaalde Azure AD-bron als onderdeel van een roltoewijzing. Wanneer u een rol toewijst, u een bereik opgeven dat de toegang van de beheerder tot een specifieke bron beperkt. Als u bijvoorbeeld een ontwikkelaar een aangepaste rol wilt toekennen, maar alleen om een specifieke toepassingsregistratie te beheren, u de specifieke toepassingsregistratie als bereik opnemen in de roltoewijzing.

  > [!Note]
  > Aangepaste rollen kunnen worden toegewezen aan directoryscope en resource scoped. Ze kunnen nog niet worden toegewezen aan de scope van de administratieve eenheid.
  > Ingebouwde rollen kunnen worden toegewezen aan directoryscope en in sommige gevallen het bereik van de administratieve eenheid. Ze kunnen nog niet worden toegewezen aan azure AD-bronbereik.

## <a name="required-license-plan"></a>Vereist licentieplan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Aangepaste roltoewijzingen maken met [de Azure-portal, Azure AD PowerShell en Graph API](roles-create-custom.md)
- [De toewijzingen voor een aangepaste rol weergeven](roles-view-assignments.md#view-assignments-of-single-application-scope)

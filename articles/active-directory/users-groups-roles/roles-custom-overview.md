---
title: Aangepaste beheerders rollen in Azure Active Directory | Microsoft Docs
description: Preview van aangepaste Azure AD-rollen voor het delegeren van identiteits beheer. Azure-rollen beheren in de Azure Portal, Power shell of Graph API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 04/22/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae244d93d679199aaa0bd08891cd34d4ca3a2ddc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82085107"
---
# <a name="custom-administrator-roles-in-azure-active-directory-preview"></a>Aangepaste beheerders rollen in Azure Active Directory (preview-versie)

In dit artikel wordt beschreven hoe u aangepaste Azure AD-rollen in Azure Active Directory (Azure AD) kunt begrijpen met op rollen gebaseerd toegangs beheer en resource bereik. Aangepaste Azure AD-rollen hebben een Opper vlakte van de onderliggende machtigingen van de [ingebouwde rollen](directory-assign-admin-roles.md), zodat u uw eigen aangepaste rollen kunt maken en ordenen. Met deze aanpak kunt u op een meer granulaire manier toegang verlenen dan ingebouwde rollen, wanneer dat nodig is. Deze eerste versie van aangepaste Azure AD-rollen bevat de mogelijkheid om een rol te maken voor het toewijzen van machtigingen voor het beheren van app-registraties. Na verloop van tijd worden extra machtigingen voor organisatie resources, zoals bedrijfs toepassingen, gebruikers en apparaten toegevoegd.  

Daarnaast ondersteunen aangepaste rollen van Azure AD ook toewijzingen per resource, naast de meer traditionele organisatie toewijzingen. Deze aanpak biedt u de mogelijkheid om toegang te verlenen tot het beheren van bepaalde bronnen (bijvoorbeeld één app-registratie) zonder dat u toegang hebt tot alle resources (alle app-registraties).

Toegangs beheer op basis van rollen in azure AD is een open bare preview-functie van Azure AD en is beschikbaar in elk betaald licentie plan voor Azure AD. Zie [aanvullende gebruiks voorwaarden voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)voor meer informatie over Previews.

## <a name="understand-azure-ad-role-based-access-control"></a>Meer informatie over Azure AD-op rollen gebaseerd toegangs beheer

Het verlenen van machtigingen met aangepaste Azure AD-rollen is een proces in twee stappen waarmee u een aangepaste roldefinitie maakt en deze vervolgens toewijst met behulp van een roltoewijzing. Een aangepaste roldefinitie is een verzameling machtigingen die u toevoegt vanuit een vooraf gedefinieerde lijst. Deze machtigingen zijn dezelfde machtigingen die worden gebruikt in de ingebouwde rollen.  

Wanneer u de roldefinitie hebt gemaakt, kunt u deze toewijzen aan een gebruiker door een roltoewijzing te maken. Een roltoewijzing verleent de gebruiker de machtigingen in een roldefinitie op een opgegeven bereik. Met dit proces met twee stappen kunt u een definitie van één rol maken en deze meerdere keren toewijzen aan verschillende bereiken. Een bereik definieert de set Azure AD-resources waartoe het rolinstantie toegang heeft. Het meest voorkomende bereik is het hele organisatie bereik (organisatiebreed). Een aangepaste rol kan worden toegewezen in het bereik van de organisatie, wat inhoudt dat het Role-lid over de rolmachtigingen beschikt over alle resources in het bedrijf. Een aangepaste rol kan ook worden toegewezen in een object bereik. Een voor beeld van een object bereik is één toepassing. Dezelfde rol kan worden toegewezen aan één gebruiker voor alle toepassingen in de organisatie en vervolgens aan een andere gebruiker met een bereik van alleen de app voor onkosten rapporten van contoso.  

Ingebouwde en aangepaste rollen van Azure AD worden toegepast op concepten die vergelijkbaar [zijn met toegangs beheer op basis van rollen](../../role-based-access-control/overview.md). Het [verschil tussen deze twee op rollen gebaseerde toegangs beheer systemen](../../role-based-access-control/rbac-and-directory-admin-roles.md) is dat Azure RBAC de toegang tot Azure-resources, zoals virtuele machines of opslag, beheert met Azure resource management en aangepaste rollen van Azure AD de toegang tot Azure AD-resources met behulp van Graph API beheren. Beide systemen maken gebruik van het concept van functie definities en roltoewijzingen.

### <a name="how-azure-ad-determines-if-a-user-has-access-to-a-resource"></a>Hoe Azure AD bepaalt of een gebruiker toegang heeft tot een resource

Hieronder vindt u de stappen op hoog niveau die door Azure AD worden gebruikt om te bepalen of u toegang hebt tot een beheer bron. Gebruik deze informatie om problemen met toegang op te lossen.

1. Een gebruiker (of Service-Principal) verkrijgt een token aan het Microsoft Graph of het eind punt van de Azure AD-grafiek.

1. De gebruiker maakt met behulp van het uitgegeven token een API-aanroep naar Azure Active Directory (Azure AD) via Microsoft Graph of Azure AD Graph.

1. Afhankelijk van de omstandigheid neemt Azure AD een van de volgende acties:

    - Evalueert de rollidmaatschap van de gebruiker op basis van de [WIDS-claim](https://docs.microsoft.com/azure/active-directory/develop/access-tokens) in het toegangs token van de gebruiker.
    - Haalt alle roltoewijzingen op die van toepassing zijn op de gebruiker, hetzij rechtstreeks of via groepslid maatschap, op de resource waarop de actie wordt uitgevoerd.

1. Azure AD bepaalt of de actie in de API-aanroep is opgenomen in de rollen die de gebruiker voor deze bron heeft.
1. Als de gebruiker geen rol heeft met de actie in het aangevraagde bereik, wordt geen toegang verleend. Anders wordt toegang verleend.

### <a name="role-assignments"></a>Roltoewijzingen

Een roltoewijzing is het object dat een functie definitie koppelt aan een gebruiker in een bepaald bereik om toegang tot Azure AD-bronnen te verlenen. Toegang wordt verleend door een roltoewijzing te maken en toegang kan worden ingetrokken door een roltoewijzing te verwijderen. Op basis van zijn kernen bestaat een roltoewijzing uit drie elementen:

- Gebruiker (een persoon die een gebruikers profiel heeft in Azure Active Directory)
- Roldefinitie ophalen
- Bron bereik

U kunt [roltoewijzingen maken](roles-create-custom.md) met behulp van de Azure Portal, Azure AD Power shell of graph API. U kunt ook [de toewijzingen voor een aangepaste rol weer geven](roles-view-assignments.md#view-the-assignments-of-a-role).

Het volgende diagram toont een voorbeeld van een roltoewijzing. In dit voor beeld is Chris groen toegewezen aan de aangepaste rol van de beheerder van de app-registratie op het bereik van de app-registratie voor contoso-widget. De toewijzing verleent Chris de machtigingen van de beheerdersrol van de app-registratie voor alleen deze specifieke app-registratie.

![Roltoewijzing is het afdwingen van machtigingen en heeft drie delen](./media/roles-custom-overview/rbac-overview.png)

### <a name="security-principal"></a>Beveiligings-principal

Een beveiligingsprincipal vertegenwoordigt de gebruiker die toegang moet krijgen tot Azure AD-resources. Een *gebruiker* is een persoon die een gebruikers profiel heeft in azure Active Directory.

### <a name="role"></a>Rol

Een roldefinitie of rol is een verzameling machtigingen. Met een roldefinitie worden de bewerkingen weer gegeven die kunnen worden uitgevoerd op Azure AD-resources, zoals maken, lezen, bijwerken en verwijderen. Er zijn twee soorten rollen in azure AD:

- Ingebouwde rollen die door micro soft zijn gemaakt, kunnen niet worden gewijzigd.
- Aangepaste rollen die door uw organisatie zijn gemaakt en beheerd.

### <a name="scope"></a>Bereik

Een bereik is de beperking van toegestane acties voor een bepaalde Azure AD-resource als onderdeel van een roltoewijzing. Wanneer u een rol toewijst, kunt u een bereik opgeven waarmee de beheerder de toegang tot een specifieke resource beperkt. Als u bijvoorbeeld een ontwikkelaar een aangepaste rol wilt verlenen, maar alleen een bepaalde toepassings registratie wilt beheren, kunt u de specifieke toepassings registratie als een bereik in de roltoewijzing toevoegen.

  > [!Note]
  > Aangepaste rollen kunnen worden toegewezen in het bereik van de map en het resource bereik. Ze kunnen nog niet worden toegewezen in het bereik van de beheer eenheid.
  > Ingebouwde rollen kunnen worden toegewezen in het bereik van de Directory, en in sommige gevallen is het bereik van de beheer eenheid. Ze kunnen nog niet worden toegewezen in het Azure AD-resource bereik.

## <a name="required-license-plan"></a>Vereist licentie plan

[!INCLUDE [License requirement for using custom roles in Azure AD](../../../includes/active-directory-p1-license.md)]

## <a name="next-steps"></a>Volgende stappen

- Aangepaste roltoewijzingen maken met behulp van [de Azure Portal, Azure AD Power shell en Graph API](roles-create-custom.md)
- [De toewijzingen voor een aangepaste rol weer geven](roles-view-assignments.md#view-assignments-of-single-application-scope)

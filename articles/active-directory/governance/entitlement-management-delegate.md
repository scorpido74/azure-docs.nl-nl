---
title: Delegatie en rollen in beheer van rechten - Azure AD
description: Meer informatie over het delegeren van toegangsbeheer van IT-beheerders aan afdelingsmanagers en projectmanagers, zodat ze zelf toegang kunnen beheren.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/07/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 86d924860e97b15a0a4af46c5bc35b0e0050292b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261838"
---
# <a name="delegation-and-roles-in-azure-ad-entitlement-management"></a>Delegatie en rollen in Azure AD-rechtenbeheer

Globale beheerders en gebruikersbeheerders kunnen standaard alle aspecten van Azure AD-rechtenbeheer maken en beheren. Het is echter mogelijk dat de gebruikers in deze rollen niet alle situaties kennen waarin toegangspakketten vereist zijn. Meestal zijn het gebruikers binnen de respectievelijke afdelingen, teams of projecten die weten met wie ze samenwerken, met welke resources en voor hoe lang. In plaats van onbeperkte machtigingen toe te kennen aan niet-beheerders, u gebruikers de minste machtigingen verlenen die ze nodig hebben om hun taak uit te voeren en te voorkomen dat er conflicterende of ongepaste toegangsrechten worden gevonden.

In deze video vindt u een overzicht van hoe u toegangsbeheer van IT-beheerder delegeren aan gebruikers die geen beheerders zijn.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE3Lq00]

## <a name="delegate-example"></a>Voorbeeld van gemachtigde

Als u wilt begrijpen hoe u toegangsbeheer delegeren in het beheer van rechten, helpt het om een voorbeeld te overwegen. Stel dat uw organisatie de volgende beheerder en managers heeft.

![Delegeren van IT-beheerder naar managers](./media/entitlement-management-delegate/delegate-admin-dept-managers.png)

Als IT-beheerder heeft Hana contacten in elke afdeling - Mamta in Marketing, Mark in Finance en Joe in Legal die verantwoordelijk zijn voor de resources en bedrijfskritische inhoud van hun afdeling.

Met beheer van rechten u toegangsbeheer delegeren aan deze niet-beheerders, omdat zij degenen zijn die weten welke gebruikers toegang nodig hebben, voor hoe lang en tot welke resources. Dit zorgt ervoor dat de juiste mensen de toegang voor hun afdelingen beheren.

Hier is een manier waarop Hana toegang beheer kan delegeren aan de marketing, financiën en juridische afdelingen.

1. Hana maakt een nieuwe Azure AD-beveiligingsgroep en voegt Mamta, Mark en Joe toe als leden van de groep.

1. Hana voegt die groep toe aan de rol van de makers van de catalogus.

    Mamta, Mark en Joe kunnen nu catalogi maken voor hun afdelingen, resources toevoegen die hun afdelingen nodig hebben en verdere delegatie doen binnen de catalogus.

    Merk op dat Mamta, Mark en Joe elkaars catalogi niet kunnen zien.

1. Mamta maakt een **marketingcatalogus,** een container met resources.

1. Mamta voegt de middelen die haar marketing afdeling bezit aan deze catalogus.

1. Mamta kan extra mensen van haar afdeling toevoegen als cataloguseigenaren voor deze catalogus. Dit helpt bij het delen van de verantwoordelijkheden voor het beheer van de catalogus.

1. Mamta kan het maken en beheren van toegangspakketten in de marketingcatalogus verder delegeren aan projectmanagers van de marketingafdeling. Ze kan dit doen door ze toe te stellen aan de rol van access package manager. Een access package manager kan toegangspakketten maken en beheren. 

In het volgende diagram ziet u catalogi met bronnen voor de marketing-, financiële en juridische afdelingen. Met behulp van deze catalogi kunnen projectmanagers toegangspakketten maken voor hun teams of projecten.

![Voorbeeld van het gemachtigde van het recht](./media/entitlement-management-delegate/elm-delegate.png)

Na delegatie kan de marketingafdeling rollen hebben die vergelijkbaar zijn met de volgende tabel.

| Gebruiker | Functie | Azure AD-rol | Rol van rechtbeheer |
| --- | --- | --- | --- |
| Hana | IT-beheerder | Globale beheerder of gebruikersbeheerder |  |
| Mamta (Mamta) | Marketing manager | Gebruiker | Catalogusmaker en cataloguseigenaar |
| Bob | Marketinglead | Gebruiker | Cataloguseigenaar |
| Jessica | Marketing projectmanager | Gebruiker | Access-pakketbeheer |

## <a name="entitlement-management-roles"></a>Beheerrollen voor rechten

Het beheer van rechten heeft de volgende rollen die specifiek zijn voor het beheer van rechten.

| Rol van rechtbeheer | Beschrijving |
| --- | --- |
| Maker van catalogus | Catalogi maken en beheren. Typisch een IT-beheerder die geen globale beheerder is, of een broneigenaar voor een verzameling resources. De persoon die een catalogus maakt, wordt automatisch de eerste cataloguseigenaar van de catalogus en kan extra cataloguseigenaren toevoegen. Een catalogusmaker kan geen catalogi beheren of zien waarvan hij geen eigenaar is en die geen resources kan toevoegen die ze niet bezitten aan een catalogus. Als de maker van de catalogus een andere catalogus moet beheren of resources moet toevoegen waarvan ze geen eigenaar zijn, kan deze een mede-eigenaar van die catalogus of bron aanvragen. |
| Cataloguseigenaar | Bestaande catalogi bewerken en beheren. Typisch een IT-beheerder of resource-eigenaren, of een gebruiker die de eigenaar van de catalogus heeft aangewezen. |
| Access-pakketbeheer | Bewerk en beheer alle bestaande toegangspakketten in een catalogus. |

Bovendien hebben een aangewezen goedkeurder en een aanvrager van een toegangspakket ook rechten, hoewel ze geen rollen zijn.

| Rechts | Beschrijving |
| --- | --- |
| Fiatteur | Geautoriseerd door een beleid om verzoeken om toegang te krijgen tot pakketten goed te keuren of te weigeren, hoewel ze de definities van toegangspakketten niet kunnen wijzigen. |
| Requestor | Geautoriseerd door een beleid van een toegangspakket om dat toegangspakket aan te vragen. |

In de volgende tabel worden de taken weergegeven die de beheerrollen van het recht kunnen uitvoeren.

| Taak | Beheerder | Maker van catalogus | Cataloguseigenaar | Access-pakketbeheer |
| --- | :---: | :---: | :---: | :---: |
| [Delegeren aan een catalogusmaker](entitlement-management-delegate-catalog.md) | :heavy_check_mark: |  |  |  |
| [Een verbonden organisatie toevoegen](entitlement-management-organization.md) | :heavy_check_mark: |  |  |  |
| [Een nieuwe catalogus maken](entitlement-management-catalog-create.md) | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Een resource toevoegen aan een catalogus](entitlement-management-catalog-create.md#add-resources-to-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Een cataloguseigenaar toevoegen](entitlement-management-catalog-create.md#add-additional-catalog-owners) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Een catalogus bewerken](entitlement-management-catalog-create.md#edit-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Een catalogus verwijderen](entitlement-management-catalog-create.md#delete-a-catalog) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Delegeren aan een toegangspakketbeheerder](entitlement-management-delegate-managers.md) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Een toegangspakketbeheerder verwijderen](entitlement-management-delegate-managers.md#remove-an-access-package-manager) | :heavy_check_mark: |  | :heavy_check_mark: |  |
| [Een nieuw toegangspakket maken in een catalogus](entitlement-management-access-package-create.md) | :heavy_check_mark: |  | :heavy_check_mark:  | :heavy_check_mark:  |
| [Resourcerollen wijzigen in een toegangspakket](entitlement-management-access-package-resources.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Beleid maken en bewerken](entitlement-management-access-package-request-policy.md) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Een gebruiker rechtstreeks toewijzen aan een toegangspakket](entitlement-management-access-package-assignments.md#directly-assign-a-user) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Zien wie een toewijzing heeft voor een toegangspakket](entitlement-management-access-package-assignments.md#view-who-has-an-assignment) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [De aanvragen van een toegangspakket weergeven](entitlement-management-access-package-requests.md#view-requests) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [De leveringsfouten van een aanvraag weergeven](entitlement-management-troubleshoot.md#view-a-requests-delivery-errors) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Een aanvraag opnieuw verwerken](entitlement-management-troubleshoot.md#reprocess-a-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Een aanvraag in behandeling annuleren](entitlement-management-troubleshoot.md#cancel-a-pending-request) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Een toegangspakket verbergen](entitlement-management-access-package-edit.md#change-the-hidden-setting) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |
| [Een toegangspakket verwijderen](entitlement-management-access-package-edit.md#delete-an-access-package) | :heavy_check_mark: |  | :heavy_check_mark: | :heavy_check_mark: |

## <a name="required-roles-to-add-resources-to-a-catalog"></a>Vereiste rollen om resources toe te voegen aan een catalogus

Een globale beheerder kan elke groep (door de cloud gemaakte beveiligingsgroepen of door de cloud gemaakte Office 365-groepen), toepassing of SharePoint Online-site in een catalogus toevoegen of verwijderen. Een gebruikersbeheerder kan een groep of toepassing in een catalogus toevoegen of verwijderen.

Voor een gebruiker die geen globale beheerder of gebruikersbeheerder is, om groepen, toepassingen of SharePoint Online-sites aan een catalogus toe te voegen, moet die gebruiker *zowel* de vereiste Azure AD-maprol rol en catalogus eigenaar rechten beheerrol. In de volgende tabel worden de rolcombinaties weergegeven die nodig zijn om resources aan een catalogus toe te voegen. Als u resources uit een catalogus wilt verwijderen, moet u dezelfde rollen hebben.

| Azure AD-maprol | Rol van rechtbeheer | Kan beveiligingsgroep toevoegen | Kan Office 365-groep toevoegen | Kan app toevoegen | Kan SharePoint Online-site toevoegen |
| --- | :---: | :---: | :---: | :---: | :---: |
| [Globale beheerder](../users-groups-roles/directory-assign-admin-roles.md) | N.v.t. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| [Gebruikersbeheerder](../users-groups-roles/directory-assign-admin-roles.md) | N.v.t. |  :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |  |
| [Intune-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Cataloguseigenaar | :heavy_check_mark: | :heavy_check_mark: |  |  |
| [Exchange-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Cataloguseigenaar |  | :heavy_check_mark: |  |  |
| [Teams-servicebeheerder](../users-groups-roles/directory-assign-admin-roles.md) | Cataloguseigenaar |  | :heavy_check_mark: |  |  |
| [SharePoint-beheerder](../users-groups-roles/directory-assign-admin-roles.md) | Cataloguseigenaar |  | :heavy_check_mark: |  | :heavy_check_mark: |
| [Toepassingsbeheerder](../users-groups-roles/directory-assign-admin-roles.md) | Cataloguseigenaar |  |  | :heavy_check_mark: |  |
| [Beheerder van cloudtoepassingen](../users-groups-roles/directory-assign-admin-roles.md) | Cataloguseigenaar |  |  | :heavy_check_mark: |  |
| Gebruiker | Cataloguseigenaar | Alleen als de eigenaar van de groep | Alleen als de eigenaar van de groep | Alleen als de eigenaar van de app |  |

Als u de minst bevoorrechte rol voor een taak wilt bepalen, u ook verwijzen naar [beheerdersrollen per beheertaak in Azure Active Directory](../users-groups-roles/roles-delegate-by-task.md#entitlement-management).

## <a name="next-steps"></a>Volgende stappen

- [Toegangsbeheer delegeren aan catalogusmakers](entitlement-management-delegate-catalog.md)
- [Een catalogus met bronnen maken en beheren](entitlement-management-catalog-create.md)

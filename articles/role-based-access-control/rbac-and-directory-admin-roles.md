---
title: Klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen
description: Hierin worden de verschillende rollen in azure-klassieke abonnements beheerders rollen, Azure-rollen en Azure Active Directory (Azure AD)-rollen beschreven
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 174f1706-b959-4230-9a75-bf651227ebf6
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/27/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro;
ms.openlocfilehash: 70baddf86207c490d3b85e0f584525592f8a7ad7
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/03/2020
ms.locfileid: "82735798"
---
# <a name="classic-subscription-administrator-roles-azure-roles-and-azure-ad-roles"></a>Klassieke abonnements beheerders rollen, Azure-rollen en Azure AD-rollen

Als u nieuw bent bij Azure, is het misschien lastig om een goed beeld te krijgen van de verschillende rollen in Azure. In dit artikel vindt u informatie over de volgende rollen en wanneer u elk type rol moet gebruiken:
- Klassieke abonnementsbeheerdersrollen
- Azure-rollen
- Azure Active Directory-rollen (Azure AD)

## <a name="how-the-roles-are-related"></a>Relatie tussen de rollen

Een korte beschrijving van de geschiedenis van Azure helpt om een beter beeld te krijgen van de verschillende rollen. Bij de introductie van Azure waren er voor het beheer van resources slechts drie beheerdersrollen: Accountbeheerder, Servicebeheerder en Medebeheerder. Later is Azure RBAC (op rollen gebaseerd toegangs beheer) toegevoegd. Azure RBAC is een nieuwer autorisatiesysteem dat gedetailleerd toegangsbeheer biedt voor Azure-resources. Azure RBAC bevat veel ingebouwde rollen die kunnen worden toegewezen aan verschillende bereiken, en u kunt uw eigen aangepaste rollen maken. Voor het beheren van resources in azure AD, zoals gebruikers, groepen en domeinen, zijn er verschillende Azure AD-rollen.

Het volgende diagram is een hoog overzicht van de manier waarop de rollen van de klassieke abonnements beheerder, Azure-rollen en Azure AD zijn gerelateerd.

![De verschillende rollen in Azure](./media/rbac-and-directory-admin-roles/rbac-admin-roles.png)


## <a name="classic-subscription-administrator-roles"></a>Klassieke abonnementsbeheerdersrollen

Accountbeheerder, Servicebeheerder en Medebeheerder zijn de drie klassieke abonnementsbeheerdersrollen in Azure. Klassieke abonnementsbeheerders hebben volledige toegang tot het Azure-abonnement. Ze kunnen resources beheren met behulp van Azure Portal, API's van Azure Resource Manager en API's van het klassieke implementatiemodel. Het account dat wordt gebruikt voor registratie bij Azure wordt automatisch ingesteld als de accountbeheerder en de servicebeheerder. Vervolgens kunnen er extra medebeheerders of co-beheerders worden toegevoegd. De service beheerder en de mede beheerders hebben de gelijkwaardige toegang van gebruikers die de rol van eigenaar (een Azure-rol) aan het abonnements bereik hebben toegewezen. De volgende tabel beschrijft de verschillen tussen deze drie klassieke abonnementsbeheerdersrollen.

| Klassiek abonnement beheerder | Limiet | Machtigingen | Opmerkingen |
| --- | --- | --- | --- |
| Accountbeheerder | 1 per Azure-account | <ul><li>[Azure-accountcentrum](https://account.azure.com/Subscriptions) openen</li><li>Alle abonnementen in een account beheren</li><li>Nieuwe abonnementen maken</li><li>Abonnementen annuleren</li><li>De facturering voor een abonnement wijzigen</li><li>De servicebeheerder wijzigen</li></ul> | Conceptueel gezien de factureringseigenaar van het abonnement.<br>De accountbeheerder heeft geen toegang tot de Azure-portal. |
| Servicebeheerder | 1 per Azure-abonnement | <ul><li>Services beheren in [Azure Portal](https://portal.azure.com)</li><li>Het abonnement annuleren</li><li>Gebruikers de rol van medebeheerder geven</li></ul> | Voor een nieuw abonnement is het standaard zo dat de accountbeheerder ook de servicebeheerder is.<br>De servicebeheerder heeft dezelfde toegang als een gebruiker met de rol van eigenaar op abonnementsniveau.<br>De servicebeheerder heeft volledige toegang tot de Azure-portal. |
| Medebeheerder | 200 per abonnement | <ul><li>Deze rol heeft dezelfde toegangsrechten als de rol Servicebeheerder, maar kan de koppeling van abonnementen aan Azure-adreslijsten niet wijzigen</li><li>Gebruikers toewijzen aan de rol Medebeheerder, maar kan de servicebeheerder niet wijzigen</li></ul> | De medebeheerder heeft dezelfde toegang als een gebruiker met de rol van eigenaar op abonnementsniveau. |

In de Azure-portal kunt u co-beheerders beheren of de servicebeheerder weergeven met behulp van het tabblad **Klassieke beheerders**.

![Klassieke abonnementsbeheerders van Azure in de Azure-portal](./media/rbac-and-directory-admin-roles/subscription-view-classic-administrators.png)

In de Azure-portal kunt u de servicebeheerder weergeven of wijzigen en de accountbeheerder weergeven op de blade Eigenschappen van uw abonnement.

![Accountbeheerder en Servicebeheerder in Azure Portal](./media/rbac-and-directory-admin-roles/account-admin.png)

Ga voor meer informatie naar [Klassieke abonnementsbeheerders van Azure](classic-administrators.md).

### <a name="azure-account-and-azure-subscriptions"></a>Azure-account en Azure-abonnementen

Een Azure-account vertegenwoordigt een factureringsrelatie. Een Azure-account wordt gevormd door een gebruikersidentiteit, een of meer Azure-abonnementen en een bijbehorende set Azure-resources. De persoon die het account maakt, is de accountbeheerder voor alle abonnementen die in dat account worden gemaakt. Deze persoon is standaard ook de servicebeheerder voor het abonnement.

Azure-abonnementen helpen u de toegang tot Azure-resources in goede banen te leiden. Ze helpen u ook om te bepalen hoe resourcegebruik wordt gerapporteerd, gefactureerd en betaald. Elk abonnement kan een andere facturerings- en betalingsinstelling hebben, zodat u per kantoor, afdeling, project, enzovoort verschillende abonnementen en plannen kunt hebben. Elke service hoort bij een abonnement en de abonnements-id is mogelijk vereist voor programmatische bewerkingen.

Elk abonnement is gekoppeld aan een Azure AD-adres lijst. Als u wilt zoeken naar de map waaraan het abonnement is gekoppeld, opent u **abonnementen** in de Azure Portal en selecteert u vervolgens een abonnement om de map weer te geven.

Accounts en abonnementen worden beheerd in het [Azure-accountcentrum](https://account.azure.com/Subscriptions).

## <a name="azure-roles"></a>Azure-rollen

Azure RBAC staat voor op rollen gebaseerd toegangsbeheer en is een machtigingssysteem dat is gebouwd op [Azure Resource Manager](../azure-resource-manager/management/overview.md). Het voorziet in een geavanceerd toegangsbeheer van resources in Azure, zoals rekenbronnen en opslag. Azure RBAC bevat meer dan 70 ingebouwde rollen. Er zijn vier belang rijke Azure-rollen. De eerste drie gelden voor alle resourcetypen:

| Azure-rol | Machtigingen | Opmerkingen |
| --- | --- | --- |
| [Eigenaar](built-in-roles.md#owner) | <ul><li>Volledige toegang tot alle resources</li><li>Toegang aan anderen delegeren</li></ul> | De servicebeheerder en medebeheerders krijgen de rol van eigenaar op abonnementsniveau<br>Geldt voor alle resourcetypen. |
| [Inzender](built-in-roles.md#contributor) | <ul><li>Alle soorten Azure-resources maken en beheren</li><li>Een nieuwe tenant maken in Azure Active Directory</li><li>Kan geen toegang aan anderen verlenen</li></ul> | Geldt voor alle resourcetypen. |
| [Lezer](built-in-roles.md#reader) | <ul><li>Azure-resources weergeven</li></ul> | Geldt voor alle resourcetypen. |
| [Beheerder voor gebruikerstoegang](built-in-roles.md#user-access-administrator) | <ul><li>Toegang van gebruikers tot Azure-resources beheren</li></ul> |  |

Met de overige ingebouwde rollen kunnen specifieke Azure-resources worden beheerd. Met de rol [Inzender voor virtuele machines](built-in-roles.md#virtual-machine-contributor) kan een gebruiker bijvoorbeeld virtuele machines maken en beheren. Zie [ingebouwde rollen in azure](built-in-roles.md)voor een lijst met alle ingebouwde rollen.

Alleen de Azure Portal en de Azure Resource Manager-Api's ondersteunen Azure RBAC. Gebruikers, groepen en toepassingen waaraan Azure-functies zijn toegewezen, kunnen de [api's van het klassieke Azure-implementatie model](../azure-resource-manager/management/deployment-models.md)niet gebruiken.

In de Azure Portal worden roltoewijzingen die gebruikmaken van Azure RBAC, weer gegeven op de Blade **toegangs beheer (IAM)** . Deze Blade kan worden gevonden in de portal, zoals beheer groepen, abonnementen, resource groepen en verschillende bronnen.

![De blade Toegangsbeheer (IAM) in Azure Portal](./media/rbac-and-directory-admin-roles/access-control-role-assignments.png)

Wanneer u op het tabblad **Rollen** klikt, ziet u de lijst met geïntegreerde en aangepaste rollen.

![Ingebouwde rollen in Azure Portal](./media/rbac-and-directory-admin-roles/roles-list.png)

Zie [Azure-roltoewijzingen toevoegen of verwijderen met behulp van de Azure Portal](role-assignments-portal.md)voor meer informatie.

## <a name="azure-ad-roles"></a>Azure AD-rollen

Azure AD-functies worden gebruikt voor het beheren van Azure AD-resources in een directory, zoals het maken of bewerken van gebruikers, het toewijzen van beheerders rollen aan anderen, het opnieuw instellen van gebruikers wachtwoorden, het beheren van gebruikers licenties en het beheren van domeinen. In de volgende tabel worden enkele belang rijke Azure AD-rollen beschreven.

| Azure AD-rol | Machtigingen | Opmerkingen |
| --- | --- | --- |
| [Globale beheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#company-administrator-permissions) | <ul><li>Toegang tot alle beheerfuncties in Azure Active Directory beheren, evenals services die federeren naar Azure Active Directory</li><li>Beheerdersrollen aan anderen toewijzen</li><li>Het wachtwoord voor gebruikers en alle andere beheerders opnieuw instellen</li></ul> | De persoon die zich registreert voor de Azure Active Directory-tenant wordt de globale beheerder. |
| [Gebruikersbeheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#user-administrator) | <ul><li>Alle aspecten van gebruikers en groepen maken en beheren</li><li>Ondersteuningstickets beheren</li><li>Servicestatus bewaken</li><li>Wachtwoorden wijzigen voor gebruikers, helpdeskbeheerders en andere gebruikersbeheerders</li></ul> |  |
| [Factureringsbeheerder](../active-directory/users-groups-roles/directory-assign-admin-roles.md#billing-administrator) | <ul><li>Aankopen doen</li><li>Abonnementen beheren</li><li>Ondersteuningstickets beheren</li><li>Servicestatus beheren</li></ul> |  |

In de Azure Portal ziet u de lijst met Azure AD-rollen op de Blade **rollen en beheerders** . Zie [machtigingen voor beheerdersrol in azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)voor een lijst met alle Azure AD-rollen.

![Azure AD-rollen in de Azure Portal](./media/rbac-and-directory-admin-roles/directory-admin-roles.png)

## <a name="differences-between-azure-roles-and-azure-ad-roles"></a>Verschillen tussen Azure-rollen en Azure AD-rollen

Op hoog niveau beheren Azure-rollen beheer machtigingen voor het beheer van Azure-resources, terwijl Azure AD-rollen machtigingen voor het beheren van Azure Active Directory resources hebben. In de volgende tabel worden enkele verschillen vergeleken.

| Azure-rollen | Azure AD-rollen |
| --- | --- |
| Toegang tot Azure-resources beheren | Toegang tot Azure Active Directory-resources beheren |
| Ondersteuning voor aangepaste rollen | Ondersteuning voor aangepaste rollen |
| Bereik kan worden opgegeven op meerdere niveaus (beheergroep, abonnement, resourcegroep, resource) | Bereik is op tenantniveau |
| Gegevens van rollen zijn beschikbaar vanuit Azure Portal, Azure CLI, Azure PowerShell, Azure Resource Manager-sjablonen en de REST-API | U kunt toegang krijgen tot de functie gegevens in de Azure-beheer Portal, Microsoft 365 beheer centrum, Microsoft Graph, AzureAD Power shell |

### <a name="do-azure-roles-and-azure-ad-roles-overlap"></a>Overlappen Azure-rollen en Azure AD-rollen elkaar?

Azure-rollen en Azure AD-rollen vallen standaard niet onder Azure en Azure AD. Als een globale beheerder de toegang echter heeft verhoogd door de globale beheerder te kiezen voor het **beheren van Azure-abonnementen en beheergroepen** switch in de Azure Portal, krijgt de globale beheerder de rol van [beheerder voor gebruikers toegang](built-in-roles.md#user-access-administrator) (een Azure-rol) voor alle abonnementen voor een bepaalde Tenant. De rol Beheerder van gebruikerstoegang stelt de gebruiker in staat om andere gebruikers toegang te verlenen tot Azure-resources. Deze schakeloptie kan handig zijn om weer toegang te krijgen tot een abonnement. Zie [toegang verhogen voor het beheer van alle Azure-abonnementen en-beheer groepen](elevate-access-global-admin.md)voor meer informatie.

Verschillende Azure AD-rollen beslaan Azure AD en Microsoft Office 365, zoals de rol van globale beheerder en gebruikers beheerder. Als u bijvoorbeeld lid bent van de rol Globale beheerder, hebt u de bevoegdheden van globale beheerders in Azure AD en Office 365, zoals het doorvoeren van wijzigingen in Microsoft Exchange en Microsoft SharePoint. De globale beheerder heeft echter standaard geen toegang tot Azure-resources.

![Azure RBAC versus Azure AD-rollen](./media/rbac-and-directory-admin-roles/azure-office-roles.png)

## <a name="next-steps"></a>Volgende stappen

- [Wat is Azure op rollen gebaseerd toegangs beheer (Azure RBAC)?](overview.md)
- [Machtigingen voor beheerrol in Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md)
- [Klassieke abonnementsbeheerders van Azure](classic-administrators.md)
- [Cloud acceptatie Framework: resource Access Management in azure](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)

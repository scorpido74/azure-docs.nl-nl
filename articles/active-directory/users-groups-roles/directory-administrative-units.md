---
title: Beheer van beheer eenheden (preview)-Azure AD | Microsoft Docs
description: Beheer eenheden gebruiken voor meer gedetailleerde delegering van machtigingen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44a01bb7d2f4aa3d31204d6235e955e82e471d5d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84729044"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Beheer van beheer eenheden in Azure Active Directory (preview-versie)

In dit artikel worden administratieve eenheden in Azure Active Directory (Azure AD) beschreven. Een beheer eenheid is een Azure AD-resource die een container kan zijn voor andere Azure AD-resources. In deze preview-versie kan een administratieve eenheid alleen gebruikers en groepen bevatten.

Met beheer eenheden kunt u beheerders machtigingen verlenen die zijn beperkt tot een afdeling, regio of ander segment van uw organisatie die u definieert. U kunt administratieve eenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om beleid op een gedetailleerd niveau in te stellen. Een gebruikers account beheerder kan bijvoorbeeld profiel gegevens bijwerken, wacht woorden opnieuw instellen en licenties voor gebruikers alleen in hun administratieve eenheid toewijzen.

 Bijvoorbeeld het delegeren van regionale ondersteunings specialisten is de beheerdersrol van de [helpdesk beheerder](directory-assign-admin-roles.md#helpdesk-administrator) beperkt tot het beheren van alleen de gebruikers in de regio die ze ondersteunen.

## <a name="deployment-scenario"></a>Implementatiescenario

Het beperken van het beheer bereik met behulp van administratieve eenheden kan nuttig zijn in organisaties die bestaan uit onafhankelijke divisies van welke aard dan ook. Bekijk het voor beeld van een grote universiteit die bestaat uit een groot aantal zelfstandige scholen (school van bedrijf, school van engineering, enzovoort) die elk een team van IT-beheerders heeft die de toegang beheren, gebruikers beheren en beleids regels voor hun school instellen. Een centrale beheerder kan het volgende doen:

- Een rol maken met beheerders machtigingen van alleen Azure AD-gebruikers in de Business School-beheer eenheid
- Een administratieve eenheid maken voor de school van het bedrijf
- De beheerder-eenheid vullen met alleen de studenten en mede werkers van zakelijke school
- Het IT-team van de zakelijke school toevoegen aan de rol met hun bereik

## <a name="license-requirements"></a>Licentievereisten

Voor het gebruik van beheer eenheden is een Azure Active Directory Premium licentie vereist voor elke beheerder van de beheer eenheid en Azure Active Directory Free licenties voor leden van de beheer eenheid. Zie [aan de slag met Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)voor meer informatie.

## <a name="manage-administrative-units"></a>Beheer eenheden beheren

In deze preview-versie kunt u beheer eenheden beheren met behulp van de Azure Portal, Power shell-cmdlets en scripts of de Microsoft Graph. Raadpleeg de documentatie voor meer informatie:

- [Rollen maken, verwijderen, vullen en toevoegen aan beheer eenheden](roles-admin-units-manage.md): procedures voor volt ooien
- [Werken met beheerders eenheden](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0): werken met beheer eenheden met behulp van Power shell
- [Graph-ondersteuning voor administratieve](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)eenheden: gedetailleerde documentatie over Microsoft Graph voor beheer eenheden.

### <a name="planning-your-administrative-units"></a>Uw beheer eenheden plannen

Beheer eenheden kunnen worden gebruikt voor het logisch groeperen van Azure AD-resources. Bijvoorbeeld: voor een organisatie waarvan de IT-afdeling wereld wijd is gespreid, kan het zinvol zijn om beheer eenheden te maken die deze geografische grenzen definiëren. In een ander scenario waarbij een Multi-National organisatie verschillende ' suborganisatie ' heeft, die semi-autonoom in bewerkingen zijn, kan elke subgroep worden vertegenwoordigd door een administratieve eenheid.

De criteria voor het maken van administratieve eenheden worden geleid door de unieke vereisten van een organisatie. Beheer eenheden zijn een gemeen schappelijke manier om structuur te definiëren tussen M365-Services. We raden u aan om uw administratieve eenheden voor te bereiden met het gebruik ervan in M365 Services. U kunt de maximale waarde van administratieve eenheden ophalen wanneer u algemene resources aan M365 in een administratieve eenheid kunt koppelen.

U kunt het maken van administratieve eenheden in de organisatie verwachten door de volgende fasen door te lopen:

1. Eerste acceptatie: uw organisatie begint met het maken van administratieve eenheden op basis van de initiële criteria en het aantal administratieve eenheden neemt toe naarmate de criteria worden verfijnd.
1. Weghalen: zodra de criteria goed zijn gedefinieerd, worden de beheer eenheden die niet meer nodig zijn, verwijderd.
1. Stabilisatie: de structuur van uw organisatie is duidelijk gedefinieerd en het aantal administratieve eenheden wordt niet aanzienlijk gewijzigd gedurende een korte periode.

## <a name="currently-supported-scenarios"></a>Momenteel ondersteunde scenario's

Globale beheerders of beheerders van bevoegde rollen kunnen de Azure AD-Portal gebruiken om beheer eenheden te maken, gebruikers als leden van beheer eenheden toe te voegen en vervolgens IT-mede werkers toe te wijzen aan beheerders rollen met beheer eenheden. De beheerders van de beheer eenheid kunnen vervolgens de Office 365-Portal gebruiken voor het basis beheer van gebruikers in hun beheer eenheden.

Daarnaast kunnen groepen worden toegevoegd als leden van de beheer eenheid en een groeps beheerder met beheerders eenheden die ze kunnen beheren met Power shell, het Microsoft Graph en de Azure AD-Portal.

In de onderstaande tabel wordt de huidige ondersteuning voor beheer-eenheids scenario's beschreven.

### <a name="administrative-unit-management"></a>Beheer van administratieve eenheden

Machtigingen |   MS Graph/Power shell   | Azure AD-Portal | Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
Beheer eenheden maken en verwijderen   |    Ondersteund    |   Ondersteund   |    Niet ondersteund
Leden van de beheer eenheid afzonderlijk toevoegen en verwijderen    |   Ondersteund    |   Ondersteund   |    Niet ondersteund
Leden van beheer eenheden bulksgewijs toevoegen en verwijderen met het CSV-bestand   |    Niet ondersteund     |  Ondersteund   |    Geen abonnement om te ondersteunen
Beheerders met beheerders rechten toewijzen  |     Ondersteund    |   Ondersteund    |   Niet ondersteund
Automatisch AU-leden toevoegen en verwijderen op basis van kenmerken | Niet ondersteund | Niet ondersteund | Niet ondersteund

### <a name="user-management"></a>Gebruikersbeheer

Machtigingen |   MS Graph/Power shell   | Azure AD-Portal | Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
beheer van beheer eenheden van gebruikers eigenschappen, wacht woorden, licenties   |    Ondersteund     |  Ondersteund   |   Ondersteund
beheer-en blok kering van gebruikers aanmeldingen op beheerders eenheid    |   Ondersteund   |    Ondersteund   |    Ondersteund
beheer van de gebruiker MFA-referenties met beheer eenheden   |    Ondersteund   |   Ondersteund   |   Niet ondersteund

### <a name="group-management"></a>Groepsbeheer

Machtigingen |   MS Graph/Power shell   | Azure AD-Portal | Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
beheer van beheer eenheden van groeps eigenschappen en leden     |  Ondersteund   |    Ondersteund    |  Niet ondersteund
beheer van beheer eenheden op groeps licenties   |    Ondersteund  |    Ondersteund   |   Niet ondersteund

> [!NOTE]
>
> Beheerders met een bereik van een beheer eenheid kunnen geen regels voor dynamische groepslid maatschappen beheren.

Beheer eenheden hebben alleen betrekking op beheer machtigingen. Ze verhinderen niet dat leden of beheerders hun [standaard gebruikers machtigingen](../fundamentals/users-default-permissions.md) kunnen gebruiken om te bladeren door andere gebruikers, groepen of bronnen buiten de administratieve eenheid. In de Office 365-Portal worden gebruikers buiten de administratieve eenheden van een bereik beheerder gefilterd, maar u kunt door andere gebruikers bladeren in de Azure AD-Portal, Power shell en andere micro soft-Services.

## <a name="next-steps"></a>Volgende stappen

- [AUs beheren](roles-admin-units-manage.md)
- [Gebruikers beheren in AUs](roles-admin-units-add-manage-users.md)
- [Groepen beheren in AUs](roles-admin-units-add-manage-groups.md)
- [Scoped rollen toewijzen aan een AU](roles-admin-units-assign-roles.md)
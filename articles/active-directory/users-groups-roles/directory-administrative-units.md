---
title: Beheereenheden in Azure Active Directory | Microsoft Docs
description: Beheereenheden gebruiken voor een meer gedetailleerde delegatie van machtigingen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: overview
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 09/22/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7aa356c806b6b96fa6a22f8642b005b2002f7c2a
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91447897"
---
# <a name="administrative-units-in-azure-active-directory"></a>Beheereenheden in Azure Active Directory

In dit artikel worden beheereenheden in Azure Active Directory (Azure AD) beschreven. Een beheereenheid is een Azure AD-resource die een container kan zijn voor andere Azure AD-resources. Een beheereenheid kan alleen gebruikers en groepen bevatten.

Met beheereenheden kunt u beheerdersmachtigingen verlenen die zijn beperkt tot een afdeling, een regio of een ander door u gedefinieerd segment van uw organisatie. U kunt beheereenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om een beleid op een gedetailleerd niveau in te stellen. Beheerders van gebruikersaccounts kunnen bijvoorbeeld profielgegevens bijwerken, wachtwoorden opnieuw instellen en licenties toewijzen voor uitsluitend gebruikers die in hun beheereenheid zijn opgenomen.

Wanneer u bijvoorbeeld de rol [Helpdeskbeheerder](directory-assign-admin-roles.md#helpdesk-administrator) delegeert aan regionale ondersteuningsspecialisten, is dat beperkt tot het beheren van uitsluitend de gebruikers in de regio die ze ondersteunen.

## <a name="deployment-scenario"></a>Implementatiescenario

Het beperken van het beheerbereik met behulp van beheereenheden kan nuttig zijn in organisaties die bestaan uit onafhankelijke divisies van welke aard dan ook. Neem bijvoorbeeld een grote universiteit die bestaat uit een groot aantal zelfstandige faculteiten (Bedrijfskunde, Technische wetenschappen, enzovoort), die elk over een team IT-beheerders beschikken die de toegang regelen, gebruikers beheren en beleidsregels voor hun school instellen. Een centrale beheerder kan het volgende doen:

- Een rol maken met beheerdersmachtigingen die betrekking hebben op uitsluitend Azure AD-gebruikers in de beheereenheid voor de faculteit Bedrijfskunde
- Een beheereenheid maken voor de faculteit Bedrijfskunde
- De beheereenheid vullen met uitsluitend de studenten en medewerkers van de faculteit Bedrijfskunde
- Het IT-team van de faculteit Bedrijfskunde toevoegen aan de rol met hun bereik

## <a name="license-requirements"></a>Licentievereisten

Voor het gebruik van beheereenheden is een Azure Active Directory Premium-licentie vereist voor elke beheerder van een beheereenheid en Azure Active Directory Free-licenties voor leden van de beheereenheid. Zie [Aan de slag met Azure AD Premium](../fundamentals/active-directory-get-started-premium.md) voor meer informatie.

## <a name="manage-administrative-units"></a>Beheereenheden beheren

U kunt beheereenheden beheren met behulp van Azure Portal, PowerShell-cmdlets en -scripts of Microsoft Graph. U kunt onze documentatie raadplegen voor meer informatie:

- [Rollen maken, verwijderen, vullen en toevoegen aan beheereenheden](roles-admin-units-manage.md): Instructieprocedures uitvoeren
- [Werken met beheereenheden](/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0&preserve-view=true): Werken met beheereenheden met behulp van PowerShell
- [Graph-ondersteuning voor beheereenheden](/graph/api/resources/administrativeunit?view=graph-rest-1.0&preserve-view=true): Gedetailleerde documentatie over Microsoft Graph voor beheereenheden.

### <a name="planning-your-administrative-units"></a>Uw beheereenheden plannen

Beheereenheden kunnen worden gebruikt voor het logisch groeperen van Azure AD-resources. Het kan bijvoorbeeld voor een organisatie waarvan de IT-afdeling over de hele wereld is verspreid zinvol zijn om beheereenheden te maken waarmee deze geografische grenzen worden gedefinieerd. In een ander scenario waarin een multinationale organisatie verschillende suborganisaties heeft die semi-autonoom hun activiteiten uitvoeren, kan voor elke suborganisatie een beheereenheid worden gebruikt.

De criteria voor het maken van beheereenheden worden bepaald door de unieke vereisten van een organisatie. Beheereenheden zijn een veelgebruikte manier om structuur aan te brengen in Microsoft 365-services. Het wordt aanbevolen om bij het voorbereiden van uw beheereenheden rekening te houden met het gebruik ervan in Microsoft 365-services. U kunt beheereenheden optimaal benutten wanneer u veelgebruikte resources in Microsoft 365 kunt koppelen in een beheereenheid.

Het maken van beheereenheden in de organisatie doorloopt doorgaans de volgende fasen:

1. Eerste aanname: Uw organisatie begint met het maken van beheereenheden op basis van aanvankelijke criteria en het aantal beheereenheden zal toenemen naarmate de criteria worden verfijnd.
1. Snoeien: Wanneer de criteria goed zijn gedefinieerd, worden beheereenheden verwijderd die niet meer nodig zijn.
1. Stabilisatie: De structuur van uw organisatie is duidelijk gedefinieerd en het aantal beheereenheden zal op korte termijn niet beduidend veranderen.

## <a name="currently-supported-scenarios"></a>Scenario's die momenteel worden ondersteund

Globale beheerders of beheerders voor bevoorrechte rollen kunnen de Azure AD-portal gebruiken om beheereenheden te maken, gebruikers als leden van beheereenheden toe te voegen en vervolgens IT-medewerkers toe te wijzen aan beheerdersrollen voor specifieke beheereenheden. De beheerders van een specifieke beheereenheid kunnen vervolgens het Microsoft 365-beheercentrum gebruiken voor het basisbeheer van gebruikers in hun beheereenheden.

Daarnaast kunnen groepen worden toegevoegd als leden van de beheereenheid en een groepsbeheerder voor een specifieke beheereenheid kan ze beheren met PowerShell, Microsoft Graph en de Azure AD-portal.

In de onderstaande tabel wordt de huidige ondersteuning voor scenario's met beheereenheden beschreven.

### <a name="administrative-unit-management"></a>Het beheren van beheereenheden

Machtigingen |   MS Graph/PowerShell   | Azure AD-portal | Het Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
Beheereenheden maken en verwijderen   |    Ondersteund    |   Ondersteund   |    Niet ondersteund
Leden van een beheereenheid afzonderlijk toevoegen en verwijderen    |   Ondersteund    |   Ondersteund   |    Niet ondersteund
Leden van een beheereenheid bulksgewijs toevoegen en verwijderen met een CSV-bestand   |    Niet ondersteund     |  Ondersteund   |    Geen abonnement die dit ondersteunt
Beheerders voor een specifieke beheereenheid toewijzen  |     Ondersteund    |   Ondersteund    |   Niet ondersteund
Leden voor een beheereenheid dynamisch toevoegen en verwijderen op basis van kenmerken | Niet ondersteund | Niet ondersteund | Niet ondersteund

### <a name="user-management"></a>Gebruikersbeheer

Machtigingen |   MS Graph/PowerShell   | Azure AD-portal | Het Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
beheer van gebruikerseigenschappen, wachtwoorden en licenties voor een specifieke beheereenheid   |    Ondersteund     |  Ondersteund   |   Ondersteund
blokkeren en deblokkeren van gebruikersaanmeldingen voor een specifieke beheereenheid    |   Ondersteund   |    Ondersteund   |    Ondersteund
beheer van MFA-referenties van gebruikers voor een specifieke beheereenheid   |    Ondersteund   |   Ondersteund   |   Niet ondersteund

### <a name="group-management"></a>Groepsbeheer

Machtigingen |   MS Graph/PowerShell   | Azure AD-portal | Het Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
beheer van groepseigenschappen en -leden voor een specifieke beheereenheid     |  Ondersteund   |    Ondersteund    |  Niet ondersteund
beheer van groepslicenties voor een specifieke beheereenheid   |    Ondersteund  |    Ondersteund   |   Niet ondersteund


Het bereik van beheereenheden is alleen van toepassing op beheermachtigingen. Ze verhinderen niet dat leden of beheerders hun [standaardgebruikersmachtigingen](../fundamentals/users-default-permissions.md) gebruiken om door andere gebruikers, groepen of resources buiten de beheereenheid te bladeren. In het Microsoft 365-beheercentrum worden gebruikers buiten de beheereenheden van een beheerder met een bereik eruit gefilterd, maar u kunt door andere gebruikers bladeren in de Azure AD-portal, PowerShell en andere Microsoft-services.

## <a name="next-steps"></a>Volgende stappen

- [Beheereenheden beheren](roles-admin-units-manage.md)
- [Gebruikers beheren in beheereenheden](roles-admin-units-add-manage-users.md)
- [Groepen beheren in beheereenheden](roles-admin-units-add-manage-groups.md)
- [Rollen met een bereik toewijzen aan een beheereenheid](roles-admin-units-assign-roles.md)

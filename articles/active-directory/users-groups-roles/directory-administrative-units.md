---
title: Beheer van beheer van beheereenheden (voorbeeld) - Azure AD | Microsoft Documenten
description: Beheereenheden gebruiken voor meer gedetailleerde overdracht van machtigingen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 04/16/2020
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 12101b170066bc52e75f4aa281c6f1c21283c004
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406467"
---
# <a name="administrative-units-management-in-azure-active-directory-preview"></a>Beheer van beheer van beheereenheden in Azure Active Directory (voorbeeld)

In dit artikel worden beheereenheden beschreven in Azure Active Directory (Azure AD). Een administratieve eenheid is een Azure AD-bron die een container kan zijn voor andere Azure AD-resources. In deze preview-versie kan een administratieve eenheid alleen gebruikers en groepen bevatten.

Met beheereenheden u beheerdersmachtigingen verlenen die beperkt zijn tot een afdeling, regio of ander segment van uw organisatie dat u definieert. U beheereenheden gebruiken om machtigingen te delegeren aan regionale beheerders of om beleid op een gedetailleerd niveau in te stellen. Een gebruikersaccountbeheerder kan bijvoorbeeld profielgegevens bijwerken, wachtwoorden opnieuw instellen en licenties toewijzen voor gebruikers alleen in hun administratieve eenheid.

 Bijvoorbeeld, delegeren aan regionale ondersteuningsspecialisten de [helpdesk beheerder](directory-assign-admin-roles.md#helpdesk-administrator) rol beperkt tot het beheer van alleen de gebruikers in de regio die zij ondersteunen.

## <a name="deployment-scenario"></a>Implementatiescenario

Het beperken van de administratieve reikwijdte met behulp van administratieve eenheden kan nuttig zijn in organisaties die bestaan uit onafhankelijke afdelingen van welke aard dan ook. Denk aan het voorbeeld van een grote universiteit die bestaat uit veel autonome scholen (School of Business, School of Engineering, enzovoort) die elk een team van IT-beheerders hebben die de toegang beheren, gebruikers beheren en beleid instellen voor hun school. Een centrale beheerder kan:

- Een rol maken met beheerdersmachtigingen voor alleen Azure AD-gebruikers in de beheereenheid van de business school
- Een administratieve eenheid voor de School of Business maken
- Bevolk de beheereenheid met alleen de business schoolstudenten en -medewerkers
- Voeg het IT-team van de Business school toe aan de rol met hun scope

## <a name="license-requirements"></a>Licentievereisten

Voor het gebruik van administratieve eenheden is een Azure Active Directory Premium-licentie vereist voor elke beheerder van de beheereenheid en Azure Active Directory Free-licenties voor leden van de beheerderseenheid. Zie [Aan de slag met Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)voor meer informatie.

## <a name="manage-administrative-units"></a>Beheereenheden beheren

In deze preview-versie u beheereenheden beheren met behulp van de Azure-portal, PowerShell-cmdlets en -scripts of de Microsoft Graph. U onze documentatie voor meer informatie bekijken:

- [Rollen maken, verwijderen, invullen en toevoegen aan administratieve eenheden:](roles-admin-units-manage.md)Procedures voltooien
- [Werken met beheereenheden:](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)werken met administratieve eenheden met PowerShell
- [Ondersteuning voor administratieve eenheidsgrafiek:](https://docs.microsoft.com/graph/api/resources/administrativeunit?view=graph-rest-beta)gedetailleerde documentatie over Microsoft Graph voor administratieve eenheden.

### <a name="planning-your-administrative-units"></a>Plannen van uw administratieve eenheden

Beheereenheden kunnen worden gebruikt om Azure AD-resources logisch te groeperen. Voor een organisatie waarvan de IT-afdeling wereldwijd is verspreid, is het bijvoorbeeld zinvol om administratieve eenheden te maken die deze geografische grenzen definiëren. In een ander scenario waarin een multinationale organisatie verschillende "suborganisaties" heeft, die semi-autonoom zijn in operaties, kan elke suborganisatie worden vertegenwoordigd door een administratieve eenheid.

De criteria waarop administratieve eenheden worden gemaakt, worden bepaald door de unieke vereisten van een organisatie. Administratieve eenheden zijn een veelvoorkomende manier om structuur te definiëren voor M365-services. We raden u aan uw administratieve eenheden voor te bereiden met het gebruik ervan in M365-services in het achterhoofd. U maximale waarde halen uit administratieve eenheden wanneer u gemeenschappelijke resources in M365 koppelen onder een administratieve eenheid.

U verwachten dat het maken van administratieve eenheden in de organisatie de volgende fasen doorloopt:

1. Eerste adoptie: Uw organisatie zal beginnen met het maken van administratieve eenheden op basis van initiële criteria en het aantal administratieve eenheden zal toenemen naarmate de criteria worden verfijnd.
1. Snoeien: Zodra de criteria goed zijn gedefinieerd, worden administratieve eenheden die niet meer nodig zijn, verwijderd.
1. Stabilisatie: Uw organisatiestructuur is goed gedefinieerd en het aantal administratieve eenheden zal over korte duur niet significant veranderen.

## <a name="currently-supported-scenarios"></a>Momenteel ondersteunde scenario's

Globale beheerders of bevoorrechte rolbeheerders kunnen de Azure AD-portal gebruiken om beheereenheden te maken, gebruikers toe te voegen als leden van beheerderseenheden en vervolgens IT-personeel toe te wijzen aan beheerdersrollen met een beheereenheid. De beheerders met een beheereenheid kunnen vervolgens de Office 365-portal gebruiken voor basisbeheer van gebruikers in hun administratieve eenheden.

Daarnaast kunnen groepen worden toegevoegd als leden van de administratieve eenheid en kan een groepsbeheerder met een beheereenheid deze beheren met PowerShell, de Microsoft Graph en de Azure AD-portal.

In de onderstaande tabel wordt de huidige ondersteuning voor scenario's voor administratieve eenheden beschreven.

### <a name="administrative-unit-management"></a>Administratief beheer van de eenheid

Machtigingen |   MS-grafiek/PowerShell   | Azure AD-portal | Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
Administratieve eenheden maken en verwijderen   |    Ondersteund    |   Ondersteund   |    Niet ondersteund
Individueel toevoegen en verwijderen van leden van de administratieve eenheid    |   Ondersteund    |   Ondersteund   |    Niet ondersteund
Bulk die leden van administratieve eenheden toevoegt en verwijdert met csv-bestand   |    Niet ondersteund     |  Ondersteund   |    Geen plan om te ondersteunen
Beheerders van administratieve eenheden toewijzen  |     Ondersteund    |   Ondersteund    |   Niet ondersteund
AU-leden dynamisch toevoegen en verwijderen op basis van kenmerken | Niet ondersteund | Niet ondersteund | Niet ondersteund

### <a name="user-management"></a>Gebruikersbeheer

Machtigingen |   MS-grafiek/PowerShell   | Azure AD-portal | Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
beheer van gebruikerseigenschappen, wachtwoorden, licenties met een beheereenheid   |    Ondersteund     |  Ondersteund   |   Ondersteund
blokkeren en deblokkeren van aanmeldingen van gebruikers met een administratieve eenheid    |   Ondersteund   |    Ondersteund   |    Ondersteund
beheer van mfa-referenties met administratieve eenheid   |    Ondersteund   |   Ondersteund   |   Niet ondersteund

### <a name="group-management"></a>Groepsbeheer

Machtigingen |   MS-grafiek/PowerShell   | Azure AD-portal | Microsoft 365-beheercentrum
----------- | ----------------------- | --------------- | -----------------
administratief beheer van groepseigenschappen en leden met eenheidsscoped     |  Ondersteund   |    Ondersteund    |  Niet ondersteund
administratief beheer van groepslicenties met eenheidsscoped   |    Ondersteund  |    Ondersteund   |   Niet ondersteund

> [!NOTE]
>
> Beheerders met een beheereenheid kunnen de regels voor dynamisch groepslidmaatschap niet beheren.

Administratieve eenheden passen scope alleen toe op beheermachtigingen. Ze voorkomen niet dat leden of beheerders hun [standaardgebruikersmachtigingen](../fundamentals/users-default-permissions.md) gebruiken om door andere gebruikers, groepen of bronnen buiten de beheereenheid te bladeren. In de Office 365-portal worden gebruikers buiten de beheereenheden van een beheerder van een scope d-ruimte uitgefilterd, maar u door andere gebruikers bladeren in de Azure AD-portal, PowerShell en andere Microsoft-services.

## <a name="next-steps"></a>Volgende stappen

- [AUS beheren](roles-admin-units-manage.md)
- [Gebruikers beheren in AU's](roles-admin-units-add-manage-users.md)
- [Groepen beheren in AU's](roles-admin-units-add-manage-groups.md)
- [Scoped rollen toewijzen aan een AU](roles-admin-units-assign-roles.md)
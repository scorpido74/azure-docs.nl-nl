---
title: Problemen met administratieve eenheden en veelgestelde vragen oplossen - Azure Active Directory | Microsoft Documenten
description: Beheereenheden onderzoeken om machtigingen met een beperkt bereik in Azure Active Directory af te geven
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
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5c9770b60edad6f956ae99c91b94a232cc48bbbd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428145"
---
# <a name="troubleshooting-and-faq-for-administrative-units-in-azure-active-directory"></a>Probleemoplossing en veelgestelde vragen voor beheereenheden in Azure Active Directory

Voor meer gedetailleerd beheerbeheerbeheer in Azure Active Directory (Azure AD) u gebruikers toewijzen aan een Azure AD-rol met een bereik dat beperkt is tot een of meer beheereenheden (AU's). U voorbeeld PowerShell-scripts https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0voor veelvoorkomende taken vinden op.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Ik kan geen administratieve eenheid maken**

**A:** Alleen een globale beheerder of bevoegde rolbeheerder kan een beheereenheid maken in Azure AD. Controleer of de gebruiker die de beheereenheid probeert te maken, de rol Globale beheerder of bevoorrechte rolbeheerder toegewezen krijgt.

**V: Ik heb een groep toegevoegd aan de administratieve eenheid, maar de leden van de groep komen nog steeds niet opdagen in de administratieve eenheid**

**A:** Wanneer u een groep toevoegt aan de administratieve eenheid, leidt dat niet tot het toevoegen van alle leden van de groep aan de administratieve eenheid. Gebruikers moeten rechtstreeks worden toegewezen aan de administratieve eenheid.

**V: Ik heb net toegevoegd / verwijderd een lid van de administratieve eenheid en het is nog steeds weergegeven in de UI**

**A:** Soms kan het enkele minuten duren voordat de verwerking van de toevoeging of verwijdering van een of meer leden van de administratieve eenheid wordt weergegeven onder de pagina **Administratieve eenheden.** U ervoor kiezen om te wachten voor een paar minuten voor het te reflecteren onder de administratieve eenheden. U ook rechtstreeks naar de eigenschappen van de gekoppelde resource gaan en zien of de actie is voltooid. Zie [Beheerderseenheden voor een gebruiker](roles-admin-units-add-manage-users.md) en [Lijst-beheereenheden voor een groep voor](roles-admin-units-add-manage-groups.md)meer informatie over gebruikers en groepen in AU's.

**V: Als gedelegeerde wachtwoordbeheerder op een administratieve eenheid kan ik het wachtwoord van een specifieke gebruiker niet opnieuw instellen**

**A:** Een beheerder die is toegewezen aan een administratieve eenheid, u het wachtwoord alleen opnieuw instellen voor gebruikers die aan uw beheereenheid zijn toegewezen. Zorg ervoor dat de gebruiker waarvoor het wachtwoord opnieuw wordt ingesteld, deel uitmaakt van de administratieve eenheden waarover u de rol hebt toegewezen. Als de gebruiker tot dezelfde administratieve eenheid behoort en u het wachtwoord van de gebruiker nog steeds niet opnieuw instellen, controleert u de rollen die aan de gebruiker zijn toegewezen. Om een verhoging van bevoegdheden te voorkomen, kan een beheerder met een beheereenheid het wachtwoord van een gebruiker die is toegewezen aan een rol met een organisatiebreed bereik, niet opnieuw instellen.

**V: Waarom zijn administratieve eenheden nodig? Hadden we geen beveiligingsgroepen kunnen gebruiken om een bereik te definiëren?**

**A:** Beveiligingsgroepen hebben een bestaand doel en autorisatiemodel. Een gebruikersbeheerder kan bijvoorbeeld het lidmaatschap van alle beveiligingsgroepen in de Azure AD-organisatie beheren, bijvoorbeeld om groepen te gebruiken om toegang tot toepassingen zoals Salesforce te beheren. Een gebruikersbeheerder mag niet de mogelijkheid hebben om het delegatiemodel zelf te beheren, wat het resultaat zou zijn als beveiligingsgroepen worden uitgebreid om 'resourcegroepering'-scenario's te ondersteunen. Administratieve eenheden, zoals organisatie-eenheden in Windows Server Active Directory, zijn bedoeld om een manier te bieden om het beheer van een breed scala aan directoryobjecten te beheren. Beveiligingsgroepen zelf kunnen lid zijn van resourcescopes. Het gebruik van beveiligingsgroepen om de set beveiligingsgroepen te definiëren die een beheerder kan beheren, kan verwarrend worden.

**V: Wat betekent het om een groep toe te voegen aan een administratieve eenheid?**

**A:** Als u een groep toevoegt aan een administratieve eenheid, wordt de groep zelf opgenomen in het beheerbereik van elke gebruikersbeheerder die ook aan die beheereenheid is verbonden. Gebruikersbeheerders voor de administratieve eenheid kunnen de naam en het lidmaatschap van de groep zelf beheren. De beheerder van de gebruiker voor de administratieve eenheid geeft geen toestemming om de gebruikers van de groep te beheren (bijvoorbeeld hun wachtwoorden opnieuw instellen). Om de gebruikersbeheerder de mogelijkheid te geven gebruikers te beheren, moeten de gebruikers directe leden van de administratieve eenheid zijn.

**V: Kan een resource (gebruiker of groep) lid zijn van meer dan één administratieve eenheid?**

**A:** Ja, een resource kan lid zijn van meer dan één administratieve eenheid. De resource kan worden beheerd door alle beheerders met een hele organisatie en beheerders met een beheerd beheersysteem die machtigingen hebben voor de resource.

**V: Zijn administratieve eenheden beschikbaar in B2C-organisaties?**

**A:** Nee, administratieve eenheden zijn niet beschikbaar voor B2C-organisaties.

**V: Worden geneste administratieve eenheden ondersteund?**

**A:** Geneste administratieve eenheden worden niet ondersteund.

**V: Worden administratieve eenheden ondersteund in PowerShell en Graph API?**

**A:** Ja. Ondersteuning voor administratieve eenheden bestaat in [PowerShell-cmdlet-documentatie](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) en [voorbeeldscripts](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview)en ondersteuning staat in de Microsoft Graph voor het [resourcetype administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit).

## <a name="next-steps"></a>Volgende stappen

- [Administratieve eenheden om de ruimte voor rollenoverzicht te beperken](directory-administrative-units.md)
- [Beheereenheden beheren](roles-admin-units-manage.md)
---
title: Probleemoplossing voor beheereenheden en veelgestelde vragen - Azure Active Directory | Microsoft Documenten
description: Beheereenheden onderzoeken om machtigingen met een beperkt bereik in Azure Active Directory toe te kennen.
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
ms.openlocfilehash: 022658306d6e4d69174cc616d230cfe4892f1204
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684861"
---
# <a name="azure-ad-administrative-units-troubleshooting-and-faq"></a>Azure AD-beheereenheden: probleemoplossing en veelgestelde vragen

Voor meer gedetailleerd beheerbeheerbeheer in Azure Active Directory (Azure AD) u gebruikers toewijzen aan een Azure AD-rol met een bereik dat is beperkt tot een of meer beheereenheden (AU's). Zie [Werken met administratieve eenheden](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0)voor voorbeeldpowershell-scripts voor veelvoorkomende taken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

**V: Waarom kan ik geen administratieve eenheid maken?**

**A:** Alleen een *globale beheerder* of *bevoegde rolbeheerder* kan een beheereenheid maken in Azure AD. Controleer of de gebruiker die de beheereenheid probeert te maken, de rol *Globale beheerder* of *bevoorrechte rolbeheerder* toegewezen krijgt.

**V: Ik heb een groep toegevoegd aan de administratieve eenheid. Waarom komen de groepsleden daar nog steeds niet opdagen?**

**A:** Wanneer u een groep toevoegt aan de administratieve eenheid, leidt dat er niet toe dat alle leden van de groep eraan worden toegevoegd. Gebruikers moeten rechtstreeks aan de administratieve eenheid worden toegewezen.

**V: Ik heb net een lid van de administratieve eenheid toegevoegd (of verwijderd). Waarom wordt het lid niet weergegeven (of nog steeds weergegeven) in de gebruikersinterface?**

**A:** Soms kan het enkele minuten duren voordat de verwerking van de toevoeging of verwijdering van een of meer leden van de administratieve eenheid wordt weergegeven op de pagina **Administratieve eenheden.** U ook rechtstreeks naar de eigenschappen van de gekoppelde resource gaan en zien of de actie is voltooid. Zie [Beheerderseenheden voor een gebruiker](roles-admin-units-add-manage-users.md) en [Lijst-beheereenheden voor een groep voor](roles-admin-units-add-manage-groups.md)meer informatie over gebruikers en groepen in AU's.

**V: Ik ben een gedelegeerde wachtwoordbeheerder op een administratieve eenheid. Waarom kan ik het wachtwoord van een specifieke gebruiker niet opnieuw instellen?**

**A:** Als beheerder van een administratieve eenheid u wachtwoorden alleen opnieuw instellen voor gebruikers die zijn toegewezen aan uw administratieve eenheid. Zorg ervoor dat de gebruiker wiens wachtwoord reset mislukt behoort tot de administratieve eenheid waaraan u bent toegewezen. Als de gebruiker tot dezelfde administratieve eenheid behoort, maar u zijn wachtwoord nog steeds niet opnieuw instellen, controleert u de rollen die aan de gebruiker zijn toegewezen. 

Om een verhoging van bevoegdheden te voorkomen, kan een beheerder met een beheereenheid het wachtwoord van een gebruiker die is toegewezen aan een rol met een organisatiebreed bereik, niet opnieuw instellen.

**V: Waarom zijn administratieve eenheden nodig? Hadden we geen beveiligingsgroepen kunnen gebruiken om een bereik te definiëren?**

**A:** Beveiligingsgroepen hebben een bestaand doel en autorisatiemodel. Een *gebruikersbeheerder*kan bijvoorbeeld het lidmaatschap van alle beveiligingsgroepen in de Azure AD-organisatie beheren. De rol kan groepen gebruiken om toegang tot toepassingen zoals Salesforce te beheren. Een *gebruikersbeheerder* mag het delegatiemodel niet zelf kunnen beheren, wat het resultaat zou zijn als beveiligingsgroepen worden uitgebreid om 'resourcegroepering'-scenario's te ondersteunen. Beheerderseenheden, zoals organisatie-eenheden in Windows Server Active Directory, zijn bedoeld om een manier te bieden om het beheer van een breed scala aan directoryobjecten te beheren. Beveiligingsgroepen zelf kunnen lid zijn van resourcescopes. Het gebruik van beveiligingsgroepen om de set beveiligingsgroepen te definiëren die een beheerder kan beheren, kan verwarrend worden.

**V: Wat betekent het om een groep toe te voegen aan een administratieve eenheid?**

**A:** Als u een groep toevoegt aan een administratieve eenheid, wordt de groep zelf opgenomen in het beheerbereik van elke *gebruikersbeheerder* die ook aan die administratieve eenheid is verbonden. Gebruikersbeheerders voor de administratieve eenheid kunnen de naam en het lidmaatschap van de groep zelf beheren. De *gebruikersbeheerder* verleent geen machtigingen voor de beheerderseenheid om de gebruikers van de groep te beheren (bijvoorbeeld om hun wachtwoorden opnieuw in te stellen). Om de *gebruikersbeheerder* de mogelijkheid te geven gebruikers te beheren, moeten de gebruikers directe leden van de administratieve eenheid zijn.

**V: Kan een resource (gebruiker of groep) lid zijn van meer dan één administratieve eenheid?**

**A:** Ja, een resource kan lid zijn van meer dan één administratieve eenheid. De resource kan worden beheerd door alle beheerders met een hele organisatie en beheerders met een beheerd beheersysteem die machtigingen hebben voor de resource.

**V: Zijn administratieve eenheden beschikbaar in B2C-organisaties?**

**A:** Nee, administratieve eenheden zijn niet beschikbaar voor B2C-organisaties.

**V: Worden geneste administratieve eenheden ondersteund?**

**A:** Nee, geneste administratieve eenheden worden niet ondersteund.

**V: Worden administratieve eenheden ondersteund in PowerShell en de Graph API?**

**A:** Ja. U vindt ondersteuning voor administratieve eenheden in [PowerShell-cmdlet-documentatie](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0-preview) en [voorbeeldscripts.](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0-preview) 

Ondersteuning zoeken voor het [resourcetype administrativeUnit](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/administrativeunit) in Microsoft Graph.

## <a name="next-steps"></a>Volgende stappen

- [Bereik voor rollen beperken met behulp van administratieve eenheden](directory-administrative-units.md)
- [Beheereenheden beheren](roles-admin-units-manage.md)

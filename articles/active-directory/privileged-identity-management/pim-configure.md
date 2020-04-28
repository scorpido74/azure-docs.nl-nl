---
title: Wat is Privileged Identity Management? - Azure AD | Microsoft Documenten
description: Biedt een overzicht van Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: pim
ms.topic: overview
ms.date: 04/21/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: bfb0cc61b61328df86c27498a1007f2372fb9548
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81867449"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Wat is Azure AD Privileged Identity Management?

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) is een service waarmee u toegang tot belangrijke resources in uw organisatie kunt beheren, controleren en bewaken. Deze bronnen omvatten bronnen in Azure AD, Azure en andere Microsoft Online Services zoals Office 365 of Microsoft Intune.

## <a name="reasons-to-use"></a>Redenen om te gebruiken

Organisaties willen het aantal mensen dat toegang heeft tot beveiligde gegevens of resources beperken, om zo de kans te verkleinen dat een kwaadwillende gebruiker toegang verkrijgt of een geautoriseerde gebruiker per ongeluk een gevoelige resource wijzigt. Gebruikers moeten echter wel bevoorrechte bewerkingen kunnen uitvoeren in Azure AD, Azure, Office 365 of SaaS-apps. Organisaties kunnen gebruikers daarom bevoorrechte JIT-toegang (Just-In-Time) geven tot Azure-resources en Azure Active Directory. Het is belangrijk dat er toezicht is op wat gebruikers met hun beheerdersbevoegdheden doen.

## <a name="what-does-it-do"></a>Wat doet het?

Privileged Identity Management biedt op tijd gebaseerde en op goedkeuring gebaseerde functieactivering om de risico's van overmatige, onnodige of misbruikte toegangsmachtigingen voor resources die u belangrijk vindt, te beperken. Hier volgen enkele van de belangrijkste kenmerken van Privileged Identity Management:

- Bevoorrechte **JIT**-toegang (Just-In-Time) bieden aan Azure Active Directory- en Azure-resources
- **Tijdsgebonden** toegang bieden aan resources met behulp van begin- en einddatums
- **Goedkeuring** vereisen om bevoorrechte rollen te activeren
- **Meervoudige verificatie** afdwingen om een rol te activeren
- Gebruikmaken van **redenen** om te begrijpen waarom gebruikers activeren
- **Meldingen** ontvangen wanneer bevoorrechte rollen zijn geactiveerd
- **Toegangsbeoordelingen** uitvoeren om te controleren of gebruikers rollen nog steeds nodig hebben
- **Controlegeschiedenis** downloaden voor interne of externe controle

## <a name="what-can-i-do-with-it"></a>Wat kan ik ermee?

Zodra u Privileged Identity Management hebt ingesteld, ziet u **taken,** **beheer**en **activiteitsopties** in het linkernavigatiemenu. Als beheerder kiest u tussen het beheren van **Azure Active Directory-rollen** en **Azure-resource**rollen. Als u het type te beheren rollen kiest, ziet u een soortgelijke set met opties voor dat roltype.

![Schermafbeelding van privileged identity management in de Azure-portal](./media/pim-configure/pim-quickstart.png)

## <a name="who-can-do-what"></a>Wie kan wat doen?

Voor Azure AD-rollen in Privileged Identity Management kan alleen een gebruiker die zich in de bevoegde rolbeheerder of globale beheerdersrol bevindt, toewijzingen voor andere beheerders beheren. U [toegang verlenen aan andere beheerders om privileged identity management te beheren.](pim-how-to-give-access-to-pim.md) Globale beheerders, beveiligingsbeheerders, wereldwijde lezers en beveiligingslezers kunnen ook toewijzingen voor Azure AD-rollen bekijken in Privileged Identity Management.

Voor Azure-bronrollen in Privileged Identity Management kunnen alleen een abonnementsbeheerder, een broneigenaar of een beheerder van gebruikerstoegang voor bronnen toewijzingen voor andere beheerders beheren. Gebruikers die privileged role administrators, security administrators of security readers zijn, hebben standaard geen toegang tot weergavetoewijzingen voor Azure-bronrollen in Privileged Identity Management.

## <a name="scenarios"></a>Scenario's

Privileged Identity Management ondersteunt de volgende scenario's:

### <a name="privileged-role-administrator-permissions"></a>Machtigingen voor bevoegde rolbeheerders

- Goedkeuring voor specifieke rollen inschakelen
- Gebruikers of groepen van goedgekeurde gebruikers opgeven om aanvragen goed te keuren
- De geschiedenis van aanvragen en goedkeuringen bekijken voor alle bevoorrechte rollen

### <a name="approver-permissions"></a>Machtigingen voor goedkeurder

- Goedkeuringen in behandeling (aanvragen) bekijken
- Aanvragen voor rolverhoging goedkeuren of weigeren (single en bulk)
- Geef rechtvaardiging voor mijn goedkeuring of afwijzing

### <a name="eligible-role-user-permissions"></a>Gebruikersmachtigingen voor in aanmerking komende rollen

- Activering van een rol waarvoor goedkeuring nodig is, aanvragen
- De status van uw aanvraag voor activeren bekijken
- Uw taak voltooien in Azure AD als de activering is goedgekeurd

## <a name="terminology"></a>Terminologie

Als u privileged identity management en de bijbehorende documentatie beter wilt begrijpen, moet u de volgende voorwaarden bekijken.

| Term of concept | Roltoewijzingscategorie | Beschrijving |
| --- | --- | --- |
| in aanmerking komend | Type | Een roltoewijzing die vereist dat een gebruiker een of meer acties uitvoert om de rol te kunnen gebruiken. Als een gebruiker in aanmerking komt voor een rol, betekent dit dat de gebruiker de rol kan activeren wanneer deze nodig is om bevoegde taken uit te voeren. Er is geen verschil in de toegang voor iemand met een permanente roltoewijzing ten opzichte van iemand die in aanmerking komt voor een roltoewijzing. Het enige verschil is dat sommige gebruikers niet voortdurend toegang nodig hebben. |
| actief | Type | Een roltoewijzing die niet vereist dat een gebruiker een actie uitvoert om de rol te kunnen gebruiken. Gebruikers die zijn toegewezen als actief zijn in het bezit van de bevoegdheden die zijn toegewezen aan de rol. |
| activeren |  | Het proces van het uitvoeren van een of meer acties om de rol te kunnen gebruiken waarvoor de gebruiker in aanmerking komt. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn. |
| toegewezen | Status | Een gebruiker met een actieve roltoewijzing. |
| geactiveerd | Status | Een gebruiker die in aanmerking komt voor een roltoewijzing, de acties voor het activeren van de rol heeft uitgevoerd en nu actief is.  Wanneer de rol is geactiveerd, kan de gebruiker deze gebruiken gedurende een vooraf geconfigureerde periode voordat ze de rol opnieuw moeten activeren. |
| permanent in aanmerking komend | Duur | Een roltoewijzing waarbij een gebruiker altijd in aanmerking komt om de rol te activeren. |
| permanent actief | Duur | Een roltoewijzing waarbij een gebruiker de rol altijd kan gebruiken zonder acties te hoeven uitvoeren. |
| verlopen - in aanmerking komend | Duur | Een roltoewijzing waarbij een gebruiker de rol binnen een opgegeven begin- en datum mag activeren. |
| verlopen - actief | Duur | Een roltoewijzing waarbij een gebruiker de rol kan gebruiken zonder acties te hoeven uitvoeren binnen een opgegeven begin- en einddatum. |
| Just-in-time-toegang (JIT) |  | Een model waarbij gebruikers tijdelijke machtigingen ontvangen om bepaalde taken uit te mogen voeren, waardoor kwaadwillende of onbevoegde gebruikers geen toegang kunnen krijgen na het verlopen van deze machtigingen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben. |
| Principe van toegang met minimale bevoegdheden |  | Een aanbevolen beveiligingsprocedure waarbij alle gebruikers enkel de minimale bevoegdheden krijgt toegewezen die nodig zijn om de taken uit te voeren waarvoor ze bevoegd zijn. Met deze procedure wordt het aantal globale beheerders tot het minimum beperkt en worden er specifieke beheerdersrollen gebruikt voor bepaalde scenario's. |

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Zie [Licentievereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)voor informatie over licenties voor gebruikers.

## <a name="next-steps"></a>Volgende stappen

- [Licentievereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)
- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management implementeren](pim-deployment-plan.md)

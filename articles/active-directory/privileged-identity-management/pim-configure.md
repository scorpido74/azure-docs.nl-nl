---
title: Wat is Privileged Identity Management? - Azure Active Directory | Microsoft Azure
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
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b204c3c30d935bc47adf76c3f0f50ccd7172393
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72554947"
---
# <a name="what-is-azure-ad-privileged-identity-management"></a>Wat is Azure AD Privileged Identity Management?

Azure Active Directory (Azure AD) Privileged Identity Management (PIM) is een service waarmee u toegang tot belangrijke resources in uw organisatie kunt beheren, controleren en bewaken. Dit is inclusief toegang tot resources in Azure AD, Azure-resources en andere Microsoft-onlineservices zoals Office 365 en Microsoft Intune.

## <a name="reasons-to-use"></a>Redenen om te gebruiken

Organisaties willen het aantal mensen dat toegang heeft tot beveiligde gegevens of resources beperken, om zo de kans te verkleinen dat een kwaadwillende gebruiker toegang verkrijgt of een geautoriseerde gebruiker per ongeluk een gevoelige resource wijzigt. Gebruikers moeten echter wel bevoorrechte bewerkingen kunnen uitvoeren in Azure AD, Azure, Office 365 of SaaS-apps. Organisaties kunnen gebruikers daarom bevoorrechte JIT-toegang (Just-In-Time) geven tot Azure-resources en Azure Active Directory. Het is belangrijk dat er toezicht is op wat gebruikers met hun beheerdersbevoegdheden doen. Privileged Identity Management helpt het risico op buitensporige, onnodige of ongebruikte toegangs rechten te verminderen.

## <a name="what-does-it-do"></a>Wat doet uw app?

Privileged Identity Management in feite helpt u bij het beheren van de wie, wat, waar, waar en waarom voor bronnen die u bevalt. Hier volgen enkele van de belangrijkste functies van Privileged Identity Management:

- Bevoorrechte **JIT**-toegang (Just-In-Time) bieden aan Azure Active Directory- en Azure-resources
- **Tijdsgebonden** toegang bieden aan resources met behulp van begin- en einddatums
- **Goedkeuring** vereisen om bevoorrechte rollen te activeren
- **Meervoudige verificatie** afdwingen om een rol te activeren
- Gebruikmaken van **redenen** om te begrijpen waarom gebruikers activeren
- **Meldingen** ontvangen wanneer bevoorrechte rollen zijn geactiveerd
- **Toegangsbeoordelingen** uitvoeren om te controleren of gebruikers rollen nog steeds nodig hebben
- **Controlegeschiedenis** downloaden voor interne of externe controle

## <a name="what-can-i-do-with-it"></a>Wat kan ik ermee doen?

Zodra u Privileged Identity Management hebt ingesteld, ziet u opties voor **taken**, **beheer**en **activiteiten** in het navigatie menu aan de linkerkant. Als beheerder kiest u tussen het beheren van **Azure Active Directory-rollen** en **Azure-resource**rollen. Als u het type te beheren rollen kiest, ziet u een soortgelijke set met opties voor dat roltype.

![Scherm opname van Privileged Identity Management in het Azure Portal](./media/pim-configure/pim-overview.png)

## <a name="who-can-do-what"></a>Wie kan er wat doen?

Als u de eerste persoon bent die Privileged Identity Management moet gebruiken, worden automatisch de rollen [beveiligings beheerder](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) en [beschermde rol beheerder](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) in de Directory toegewezen.

Voor Azure AD-rollen in Privileged Identity Management kan alleen een gebruiker met de rol bevoorrechte rol beheerder toewijzingen voor andere beheerders beheren. U kunt [andere beheerders toegang verlenen voor het beheren van privileged Identity Management](pim-how-to-give-access-to-pim.md). Globale beheerders, beveiligings beheerders, globale lezers en beveiligings lezers kunnen ook toewijzingen weer geven voor Azure AD-rollen in Privileged Identity Management.

Voor Azure-resource rollen in Privileged Identity Management kan alleen een abonnements beheerder, een resource-eigenaar of een resource gebruiker Access-beheerder toewijzingen voor andere beheerders beheren. Gebruikers die geprivilegieerde rol beheerders, beveiligings beheerders of beveiligings lezers zijn, hebben standaard geen toegang voor het weer geven van toewijzingen aan Azure-resource rollen in Privileged Identity Management.

## <a name="scenarios"></a>Scenario's

Privileged Identity Management ondersteunt de volgende scenario's:

### <a name="privileged-role-administrator-permissions"></a>Beheerders machtigingen voor geprivilegieerde rollen

- Goedkeuring voor specifieke rollen inschakelen
- Gebruikers of groepen voor goed keurder opgeven om aanvragen goed te keuren
- De geschiedenis van aanvragen en goedkeuringen bekijken voor alle bevoorrechte rollen

### <a name="approver-permissions"></a>Machtigingen voor goed keurder

- Goedkeuringen in behandeling (aanvragen) bekijken
- Aanvragen voor uitbrei ding van rol goed keuren of afwijzen (enkel en bulksgewijs)
- Geef een reden voor mijn goed keuring of weigering

### <a name="eligible-role-user-permissions"></a>Gebruikers machtigingen in aanmerking komende rollen

- Activering van een rol waarvoor goedkeuring nodig is, aanvragen
- De status van uw aanvraag voor activeren bekijken
- Uw taak voltooien in Azure AD als de activering is goedgekeurd

## <a name="terminology"></a>Terminologie

Raadpleeg de volgende voor waarden als u meer wilt weten over Privileged Identity Management en de bijbehorende documentatie.

| Term of concept | Roltoewijzingscategorie | Beschrijving |
| --- | --- | --- |
| in aanmerking komend | Type | Een roltoewijzing die vereist dat een gebruiker een of meer acties uitvoert om de rol te kunnen gebruiken. Als een gebruiker in aanmerking komt voor een rol, betekent dit dat de gebruiker de rol kan activeren wanneer deze nodig is om bevoegde taken uit te voeren. Er is geen verschil in de toegang voor iemand met een permanente roltoewijzing ten opzichte van iemand die in aanmerking komt voor een roltoewijzing. Het enige verschil is dat sommige gebruikers niet voortdurend toegang nodig hebben. |
| actief | Type | Een roltoewijzing die niet vereist dat een gebruiker een actie uitvoert om de rol te kunnen gebruiken. Gebruikers die zijn toegewezen als actief zijn in het bezit van de bevoegdheden die zijn toegewezen aan de rol. |
| activeren |  | Het proces van het uitvoeren van een of meer acties om de rol te kunnen gebruiken waarvoor de gebruiker in aanmerking komt. Acties kunnen bijvoorbeeld een meervoudige verificatiecontrole, het opgeven van een zakelijke reden of het vragen om toestemming bij aangewezen fiatteurs zijn. |
| toegewezen | Staat | Een gebruiker met een actieve roltoewijzing. |
| geactiveerd | Staat | Een gebruiker die in aanmerking komt voor een roltoewijzing, de acties voor het activeren van de rol heeft uitgevoerd en nu actief is.  Wanneer de rol is geactiveerd, kan de gebruiker deze gebruiken gedurende een vooraf geconfigureerde periode voordat ze de rol opnieuw moeten activeren. |
| permanent in aanmerking komend | Duur | Een roltoewijzing waarbij een gebruiker altijd in aanmerking komt om de rol te activeren. |
| permanent actief | Duur | Een roltoewijzing waarbij een gebruiker de rol altijd kan gebruiken zonder acties te hoeven uitvoeren. |
| verlopen - in aanmerking komend | Duur | Een roltoewijzing waarbij een gebruiker de rol binnen een opgegeven begin- en datum mag activeren. |
| verlopen - actief | Duur | Een roltoewijzing waarbij een gebruiker de rol kan gebruiken zonder acties te hoeven uitvoeren binnen een opgegeven begin- en einddatum. |
| Just-in-time-toegang (JIT) |  | Een model waarbij gebruikers tijdelijke machtigingen ontvangen om bepaalde taken uit te mogen voeren, waardoor kwaadwillende of onbevoegde gebruikers geen toegang kunnen krijgen na het verlopen van deze machtigingen. Toegang wordt alleen verleend wanneer gebruikers deze nodig hebben. |
| Principe van toegang met minimale bevoegdheden |  | Een aanbevolen beveiligingsprocedure waarbij alle gebruikers enkel de minimale bevoegdheden krijgt toegewezen die nodig zijn om de taken uit te voeren waarvoor ze bevoegd zijn. Met deze procedure wordt het aantal globale beheerders tot het minimum beperkt en worden er specifieke beheerdersrollen gebruikt voor bepaalde scenario's. |

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

Zie [licentie vereisten voor het gebruik van privileged Identity Management](subscription-requirements.md)voor meer informatie over licenties voor gebruikers.

## <a name="next-steps"></a>Volgende stappen

- [Licentie vereisten voor het gebruik van Privileged Identity Management](subscription-requirements.md)
- [Bevoegde toegang beveiligen voor hybride implementaties en cloudimplementaties in Azure AD](../users-groups-roles/directory-admin-roles-secure.md?toc=%2fazure%2factive-directory%2fprivileged-identity-management%2ftoc.json)
- [Privileged Identity Management implementeren](pim-deployment-plan.md)

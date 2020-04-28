---
title: Wat is een toegevoegd Azure AD-apparaat?
description: Meer informatie over hoe het beheer van apparaat-id's u kan helpen bij het beheren van apparaten die toegang hebben tot resources in uw omgeving.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672670"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-gekoppelde apparaten

Deelname aan Azure AD is bedoeld voor organisaties die alleen in de Cloud of alleen in de Cloud moeten worden opgenomen. Elke organisatie kan aan Azure AD gekoppelde apparaten implementeren, ongeacht de omvang of de branche. Azure AD-deelname werkt zelfs in een hybride omgeving en biedt toegang tot zowel Cloud-als on-premises apps en bronnen.

|   | Azure AD-Join |
| --- | --- |
| **Definitie** | Alleen opgenomen in azure AD waarvoor een organisatie account is vereist om zich aan te melden bij het apparaat |
| **Primaire doel groep** | Alleen geschikt voor Cloud-en hybride organisaties. |
|   | Van toepassing op alle gebruikers in een organisatie |
| **Apparaateigendom** | Organisatie |
| **Besturings systemen** | Alle Windows 10-apparaten |
| **Inrichten** | Self-service: Windows OOBE of instellingen |
|   | Bulkinschrijving |
|   | Windows Autopilot |
| **Aanmeldings opties voor apparaten** | Organisatie accounts met: |
|   | Wachtwoord |
|   | Windows Hello voor Bedrijven |
|   | FIDO 2.0-beveiligings sleutels (preview-versie) |
| **Apparaatbeheer** | Beheer van mobiele apparaten (voor beeld: Microsoft Intune) |
|   | Co-beheer met Microsoft Intune en micro soft-eind punt Configuration Manager |
| **Belangrijkste mogelijkheden** | Eenmalige aanmelding voor zowel Cloud-als on-premises resources |
|   | Voorwaardelijke toegang via MDM-inschrijving en MDM-nalevings evaluatie |
|   | Selfservice voor wachtwoord herstel en Windows hello pincode opnieuw instellen op vergrendelings scherm |
|   | Enterprise State Roaming op verschillende apparaten |

Aan Azure AD gekoppelde apparaten zijn aangemeld voor het gebruik van een Azure AD-account in de organisatie. Toegang tot resources in de organisatie kan verder worden beperkt op basis van het Azure AD-account en het [beleid voor voorwaardelijke toegang](../conditional-access/overview.md) dat wordt toegepast op de apparaat-id.

Beheerders kunnen apparaten die zijn toegevoegd aan Azure AD beveiligen en verder beheren met de MDM-hulpprogram ma's (Mobile Device Management), zoals Microsoft Intune of in scenario's voor co-beheer met micro soft endpoint Configuration Manager. Deze hulpprogram ma's bieden een manier om organisatie-vereiste configuraties af te dwingen, zoals het vereisen van een versleutelde opslag, wachtwoord complexiteit, software-installatie en software-updates. Beheerders kunnen organisatie toepassingen beschikbaar maken voor apparaten die lid zijn van Azure AD met behulp van Configuration Manager om [apps te beheren via de Microsoft Store voor bedrijven en onderwijs](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Azure AD-deelname kan worden uitgevoerd met selfservice opties zoals out of Box Experience (OOBE), bulk inschrijving of [Windows auto pilot](/intune/enrollment-autopilot).

Apparaten die aan Azure AD zijn toegevoegd, kunnen nog steeds toegang tot uw on-premises resources hebben bij het gebruik van eenmalige aanmelding wanneer ze zich op het netwerk van de organisatie bevinden. Apparaten die zijn toegevoegd aan Azure AD, kunnen nog steeds worden geverifieerd voor on-premises servers zoals bestanden, printers en andere toepassingen.

## <a name="scenarios"></a>Scenario's

Hoewel Azure AD-koppeling voornamelijk is bedoeld voor organisaties die geen on-premises Windows Server Active Directory-infrastructuur hebben, kunt u dit beslist gebruiken in scenario’s waarbij:

- U wilt overstappen op cloudgebaseerde infrastructuur met behulp van Azure AD en MDM zoals Intune.
- U geen on-premises domeinkoppeling kunt gebruiken, bijvoorbeeld als u mobiele apparaten zoals tablets en telefoons onder controle moet krijgen.
- Uw gebruikers voornamelijk toegang nodig hebben tot Office 365 of andere SaaS-apps die zijn geïntegreerd met Azure AD.
- U een groep gebruikers in Azure AD wilt beheren in plaats van in Active Directory. Dit scenario kan bijvoorbeeld van toepassing zijn op seizoen arbeiders, aannemers of studenten.
- U koppelingsmogelijkheden wilt bieden aan medewerkers in externe filialen met beperkte on-premises infrastructuur.

U kunt Azure AD-gekoppelde apparaten configureren voor Windows 10-apparaten.

Het doel van Azure AD-gekoppelde apparaten is vereenvoudiging van:

- Windows-implementaties van apparaten die eigendom van het bedrijf zijn
- Toegang tot apps en bronnen van de organisatie vanaf elk Windows-apparaat
- Cloudgebaseerd beheer van apparaten die eigendom van het bedrijf zijn
- Gebruikers om zich aan te melden bij hun apparaten met hun Azure AD-of gesynchroniseerde Active Directory werk-of school account.

![Azure AD-gekoppelde apparaten](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD-koppeling kan via een van de volgende methoden worden geïmplementeerd:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Bulkimplementatie](/intune/windows-bulk-enroll)
- [Selfservice-ervaring](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Volgende stappen

- [Uw implementatie van Azure AD-deelname plannen](azureadjoin-plan.md)
- [De lokale groep Administrators beheren op apparaten die zijn toegevoegd aan Azure AD](assign-local-admin.md)
- [Apparaat-id's beheren met de Azure Portal](device-management-azure-portal.md)
- [Verlopen apparaten beheren in Azure Active Directory](manage-stale-devices.md)

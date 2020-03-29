---
title: Wat is een Azure AD-apparaat dat is samengevoegd?
description: Ontdek hoe apparaatidentiteitsbeheer u kan helpen bij het beheren van apparaten die toegang hebben tot bronnen in uw omgeving.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672670"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-gekoppelde apparaten

Azure AD join is bedoeld voor organisaties die alleen cloud of cloud willen zijn. Elke organisatie kan Azure AD-samengevoegde apparaten implementeren, ongeacht de grootte of de branche. Azure AD-join werkt zelfs in een hybride omgeving, waardoor toegang tot zowel cloud- als on-premises apps en resources mogelijk is.

|   | Azure AD-Join |
| --- | --- |
| **Definitie** | Alleen lid geworden van Azure AD waarbij een organisatieaccount moet worden aangemeld bij het apparaat |
| **Primaire doelgroep** | Geschikt voor zowel cloud-only als hybride organisaties. |
|   | Van toepassing op alle gebruikers in een organisatie |
| **Apparaateigendom** | Organisatie |
| **Besturingssystemen** | Alle Windows 10-apparaten |
| **Inrichten** | Selfservice: Windows OOBE of Instellingen |
|   | Bulkinschrijving |
|   | Windows Autopilot |
| **Inlogopties voor apparaten** | Organisatieaccounts met behulp van: |
|   | Wachtwoord |
|   | Windows Hello voor Bedrijven |
|   | FIDO2.0-beveiligingssleutels (voorbeeld) |
| **Apparaatbeheer** | Beheer van mobiele apparaten (voorbeeld: Microsoft Intune) |
|   | Co-management met Microsoft Intune en Microsoft Endpoint Configuration Manager |
| **Belangrijkste mogelijkheden** | SSO naar zowel cloud- als on-premises resources |
|   | Voorwaardelijke toegang via MDM-inschrijving en MDM-nalevingsbeoordeling |
|   | Selfservice Wachtwoord reset en Windows Hello PIN reset op vergrendelingsscherm |
|   | Bedrijfsstatusroaming op verschillende apparaten |

Azure AD-apparaten zijn aangemeld bij het gebruik van een Azure AD-account voor een organisatie. De toegang tot bronnen in de organisatie kan verder worden beperkt op basis van dat Azure AD-account en [het beleid voor voorwaardelijke toegang](../conditional-access/overview.md) dat wordt toegepast op de apparaatidentiteit.

Beheerders kunnen Azure AD-apparaten beveiligen en verder beheren met MDM-hulpprogramma's (Mobile Device Management), zoals Microsoft Intune of in co-managementscenario's met Microsoft Endpoint Configuration Manager. Deze hulpprogramma's bieden een middel om door de organisatie vereiste configuraties af te dwingen, zoals het vereisen van opslag, complexiteit van wachtwoorden, software-installaties en software-updates. Beheerders kunnen organisatietoepassingen beschikbaar maken voor Azure AD-apparaten die zijn samengevoegd met Configuratiebeheer om [apps te beheren vanuit de Microsoft Store voor Bedrijven en Onderwijs](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Azure AD-join kan worden uitgevoerd met selfservice-opties zoals de Out of Box Experience (OOBE), bulkinschrijving of [Windows Autopilot.](/intune/enrollment-autopilot)

Azure AD-apparaten kunnen nog steeds toegang tot on-premises resources behouden wanneer deze zich in het netwerk van de organisatie bevinden. Apparaten die zijn aangesloten bij Azure AD, kunnen zich nog steeds verifiëren voor on-premises servers zoals bestands-, afdruk- en andere toepassingen.

## <a name="scenarios"></a>Scenario's

Hoewel Azure AD-koppeling voornamelijk is bedoeld voor organisaties die geen on-premises Windows Server Active Directory-infrastructuur hebben, kunt u dit beslist gebruiken in scenario’s waarbij:

- U wilt overstappen op cloudgebaseerde infrastructuur met behulp van Azure AD en MDM zoals Intune.
- U geen on-premises domeinkoppeling kunt gebruiken, bijvoorbeeld als u mobiele apparaten zoals tablets en telefoons onder controle moet krijgen.
- Uw gebruikers voornamelijk toegang nodig hebben tot Office 365 of andere SaaS-apps die zijn geïntegreerd met Azure AD.
- U een groep gebruikers in Azure AD wilt beheren in plaats van in Active Directory. Dit scenario kan bijvoorbeeld van toepassing zijn op seizoenarbeiders, aannemers of studenten.
- U koppelingsmogelijkheden wilt bieden aan medewerkers in externe filialen met beperkte on-premises infrastructuur.

U kunt Azure AD-gekoppelde apparaten configureren voor Windows 10-apparaten.

Het doel van Azure AD-gekoppelde apparaten is vereenvoudiging van:

- Windows-implementaties van apparaten die eigendom van het bedrijf zijn
- Toegang tot apps en bronnen van de organisatie vanaf elk Windows-apparaat
- Cloudgebaseerd beheer van apparaten die eigendom van het bedrijf zijn
- Gebruikers die zich bij hun apparaten aanmelden met hun Azure AD of gesynchroniseerde Active Directory-werk- of schoolaccounts.

![Azure AD-gekoppelde apparaten](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD-koppeling kan via een van de volgende methoden worden geïmplementeerd:

- [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Bulkimplementatie](/intune/windows-bulk-enroll)
- [Selfservice-ervaring](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Volgende stappen

- [Uw implementatie van Azure AD-deelname plannen](azureadjoin-plan.md)
- [De groep lokale beheerders beheren op Azure AD-apparaten](assign-local-admin.md)
- [Apparaatidentiteiten beheren met behulp van de Azure-portal](device-management-azure-portal.md)
- [Verlopen apparaten beheren in Azure Active Directory](manage-stale-devices.md)

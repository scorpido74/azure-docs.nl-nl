---
title: Wat zijn door Azure AD geregistreerde apparaten?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67462757"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-geregistreerde apparaten

Het doel van azure AD-geregistreerde apparaten is om uw gebruikers ondersteuning te bieden voor de BYOD (Bring Your Own Device) of mobiele apparaat. In deze scenario's heeft een gebruiker toegang tot de Azure Active Directory-beheerde bronnen van uw organisatie met behulp van een persoonlijk apparaat.

|   | Azure AD geregistreerd |
| --- | --- |
| **Definitie** | Geregistreerd bij Azure AD zonder dat een organisatieaccount zich bij het apparaat moet aanmelden |
| **Primaire doelgroep** | Van toepassing op alle gebruikers met de volgende criteria: |
|   | BYOD (Bring Your Own Device) |
|   | Mobiele apparaten |
| **Apparaateigendom** | Gebruiker of organisatie |
| **Besturingssystemen** | Windows 10, iOS, Android en MacOS |
| **Inrichten** | Windows 10 – Instellingen |
|   | iOS/Android – Bedrijfsportal of Microsoft Authenticator-app |
|   | MacOS – Bedrijfsportaal |
| **Inlogopties voor apparaten** | Lokale referenties van eindgebruikers |
|   | Wachtwoord |
|   | Windows Hello |
|   | Pincode |
|   | Biometrie of patroon voor andere apparaten |
| **Apparaatbeheer** | Beheer van mobiele apparaten (voorbeeld: Microsoft Intune) |
|   | mobiel applicatiebeheer |
| **Belangrijkste mogelijkheden** | SSO naar cloudresources |
|   | Voorwaardelijke toegang wanneer u bent ingeschreven bij Intune |
|   | Voorwaardelijke toegang via app-beveiligingsbeleid |
|   | Aanmelden via telefoon mogelijk maken met microsoft authenticator-app |

![Azure AD-geregistreerde apparaten](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Azure AD-geregistreerde apparaten zijn aangemeld bij het gebruik van een lokaal account zoals een Microsoft-account op een Windows 10-apparaat, maar hebben bovendien een Azure AD-account gekoppeld voor toegang tot organisatiebronnen. De toegang tot bronnen in de organisatie kan verder worden beperkt op basis van dat Azure AD-account en het beleid voor voorwaardelijke toegang dat wordt toegepast op de apparaatidentiteit.

Beheerders kunnen deze door Azure AD geregistreerde apparaten beveiligen en verder beheren met mdm-hulpprogramma's (Mobile Device Management), zoals Microsoft Intune. MDM biedt een middel om door de organisatie vereiste configuraties af te dwingen, zoals het vereisen van opslag om te worden versleuteld, de complexiteit van wachtwoorden en beveiligingssoftware bijgewerkt. 

Azure AD-registratie kan worden uitgevoerd wanneer u voor het eerst of handmatig toegang krijgt tot een werktoepassing via het menu Instellingen van Windows 10. 

## <a name="scenarios"></a>Scenario's

Een gebruiker in uw organisatie wil toegang krijgen tot hulpprogramma's voor e-mail, het rapporteren van time-off en het behalen van inschrijving vanaf zijn pc thuis. Uw organisatie heeft deze tools achter een beleid voor voorwaardelijke toegang dat toegang vereist vanaf een intune-compatibel apparaat. De gebruiker voegt zijn organisatieaccount toe en registreert zijn thuis-pc met Azure AD en het vereiste Intune-beleid wordt afgedwongen en geeft de gebruiker toegang tot zijn bronnen.

Een andere gebruiker wil toegang tot hun organisatorische e-mail op hun persoonlijke Android-telefoon die is geworteld. Uw bedrijf heeft een compatibel apparaat nodig en heeft een Intune-nalevingsbeleid opgesteld om gewortelde apparaten te blokkeren. De werknemer heeft geen toegang meer tot organisatiebronnen op dit apparaat.

## <a name="next-steps"></a>Volgende stappen

- [Apparaatidentiteiten beheren met behulp van de Azure-portal](device-management-azure-portal.md)
- [Verlopen apparaten beheren in Azure Active Directory](manage-stale-devices.md)

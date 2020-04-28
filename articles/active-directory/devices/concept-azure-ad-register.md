---
title: Wat zijn geregistreerde Azure AD-apparaten?
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
ms.openlocfilehash: 7e2a8cad7cd4410a95a6ebd60ada22de456737bf
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67462757"
---
# <a name="azure-ad-registered-devices"></a>Azure AD-geregistreerde apparaten

Het doel van geregistreerde Azure AD-apparaten is om uw gebruikers ondersteuning te bieden voor de scenario's voor het nemen van uw eigen apparaten (BYOD) of mobiele apparaten. In deze scenario's heeft een gebruiker toegang tot de Azure Active Directory beheerde resources van uw organisatie met behulp van een persoonlijk apparaat.

|   | Geregistreerde Azure AD |
| --- | --- |
| **Definitie** | Geregistreerd bij Azure AD zonder organisatie account om u aan te melden bij het apparaat |
| **Primaire doel groep** | Van toepassing op alle gebruikers met de volgende criteria: |
|   | BYOD (Bring Your Own Device) |
|   | Mobiele apparaten |
| **Apparaateigendom** | Gebruiker of organisatie |
| **Besturings systemen** | Windows 10, iOS, Android en MacOS |
| **Inrichten** | Windows 10 – instellingen |
|   | iOS/Android – Bedrijfsportal-of Microsoft Authenticator-app |
|   | MacOS-Bedrijfsportal |
| **Aanmeldings opties voor apparaten** | Lokale referenties voor eind gebruikers |
|   | Wachtwoord |
|   | Windows Hello |
|   | Pincode |
|   | Biometrie of patroon voor andere apparaten |
| **Apparaatbeheer** | Beheer van mobiele apparaten (voor beeld: Microsoft Intune) |
|   | mobiel applicatiebeheer |
| **Belangrijkste mogelijkheden** | Eenmalige aanmelding bij cloud resources |
|   | Voorwaardelijke toegang wanneer Inge schreven bij intune |
|   | Voorwaardelijke toegang via beleid voor app-beveiliging |
|   | Aanmelden met Microsoft Authenticator-app inschakelen |

![Azure AD-geregistreerde apparaten](./media/concept-azure-ad-register/azure-ad-registered-device.png)

Geregistreerde Azure AD-apparaten zijn aangemeld voor het gebruik van een lokaal account, zoals een Microsoft-account op een Windows 10-apparaat, maar er is bovendien een Azure AD-account gekoppeld voor toegang tot resources van de organisatie. Toegang tot resources in de organisatie kan verder worden beperkt op basis van het Azure AD-account en het beleid voor voorwaardelijke toegang dat wordt toegepast op de apparaat-id.

Beheerders kunnen deze door Azure AD geregistreerde apparaten beveiligen en verder beheren met de MDM-hulpprogram ma's (Mobile Device Management) zoals Microsoft Intune. MDM biedt een manier om organisatie-vereiste configuraties af te dwingen, zoals het vereisen van het versleutelen van de opslag, de complexiteit van het wacht woord en beveiligings software die bijgewerkt blijven. 

De registratie van Azure AD kan worden uitgevoerd wanneer een werk toepassing voor het eerst wordt geopend of hand matig via het menu instellingen van Windows 10. 

## <a name="scenarios"></a>Scenario's

Een gebruiker in uw organisatie wil toegang krijgen tot hulp middelen voor e-mail, rapportage van time-out en voor delen van de computer thuis. Uw organisatie heeft deze hulpprogram ma's achter een beleid voor voorwaardelijke toegang waarvoor toegang is vereist vanaf een apparaat dat compatibel is met intune. De gebruiker voegt zijn organisatie account toe en registreert hun thuis computer met Azure AD en de vereiste intune-beleids regels worden afgedwongen, zodat de gebruiker toegang heeft tot hun resources.

Een andere gebruiker wil toegang tot hun organisatie-e-mail op hun persoonlijke Android-telefoon die is geroot. Uw bedrijf vereist een compatibel apparaat en heeft een intune-nalevings beleid gemaakt om alle geroote apparaten te blok keren. De werk nemer heeft geen toegang tot de resources van de organisatie op dit apparaat.

## <a name="next-steps"></a>Volgende stappen

- [Apparaat-id's beheren met de Azure Portal](device-management-azure-portal.md)
- [Verlopen apparaten beheren in Azure Active Directory](manage-stale-devices.md)

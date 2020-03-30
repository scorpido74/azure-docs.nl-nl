---
title: Groepsbeleids- en MDM-instellingen voor ESR - Azure Active Directory
description: Beheerinstellingen voor roaming van enterprisestate
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672372"
---
# <a name="group-policy-and-mdm-settings"></a>Groepsbeleids- en MDM-instellingen

Gebruik deze MDM-instellingen (Group Policy and Mobile Device Management) alleen op apparaten die eigendom zijn van bedrijven, omdat dit beleid wordt toegepast op het hele apparaat van de gebruiker. Het toepassen van een MDM-beleid om instellingensynchronisatie uit te schakelen voor een persoonlijk apparaat dat eigendom is van de gebruiker, heeft een negatieve invloed op het gebruik van dat apparaat. Daarnaast worden ook andere gebruikersaccounts op het apparaat beïnvloed door het beleid.

Ondernemingen die roaming voor persoonlijke (onbeheerde) apparaten willen beheren, kunnen de Azure-portal gebruiken om roaming in te schakelen of uit te schakelen, in plaats van groepsbeleid of MDM te gebruiken.
In de volgende tabellen worden de beschikbare beleidsinstellingen beschreven.

> [!NOTE]
> Dit artikel is van toepassing op de Microsoft Edge Legacy HTML-gebaseerde browser die in juli 2015 met Windows 10 is gelanceerd. Het artikel is niet van toepassing op de nieuwe Microsoft Edge Chromium-gebaseerde browser uitgebracht op 15 januari 2020. Zie het artikel Microsoft Edge Sync voor meer informatie over het gedrag Synchroniseren voor de nieuwe Microsoft [Edge.](/deployedge/microsoft-edge-enterprise-sync)

## <a name="mdm-settings"></a>MDM-instellingen

De MDM-beleidsinstellingen zijn van toepassing op zowel Windows 10 als Windows 10 Mobile.  Windows 10 Mobile-ondersteuning bestaat alleen voor Microsoft-accountgebaseerde roaming via het OneDrive-account van de gebruiker. Raadpleeg [Apparaten en eindpunten](enterprise-state-roaming-windows-settings-reference.md) voor meer informatie over welke apparaten worden ondersteund voor synchronisatie op basis van Azure AD.

| Name | Beschrijving |
| --- | --- |
| Microsoft-accountverbinding toestaan |Hiermee kunnen gebruikers zich verifiëren met een Microsoft-account op het apparaat |
| Synchronisatie met mijn instellingen toestaan |Hiermee kunnen gebruikers door Windows-instellingen en app-gegevens zwerven; Als u dit beleid uitschakelt, worden synchronisatie en back-ups op mobiele apparaten uitgeschakeld |

## <a name="group-policy-settings"></a>Instellingen voor groepsbeleid

De groepsbeleidsinstellingen zijn van toepassing op Windows 10-apparaten die zijn verbonden met een Active Directory-domein. De tabel bevat ook oudere instellingen die synchronisatie-instellingen lijken te beheren, maar die niet werken voor Enterprise State Roaming voor Windows 10, die worden opgemerkt met 'Niet gebruiken' in de beschrijving.

Deze instellingen bevinden zich op:`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Name | Beschrijving |
| --- | --- |
| Accounts: Microsoft-accounts blokkeren |Met deze beleidsinstelling voorkomt u dat gebruikers nieuwe Microsoft-accounts op deze computer toevoegen |
| Niet synchroniseren |Hiermee voorkomt u dat gebruikers roamen met Windows-instellingen en app-gegevens |
| Niet synchroniseren personaliseren |Hiermee schakelt u synchronisatie van de groep Thema's uit |
| Browserinstellingen niet synchroniseren |Hiermee schakelt u de synchronisatie van de groep Internet Explorer uit |
| Wachtwoorden niet synchroniseren |Hiermee wordt het synchroniseren van de groep Wachtwoorden uitgeschakeld |
| Andere Windows-instellingen niet synchroniseren |Hiermee wordt het synchroniseren van de groep Andere Windows-instellingen uitgeschakeld |
| Bureaubladpersonalisatie niet synchroniseren |Niet gebruiken; heeft geen effect |
| Niet synchroniseren op verbindingen met datalimiet |Hiermee schakelt u roaming op verbindingen met datalimiet uit, zoals mobiel 3G |
| Apps niet synchroniseren |Niet gebruiken; heeft geen effect |
| App-instellingen niet synchroniseren |Hiermee schakelt u roaming van app-gegevens uit |
| Startinstellingen niet synchroniseren |Niet gebruiken; heeft geen effect |

## <a name="next-steps"></a>Volgende stappen

Zie het [overzicht enterprise State Roaming voor](enterprise-state-roaming-overview.md)een overzicht van enterprise State Roaming voor een overzicht.

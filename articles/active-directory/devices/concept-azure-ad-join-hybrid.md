---
title: Wat is een hybride Azure AD-apparaat?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512246"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybride Azure AD-gekoppelde apparaten

Al meer dan tien jaar gebruiken vele organisaties de domeinkoppeling naar hun on-premises Active Directory om:

- IT-afdelingen vanaf een centrale locatie apparaten te laten beheren die eigendom van het bedrijf zijn.
- Gebruikers zich bij hun apparaat te laten aanmelden met hun Active Directory-werkaccount of -schoolaccount.

Organisaties met een on-premises voetafdruk vertrouwen doorgaans op beeldvormingsmethoden voor het inrichten van apparaten en gebruiken vaak **Configuration Manager** of **groepsbeleid (GP)** om ze te beheren.

Als uw omgeving een on-premises AD-voetafdruk heeft en u ook wilt profiteren van de mogelijkheden die Azure Active Directory biedt, kunt u hybride Azure AD-gekoppelde apparaten implementeren. Dit zijn apparaten die zijn gekoppeld aan uw on-premises Active Directory en zijn geregistreerd bij uw Azure Active Directory.

|   | Hybride Azure AD Deelnemen |
| --- | --- |
| **Definitie** | Lid geworden bij on-premises AD en Azure AD waarbij een organisatieaccount moet worden aangemeld bij het apparaat |
| **Primaire doelgroep** | Geschikt voor hybride organisaties met bestaande on-premises AD-infrastructuur |
|   | Van toepassing op alle gebruikers in een organisatie |
| **Apparaateigendom** | Organisatie |
| **Besturingssystemen** | Windows 10, 8.1 en 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 en 2019 |
| **Inrichten** | Windows 10, Windows Server 2016/2019 |
|   | Domeinverbinding met IT en automatisch verbinden via Azure AD Connect of ADFS-config |
|   | Domeinlid worden door Windows Autopilot en automatisch deelnemen via Azure AD Connect of ADFS-config |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 en Windows Server 2008 R2 - MSI vereisen |
| **Inlogopties voor apparaten** | Organisatieaccounts met behulp van: |
|   | Wachtwoord |
|   | Windows Hello voor Bedrijven voor Win10 |
| **Apparaatbeheer** | Groepsbeleid |
|   | Configuratiebeheer zelfstandig of co-management met Microsoft Intune |
| **Belangrijkste mogelijkheden** | SSO naar zowel cloud- als on-premises resources |
|   | Voorwaardelijke toegang via domeinjoin of via Intune als deze samen wordt beheerd |
|   | Selfservice Wachtwoord reset en Windows Hello PIN reset op vergrendelingsscherm |
|   | Bedrijfsstatusroaming op verschillende apparaten |

![Hybride Azure AD-gekoppelde apparaten](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenario's

Gebruik Azure AD hybrid-apparaten als:

- U Win32-apps op deze apparaten hebt geïmplementeerd die afhankelijk zijn van Active Directory-machineverificatie.
- U wilt groepsbeleid blijven gebruiken om de apparaatconfiguratie te beheren.
- U wilt bestaande imaging-oplossingen blijven gebruiken om apparaten te implementeren en configureren.
- Naast Windows 10-apparaten op downniveau moeten ook Windows 7- en 8.1-apparaten op downniveau worden ondersteund

## <a name="next-steps"></a>Volgende stappen

- [Uw implementatie van hybride Azure AD-deelname plannen](hybrid-azuread-join-plan.md)
- [Apparaatidentiteiten beheren met behulp van de Azure-portal](device-management-azure-portal.md)
- [Verlopen apparaten beheren in Azure Active Directory](manage-stale-devices.md)

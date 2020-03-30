---
title: Wat is roaming voor bedrijfsstatusin Azure Active Directory?
description: Enterprise State Roaming biedt gebruikers een uniforme ervaring op hun Windows-apparaten
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77194276"
---
# <a name="what-is-enterprise-state-roaming"></a>Wat is Enterprise State Roaming?

Met Windows 10 krijgen [Azure Active Directory-gebruikers](../fundamentals/active-directory-whatis.md) de mogelijkheid om hun gebruikersinstellingen en toepassingsinstellingen veilig te synchroniseren met de cloud. Enterprise State Roaming biedt gebruikers een uniforme ervaring op hun Windows-apparaten en vermindert de tijd die nodig is voor het configureren van een nieuw apparaat. Enterprise State Roaming werkt vergelijkbaar met de standaard [synchronisatie van consumenteninstellingen](https://go.microsoft.com/fwlink/?linkid=2015135) die voor het eerst werd geïntroduceerd in Windows 8. Daarnaast biedt Enterprise State Roaming:

* **Scheiding van bedrijfsgegevens en consumentengegevens** – Organisaties hebben de controle over hun gegevens en er is geen vermenging van bedrijfsgegevens in een consumentencloudaccount of consumentengegevens in een enterprise cloud-account.
* **Verbeterde beveiliging** : gegevens worden automatisch versleuteld voordat u het Windows 10-apparaat van de gebruiker verlaat met Azure Rights Management (Azure RMS) en gegevens blijven in rust in de cloud versleuteld. Alle inhoud blijft versleuteld in de cloud, met uitzondering van de naamruimten, zoals instellingen namen en Windows app namen.  
* **Beter beheer en bewaking** : biedt controle en inzicht over wie de instellingen in uw organisatie synchroniseert en op welke apparaten via de Azure AD-portalintegratie. 

Bedrijfsstatusroaming is beschikbaar in meerdere Azure-regio's. U vindt de bijgewerkte lijst met beschikbare regio's op de pagina [Azure Services by Regions](https://azure.microsoft.com/regions/#services) onder Azure Active Directory.

| Artikel | Beschrijving |
| --- | --- |
| [Enterprise state roaming inschakelen in Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming is beschikbaar voor elke organisatie met een Azure AD-abonnement (Premium Azure Active Directory). Zie de productpagina [van Azure AD](https://azure.microsoft.com/services/active-directory) voor meer informatie over het aanschaffen van een Azure AD-abonnement. |
| [Veelgestelde vragen over instellingen en gegevensroaming](enterprise-state-roaming-faqs.md) |In dit artikel worden enkele vragen beantwoord die IT-beheerders mogelijk hebben over instellingen en het synchroniseren van app-gegevens. |
| [Groepsbeleid en MDM-instellingen voor synchronisatie van instellingen](enterprise-state-roaming-group-policy-settings.md) |Windows 10 biedt mdm-beleidsinstellingen (Group Policy en Mobile Device Management) om de synchronisatie van instellingen te beperken. |
| [Naslaginformatie over roaminginstellingen voor Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Een lijst met instellingen die worden geroamd en/of een back-up maken in Windows 10. |
| [Probleemoplossing](enterprise-state-roaming-troubleshooting.md) |Dit artikel gaat door een aantal basisstappen voor het oplossen van problemen, en bevat een lijst met bekende problemen. |

## <a name="next-steps"></a>Volgende stappen

Zie roaming [van ondernemingsstaten inschakelen](enterprise-state-roaming-enable.md)voor informatie over roaming van ondernemingsstaten.

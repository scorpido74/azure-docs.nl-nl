---
title: Wat is de Enter prise State roaming in Azure Active Directory?
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
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/13/2020
ms.locfileid: "77194276"
---
# <a name="what-is-enterprise-state-roaming"></a>Wat is Enterprise State Roaming?

Met Windows 10 kunnen gebruikers van [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) hun gebruikers instellingen en toepassings instellingen veilig synchroniseren met de Cloud. Enterprise State Roaming biedt gebruikers een uniforme ervaring op hun Windows-apparaten en verkort de tijd die nodig is voor het configureren van een nieuw apparaat. Enterprise State Roaming werkt op dezelfde manier als de standaard [synchronisatie van consumenten instellingen](https://go.microsoft.com/fwlink/?linkid=2015135) die voor het eerst werd geïntroduceerd in Windows 8. Enterprise State Roaming biedt daarnaast de volgende opties:

* **Schei ding van bedrijfs-en consumenten gegevens** : organisaties hebben de controle over hun gegevens en er is geen combi natie van Bedrijfs gegevens in een Cloud account of consument gegevens in een bedrijfs-Cloud account.
* **Verbeterde beveiliging** : gegevens worden automatisch versleuteld voordat het Windows 10-apparaat van de gebruiker wordt verlaten met behulp van Azure Rights Management (Azure RMS) en de gegevens worden versleuteld in de rest van de Cloud. Alle inhoud blijft versleuteld op rest in de Cloud, met uitzonde ring van de naam ruimten, zoals instellingen namen en Windows-app-namen.  
* **Beter beheer en controle** : voorziet in beheer en zicht baarheid van wie de instellingen in uw organisatie synchroniseert en op welke apparaten de Azure AD-Portal is geïntegreerd. 

Enterprise State Roaming is beschikbaar in meerdere Azure-regio's. U kunt de bijgewerkte lijst met beschik bare regio's vinden op de pagina [Azure-Services per regio](https://azure.microsoft.com/regions/#services) onder Azure Active Directory.

| Artikel | Beschrijving |
| --- | --- |
| [Enterprise State Roaming in Azure Active Directory inschakelen](enterprise-state-roaming-enable.md) |Enterprise State Roaming is beschikbaar voor elke organisatie met een Premium-Azure Active Directory-abonnement (Azure AD). Zie de product pagina van [Azure AD](https://azure.microsoft.com/services/active-directory) voor meer informatie over het verkrijgen van een Azure AD-abonnement. |
| [Veelgestelde vragen over instellingen en gegevens roaming](enterprise-state-roaming-faqs.md) |In dit artikel vindt u antwoorden op enkele vragen die beheerders kunnen hebben over instellingen en app-gegevens synchronisatie. |
| [Groeps beleid en MDM-instellingen voor instellingen synchronisatie](enterprise-state-roaming-group-policy-settings.md) |Windows 10 biedt groepsbeleid-en Mobile Device Management (MDM)-beleids instellingen om de synchronisatie van instellingen te beperken. |
| [Naslag informatie voor Windows 10-instellingen voor roaming](enterprise-state-roaming-windows-settings-reference.md) |Een lijst met instellingen die worden geroamd en/of waarvan een back-up wordt gemaakt in Windows 10. |
| [Problemen oplossen](enterprise-state-roaming-troubleshooting.md) |In dit artikel vindt u enkele eenvoudige stappen voor het oplossen van problemen en een lijst met bekende problemen. |

## <a name="next-steps"></a>Volgende stappen

Zie [Enter prise State roaming inschakelen](enterprise-state-roaming-enable.md)voor meer informatie over het inschakelen van de Enter prise State roaming.

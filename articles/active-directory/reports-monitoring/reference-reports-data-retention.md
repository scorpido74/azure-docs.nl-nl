---
title: Hoe lang slaat Azure AD rapportagegegevens op? | Microsoft Docs
description: Meer informatie over hoe lang Azure de verschillende typen rapportagegegevens opslaat.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/24/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54636600c208f8f5df9fa2e25460c63dd9f46e85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80239547"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Hoe lang slaat Azure AD rapportagegegevens op?


In dit artikel vindt u meer informatie over het beleid voor het bewaren van gegevens voor de verschillende activiteitenrapporten in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Wanneer begint Azure AD met het verzamelen van gegevens?

| Azure AD-editie | Verzameling gestart |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Wanneer u zich aanmeldt voor een abonnement |
| Azure AD Free| De eerste keer dat u het [Azure Active Directory-blad opent](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) of de [rapportage-API's](https://aka.ms/aadreports) gebruikt  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Wanneer zijn de activiteitsgegevens beschikbaar in de Azure-portal?

- **Onmiddellijk** - Als u al hebt gewerkt met rapporten in de Azure-portal.
- **Binnen 2 uur** - Als u de rapportage niet hebt ingeschakeld in de Azure-portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hoe snel kan ik activiteitengegevens zien nadat ik een premium licentie heb verkregen?

Als u al activiteitengegevens hebt met uw gratis licentie, dan u deze onmiddellijk zien bij de upgrade. Als u geen gegevens hebt, duurt het een of twee dagen voordat de gegevens in de rapporten worden weergegeven nadat u een upgrade naar een premiumlicentie hebt uitgevoerd.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Kan ik de gegevens van vorige maand zien nadat ik een Azure AD PREMIUM-licentie heb verkregen?

Als u onlangs bent overgestapt op een premiumversie (inclusief een proefversie), u gegevens in eerste instantie tot 7 dagen zien. Wanneer gegevens zich ophopen, u gegevens van de afgelopen 30 dagen zien.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Wanneer begint Azure AD met het verzamelen van beveiligingssignaalgegevens?  

Voor beveiligingssignalen wordt het verzamelen gestart wanneer u zich aanmeldt voor het **Identity Protection Center.** 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hoe lang slaat Azure AD de gegevens op?

**Activiteitsrapporten**    

| Rapport                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditlogboeken             | 7 dagen        | 30 dagen             | 30 dagen             |
| Aanmeldingen               | 7 dagen        | 30 dagen             | 30 dagen             |
| Azure MFA-gebruik        | 30 dagen       | 30 dagen             | 30 dagen             |

U de controle- en aanmeldingsactiviteitsgegevens langer bewaren dan de hierboven beschreven standaardbewaarperiode door deze te routeren naar een Azure-opslagaccount met Azure Monitor. Zie [Azure AD-logboeken archiveren voor meer informatie naar een Azure-opslagaccount](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Beveiligingssignalen**

| Rapport         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Gebruikers die risico lopen  | 7 dagen        | 30 dagen             | 90 dagen             |
| Riskante aanmeldingen | 7 dagen        | 30 dagen             | 90 dagen             |

---

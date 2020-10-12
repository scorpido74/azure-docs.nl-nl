---
title: Hoe lang worden de gegevens van Azure AD Store gerapporteerd? | Microsoft Docs
description: Meer informatie over hoe lang Azure de verschillende typen rapport gegevens opslaat.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "80239547"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>Hoe lang worden de gegevens van Azure AD Store gerapporteerd?


In dit artikel vindt u informatie over het Bewaar beleid voor gegevens voor de verschillende activiteiten rapporten in Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>Wanneer begint Azure AD met het verzamelen van gegevens?

| Azure AD-editie | Begin verzameling |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Wanneer u zich registreert voor een abonnement |
| Azure AD Free| De eerste keer dat u de [blade Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) opent of de [rapportage-api's](https://aka.ms/aadreports) gebruikt  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>Wanneer zijn de activiteit gegevens beschikbaar in de Azure Portal?

- **Onmiddellijk** : als u al met rapporten werkt in de Azure Portal.
- **Binnen 2 uur** : als u rapportage niet hebt ingeschakeld in de Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hoe kan ik activiteiten gegevens weer geven na het verkrijgen van een Premium-licentie?

Als u al activiteiten gegevens met uw gratis licentie hebt, kunt u deze direct na de upgrade bekijken. Als u geen gegevens hebt, duurt het één of twee dagen voordat de gegevens in de rapporten worden weer gegeven nadat u een upgrade hebt uitgevoerd naar een Premium-licentie.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>Kan ik de gegevens van de afgelopen maand zien na het ophalen van een Azure AD Premium-licentie?

Als u onlangs bent overgeschakeld naar een Premium-versie (met inbegrip van een proef versie), kunt u in eerste instantie de gegevens weer geven tot 7 dagen. Wanneer gegevens worden verzameld, kunt u de gegevens weer geven voor de afgelopen 30 dagen.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>Wanneer wordt door Azure AD begonnen met het verzamelen van gegevens over beveiligings signalen?  

Voor beveiligings signalen wordt het verzamelings proces gestart wanneer u zich aanmeldt om het **Identity Protection Center**te gebruiken. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>Hoe lang worden de gegevens opgeslagen in azure AD?

**Activiteitsrapporten**    

| Rapport                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Auditlogboeken             | 7 dagen        | 30 dagen             | 30 dagen             |
| Aanmeldingen               | 7 dagen        | 30 dagen             | 30 dagen             |
| Gebruik van Azure MFA        | 30 dagen       | 30 dagen             | 30 dagen             |

U kunt de gegevens voor audits en aanmeldings activiteiten langer bewaren dan de standaard retentie periode die hierboven wordt beschreven door deze te routeren naar een Azure-opslag account met behulp van Azure Monitor. Zie [Azure AD-logboeken archiveren in een Azure Storage-account](quickstart-azure-monitor-route-logs-to-storage-account.md)voor meer informatie.

**Beveiligings signalen**

| Rapport         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Gebruikers die risico lopen  | 7 dagen        | 30 dagen             | 90 dagen             |
| Riskante aanmeldingen | 7 dagen        | 30 dagen             | 90 dagen             |

---

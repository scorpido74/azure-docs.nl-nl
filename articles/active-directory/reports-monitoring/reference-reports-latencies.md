---
title: Latenlaten van Azure Active Directory-rapportage | Microsoft Documenten
description: Meer informatie over de tijd die het kost om rapportagegebeurtenissen weer te geven in uw Azure-portal
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 9b88958d-94a2-4f4b-a18c-616f0617a24e
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d031546bb4f1f05e9ea2abb5b74fe911b0b507f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74007704"
---
# <a name="azure-active-directory-reporting-latencies"></a>Azure Active Directory reporting latencies (Rapportagevertraging in Azure Active Directory)

Latentie is de hoeveelheid tijd die nodig is om Azure Active Directory-rapportagegegevens (Azure AD) weer te geven in de [Azure-portal.](https://portal.azure.com) In dit artikel vindt u de verwachte latentie voor de verschillende typen rapporten. 

## <a name="activity-reports"></a>Activiteitsrapporten

Er zijn twee soorten activiteitenrapporten:

- [Aanmeldingsprogramma's](concept-sign-ins.md) – Biedt informatie over het gebruik van beheerde toepassingen en aanmeldingsactiviteiten voor gebruikers
- [Controlelogboeken](concept-audit-logs.md) - Biedt informatie over systeemactiviteit over gebruikers en groepen, beheerde toepassingen en directoryactiviteiten

In de volgende tabel worden de latentiegegevens voor activiteitenrapporten weergegeven. 

> [!NOTE]
> **Latentie (95e percentiel)** verwijst naar de tijd waarin 95% van de logboeken wordt gerapporteerd, en **Latentie (99e percentiel)** verwijst naar de tijd waarmee 99% van de logboeken zal worden gerapporteerd. 
>

| Rapport | Latentie (95e percentiel) |Latentie (99e percentiel)|
| :-- | --- | --- |
| Auditlogboeken | 2 minuten  | 5 minuten  |
| Aanmeldingen | 2 minuten  | 5 minuten |

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>Hoe snel kan ik activiteitengegevens zien nadat ik een premium licentie heb verkregen?

Als u al activiteitengegevens hebt met uw gratis licentie, dan u deze onmiddellijk zien bij de upgrade. Als u geen gegevens hebt, duurt het een of twee dagen voordat de gegevens in de rapporten worden weergegeven nadat u een upgrade naar een premiumlicentie hebt uitgevoerd.

## <a name="security-reports"></a>Beveiligingsrapporten

Er zijn twee typen beveiligingsrapporten:

- [Riskante aanmeldingen](concept-risky-sign-ins.md) - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is. 
- [Gebruikers voor wie wordt aangegeven dat ze risico lopen](concept-user-at-risk.md) - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast. 

In de volgende tabel worden de latentiegegevens voor beveiligingsrapporten weergegeven.

| Rapport | Minimum | Average | Maximum |
| :-- | --- | --- | --- |
| Gebruikers die risico lopen          | 5 minuten   | 15 minuten  | 2 uur  |
| Riskante aanmeldingen         | 5 minuten   | 15 minuten  | 2 uur  |

## <a name="risk-detections"></a>Risicodetectie

Azure AD maakt gebruik van adaptieve machine learning-algoritmen en heuristiek om verdachte acties te detecteren die gerelateerd zijn aan uw gebruikersaccounts. Elke gedetecteerde verdachte actie wordt opgeslagen in een record genaamd een **risicodetectie.**

In de volgende tabel worden de latentie-informatie voor risicodetecties weergegeven.

| Rapport | Minimum | Average | Maximum |
| :-- | --- | --- | --- |
| Aanmeldingen vanaf anonieme IP-adressen |5 minuten |15 minuten |2 uur |
| Aanmeldingen vanaf onbekende locaties |5 minuten |15 minuten |2 uur |
| Gebruikers van wie de referenties zijn gelekt |2 uur |4 uur |8 uur |
| Onmogelijke reis naar ongewone locaties |5 minuten |1 uur |8 uur  |
| Aanmeldingen vanaf geïnfecteerde apparaten |2 uur |4 uur |8 uur  |
| Aanmeldingen van IP-adressen met verdachte activiteit |2 uur |4 uur |8 uur  |


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van Azure AD-rapporten](overview-reports.md)
* [Programmatische toegang tot Azure AD-rapporten](concept-reporting-api.md)
* [Risicodetecties van Azure Active Directory](concept-risk-events.md)

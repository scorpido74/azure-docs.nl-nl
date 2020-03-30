---
title: Wat is de modus Alleen voor rapportvoorwaardelijke toegang? - Azure Active Directory
description: Hoe kan de modus Alleen-rapporthelpen bij de implementatie van beleid voor voorwaardelijke toegang
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd41e79a1e08c57e806f6ada32faccfa5fdf5792
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295290"
---
# <a name="what-is-conditional-access-report-only-mode"></a>Wat is de modus Alleen voor rapportvoorwaardelijke toegang?

Voorwaardelijke toegang wordt veel gebruikt door onze klanten om veilig te blijven door de juiste toegangscontroles toe te passen in de juiste omstandigheden. Een van de uitdagingen bij het implementeren van een beleid voor voorwaardelijke toegang in uw organisatie is echter het bepalen van de impact op eindgebruikers. Het kan moeilijk zijn om te anticiperen op het aantal en de namen van gebruikers die worden beïnvloed door gemeenschappelijke implementatie-initiatieven, zoals het blokkeren van verouderde verificatie, het vereisen van meervoudige verificatie voor een populatie gebruikers of het implementeren van aanmeldingsrisicobeleid. 

Rapportmodus is een nieuwe status van het beleid voor voorwaardelijke toegang waarmee beheerders de impact van beleid voor voorwaardelijke toegang kunnen evalueren voordat ze in hun omgeving worden ingeschakeld.  Met de release van rapport-only modus:

- Beleid voor voorwaardelijke toegang kan worden ingeschakeld in de modus Alleen rapporteren.
- Tijdens het aanmelden wordt beleid in de modus alleen-rapporteren geëvalueerd, maar niet afgedwongen.
- De resultaten worden geregistreerd in de tabbladen **Voorwaardelijke toegang** en Alleen **rapport (Voorvertoning)** van de aanmeldingslogboekgegevens.
- Klanten met een Azure Monitor-abonnement kunnen de impact van hun beleid voor voorwaardelijke toegang controleren met behulp van de werkmap Voorwaardelijke toegang-inzichten.

> [!WARNING]
> Beleidsregels in de rapportmodus waarvoor compatibele apparaten compatibel zijn, kunnen gebruikers op Mac, iOS en Android vragen om een apparaatcertificaat te selecteren tijdens de beleidsevaluatie, ook al wordt de naleving van het apparaat niet afgedwongen. Deze aanwijzingen kunnen worden herhaald totdat het apparaat compatibel is gemaakt. Om te voorkomen dat eindgebruikers tijdens het aanmelden aanwijzingen ontvangen, sluit u apparaatplatforms Mac, iOS en Android uit van beleid dat alleen rapporteren aan het beleid voor naleving van apparaten.

![Tabblad Alleen-rapport in azure AD-aanmeldingslogboek](./media/concept-conditional-access-report-only/report-only-detail-in-sign-in-log.png)

## <a name="policy-results"></a>Beleidsresultaten

Wanneer een beleid in de rapportmodus wordt geëvalueerd voor een bepaalde aanmelding, zijn er vier nieuwe mogelijke resultaatwaarden:

| Resultaat | Beschrijving |
| --- | --- |
| Alleen-rapport: Succes | Alle geconfigureerde beleidsvoorwaarden, vereiste niet-interactieve subsidiebesturingselementen en sessiebesturingselementen werden voldaan. Een vereiste voor meervoudige verificatie wordt bijvoorbeeld voldaan door een MFA-claim die al in het token aanwezig is, of een compatibel apparaatbeleid wordt voldaan door een apparaatcontrole uit te voeren op een compatibel apparaat. |
| Alleen rapport: mislukt | Aan alle geconfigureerde beleidsvoorwaarden was voldaan, maar niet aan alle vereiste niet-interactieve subsidiebesturingselementen of sessiebesturingselementen is voldaan. Een beleid is bijvoorbeeld van toepassing op een gebruiker waarbij een blokbesturingselement is geconfigureerd of een apparaat voldoet aan een compatibel apparaatbeleid. |
| Alleen rapport: actie van de gebruiker vereist | Aan alle geconfigureerde beleidsvoorwaarden is voldaan, maar er moet actie van de gebruiker worden ondernomen om te voldoen aan de vereiste subsidiecontroles of sessiebesturingselementen. Met de modus Alleen-rapporteren wordt de gebruiker niet gevraagd om aan de vereiste besturingselementen te voldoen. Gebruikers worden bijvoorbeeld niet gevraagd om multi-factor authenticatie uitdagingen of gebruiksvoorwaarden.   |
| Rapport-only: Niet toegepast | Niet aan alle geconfigureerde beleidsvoorwaarden was voldaan. De gebruiker is bijvoorbeeld uitgesloten van het beleid of het beleid is alleen van toepassing op bepaalde vertrouwde locaties met de naam. |

## <a name="conditional-access-insights-workbook"></a>Werkmap voorwaardelijke toegang tot insights

Beheerders hebben de mogelijkheid om meerdere beleidsregels te maken in de modus Alleen rapporteren, dus het is noodzakelijk om zowel de individuele impact van elk beleid als de gecombineerde impact van meerdere beleidsregels samen te begrijpen. Met de nieuwe werkmap Conditional Access Insights kunnen beheerders voorwaardelijke toegangsquery's visualiseren en de impact van een beleid voor een bepaald tijdsbereik, reeks toepassingen en gebruikers controleren. 
 
## <a name="next-steps"></a>Volgende stappen

[Modus alleen rapport configureren voor een beleid voor voorwaardelijke toegang](howto-conditional-access-report-only.md)

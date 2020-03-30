---
title: Naslaginformatieover zoekinformatie over Azure Active Directory-bewerkingen
description: Deze handleiding voor bewerkingen beschrijft de controles en acties die u moet uitvoeren om identiteits- en toegangsbeheer, verificatie, beheer en bewerkingen te beveiligen en te onderhouden
services: active-directory
author: martincoetzer
manager: daveba
tags: azuread
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: fundamentals
ms.date: 10/31/2019
ms.author: martinco
ms.openlocfilehash: c815256e60e5ca6582220d247d587a4488c4a55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74535312"
---
# <a name="azure-active-directory-operations-reference-guide"></a>Naslaginformatieover zoekinformatie over Azure Active Directory-bewerkingen

In deze handleiding voor bewerkingen worden de controles en acties beschreven die u moet uitvoeren om de volgende gebieden te beveiligen en te onderhouden:

- **[Identiteits- en toegangsbeheer](active-directory-ops-guide-iam.md)** - de mogelijkheid om de levenscyclus van identiteiten en hun rechten te beheren.
- **[Verificatiebeheer](active-directory-ops-guide-auth.md)** - de mogelijkheid om referenties te beheren, verificatie-ervaring te definiëren, toewijzing te delegeren, het gebruik te meten en toegangsbeleid te definiëren op basis van de beveiligingshouding van het bedrijf.
- **[Governance](active-directory-ops-guide-govern.md)** - de mogelijkheid om de toegekende toegang tot niet-bevoorrechte en bevoorrechte identiteiten te beoordelen en te bevestigen, te controleren en te controleren op wijzigingen in de omgeving.
- **[Bewerkingen](active-directory-ops-guide-ops.md)** - optimaliseer de bewerkingen Azure Active Directory (Azure AD).

Sommige aanbevelingen zijn hier mogelijk niet van toepassing op de omgeving van alle klanten, bijvoorbeeld ad FS-aanbevolen procedures zijn mogelijk niet van toepassing als uw organisatie wachtwoordhashsynchronisatie gebruikt.

> [!NOTE]
> Deze aanbevelingen zijn actueel vanaf de publicatiedatum, maar kunnen in de loop van de tijd veranderen. Organisaties moeten hun identiteitspraktijken voortdurend evalueren terwijl Microsoft-producten en -services in de loop van de tijd evolueren. Aanbevelingen kunnen worden gewijzigd wanneer organisaties zich abonneren op een andere Azure AD Premium-licentie. Azure AD Premium P2 bevat bijvoorbeeld meer governanceaanbevelingen.

## <a name="stakeholders"></a>Belanghebbenden

Elke sectie in deze referentiegids beveelt aan belanghebbenden toe te stellen om belangrijke taken met succes te plannen en uit te voeren. In de volgende tabel wordt de lijst van alle belanghebbenden in deze gids beschreven:

| Belanghebbende | Beschrijving |
| :- | :- |
| IAM Operations Team | Dit team verzorgt het beheer van de dagelijkse activiteiten van het Identity and Access Management-systeem |
| Productiviteitsteam | Dit team is eigenaar en beheerder van de productiviteitstoepassingen zoals e-mail, delen en samenwerken van bestanden, instant messaging en vergaderen. |
| Toepassings-eigenaar | Dit team is eigenaar van de specifieke toepassing vanuit een bedrijf en meestal een technisch perspectief in een organisatie. |
| InfoSec-architectuurteam | Dit team plant en ontwerpt de informatiebeveiligingspraktijken van een organisatie. |
| InfoSec Operations Team | Dit team beheert en bewaakt de geïmplementeerde informatiebeveiligingspraktijken van het InfoSec Architecture-team. |

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag met de [controles en acties voor identiteits- en toegangsbeheer.](active-directory-ops-guide-iam.md)

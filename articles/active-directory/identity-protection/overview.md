---
title: Azure Active Directory Identity Protection | Microsoft Docs
description: Meer informatie over hoe u met Azure AD Identity Protection kunt u de mogelijkheid van een aanvaller om misbruik te maken van een identiteit of apparaat te beperken en om een identiteit of een apparaat te beveiligen dat eerder is vermoed of waarvan bekend is dat deze zijn aangetast.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: overview
ms.date: 01/29/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b89cab41061376fc1d8b4cbffc8fe87b9677688
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125676"
---
# <a name="what-is-azure-active-directory-identity-protection"></a>Wat is Azure Active Directory Identity Protection?

Met [](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis#terminology) Azure Active Directory identiteits beveiliging kunnen organisaties automatische antwoorden configureren op gedetecteerde verdachte acties die betrekking hebben op gebruikers identiteiten.

## <a name="get-started"></a>Aan de slag

Micro soft heeft al meer dan tien jaar identiteiten op basis van de Cloud beveiligd. Met Azure Active Directory Identity Protection, in uw omgeving, kunt u dezelfde beveiligings systemen gebruiken die micro soft gebruikt om identiteiten te beveiligen.

Het overgrote deel van de beveiligings Risico's doen zich voor wanneer aanvallers toegang krijgen tot een omgeving door de identiteit van een gebruiker te stelen. Over de jaren zijn aanvallers steeds effectiever geworden bij inbreuken van derden en met behulp van geavanceerde phishing-aanvallen. Zodra een aanvaller toegang krijgt tot zelfs gebruikers accounts met beperkte rechten, is het relatief eenvoudig om toegang te krijgen tot belang rijke bedrijfs bronnen via een later verplaatsings beweging.

Als gevolg hiervan moet u het volgende doen:

- Alle identiteiten beveiligen, ongeacht het bevoegdheden niveau
- Proactief voor komen dat identiteiten misbruikt worden

Het detecteren van gemanipuleerde identiteiten is geen eenvoudige taak. Azure Active Directory gebruikt adaptieve machine learning algoritmen en heuristiek om afwijkingen en verdachte incidenten te detecteren die duiden op mogelijke verdachte identiteiten. Met behulp van deze gegevens genereren identiteits beveiliging rapporten en waarschuwingen waarmee u de gedetecteerde problemen kunt evalueren en de juiste oplossings-of herstel acties kunt ondernemen.

Azure Active Directory Identity Protection is meer dan een hulp programma voor bewaking en rapportage. Als u de identiteit van uw organisatie wilt beveiligen, kunt u op risico gebaseerd beleid configureren dat automatisch reageert op gedetecteerde problemen wanneer een opgegeven risico niveau is bereikt. Deze beleids regels, naast andere besturings elementen voor voorwaardelijke toegang van Azure Active Directory en [Enterprise Mobility + Security](https://docs.microsoft.com/enterprise-mobility-security/) (EMS), kunnen automatisch adaptieve herstel acties blok keren of initiëren, inclusief het opnieuw instellen van wacht woorden en multi-factor Authentication-afdwinging.

### <a name="identity-protection-capabilities"></a>Mogelijkheden voor identiteits beveiliging

**Beveiligings problemen en Risk ante accounts detecteren:**  

- Aangepaste aanbevelingen bieden voor het verbeteren van de algemene beveiligings postuur door beveiligings lekken te markeren
- Risico niveaus voor aanmelding worden berekend
- De risico niveaus van gebruikers worden berekend

**Risico detecties onderzoeken:**

- Meldingen verzenden voor risico detecties
- Risico detecties onderzoeken met behulp van relevante en contextuele informatie
- Eenvoudige werk stromen bieden voor het bijhouden van onderzoeken
- Eenvoudige toegang bieden tot herstel acties, zoals het opnieuw instellen van wacht woorden

**Beleid voor voorwaardelijke toegang op basis van risico:**

- Beleid om Risk ante aanmeldingen te beperken door aanmeldingen te blok keren of om multi-factor Authentication-uitdagingen te vereisen
- Beleid voor het blok keren of beveiligen van Risk ante gebruikers accounts
- Beleid om te vereisen dat gebruikers zich registreren voor multi-factor Authentication

## <a name="identity-protection-roles"></a>Rollen voor identiteits beveiliging

Als u de beheer activiteiten rond uw implementatie van de identiteits beveiliging wilt verdelen, kunt u verschillende rollen toewijzen. Azure AD Identity Protection ondersteunt drie Directory-rollen:

| Role | Kan doen | Kan niet |
| :-- | --- | --- |
| Globale beheerder | Volledige toegang tot identiteits beveiliging, onboarding-identiteits beveiliging| |
| Beveiligingsbeheerder | Volledige toegang tot identiteits beveiliging | Identiteits beveiliging voor onboarding, wacht woorden opnieuw instellen voor een gebruiker |
| Beveiligingslezer | Alleen-lezen toegang tot identiteits beveiliging | Identiteits beveiliging voor onboarding, gebruikers herstellen, beleid configureren, wacht woorden opnieuw instellen |

Zie [beheerders rollen toewijzen in azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md) voor meer informatie.

## <a name="detection"></a>Detectie

### <a name="vulnerabilities"></a>Beveiligingsproblemen

Azure Active Directory Identity Protection analyseert uw configuratie en detecteert beveiligings problemen die invloed kunnen hebben op de identiteiten van uw gebruikers. Zie [beveiligings problemen die worden gedetecteerd door Azure Active Directory Identity Protection](vulnerabilities.md)voor meer informatie.

### <a name="risk-detections"></a>Risico detecties

Azure Active Directory gebruikt adaptieve machine learning algoritmen en heuristiek om verdachte acties te detecteren die betrekking hebben op de identiteiten van uw gebruikers. Het systeem maakt een record voor elke gedetecteerde verdachte actie. Deze records worden ook wel risico detecties genoemd.  
Zie [Azure Active Directory-risico detectie](../active-directory-identity-protection-risk-events.md)voor meer informatie.

## <a name="investigation"></a>Onderzoek

Uw traject voor identiteits beveiliging begint meestal met het dash board voor identiteits beveiliging.

![Herstel](./media/overview/1000.png "Herstel")

Het dashboard biedt u toegang tot:

- Rapporten zoals gebruikers die zijn **gemarkeerd voor risico**, **detectie van Risico's** en **beveiligings problemen**
- Instellingen, zoals de configuratie van uw **beveiligings beleid**, **meldingen** en **registratie van multi-factor Authentication**

Het is doorgaans uw begin punt voor onderzoek. Dit is het proces van het controleren van de activiteiten, logboeken en andere relevante informatie met betrekking tot een risico detectie om te bepalen of de stappen voor herstel of beperking nood zakelijk zijn, en hoe de identiteit is u hebt geknoeid en weet hoe de aangetaste identiteit is gebruikt.

U kunt uw onderzoeksactiviteiten koppelen aan de [meldingen](notifications.md) Azure Active Directory bescherming per e-mail wordt verzonden.

## <a name="policies"></a>Beleidsregels

Azure Active Directory Identity Protection biedt u drie beleids regels om automatische reacties te implementeren:

- [Registratie beleid voor multi-factor Authentication](howto-mfa-policy.md)
- [Beleid voor gebruikers Risico's](howto-user-risk-policy.md)
- [Beleid voor aanmeldings Risico's](howto-sign-in-risk-policy.md)

## <a name="license-requirements"></a>Licentievereisten

[!INCLUDE [Active Directory P2 license](../../../includes/active-directory-p2-license.md)]

## <a name="next-steps"></a>Volgende stappen

- [Channel 9: Azure AD en identiteits weergave: Preview van identiteits beveiliging](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
- [Azure Active Directory Identity Protection inschakelen](enable.md)

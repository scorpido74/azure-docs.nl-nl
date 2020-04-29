---
title: Rapport Risk ante aanmeldingen in de portal | Microsoft Docs
description: Meer informatie over het rapport Riskante aanmeldingen in de Azure Active Directory-portal
services: active-directory
author: MarkusVi
manager: daveba
ms.assetid: 7728fcd7-3dd5-4b99-a0e4-949c69788c0f
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b77486064139895799ac5a48327377154f75da6d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78273839"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Het rapport Riskante aanmeldingen in de Azure Active Directory-portal

Azure Active Directory (Azure AD) detecteert verdachte acties die zijn gerelateerd aan uw gebruikers accounts. Voor elke gedetecteerde actie wordt een record gemaakt met de naam **risico detectie** . Zie voor meer informatie [Azure AD-risico detecties](concept-risk-events.md). 

De beveiligingsrapporten zijn beschikbaar via de [Azure-portal](https://portal.azure.com). Selecteer de blade **Azure Active Directory** en ga vervolgens naar de sectie **Beveiliging**. 

Er zijn twee verschillende beveiligings rapporten die worden berekend op basis van de risico detecties:

- **Riskante aanmeldingen** - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- **Gebruikers voor wie wordt aangegeven dat ze risico lopen** - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/10.png)

Zie [How to configure the User Risk](../identity-protection/howto-user-risk-policy.md)policies (Engelstalig) voor meer informatie over het configureren van het beleid voor het activeren van deze risico detecties.  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Wie kan het rapport Risk ante aanmeldingen openen?

De rapporten met Risk ante aanmeldingen zijn beschikbaar voor gebruikers in de volgende rollen:

- Beveiligingsbeheer
- Globale beheerder
- Beveiligingslezer

Zie [beheerders rollen weer geven en toewijzen in azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)voor meer informatie over het toewijzen van beheerders rollen aan een gebruiker in azure Active Directory.

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot een beveiligingsrapport?  

Alle versies van Azure AD bieden u rapporten met Risk ante aanmeldingen. Het detailniveau van rapporten verschilt wel per editie: 

- In de **Azure Active Directory Free-editie**krijgt u een lijst met Risk ante aanmeldingen. 

- Daarnaast kunt u met de **Azure Active Directory Premium 1** -editie enkele van de onderliggende risico detecties onderzoeken die zijn gedetecteerd voor elk rapport. 

- De versie van **Azure Active Directory Premium 2** biedt u de meest gedetailleerde informatie over alle onderliggende risico detecties. Daarnaast kunt u hiermee beveiligings beleid configureren dat automatisch reageert op geconfigureerde risico niveaus.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Rapport Risk ante aanmeldingen voor de gratis versie van Azure AD

De gratis versie van Azure AD biedt u een lijst met Risk ante aanmeldingen die zijn gedetecteerd voor uw gebruikers. Elke record bevat de volgende kenmerken:

- **Gebruiker** -de naam van de gebruiker die is gebruikt tijdens de aanmeldings bewerking.
- **IP** : het IP-adres van het apparaat dat is gebruikt om verbinding te maken met Azure Active Directory.
- **Locatie** : de locatie die wordt gebruikt om verbinding te maken met Azure Active Directory. Dit is een best mogelijke benadering op basis van traceringen, register gegevens, het terugdraaien van ups en andere informatie.
- **Tijd van aanmelden**: de tijd waarop de aanmelding heeft plaatsgevonden
- **Status**: de status van de aanmelding

![Riskante aanmeldingen](./media/concept-risky-sign-ins/01.png)

Op basis van uw onderzoek naar de Risk ante aanmelding kunt u Azure AD feedback geven door de volgende acties uit te voeren:

- Oplossen
- Markeren als fout-positief
- Negeren
- Opnieuw activeren

![Riskante aanmeldingen](./media/concept-risky-sign-ins/21.png)

Dit rapport geeft u ook een optie om het volgende te doen:

- Resources zoeken
- De rapportgegevens downloaden

![Riskante aanmeldingen](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Rapport Risk ante aanmeldingen voor Azure AD Premium-edities

Het rapport Risk ante aanmeldingen in de Azure AD Premium-edities biedt u het volgende:

- Samengevoegde informatie over de [typen risico detectie](concept-risk-events.md) die zijn gedetecteerd. Met de **Azure AD Premium P1 Edition**worden detecties die niet onder uw licentie vallen, weer gegeven als het aanmelden van de risico detectie **met een extra risico gedetecteerd**. Met de **Azure AD Premium P2-editie**krijgt u de meest gedetailleerde informatie over alle onderliggende detecties.

- Een optie voor het downloaden van het rapport

![Riskante aanmeldingen](./media/concept-risky-sign-ins/456.png)

Wanneer u een risico detectie selecteert, krijgt u een gedetailleerde rapport weergave voor deze risico detectie waarmee u het volgende kunt doen:

- Een optie voor het configureren van een [beleid voor herstel van gebruikersrisico's](../identity-protection/howto-user-risk-policy.md)  

- De detectie tijdlijn voor de risico detectie bekijken  

- Bekijk een lijst met gebruikers waarvoor deze risico detectie is gedetecteerd

- Sluit risico detecties hand matig. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Soms vindt u een risico detectie zonder een corresponderende aanmeldings vermelding in het [aanmeld rapport](concept-sign-ins.md). Dit komt doordat identiteits beveiliging het risico evalueert voor **interactieve** en **niet-interactieve** aanmeldingen, terwijl in het rapport voor aanmeldingen alleen de interactieve aanmeldingen worden weer gegeven.

Wanneer u een gebruiker selecteert, krijgt u een gedetailleerde rapportweergave voor deze gebruiker waarmee u het volgende kunt:

- De weergave Alle aanmeldingen openen

- Het gebruikerswachtwoord opnieuw instellen

- Alle gebeurtenissen sluiten

- Bekijk de gemelde risico detecties voor de gebruiker. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/324.png)

Als u een risico detectie wilt onderzoeken, selecteert u er een in de lijst.  
Hiermee opent u de Blade **Details** voor deze risico detectie. Op de Blade **Details** hebt u de mogelijkheid om een risico detectie hand matig te sluiten of een hand matig gesloten risico detectie opnieuw te activeren. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Volgende stappen

- [Het beleid voor gebruikersrisico's configureren](../identity-protection/howto-user-risk-policy.md)
- [How to configure the risk remediation policy](../identity-protection/howto-user-risk-policy.md) (Beleid voor herstel gebruikersrisico configureren)
- [Typen risico detectie](concept-risk-events.md)

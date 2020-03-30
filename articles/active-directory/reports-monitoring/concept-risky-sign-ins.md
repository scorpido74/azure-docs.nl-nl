---
title: Melding van risicovolle aanmeldingen in portal | Microsoft Documenten
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273839"
---
# <a name="risky-sign-ins-report-in-the-azure-active-directory-portal"></a>Het rapport Riskante aanmeldingen in de Azure Active Directory-portal

Azure Active Directory (Azure AD) detecteert verdachte acties die gerelateerd zijn aan uw gebruikersaccounts. Voor elke gedetecteerde actie wordt een record gemaakt dat een **risicodetectie** wordt genoemd. Zie [Azure AD-risicodetecties](concept-risk-events.md)voor meer informatie. 

De beveiligingsrapporten zijn beschikbaar via de [Azure-portal](https://portal.azure.com). Selecteer de blade **Azure Active Directory** en ga vervolgens naar de sectie **Beveiliging**. 

Er zijn twee verschillende beveiligingsrapporten die worden berekend op basis van de risicodetecties:

- **Riskante aanmeldingen** - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is.

- **Gebruikers voor wie wordt aangegeven dat ze risico lopen** - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/10.png)

Zie [Het gebruikersrisicobeleid configureren](../identity-protection/howto-user-risk-policy.md)voor meer informatie over het configureren van het beleid dat deze risicodetecties activeert.  

## <a name="who-can-access-the-risky-sign-ins-report"></a>Wie heeft toegang tot het risicovolle aanmeldingsrapport?

De riskante aanmeldingsrapporten zijn beschikbaar voor gebruikers in de volgende rollen:

- Beveiligingsbeheer
- Globale beheerder
- Beveiligingslezer

Zie [Beheerdersrollen weergeven en toewijzen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)voor meer informatie over het toewijzen van beheerdersrollen aan een gebruiker in Azure Active Directory.

## <a name="what-azure-ad-license-do-you-need-to-access-a-security-report"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot een beveiligingsrapport?  

Alle edities van Azure AD bieden u risicovolle aanmeldingsrapporten. Het detailniveau van rapporten verschilt wel per editie: 

- In de **Azure Active Directory Free-editie**krijgt u een lijst met riskante aanmeldingen. 

- Bovendien u met de **Azure Active Directory Premium 1-editie** enkele onderliggende risicodetecties onderzoeken die voor elk rapport zijn gedetecteerd. 

- De **Azure Active Directory Premium 2-editie** biedt u de meest gedetailleerde informatie over alle onderliggende risicodetecties en stelt u ook in staat om beveiligingsbeleid te configureren dat automatisch reageert op geconfigureerde risiconiveaus.

## <a name="risky-sign-ins-report-for-azure-ad-free-edition"></a>Riskante aanmeldingen rapport voor Azure AD gratis editie

De gratis editie van Azure AD biedt u een lijst met riskante aanmeldingen die zijn gedetecteerd voor uw gebruikers. Elke record bevat de volgende kenmerken:

- **Gebruiker** - De naam van de gebruiker die is gebruikt tijdens de aanmeldingsbewerking.
- **IP** - Het IP-adres van het apparaat dat is gebruikt om verbinding te maken met Azure Active Directory.
- **Locatie** - De locatie die wordt gebruikt om verbinding te maken met Azure Active Directory. Dit is een best effort benadering op basis van traces, registergegevens, reverse look ups en andere informatie.
- **Tijd van aanmelden**: de tijd waarop de aanmelding heeft plaatsgevonden
- **Status**: de status van de aanmelding

![Riskante aanmeldingen](./media/concept-risky-sign-ins/01.png)

Op basis van uw onderzoek naar de riskante aanmelding u feedback geven aan Azure AD door de volgende acties uit te voeren:

- Oplossen
- Markeren als fout-positief
- Negeren
- Opnieuw activeren

![Riskante aanmeldingen](./media/concept-risky-sign-ins/21.png)

Dit rapport biedt u ook de mogelijkheid om:

- Resources zoeken
- De rapportgegevens downloaden

![Riskante aanmeldingen](./media/concept-risky-sign-ins/93.png)

## <a name="risky-sign-ins-report-for-azure-ad-premium-editions"></a>Riskantaanmeldingsrapport voor Azure AD Premium-edities

Het risicovolle aanmeldingsrapport in de Azure AD Premium-edities biedt u de tijd:

- Geaggregeerde informatie over de [gedetecteerde typen risicodetectie.](concept-risk-events.md) Met de **Azure AD Premium P1-editie**worden detecties die niet onder uw licentie vallen, weergegeven als de tekendetectie van risicodetectie **met extra risico's gedetecteerd.** Met de **Azure AD Premium P2-editie**krijgt u de meest gedetailleerde informatie over alle onderliggende detecties.

- Een optie voor het downloaden van het rapport

![Riskante aanmeldingen](./media/concept-risky-sign-ins/456.png)

Wanneer u een risicodetectie selecteert, krijgt u een gedetailleerde rapportweergave voor deze risicodetectie waarmee u:

- Een optie voor het configureren van een [beleid voor herstel van gebruikersrisico's](../identity-protection/howto-user-risk-policy.md)  

- Bekijk de detectietijdlijn voor de risicodetectie  

- Een lijst bekijken van gebruikers waarvoor deze risicodetectie is gedetecteerd

- Handmatig risicodetecties sluiten. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/457.png)

> [!IMPORTANT]
> Soms vindt u een risicodetectie zonder een overeenkomstige aanmeldingsvermelding in het [aanmeldingsrapport.](concept-sign-ins.md) Identiteitsbescherming evalueert namelijk het risico voor zowel **interactieve** als **niet-interactieve** aanmeldingen, terwijl in het aanmeldingsrapport alleen de interactieve aanmeldingen worden weergegeven.

Wanneer u een gebruiker selecteert, krijgt u een gedetailleerde rapportweergave voor deze gebruiker waarmee u het volgende kunt:

- De weergave Alle aanmeldingen openen

- Het gebruikerswachtwoord opnieuw instellen

- Alle gebeurtenissen sluiten

- Onderzoek gerapporteerde risicodetecties voor de gebruiker. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/324.png)

Als u een risicodetectie wilt onderzoeken, selecteert u er een in de lijst.  
Hiermee wordt het **detailblad** geopend voor deze risicodetectie. Op het **detailblad** hebt u de mogelijkheid om handmatig een risicodetectie te sluiten of een handmatig gesloten risicodetectie opnieuw te activeren. 

![Riskante aanmeldingen](./media/concept-risky-sign-ins/325.png)

## <a name="next-steps"></a>Volgende stappen

- [Het beleid voor gebruikersrisico's configureren](../identity-protection/howto-user-risk-policy.md)
- [How to configure the risk remediation policy](../identity-protection/howto-user-risk-policy.md) (Beleid voor herstel gebruikersrisico configureren)
- [Risicodetectietypen](concept-risk-events.md)

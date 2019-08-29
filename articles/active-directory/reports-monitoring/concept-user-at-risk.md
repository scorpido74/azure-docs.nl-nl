---
title: Beveiligingsrapport over gebruikers voor wie wordt aangegeven dat ze risico lopen in de Azure Active Directory-portal | Microsoft Docs
description: Meer informatie over het beveiligingsrapport over gebruikers voor wie wordt aangegeven dat ze risico lopen in de Azure Active Directory-portal
services: active-directory
author: cawrites
manager: daveba
ms.assetid: addd60fe-d5ac-4b8b-983c-0736c80ace02
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 01/17/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e6b79c7d5c2ed9744dc00eb1588c35f8ea94a76
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70127642"
---
# <a name="users-flagged-for-risk-report-in-the-azure-portal"></a>Rapport met gebruikers die risico lopen in de Azure-portal

Azure Active Directory (Azure AD) detecteert verdachte activiteit in verband met uw gebruikersaccounts. Voor elke gedetecteerde actie wordt een record gemaakt met de naam [risico detectie](concept-risk-events.md) .

De beveiligingsrapporten zijn beschikbaar via de [Azure-portal](https://portal.azure.com). Selecteer de blade **Azure Active Directory** en ga vervolgens naar de sectie **Beveiliging**. 

De gedetecteerde risico detecties worden gebruikt om het volgende te berekenen:

- **Riskante aanmeldingen** - Een riskante aanmelding is een indicator van een aanmeldingspoging die mogelijk is uitgevoerd door iemand die geen rechtmatige eigenaar van een gebruikersaccount is. 

- **Gebruikers voor wie wordt aangegeven dat ze risico lopen** - Een riskante gebruiker is een indicator van een gebruikersaccount dat mogelijk is aangetast. 

Zie [How to configure the User Risk](../identity-protection/howto-user-risk-policy.md)policies (Engelstalig) voor meer informatie over het configureren van het beleid voor het activeren van deze risico detecties. 

![Riskante aanmeldingen](./media/concept-user-at-risk/10.png)


## <a name="what-azure-ad-license-do-you-need-to-access-the-users-at-risk-report"></a>Welke Azure AD-licentie heb ik nodig voor toegang tot het rapport met gebruikers die risico lopen?  

Alle edities van Azure Active Directory bieden rapporten over gebruikers voor wie wordt aangegeven dat ze risico lopen. Het detailniveau van rapporten verschilt wel per editie: 

- In de edities **Azure Active Directory Free en Basic** hebt u toegang tot een lijst met gebruikers voor wie wordt aangegeven dat ze risico lopen. 

- Daarnaast kunt u met de **Azure Active Directory Premium 1** -editie enkele van de onderliggende risico detecties onderzoeken die zijn gedetecteerd voor elk rapport. 

- De versie van **Azure Active Directory Premium 2** biedt u de meest gedetailleerde informatie over alle onderliggende risico detecties. Daarnaast kunt u hiermee beveiligings beleid configureren dat automatisch reageert op geconfigureerde risico niveaus.


## <a name="users-at-risk-report-for-azure-ad-free-and-basic-editions"></a>Rapport met gebruikers die risico lopen voor de gratis en Basic-editie van Azure AD

Het rapport over gebruikers voor wie wordt aangegeven dat ze risico lopen in de gratis en Basic-editie van Azure AD biedt een lijst gebruikersaccounts die mogelijk zijn aangetast. 

![Riskante aanmeldingen](./media/concept-user-at-risk/03.png)

Door een gebruiker te selecteren, kunt u aanmeldingsgegevens bekijken. Controleer de aanmeldgeschiedenis van gebruikers die risico lopen en stel het wachtwoord indien nodig opnieuw in.

Dit rapport biedt de volgende mogelijkheden:

- Het rapport downloaden
- Gebruikers zoeken

    ![Riskante aanmeldingen](./media/concept-user-at-risk/16.png)

Voor gedetailleerdere informatie hebt u een premium-licentie nodig.

## <a name="users-at-risk-report-for-azure-ad-premium-editions"></a>Rapport met gebruikers die risico lopen voor Premium-edities van Azure AD

Het rapport over gebruikers voor wie wordt aangegeven dat ze risico lopen in de Azure AD Premium-edities biedt u het volgende:

- Een lijst met gebruikersaccounts die mogelijk zijn aangetast 

- Samengevoegde informatie over de [typen risico detectie](concept-risk-events.md) die zijn gedetecteerd

- Een optie voor het downloaden van het rapport

- Een optie voor het configureren van een [beleid voor herstel van gebruikersrisico's](../identity-protection/howto-user-risk-policy.md)  

![Riskante aanmeldingen](./media/concept-user-at-risk/71.png)

Wanneer u een gebruiker selecteert, krijgt u een gedetailleerde rapportweergave voor deze gebruiker waarmee u het volgende kunt:

- De weergave Alle aanmeldingen openen

- Het gebruikerswachtwoord opnieuw instellen

- Alle gebeurtenissen sluiten

- Bekijk de gemelde risico detecties voor de gebruiker. 

![Riskante aanmeldingen](./media/concept-user-at-risk/324.png)

Als u een risico detectie wilt onderzoeken, selecteert u er een in de lijst om de Blade **Details** voor deze risico detectie te openen. Op de Blade **Details** hebt u de mogelijkheid om een risico detectie hand matig te sluiten of een hand matig gesloten risico detectie opnieuw te activeren. 

![Riskante aanmeldingen](./media/concept-user-at-risk/325.png)


## <a name="next-steps"></a>Volgende stappen

- [Het beleid voor gebruikersrisico's configureren](../identity-protection/howto-user-risk-policy.md)
- [How to configure the risk remediation policy](../identity-protection/howto-user-risk-policy.md) (Beleid voor herstel gebruikersrisico configureren)
- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)


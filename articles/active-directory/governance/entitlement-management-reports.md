---
title: Rapporten en logboeken weer geven in het beheer van rechten van Azure AD-Azure Active Directory
description: Meer informatie over het weer geven van het rapport van gebruikers toewijzingen en controle Logboeken in Azure Active Directory rechten beheer.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/28/2019
ms.author: ajburnle
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3302fc3b2513794cd66d1ebf6db2cbcdb0f713dd
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73173878"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Rapporten en logboeken weer geven in het beheer van rechten van Azure AD

De Azure AD-beheer rapporten en het Azure AD-controle logboek bieden aanvullende informatie over de resources waartoe gebruikers toegang hebben. Als beheerder kunt u de toegangs pakketten en resource toewijzingen voor een gebruiker weer geven en aanvraag logboeken voor controle doeleinden bekijken of de status van de aanvraag van een gebruiker vaststellen. In dit artikel wordt beschreven hoe u de rechten beheer rapporten en Azure AD-controle Logboeken kunt gebruiken.

Bekijk de volgende video voor meer informatie over hoe u kunt zien welke resources gebruikers toegang hebben tot het rechten beheer:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Toegangs pakketten voor een gebruiker weer geven

Met dit rapport kunt u een lijst weer geven van alle toegangs pakketten die een gebruiker kan aanvragen en de toegangs pakketten die momenteel aan de gebruiker zijn toegewezen.

**Vereiste rol:** Globale beheerder of gebruikers beheerder

1. Klik op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het linkermenu op **rapporten**.

1. Klik op **toegangs pakketten voor een gebruiker**.

1. Klik op **gebruikers selecteren** om het deel venster gebruikers selecteren te openen.

1. Zoek de gebruiker in de lijst en klik vervolgens op **selecteren**.

    Op het tabblad **can request** wordt een lijst weer gegeven met de toegangs pakketten die de gebruiker kan aanvragen. Deze lijst wordt bepaald door het [aanvraag beleid](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) dat voor de toegangs pakketten is gedefinieerd. 

    ![Toegangs pakketten voor een gebruiker](./media/entitlement-management-reports/access-packages-report.png)

1. Als er meer dan één resource rollen of-beleid is voor een toegangs pakket, klikt u op de vermelding resource rollen of beleid om de selectie details te bekijken.

1. Klik op het tabblad **toegewezen** voor een overzicht van de toegangs pakketten die momenteel aan de gebruiker zijn toegewezen. Wanneer een toegangs pakket wordt toegewezen aan een gebruiker, betekent dit dat de gebruiker toegang heeft tot alle resource rollen in het toegangs pakket.

## <a name="view-resource-assignments-for-a-user"></a>Resource toewijzingen voor een gebruiker weer geven

Met dit rapport kunt u de resources weer geven die momenteel aan een gebruiker zijn toegewezen in het rechts beheer. Houd er rekening mee dat dit rapport is voor resources die worden beheerd met het rechten beheer. De gebruiker heeft mogelijk toegang tot andere resources in uw directory buiten het beheer van rechten.

**Vereiste rol:** Globale beheerder of gebruikers beheerder

1. Klik op **Azure Active Directory** en klik vervolgens op **Identity governance**.

1. Klik in het linkermenu op **rapporten**.

1. Klik op **resource toewijzingen voor een gebruiker**.

1. Klik op **gebruikers selecteren** om het deel venster gebruikers selecteren te openen.

1. Zoek de gebruiker in de lijst en klik vervolgens op **selecteren**.

    Er wordt een lijst weer gegeven met de resources die momenteel aan de gebruiker zijn toegewezen. De lijst bevat ook het toegangs pakket en het beleid waarvan ze de resource functie hebben ontvangen, samen met de begin-en eind datum voor toegang.
    
    Als een gebruiker toegang heeft tot dezelfde resource in twee of meer pakketten, kunt u op een pijl klikken om elk pakket en beleid weer te geven.

    ![Resource toewijzingen voor een gebruiker](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>De status van de aanvraag van een gebruiker bepalen

Als u meer wilt weten over de manier waarop een gebruiker toegang tot een toegangs pakket heeft aangevraagd en gekregen, kunt u het Azure AD-controle logboek gebruiken. U kunt met name de logboek records in de categorieën `EntitlementManagement` en `UserManagement` gebruiken om meer informatie te krijgen over de verwerkings stappen voor elke aanvraag.  

1. Klik op **Azure Active Directory** en klik vervolgens op **audit logboeken**.

1. Wijzig bovenaan de **categorie** in `EntitlementManagement` of `UserManagement`, afhankelijk van de controle record die u zoekt.  

1. Klik op **Apply** (Toepassen).

1. Klik op **downloaden**om de logboeken te downloaden.

Wanneer Azure AD een nieuwe aanvraag ontvangt, wordt een controle record geschreven waarin de **categorie** wordt `EntitlementManagement` en is de **activiteit** meestal `User requests access package assignment`.  In het geval van een directe toewijzing die in de Azure Portal is gemaakt, wordt het veld **activiteit** van de controle record `Administrator directly assigns user to access package`en wordt de gebruiker die de toewijzing uitvoert, geïdentificeerd door de **ActorUserPrincipalName**.

Azure AD schrijft extra audit records tijdens de uitvoering van de aanvraag, waaronder:

| Category | Activiteit | Aanvraag status |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Aanvraag heeft geen goed keuring vereist |
| `UserManagement` | `Create request approval` | Aanvraag moet worden goedgekeurd |
| `UserManagement` | `Add approver to request approval` | Aanvraag moet worden goedgekeurd |
| `EntitlementManagement` | `Approve access package assignment request` | Aanvraag goedgekeurd |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Aanvraag goedgekeurd of vereist geen goed keuring |

Wanneer een gebruiker toegang toegewezen krijgt, schrijft Azure AD een controle record voor de `EntitlementManagement` categorie met de **activiteit** `Fulfill access package assignment`.  De gebruiker die de toegang heeft ontvangen, wordt aangeduid met het veld **ActorUserPrincipalName** .

Als er geen toegang is toegewezen, schrijft Azure AD een controle record voor de `EntitlementManagement` categorie met de **activiteit** `Deny access package assignment request`, als de aanvraag is geweigerd door een goed keurder of `Access package assignment request timed out (no approver action taken)`, als er een time-out is opgetreden voor de aanvraag voordat een fiatteur kan goed keuren.

Wanneer de toewijzing van het toegangs pakket van de gebruiker verloopt, wordt geannuleerd door de gebruiker of door een beheerder wordt verwijderd, schrijft Azure AD een controle record voor de `EntitlementManagement` categorie met de **activiteit** van `Remove access package assignment`.

## <a name="next-steps"></a>Volgende stappen

- [Het beheer van rechten voor Azure AD oplossen](entitlement-management-troubleshoot.md)
- [Algemene scenario's](entitlement-management-scenarios.md)

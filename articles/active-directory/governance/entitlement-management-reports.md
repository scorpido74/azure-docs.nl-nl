---
title: Rapporten weergeven & logboeken in beheer van rechten - Azure AD
description: Meer informatie over het weergeven van het rapport- en controlelogboeken voor gebruikerstoewijzingen in Azure Active Directory-rechtenbeheer.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: jocastel-MSFT
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: jocastel
ms.collection: M365-identity-device-management
ms.openlocfilehash: 514f8e86d6bd28cc5212e0f0058f00e270f43e35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128422"
---
# <a name="view-reports-and-logs-in-azure-ad-entitlement-management"></a>Rapporten en logboeken weergeven in Azure AD-rechtenbeheer

De beheerrapporten voor Azure AD-rechten beheer en Azure AD-controlelogboek bevatten aanvullende details over welke resources gebruikers toegang hebben. Als beheerder u de toegangspakketten en resourcetoewijzingen voor een gebruiker bekijken en aanvraaglogboeken weergeven voor controledoeleinden of om de status van de aanvraag van een gebruiker te bepalen. In dit artikel wordt beschreven hoe u de beheerrapporten voor rechten en Azure AD-controlelogboeken gebruiken.

Bekijk de volgende video voor meer informatie over de informatie over de toegang van bronnen die gebruikers hebben in het beheer van rechten:

>[!VIDEO https://www.youtube.com/embed/omtNJ7ySjS0]

## <a name="view-access-packages-for-a-user"></a>Toegangspakketten voor een gebruiker weergeven

Met dit rapport u alle toegangspakketten weergeven die een gebruiker kan aanvragen en de toegangspakketten die momenteel aan de gebruiker zijn toegewezen.

**Vereiste rol:** Globale beheerder of gebruikersbeheerder

1. Klik op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Rapporten**.

1. Klik **op Access-pakketten voor een gebruiker**.

1. Klik **op Gebruikers selecteren** om het deelvenster Gebruikers selecteren te openen.

1. Zoek de gebruiker in de lijst en klik op **Selecteren**.

    Op het tabblad **Kan aanvragen** wordt een lijst weergegeven met de toegangspakketten die de gebruiker kan aanvragen. Deze lijst wordt bepaald door het [aanvraagbeleid](entitlement-management-access-package-request-policy.md#for-users-in-your-directory) dat is gedefinieerd voor de toegangspakketten. 

    ![Toegang tot pakketten voor een gebruiker](./media/entitlement-management-reports/access-packages-report.png)

1. Als er meer dan één resourcerollen of -beleid voor een toegangspakket zijn, klikt u op de bronrollen of beleidsinvoer om selectiedetails te bekijken.

1. Klik **op het** tabblad Toegewezen om een lijst te zien met de toegangspakketten die momenteel aan de gebruiker zijn toegewezen. Wanneer een toegangspakket aan een gebruiker is toegewezen, betekent dit dat de gebruiker toegang heeft tot alle resourcerollen in het toegangspakket.

## <a name="view-resource-assignments-for-a-user"></a>Resourcetoewijzingen voor een gebruiker weergeven

Met dit rapport u de resources weergeven die momenteel aan een gebruiker zijn toegewezen in het beheer van rechten. Houd er rekening mee dat dit rapport is voor resources die worden beheerd met beheer van rechten. De gebruiker heeft mogelijk toegang tot andere bronnen in uw directory buiten het beheer van de rechten.

**Vereiste rol:** Globale beheerder of gebruikersbeheerder

1. Klik op **Azure Active Directory** en klik vervolgens op **Identiteitsbeheer**.

1. Klik in het linkermenu op **Rapporten**.

1. Klik **op Resourcetoewijzingen voor een gebruiker**.

1. Klik **op Gebruikers selecteren** om het deelvenster Gebruikers selecteren te openen.

1. Zoek de gebruiker in de lijst en klik op **Selecteren**.

    Er wordt een lijst weergegeven met de resources die momenteel aan de gebruiker zijn toegewezen. De lijst toont ook het toegangspakket en het beleid waar ze de resourcerol van hebben gekregen, samen met de begin- en einddatum voor toegang.
    
    Als een gebruiker in twee of meer pakketten toegang heeft gekregen tot dezelfde resource, u op een pijl klikken om elk pakket en elk beleid te bekijken.

    ![Resourcetoewijzingen voor een gebruiker](./media/entitlement-management-reports/resource-assignments-report.png)

## <a name="determine-the-status-of-a-users-request"></a>De status van het verzoek van een gebruiker bepalen

Als u meer informatie wilt krijgen over hoe een gebruiker toegang heeft gevraagd en ontvangen tot een toegangspakket, u het Azure AD-controlelogboek gebruiken. U met name de logboekrecords in de `EntitlementManagement` categorieën en `UserManagement` categorieën gebruiken om meer informatie te krijgen over de verwerkingsstappen voor elke aanvraag.  

1. Klik op **Azure Active Directory** en klik vervolgens op **Controlelogboeken**.

1. Wijzig bovenaan de **categorie** in `EntitlementManagement` `UserManagement`een of , afhankelijk van de controlerecord die u zoekt.  

1. Klik op **Toepassen**.

1. Als u de logboeken wilt downloaden, klikt u op **Downloaden**.

Wanneer Azure AD een nieuwe aanvraag ontvangt, wordt een `EntitlementManagement` controlerecord geschreven waarin de **categorie** zich bevindt en de **activiteit** doorgaans `User requests access package assignment`.  In het geval van een directe toewijzing **Activity** die is gemaakt in `Administrator directly assigns user to access package`de Azure-portal, is het veld Activiteit van de controlerecord en wordt de gebruiker die de toewijzing uitvoert geïdentificeerd door de **ActorUserPrincipalName**.

Azure AD schrijft extra controlerecords terwijl de aanvraag aan de gang is, waaronder:

| Categorie | Activiteit | Aanvraagstatus |
| :---- | :------------ | :------------ |
| `EntitlementManagement` | `Auto approve access package assignment request` | Aanvraag vereist geen goedkeuring |
| `UserManagement` | `Create request approval` | Aanvraag vereist goedkeuring |
| `UserManagement` | `Add approver to request approval` | Aanvraag vereist goedkeuring |
| `EntitlementManagement` | `Approve access package assignment request` | Aanvraag goedgekeurd |
| `EntitlementManagement` | `Ready to fulfill access package assignment request` |Aanvraag goedgekeurd, of vereist geen goedkeuring |

Wanneer een gebruiker toegang krijgt toegewezen, schrijft `EntitlementManagement` Azure AD een auditrecord voor de categorie met **Activiteit** `Fulfill access package assignment`.  De gebruiker die de toegang heeft ontvangen, wordt geïdentificeerd door **actoruserprincipalname.**

Als de toegang niet is toegewezen, schrijft `EntitlementManagement` Azure AD `Deny access package assignment request`een controlerecord voor de categorie met `Access package assignment request timed out (no approver action taken)` **Activiteit,** als de aanvraag is geweigerd door een goedkeurder of als de aanvraag een time-out heeft gehad voordat een goedkeurder het kan goedkeuren.

Wanneer de toewijzing van het toegangspakket van de gebruiker verloopt, door de gebruiker wordt geannuleerd `EntitlementManagement` of door `Remove access package assignment`een beheerder wordt verwijderd, schrijft Azure AD een auditrecord voor de categorie met **Activiteit** van .

## <a name="next-steps"></a>Volgende stappen

- [Archiefrapporten en logboeken](entitlement-management-logs-and-reporting.md)
- [Azure AD-rechtenbeheer oplossen](entitlement-management-troubleshoot.md)
- [Algemene scenario's](entitlement-management-scenarios.md)
